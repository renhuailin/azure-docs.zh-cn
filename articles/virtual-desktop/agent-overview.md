---
title: Windows 虚拟桌面代理入门
description: Windows 虚拟桌面代理和更新过程的概述。
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 6450b44e5c7281f0a24fd5000c9feec6e61fa29c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540517"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Windows 虚拟桌面代理入门

在 Windows 虚拟桌面服务框架中，有三个主要组件：远程桌面客户端、服务和虚拟机。 这些虚拟机在安装了 Windows 虚拟桌面代理和代理引导程序的客户订阅中。 该代理充当服务和虚拟机之间的中间 communicator，实现连接性。 因此，如果在安装、更新或配置代理时遇到任何问题，则虚拟机无法连接到该服务。 代理引导程序是加载代理的可执行文件。 

本文简要概述了代理安装和更新过程。

>[!NOTE]
>此文档不适用于 FSLogix 代理或远程桌面客户端代理。


## <a name="initial-installation-process"></a>初始安装过程

Windows 虚拟桌面代理最初以两种方式之一安装。 如果在 Azure 门户和 Azure Marketplace 中 (Vm) 设置虚拟机，则会自动安装代理和代理自动引导程序。 如果使用 PowerShell 预配 Vm，则在 [使用 powershell 创建 Windows 虚拟机主机池](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)时，必须手动下载代理和代理引导程序 .msi 文件。 安装代理时，也会同时安装 Windows 虚拟桌面并行堆栈和 Geneva 监视代理。 用户需要使用并行堆栈组件来安全地建立反向服务器到客户端连接。 Geneva Monitoring agent 监视代理的运行状况。 这三个组件都是端到端用户连接正常运行所必需的。

>[!IMPORTANT]
>若要成功安装 Windows 虚拟桌面代理、并排堆栈和 Geneva 监视代理，必须取消阻止 [所需 URL 列表](safe-url-list.md#virtual-machines)中列出的所有 url。 使用 Windows 虚拟桌面服务需要取消阻止这些 Url。

## <a name="agent-update-process"></a>代理更新过程

每当更新可用时，Windows 虚拟桌面服务就会自动更新代理。 代理更新可以包括新功能或修复以前的问题。 安装 Windows 虚拟桌面代理的初始版本后，代理会定期查询 Windows 虚拟桌面服务，以确定是否有更新版本的代理及其组件可用。 如果有新版本，代理自动引导程序会自动下载最新版本的代理、并排堆栈和 Geneva 监视代理。

>[!NOTE]
>- 当 Geneva 监视代理更新到最新版本时，将查找并禁用旧 GenevaTask 任务，然后再为新的监视代理创建新的任务。 如果监视代理的最新版本存在需要还原到早期版本来修复的问题，则不会删除较早版本的监视代理。 如果最新版本有问题，则会重新启用旧监视代理以继续提供监视数据。 早于在更新之前安装的监视器的所有版本都将从 VM 中删除。
>- VM 一次保留了三个版本的并行堆栈。 如果更新出现问题，则这允许进行快速恢复。 只要堆栈更新，堆栈的最早版本就会从 VM 中删除。

此更新安装通常会在新的 VM 上持续2-3 分钟，并不会导致 VM 断开连接或关闭。 此更新过程适用于 Windows 虚拟桌面 (经典) 和 Azure 资源管理器最新版本的 Windows 虚拟桌面。

## <a name="next-steps"></a>后续步骤

现在，你可以更好地了解 Windows 虚拟桌面代理，下面提供了一些可以帮助你：

- 查看 [Windows 虚拟桌面代理更新](whats-new.md) 部分，查看每个月的新代理更新的相关信息。
- 如果遇到与代理或连接相关的问题，请查看 [Windows 虚拟桌面代理问题疑难解答指南](troubleshoot-agent.md)。