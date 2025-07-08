# 一、环境准备
## 1.软件版本
主从库需安装 相同版本 的 PostgreSQL（建议使用 12+ 版本，支持 standby.signal 简化配置）。
## 2.路径规划：
主库路径：C:\Program Files\PostgreSQL\15\data（默认安装路径，以下简称 主库data目录）
从库路径：建议单独创建目录，如 D:\PostgreSQL\slave_data（以下简称 从库data目录，需确保为空）。
## 3.网络配置：
主库和从库需互通（关闭防火墙或开放 5432 端口）。
记录主库 IP 地址（如 192.168.1.100）和从库 IP 地址（如 192.168.1.101）。

# 二、主库配置
## 1. 修改主库配置文件
主库的配置文件位于 主库data目录 下：
postgresql.conf（核心配置）：
用记事本或编辑器打开，修改以下参数（若参数被注释，需移除 #）：
```
# 允许所有IP连接（主库需被从库访问）
listen_addresses = '*'
 
# 启用复制所需的WAL级别（replica 或 logical）
wal_level = replica
 
# 最大复制连接数（根据从库数量设置）
max_wal_senders = 5
 
# WAL日志保留数量（防止从库同步时日志被清理，建议32及以上）
wal_keep_size = 1GB  # 替代旧版本的 wal_keep_segments
 
# 允许从库在恢复时查询（可选，提升从库可用性）
hot_standby = on
```

- pg_hba.conf（访问控制）：
末尾添加从库的复制权限配置，允许从库 IP 通过 replication 角色连接：
```
# 格式：host  数据库名   用户名   从库IP/32   认证方式
host    replication     replicator    192.168.1.8/32    md5
```

- 若从库 IP 不固定，可临时用 0.0.0.0/0 允许所有 IP（生产环境不建议）。
```
# 格式：host  数据库名   用户名   从库IP/32   认证方式
host    replication     replicator    192.168.1.8/32    md5
```
## 2. 重启主库服务
打开 服务 窗口（win + r 输入 services.msc）。
找到 PostgreSQL-x64-15（版本号根据实际修改），右键 重启。
## 3. 创建复制用户
在主库中执行 SQL，创建用于复制的专用用户（也可以使用已存在的用户）：
```
-- 打开 pgAdmin 或通过 psql 连接主库，执行：
CREATE ROLE replicator WITH REPLICATION LOGIN PASSWORD '你的密码';
```

# 二、从库配置
## 1. 停止从库服务（若已安装）
若从库已安装 PostgreSQL，需先停止服务：
在服务中找到从库的 PostgreSQL 服务，右键停止，并删除默认数据目录（或备份后清空，如 C:\Program   Files\PostgreSQL\15\data）。
## 2. 从主库同步基础数据
使用 pg_basebackup 工具，从主库复制初始数据到从库：
打开 命令提示符（管理员），进入从库的 bin 目录（如 C:\Program Files\PostgreSQL\15\bin）。
执行以下命令（替换参数为实际值）：
```
pg_basebackup -h 192.168.1.8 -p 5432 -U replicator -D "D:\PostgreSQL\slave_data" -Fp -Xs -P -R
```

参数说明：
-h：主库 IP
-p：主库端口（默认 5432）
-U：复制用户（replicator）
-D：从库数据目录（需为空，路径用英文引号包裹）
-R：自动生成从库的复制配置文件

执行后输入步骤 3 中设置的 replicator 密码，等待备份完成（进度条显示 100%）。
## 3. 配置从库复制参数（可选）
pg_basebackup 加 -R 参数后，会自动在从库数据目录生成 standby.signal 文件和 postgresql.auto.conf（包含主库连接信息）。若需自定义，可手动修改：
打开从库数据目录（如 D:\PostgreSQL\slave_data），确认存在 standby.signal（空文件，标记为从库）。
编辑 postgresql.auto.conf，添加或修改：
```
primary_conninfo = 'host=192.168.1.100 port=5432 user=replicator password=你的密码 application_name=slave1'
recovery_min_apply_delay = 0  # 可选，设置延迟复制（如 30s）
```
    编辑 postgresql.conf 文件，启用从库相关参数。
```
# 启用热备模式
hot_standby = on
 
# 从库可接受的最大连接数
max_connections = 200  # 根据实际需求调整
```
## 4. 启动从库服务
若从库未注册为服务，需先注册（管理员命令提示符执行）：
```
pg_ctl register -N "PostgreSQL-Slave" -D "D:\PostgreSQL\slave_data"
```
-N：服务名称（自定义，如 PostgreSQL-Slave）
-D：从库数据目录

如果已经注册，或使用原data目录可以直接将slave_data目录修改成data
在 服务 中找到 PostgreSQL-Slave，右键 启动。(如果服务启动不起来，可尝试重启电脑)，如以下错误

<img width="480" height="42" alt="Image" src="https://github.com/user-attachments/assets/9a3b7174-7fe9-4b13-bbd1-f4d72bf11f1b" />

# 三、验证主从复制
## 1. 主库验证
连接主库，执行 SQL 查看复制状态：
SELECT client_addr, application_name, state FROM pg_stat_replication;
若返回从库 IP 和 streaming 状态，说明主从连接成功。
## 2. 从库验证
连接从库，执行 SQL 确认是否处于恢复模式（从库状态）
```
SELECT pg_is_in_recovery();  -- 返回 true 表示为从库
```
## 3. 数据同步测试
在主库创建测试表并插入数据：
```
CREATE TABLE test_replica (id int);
INSERT INTO test_replica VALUES (1);
```
在从库查询，若能看到数据，说明复制生效。

# 四、注意事项
## 1.路径与权限：
从库数据目录需确保 PostgreSQL 服务用户（默认 Network Service）有 读写权限。
避免使用中文路径，防止工具识别异常。

## 2.防火墙：
主库需开放 5432 端口（入站规则），从库需允许出站连接到主库 5432 端口。

## 3.服务管理：
重启配置文件后需重启服务生效（主库修改 postgresql.conf 或 pg_hba.conf 后必须重启）。

## 4. 故障转移：
若主库故障，可在从库执行 pg_ctl promote -D "从库数据目录" 提升为新主库（生成 recovery.signal 文件）。