---
title: 在 Azure 政府中设置 Azure Migrate 设备
description: 了解如何在 Azure 政府中设置 Azure Migrate 设备
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775198"
---
# <a name="set-up-an-appliance-in-azure-government"></a>在 Azure 政府中设置设备 

按照本文说明，在 Azure 政府云中为 VMware 环境中的服务器、Hyper-V 上的服务器以及物理服务器部署 [Azure Migrate 设备](./migrate-appliance-architecture.md)。 将通过运行脚本来创建设备，并验证其是否可连接到 Azure。 如果要在公有云中设置设备，请按[本文](deploy-appliance-script.md)所述操作。


> [!NOTE]
> Azure 政府不支持使用模板（为 VMware 环境和 Hyper-V 环境中的服务器）部署设备的选项。


## <a name="prerequisites"></a>先决条件

该脚本在现有物理服务器或虚拟化服务器上设置 Azure Migrate 设备。

- 用作设备的服务器须运行 Windows Server 2016，具有 32 GB 内存、8 个 vCPU、约 80 GB 的磁盘存储和外部虚拟交换机。 需要使用静态或动态 IP 地址，且能访问 Internet。
- 在部署设备之前，请先检查针对 [VMware 上的服务器](migrate-appliance.md#appliance---vmware)和 [Hyper-V 上的服务器](migrate-appliance.md#appliance---hyper-v)以及[物理服务器](migrate-appliance.md#appliance---physical)的详细设备要求。
- 请勿在现有 Azure Migrate 设备上运行脚本。

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

若要为 VMware 设置设备，请从 Azure 门户下载压缩文件并提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并完成首次配置。 然后将设备注册到项目。

### <a name="download-the-script"></a>下载脚本

1. 在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”。
3. 单击“下载”以下载压缩文件。

### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 验证最新设备版本和哈希值：

    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>运行脚本

以下是脚本功能：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置。
- 创建日志和配置文件，如下所示：
    - 配置文件：%ProgramData%\Microsoft Azure\Config
    - 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。
2. 以管理员（提升）权限在服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. 脚本成功运行后，将启动设备 Web 应用程序，以便可以设置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-V 设置设备

若要为 Hyper-V 设置设备，请从 Azure 门户下载压缩文件并提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并完成首次配置。 然后将设备注册到项目。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2.  在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 Hyper-V”  。
3.  单击“下载”以下载压缩文件。 


### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 验证最新设备版本和哈希值：

    **方案** | **下载** | **SHA256**
    --- | --- | ---
    Hyper-V (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>运行脚本

以下是脚本功能：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置。
- 创建日志和配置文件，如下所示：
    - 配置文件：%ProgramData%\Microsoft Azure\Config
    - 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 确保不要在已运行有 Azure Migrate 设备的服务器上运行该脚本。
2. 以管理员（提升）权限在服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示： 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. 脚本成功运行后，将启动设备 Web 应用程序，以便可以设置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-physical-servers"></a>为物理服务器设置设备

若要为 VMware 设置设备，请从 Azure 门户下载压缩文件并提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并完成首次配置。 然后将设备注册到项目。

### <a name="download-the-script"></a>下载脚本

1. 在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“未虚拟化/其他”  。
3. 单击“下载”以下载压缩文件。

### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在已将文件下载到其中的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 验证最新设备版本和哈希值：

    **方案** | **下载** _ | _ *哈希值**
    --- | --- | ---
    物理 (85 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>运行脚本

以下是脚本功能：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置。
- 创建日志和配置文件，如下所示：
    - 配置文件：%ProgramData%\Microsoft Azure\Config
    - 日志文件：%ProgramData%\Microsoft Azure\Logs

若要运行该脚本，请执行以下操作：

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 确保不要在已运行有 Azure Migrate 设备的服务器上运行该脚本。
2. 以管理员（提升）权限在服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. 脚本成功运行后，将启动设备 Web 应用程序，以便可以设置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

## <a name="next-steps"></a>后续步骤

部署设备后，需要完成首次配置并将其注册到项目中。

- 为 [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) 设置设备。
- 为 [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) 设置设备。
- 为[物理服务器](how-to-set-up-appliance-physical.md)设置设备。
