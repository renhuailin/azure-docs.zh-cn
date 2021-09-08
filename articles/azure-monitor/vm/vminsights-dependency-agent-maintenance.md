---
title: 如何升级 VM 见解依赖项代理
description: 本文介绍如何使用命令行、安装向导和其他方法升级 VM 见解依赖项代理。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 282df705e8a98a7c236cfff549447cdd36017df8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431433"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>如何升级 VM 见解依赖项代理

对 VM 见解依赖项代理进行初始部署之后，会有发布的更新，其中包括 Bug 修复或者新特性或新功能的支持。  本文帮助你了解可用的方法以及如何通过手动或自动化方式进行升级。

## <a name="upgrade-options"></a>升级选项 

适用于 Windows 和 Linux 的依赖项代理可以手动或自动升级到最新版本，具体取决于部署方案以及计算机运行时所在的环境。 可以使用以下方法来升级代理。

|环境 |安装方法 |升级方法 |
|------------|--------------------|---------------|
|Azure VM | 适用于 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 和 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 的依赖项代理 VM 扩展 | 默认情况下代理会自动升级，除非你通过将 *autoUpgradeMinorVersion* 属性设置为 **false** 来将 Azure 资源管理器模板配置为选择退出。 禁用自动升级的次要版本升级和主版本升级遵循相同的方法 - 卸载并重新安装扩展。 |
| 自定义 Azure VM 映像 | 手动安装适用于 Windows/Linux 的依赖项代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。|
| 非 Azure VM | 手动安装适用于 Windows/Linux 的依赖项代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。 |

## <a name="upgrade-windows-agent"></a>升级 Windows 代理 

若要将 Windows VM 上的代理更新为未使用依赖项代理 VM 扩展进行安装的最新版本，你可以通过命令提示符、脚本或其他自动化解决方案来运行，也可以使用 InstallDependencyAgent-Windows.exe 安装向导。  

可从[此处](https://aka.ms/dependencyagentwindows)下载最新版本的 Windows 代理。

### <a name="using-the-setup-wizard"></a>使用安装向导

1. 使用具有管理权限的帐户登录到计算机。

2. 执行 InstallDependencyAgent-Windows.exe 以启动安装向导。
   
3. 按照 Dependency Agent 安装向导的要求卸载以前版本的依赖项代理，然后安装最新版本。


### <a name="from-the-command-line"></a>从命令行中

1. 使用具有管理权限的帐户登录到计算机。

2. 运行以下命令。

    ```cmd
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual` 参数可防止此升级在某些进程使用以前版本的文件并对其进行锁定的情况下自动重启计算机。 

3. 若要确认升级是否成功，请查看 `install.log`，了解详细的安装信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升级 Linux 代理 

从 Linux 上的旧版依赖项代理进行升级是受支持的操作，进行升级时使用与新安装相同的命令。

可从[此处](https://aka.ms/dependencyagentlinux)下载最新版本的 Linux 代理。

1. 使用具有管理权限的帐户登录到计算机。

2. 以 root 身份运行以下命令。

    ```bash
    InstallDependencyAgent-Linux64.bin -s
    ```

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。 

## <a name="next-steps"></a>后续步骤

如果要停止监视 VM 一段时间，或者要完全删除 VM 见解，请参阅[在 VM 见解中禁用对 VM 的监视](../vm/vminsights-optout.md)。
