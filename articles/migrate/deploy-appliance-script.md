---
title: 使用脚本设置 Azure Migrate 设备
description: 了解如何使用脚本设置 Azure Migrate 设备
ms.topic: how-to
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 04e89d4fa783159bec8869809f6db5a7e2f6950a
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093566"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用脚本设置设备

按照本文说明，使用 PowerShell 脚本部署 [Azure Migrate 设备](./migrate-appliance-architecture.md)，以便执行以下操作：
- 发现和评估在 VMware 环境中运行的服务器，并对这些服务器进行无代理复制
- 发现和评估在 Hyper-V 环境中运行的服务器。

可以使用脚本为 VMware 和 Hyper-V 上的服务器部署设备，也可以使用从 Azure 门户下载的模板 (OVA/VHD) 进行部署。 如果无法使用下载的模板创建设备，则使用脚本将非常有用。

- 若要使用模板，请按照 [VMware](./tutorial-discover-vmware.md) 和 [Hyper-V](./tutorial-discover-hyper-v.md) 的教程操作。
- 若要为物理服务器设置设备，则只能使用脚本。 按照[本文](how-to-set-up-appliance-physical.md)所述操作。
- 若要在 Azure 政府云中设置设备，只能使用脚本。 按照[本文](deploy-appliance-script-government.md)所述操作。

## <a name="prerequisites"></a>先决条件

可以使用脚本将 Azure Migrate 设备部署到 VMware 或 Hyper-V 环境中的现有服务器上。

- 托管设备的服务器必须满足以下硬件和操作系统要求：

方案 | 要求
--- | ---
VMware | Windows Server 2016，含 32 GB 内存，8 个 vCPU，约 80 GB 的磁盘存储空间。
Hyper-V | Windows Server 2016，含 16 GB 内存，8 个 vCPU，约 80 GB 的磁盘存储空间。

- 服务器还需要有一个外部虚拟交换机。 它需要静态或动态 IP 地址。 
- 在部署设备之前，请先查看针对 [VMware](migrate-appliance.md#appliance---vmware) 和 [Hyper-V](migrate-appliance.md#appliance---hyper-v) 的详细设备要求。
- 如果你在已设置 Azure Migrate 设备的服务器上运行脚本，可以选择清理现有配置，并设置具有所需配置的新设备。 执行脚本时，你将收到如下通知：

    ![使用所需配置设置设备](./media/deploy-appliance-script/script-reconfigure-appliance.png)

## <a name="set-up-the-appliance-for-vmware"></a>为 VMware 设置设备

1. 若要设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2116601)下载名为 AzureMigrateInstaller.zip 的压缩文件。
1. 在要在其中部署设备的服务器上提取内容。
1. 执行 PowerShell 脚本以启动设备配置管理器。
1. 设置设备并完成首次配置。

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  验证最新设备版本和哈希值：

    **下载** | **哈希值**
    --- | ---
    [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

> [!NOTE]
> 同样的脚本可用于为 Azure 公有云或 Azure 政府云设置 VMware 设备。

### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。
> [!NOTE]
> 请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。 在 Azure Migrate 设备上运行脚本将删除正在运行的配置并将其替换为新定义的配置。

2. 使用管理（提升）权限在上述服务器上启动 PowerShell。

3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。

4. 通过运行以下命令，运行名为“AzureMigrateInstaller.ps1”的脚本：

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. 从场景、云和连接选项中进行选择，以部署具有所需配置的设备。 例如，下面所示的选择会设置一个设备，用于发现和评估 VMware 环境中运行的服务器，并通过 Azure 公有云上的默认（公共终结点）连接将其迁移到 Azure Migrate 项目 。

   :::image type="content" source="./media/deploy-appliance-script/script-vmware-default-inline.png" alt-text="显示如何设置具有所需配置的 VMware 设备的屏幕截图。" lightbox="./media/deploy-appliance-script/script-vmware-default-expanded.png":::

6. 此安装程序脚本执行以下操作：

 - 安装代理和 Web 应用程序。
 - 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
 - 下载并安装 IIS 可重写模块。
 - 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
 - 在路径下创建以下文件：
    - 配置文件：`%ProgramData%\Microsoft Azure\Config`
    - 日志文件：`%ProgramData%\Microsoft Azure\Logs`

成功执行该脚本后，将自动启动设备配置管理器。

> [!NOTE]
> 如果遇到任何问题，可以访问位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志来进行故障排除。

### <a name="verify-access"></a>验证访问权限

确保设备可以连接到[公有云](migrate-appliance.md#public-cloud-urls)的 Azure URL。

## <a name="set-up-the-appliance-for-hyper-v"></a>为 Hyper-V 设置设备

1. 若要设置设备，请从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2116657)下载名为 AzureMigrateInstaller.zip 的压缩文件。
1. 在要在其中部署设备的服务器上提取内容。
1. 执行 PowerShell 脚本以启动设备配置管理器。
1. 设置设备并完成首次配置。

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  验证最新设备版本和哈希值：

    **下载** | **哈希值**
    --- | ---
    [最新版本](https://go.microsoft.com/fwlink/?linkid=2116657) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

> [!NOTE]
> 同样的脚本可用于为 Azure 公有云或 Azure 政府云设置 Hyper-V 设备。

### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。
> [!NOTE]
> 请确保不要在现有 Azure Migrate 设备上运行脚本。 在 Azure Migrate 设备上运行脚本将删除正在运行的配置并将其替换为新定义的配置。

2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。
4. 通过运行以下命令，运行名为“`AzureMigrateInstaller.ps1`”的脚本：

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. 从场景、云和连接选项中进行选择，以部署具有所需配置的设备。 例如，下面所示的选择会设置一个设备，用于发现和评估 Hyper-V 环境中运行的服务器，并通过 Azure 公有云上的默认（公共终结点）连接将其迁移到 Azure Migrate 项目 。

    :::image type="content" source="./media/deploy-appliance-script/script-hyperv-default-inline.png" alt-text="显示如何设置具有所需配置的 Hyper-V 设备的屏幕截图。" lightbox="./media/deploy-appliance-script/script-hyperv-default-expanded.png":::

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

确保设备可以连接到[公有云](migrate-appliance.md#public-cloud-urls)的 Azure URL。

## <a name="next-steps"></a>后续步骤

部署设备后，需要完成首次配置并将其注册到项目中。

- 为 [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) 设置设备。
- 为 [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) 设置设备。
