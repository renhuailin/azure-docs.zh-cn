---
title: 使用脚本设置 Azure Migrate 设备
description: 了解如何使用脚本设置 Azure Migrate 设备
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: c78778f9152fd4c07fb9e550e562cfef858333c8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786730"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用脚本设置设备

按照本文所述，创建一个 [Azure Migrate 设备](./migrate-appliance-architecture.md)，以用于在 VMware 和 Hyper-V 上评估/迁移服务器。 运行脚本以创建设备，并验证其是否可以连接到 Azure。 

可以使用脚本为 VMware 和 Hyper-V 上的服务器部署设备，也可以使用从 Azure 门户下载的模板进行部署。 如果无法使用下载的模板创建设备，则使用脚本将非常有用。

- 若要使用模板，请遵循有关 [VMware](./tutorial-discover-vmware.md) 或 [Hyper-V](./tutorial-discover-hyper-v.md) 的教程。
- 若要为物理服务器设置设备，则只能使用脚本。 按照[本文](how-to-set-up-appliance-physical.md)所述操作。
- 若要在 Azure 政府版云中设置设备，请按照[本文](deploy-appliance-script-government.md)所述操作。

## <a name="prerequisites"></a>先决条件

脚本是在现有服务器上设置 Azure Migrate 设备。

- 充当设备的服务器必须满足以下硬件和操作系统要求：

方案 | 要求
--- | ---
VMware | Windows Server 2016，含 32 GB 内存，8 个 vCPU，约 80 GB 的磁盘存储
Hyper-V | Windows Server 2016，含 16 GB 内存，8 个 vCPU，约 80 GB 的磁盘存储

- 服务器还需要有一个外部虚拟交换机。 其需要静态或动态 IP 地址，并且要能访问 Internet。
- 在部署设备之前，请先检查针对 [VMware 上服务器](migrate-appliance.md#appliance---vmware)和 [Hyper-V 上](migrate-appliance.md#appliance---hyper-v)服务器的详细设备要求。
- 请勿在现有的 Azure Migrate 设备上运行脚本。

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

若要为 VMware 设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载名为 AzureMigrateInstaller-Server-Public.zip 的压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并完成首次配置。 然后将设备注册到项目中。

### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. 验证 Azure 公有云的最新设备版本和脚本：

    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

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

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 请确保不要在现有 Azure Migrate 设备上运行脚本。
2. 以管理员（提升）权限在服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. 脚本成功运行后，将启动设备 Web 应用程序，以便可以设置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[公有云](migrate-appliance.md#public-cloud-urls)的 Azure URL。

## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-V 设置设备

若要为 Hyper-V 设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2105112)下载名为 AzureMigrateInstaller-Server-Public.zip 的压缩文件，然后提取内容。 运行 PowerShell 脚本以启动设备 Web 应用。 设置设备并完成首次配置。 然后将设备注册到项目中。


### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. 验证 Azure 公有云的最新设备版本和脚本：

    **方案** | **下载** | **SHA256**
    --- | --- | ---
    Hyper-V (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 请确保不要在现有 Azure Migrate 设备上运行脚本。
2. 以管理员（提升）权限在服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 脚本成功运行后，将启动设备 Web 应用程序，以便可以设置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[公有云](migrate-appliance.md#public-cloud-urls)的 Azure URL。

## <a name="next-steps"></a>后续步骤

部署设备后，需要完成首次配置并将其注册到项目中。

- 为 [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) 设置设备。
- 为 [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) 设置设备。