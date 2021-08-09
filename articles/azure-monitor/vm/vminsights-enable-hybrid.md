---
title: 为混合环境启用 Azure Monitor
description: 本文介绍了如何为包含一个或多个虚拟机的混合云环境启用 VM insights。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: b6a93ec97a5abaa55bb8aa6a9db963d98bc3b615
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112032098"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>为混合虚拟机启用 VM 见解
本文介绍了如何为 Azure 外部的虚拟机（包括本地和其他云环境）启用 VM insights。

> [!IMPORTANT]
> 启用混合 VM 时，建议首先启用 [Azure Arc for servers](../../azure-arc/servers/overview.md)，以便可以使用类似于 Azure VM 的进程为 VM insights 启用 VM。 本文介绍了如果选择不使用 Azure Arc 时，应如何载入混合 VM。

## <a name="prerequisites"></a>先决条件

- [创建和配置 Log Analytics 工作区](./vminsights-configure-workspace.md)。
- 请参阅[支持的操作系统](./vminsights-enable-overview.md#supported-operating-systems)，以确保正在启用的虚拟机或虚拟机规模集的操作系统是受支持的。 


## <a name="overview"></a>概述
Azure 之外的虚拟机必须使用与 Azure VM 相同的 Log Analytics 代理和 Dependency Agent。 尽管无法使用 VM 扩展来安装代理，但必须手动将它们安装在来宾操作系统中，或通过其他方法安装它们。 

有关部署 Log Analytics 代理的详细信息，请参阅[将 Windows 计算机连接到 Azure Monitor](../agents/agent-windows.md) 或[将 Linux 计算机连接到 Azure Monitor](../agents/agent-linux.md)。 本文提供了有关 Dependency Agent 的详细信息。 

## <a name="firewall-requirements"></a>防火墙要求
有关 Log Analytics 代理的防火墙要求，请参阅 [Log Analytics 代理概述](../agents/log-analytics-agent.md#network-requirements)。 VM insights Map Dependency Agent 本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理传输到 Azure Monitor 服务 - 要么采用直接传输的方式，要么通过 [Operations Management Suite 网关](../../azure-monitor/agents/gateway.md)进行传输（如果 IT 安全策略不允许网络中的计算机连接到 Internet）。


## <a name="dependency-agent"></a>依赖关系代理

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](./service-map.md)。  

可从以下位置下载依赖项代理：

| 文件 | (OS) | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.9.15340 | 3F34A36CF569724A5C83B1C8DFEC54263B7ABCFEAC9EB9BB40AF822A31265AF7  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.9.15340 | 0B0566A11A9B218FA6E44B057E7BA93986B8D6539B928C6D36D97D13A2F8B8A6 |


## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理

可通过运行 `InstallDependencyAgent-Windows.exe` 在 Windows 计算机上手动安装 Dependency Agent。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个安装向导，以交互方式指导用户安装代理。 需要在来宾操作系统上具有“管理员”权限才能安装或卸载代理。

下表突出显示了通过命令行安装代理时支持的参数。

| 参数 | 说明 |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要使用 `/?` 参数运行安装程序，请输入 **InstallDependencyAgent-Windows.exe /?**。

默认情况下，Windows Dependency Agent 的文件在 *C:\Program Files\Microsoft Dependency Agent* 中安装。 如果在安装完成后依赖项代理无法启动，请查看日志以获取详细的错误信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

### <a name="powershell-script"></a>PowerShell 脚本
使用下面的示例 PowerShell 脚本来下载并安装代理：

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent

通过 *InstallDependencyAgent-Linux64.bin*（具有自解压二进制文件的 Shell 脚本）在 Linux 服务器上安装 Dependency Agent。 可使用 `sh` 来运行该文件或将执行权限添加到文件本身。

>[!NOTE]
> 需要根目录访问才能安装或配置代理。
>

| 参数 | 描述 |
|:--|:--|
| -help | 获取命令行选项列表。 |
| -S | 执行无提示安装，无用户提示。 |
| --check | 检查权限和操作系统，但不安装代理。 |

例如，若要使用 `-help` 参数运行安装程序，请输入 **InstallDependencyAgent-Linux64.bin -help**。 通过运行命令 `sh InstallDependencyAgent-Linux64.bin` 来以 root 用户身份安装 Linux 依赖项代理。

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的文件放置在以下目录中：

| 文件 | 位置 |
|:--|:--|
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Shell 脚本 
使用下面的示例 shell 脚本下载并安装代理：

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

若通过期望状态配置 (DSC) 部署 Dependency Agent，可通过如下示例代码使用 xPSDesiredStateConfiguration 模块：

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>疑难解答

### <a name="vm-doesnt-appear-on-the-map"></a>VM 未出现在映射上

如果 Dependency Agent 安装成功，但在映射上没有看到你的计算机，请按照以下步骤诊断问题。

1. Dependency Agent 是否已安装成功？ 可通过检查是否已安装并运行服务来验证这一点。

    **Windows**：查找名为“Microsoft Dependency Agent”的服务。

    **Linux**：查找正在运行的进程“microsoft-dependency-agent”

2. 是否处于 [Log Analytics 的免费定价层](../insights/solutions.md)？ 免费计划最多允许五台仅有的计算机。 任何后续的计算机都不会出现在映射上，即使之前的五台计算机不再发送数据，也是如此。

3. 计算机是否正在向 Azure Monitor 日志发送日志和性能数据？ 对计算机执行以下查询：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    它是否返回了一个或多个结果？ 是否为最新数据？ 如果是，则表示 Log Analytics 代理正常运行并正在与服务通信。 如果不是，请在服务器上检查代理：[用于 Windows 的 Log Analytics 代理故障排除](../agents/agent-windows-troubleshoot.md)或[用于 Linux 的 Log Analytics 代理故障排除](../agents/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>计算机出现在映射上，但没有进程

如果在映射上看到了服务器，但它没有任何进程或连接数据，则表明已安装并运行 Dependency Agent，但未加载内核驱动程序。

请检查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file（针对 Windows）或 /var/opt/microsoft/dependency-agent/log/service.log file（针对 Linux）。 文件的最后几行应指出为何未加载内核。 例如，如果更新内核，则内核在 Linux 上可能不受支持。


## <a name="next-steps"></a>后续步骤

现在已为虚拟机启用监视，这些信息可供 VM 见解用于分析目的。

- 要查看已发现的应用程序依赖项，请参阅[查看 VM 见解地图](vminsights-maps.md)。

- 若要通过 VM 的性能了解瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
