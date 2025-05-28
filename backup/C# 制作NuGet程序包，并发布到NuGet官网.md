# 一、准备工作：在NuGet上创建并获取API Keys
网址：https://www.nuget.org/
## 1、首先需要登录，直接用微软账户登录即可

![Image](https://github.com/user-attachments/assets/788785ad-c1de-4563-bc36-b148ef2eee76)


## 2、点击右上角菜单API Keys，创建Key

![Image](https://github.com/user-attachments/assets/e2df67b6-7cb2-402f-ad50-416b63ca4ab2)

## 3、填写信息并创建
### 这里写你要上传NuGet程序包的名称 【每个NuGet程序包（对应一个名称）可以上传多个版本】

![Image](https://github.com/user-attachments/assets/429e8bf9-cb72-4b9c-8af8-986452a2ffd1)

## 4、复制API Key

![Image](https://github.com/user-attachments/assets/dd846f0c-3b31-48f0-9691-00fbc80d3910)


# 二、制作dll


# 三、创建发布文件夹


# 四、上传NuGet程序包并发布

## 1、使用界面工具上传，需要下载NuGet Package Explorer【推荐！】
### 1.1、下载NuGet Package Explorer
https://www.microsoft.com/zh-cn/p/nuget-package-explorer/9wzdncrdmdm3?activetab=pivot:overviewtab

### 1.2、创建新Package

![Image](https://github.com/user-attachments/assets/a3df5d18-399a-4e0b-9c92-724b1cdf12c2)


### 1.3、新增lib文件夹，新增文件夹，添加已有文件

![Image](https://github.com/user-attachments/assets/dda4b916-6e11-45a8-adfd-7f21d3d2bce1)

![Image](https://github.com/user-attachments/assets/79735f97-ce8c-450e-8b34-c7cd5f494b7f)

![Image](https://github.com/user-attachments/assets/2dc92730-433e-45e9-be7f-2de1b0eb49f0)


### 1.4、编辑上传数据

这里选择Edit Metadata

![Image](https://github.com/user-attachments/assets/9036c5a0-5968-40ae-a384-56d5294a0b7e)

更改参数

![Image](https://github.com/user-attachments/assets/b4f4938e-40e9-409f-aaa3-1907c84a0323)

添加项目依赖，即依赖哪些框架。比如.NETFramework，.NET Core等

![Image](https://github.com/user-attachments/assets/305b8fb9-d513-4d69-a684-5faf9af8d0f4)

![Image](https://github.com/user-attachments/assets/6d6d9cd9-73e6-438a-a209-d68dd228a237)

最后，点击确认

![Image](https://github.com/user-attachments/assets/3d8f969a-cd57-4d4c-aeb7-9f7e9edba939)

编辑完成，看下效果

![Image](https://github.com/user-attachments/assets/e892341c-16cb-4f89-8068-6f15fa91d600)

看一下Metadata Source对应的内容，其实和刚刚编辑的信息是一致的。

![Image](https://github.com/user-attachments/assets/55ff933f-fea4-4332-b27b-a0e69595185d)


### 1.5、保存nupkg文件

![Image](https://github.com/user-attachments/assets/a6fea6d9-5e5e-464c-a41c-da7a8704ae88)

![Image](https://github.com/user-attachments/assets/40e48a77-6e08-4bc6-aff5-f76e520fb630)


### 1.6、发布到NuGet

![Image](https://github.com/user-attachments/assets/9fd90acc-a18a-466a-885c-3c0aa0fb76ca)

![Image](https://github.com/user-attachments/assets/d5600d57-e303-4735-807a-5749fcac860f)

![Image](https://github.com/user-attachments/assets/1d8f9123-3746-4056-838b-afaf287a6718)
