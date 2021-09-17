---
title: 在 Azure Windows VM 中运行脚本
description: 本主题介绍如何在 Windows 虚拟机中运行脚本
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
ms.openlocfilehash: 7c45718726e9037021edc53346d20f26273a92b3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687486"
---
# <a name="run-scripts-in-your-windows-vm"></a>在 Windows VM 中运行脚本

**适用于：** VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

可能需要在 VM 中运行命令来自动完成任务或排查问题。 下文概述了可以用来在 VM 中运行脚本和命令的功能。

## <a name="custom-script-extension"></a>自定义脚本扩展

[自定义脚本扩展](../extensions/custom-script-windows.md)主要用于部署后配置和软件安装。

* 在 Azure 虚拟机中下载并运行脚本。
* 可以使用 Azure 资源管理器模板、Azure CLI、REST API、PowerShell 或 Azure 门户来运行。
* 脚本文件可以从 Azure 存储或 GitHub 下载，也可以从电脑获取（通过 Azure 门户运行时）。
* 在 Windows 计算机中运行 PowerShell 脚本，在 Linux 计算机中运行 Bash 脚本。
* 适用于部署后配置、软件安装以及其他配置或管理任务。

## <a name="run-command"></a>运行命令

[运行命令](run-command.md)功能支持使用脚本进行虚拟机、应用程序管理和故障排除，并且即使在计算机不可访问时也可用，例如，如果来宾防火墙没有打开 RDP 或 SSH 端口。

* 在 Azure 虚拟机中运行脚本。
* 可以使用 [Azure 门户](run-command.md)、[REST API](/rest/api/compute/virtual-machines-run-commands/run-command)、[Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke) 或 [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) 运行
* 在 Azure 门户中快速运行脚本并查看输出，并根据需要重复此过程。
* 可以直接键入脚本，也可以运行其中一个内置脚本。
* 在 Windows 计算机中运行 PowerShell 脚本，在 Linux 计算机中运行 Bash 脚本。
* 适用于虚拟机和应用程序管理以及在无法访问的虚拟机中运行脚本。

## <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色

[混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)使用存储在自动化帐户中的用户自定义脚本提供常规计算机、应用程序和环境管理。

* 在 Azure 和非 Azure 计算机中运行脚本。
* 可以使用 Azure 门户、Azure CLI、REST API、PowerShell、Webhook 运行。
* 在自动化帐户中存储和管理的脚本。
* 运行 PowerShell、PowerShell 工作流、Python 或图形 runbook
* 脚本运行时间没有时间限制。
* 可以同时运行多个脚本。
* 返回并存储完整的脚本输出。
* 作业历史记录在 90 天内可用。
* 脚本可以作为本地系统运行，也可以使用用户提供的凭据运行。
* 需要[手动安装](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>串行控制台

[串行控制台](/troubleshoot/azure/virtual-machines/serial-console-windows)提供对 VM 的直接访问，类似于将键盘连接到 VM。

* 在 Azure 虚拟机中运行命令。
* 可以在 Azure 门户中使用基于文本的控制台运行到计算机。
* 使用本地用户帐户登录到计算机。
* 无论计算机的网络或操作系统状态如何，当需要访问虚拟机时都很有用。

## <a name="next-steps"></a>后续步骤

详细了解可以用来在 VM 中运行脚本和命令的不同功能。

* [自定义脚本扩展](../extensions/custom-script-windows.md)
* [运行命令](run-command.md)
* [混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)
* [串行控制台](/troubleshoot/azure/virtual-machines/serial-console-windows)
