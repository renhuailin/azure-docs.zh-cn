---
title: 在 Azure 政府中设置 Azure Migrate 设备
description: 了解如何在 Azure 政府中设置 Azure Migrate 设备
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 20b4a76756a70660ff638207fa20b5a4801e783d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740392"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>为 Azure 政府云设置设备

按照本文说明，为 Azure 政府云部署 [Azure Migrate 设备](./migrate-appliance-architecture.md)，以便执行以下操作：

- 发现和评估在 VMware 环境中运行的服务器，并对这些服务器进行无代理复制
- 发现和评估在 Hyper-V 环境中运行的服务器
- 发现和评估在 AWS、GCP、Xen 等其他云上运行的物理服务器或服务器。

如果要在公有云中设置设备，请按[本文](deploy-appliance-script.md)所述操作。

> [!NOTE]
> Azure 政府不支持使用模板（为 VMware 或 Hyper-V 环境中运行的服务器）部署设备的选项。 只需使用安装程序脚本。

## <a name="prerequisites"></a>先决条件

可使用脚本将 Azure Migrate 设备部署到现有物理服务器或虚拟化服务器上。

- 将充当设备的服务器必须运行 Windows Server 2016 并满足 [VMware](migrate-appliance.md#appliance---vmware)、[Hyper-V](migrate-appliance.md#appliance---hyper-v) 和[物理服务器](migrate-appliance.md#appliance---physical)的其他要求。
- 如果你在已设置 Azure Migrate 设备的服务器上运行脚本，可以选择清理现有配置，并设置具有所需配置的新设备。 执行脚本时，你将收到如下通知：
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="显示如何重新配置设备的屏幕截图。":::

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

1. 若要设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载名为 AzureMigrateInstaller.zip 的压缩文件。
1. 在要在其中部署设备的服务器上提取内容。
1. 执行 PowerShell 脚本以启动设备配置管理器。
1. 设置设备并完成首次配置。

### <a name="download-the-script"></a>下载脚本

1. 在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”。
3. 提供设备名称，并在门户中生成项目密钥。
3. 单击“下载”以下载压缩文件。

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  验证最新设备版本和哈希值：

    **下载** | **哈希值**
    --- | ---
    [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2


### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。  请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本：

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 从场景、云和连接选项中进行选择，以部署具有所需配置的设备。 例如，下面所示的选择会在 Azure 政府云上一个已建立默认（公共终结点）连接的 Azure Migrate 项目中，设置一个设备用于发现、评估和迁移 VMware 环境中运行的服务器  。

    :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="显示如何为 Vmware 设置具有所需配置的设备的屏幕截图。" lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. 此安装程序脚本执行以下操作：

- 安装代理和 Web 应用程序。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
- 在路径下创建以下文件：
    - **配置文件**：%Programdata%\Microsoft Azure\Config
    - **日志文件**：%Programdata%\Microsoft Azure\Logs

成功执行该脚本后，将自动启动设备配置管理器。


### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-V 设置设备

1. 若要设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载名为 AzureMigrateInstaller.zip 的压缩文件。
1. 在要在其中部署设备的服务器上提取内容。
1. 执行 PowerShell 脚本以启动设备配置管理器。
1. 设置设备并完成首次配置。

### <a name="download-the-script"></a>下载脚本

1.  在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2.  在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 Hyper-V”  。
3. 提供设备名称，并在门户中生成项目密钥。
3. 单击“下载”以下载压缩文件。 

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  验证最新设备版本和哈希值：

    **下载** | **哈希值**
    --- | ---
    [最新版本](https://go.microsoft.com/fwlink/?linkid=2140424) | 15a94b637a39c53ac91a2d8b21cc3cca8905187e4d9fb4d895f4fa6fd2f30b9f

### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。  请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本：

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 从场景、云和连接选项中进行选择，以部署具有所需配置的设备。 例如，下面所示的选择会在 Azure 政府云上一个已建立默认（公共终结点）连接的 Azure Migrate 项目中，设置一个设备用于发现和评估 Hyper-V 环境中运行的服务器  。

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="显示如何为 Hyper-V 设置具有所需配置的设备的屏幕截图。" lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. 此安装程序脚本执行以下操作：

    - 安装代理和 Web 应用程序。
    - 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
    - 下载并安装 IIS 可重写模块。
    - 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
    - 在路径下创建以下文件：
    - **配置文件**：%Programdata%\Microsoft Azure\Config
    - **日志文件**：%Programdata%\Microsoft Azure\Logs

成功执行该脚本后，将自动启动设备配置管理器。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-physical-servers"></a>为物理服务器设置设备

1. 若要设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载名为 AzureMigrateInstaller.zip 的压缩文件。
1. 在要在其中部署设备的服务器上提取内容。
1. 执行 PowerShell 脚本以启动设备配置管理器。
1. 设置设备并完成首次配置。

### <a name="download-the-script"></a>下载脚本

1. 在“迁移目标” > “Windows、Linux 和 SQL 服务器” > “Azure Migrate: 发现和评估”中，单击“发现”。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“物理或其他(AWS、GCP、Xen 等)”  。
3. 单击“下载”以下载压缩文件。

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  验证最新设备版本和哈希值：

    **下载** | **哈希值**
    --- | ---
    [最新版本](https://go.microsoft.com/fwlink/?linkid=2140338) | 15a94b637a39c53ac91a2d8b21cc3cca8905187e4d9fb4d895f4fa6fd2f30b9f

> [!NOTE]
> 可以使用同一脚本为已连接到公共或专用终结点的 Azure 政府云设置物理设备。

### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。  请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本：

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 从场景、云和连接选项中进行选择，以部署具有所需配置的设备。 例如，下面所示的选择会在 Azure 政府云上已建立默认（公共终结点）连接的 Azure Migrate 项目中，设置一个设备用于发现和评估物理服务器（或在 AWS、GCP、Xen 等其他云上运行的服务器） 。

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="显示如何为物理服务器设置具有所需配置的设备的屏幕截图。" lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

6. 此安装程序脚本执行以下操作：

    - 安装代理和 Web 应用程序。
    - 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
    - 下载并安装 IIS 可重写模块。
    - 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
    - 在路径下创建以下文件：
        - **配置文件**：%Programdata%\Microsoft Azure\Config
        - **日志文件**：%Programdata%\Microsoft Azure\Logs

成功执行该脚本后，将自动启动设备配置管理器。

> [!NOTE]
> 如果遇到任何问题，可以访问位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志来进行故障排除。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

## <a name="next-steps"></a>后续步骤

部署设备后，需要完成首次配置并将其注册到项目中。

- 为 [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) 设置设备。
- 为 [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) 设置设备。
- 为[物理服务器](how-to-set-up-appliance-physical.md)设置设备。
