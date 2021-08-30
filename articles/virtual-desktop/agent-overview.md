---
title: Azure 虚拟桌面代理入门
description: Azure 虚拟桌面代理和更新过程的概述。
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 61716993bc4c9f3da4ad606789f050a280a94817
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754637"
---
# <a name="get-started-with-the-azure-virtual-desktop-agent"></a>Azure 虚拟桌面代理入门

Azure 虚拟桌面服务框架包含三个主要组件：远程桌面客户端、服务和虚拟机。 这些虚拟机位于安装了 Azure 虚拟桌面代理和代理引导加载程序的客户订阅中。 代理充当服务和虚拟机之间的中介通信器，实现二者间的连接。 因此如果在代理安装、更新或配置时遇到问题，虚拟机将无法连接到服务。 代理引导加载程序是加载代理的可执行文件。 

本文将简要概述代理安装和更新过程。

>[!NOTE]
>本文档不适用于 FSLogix 代理或远程桌面客户端代理。


## <a name="initial-installation-process"></a>初始安装过程

Azure 虚拟桌面代理最初是通过两种方式安装的。 如果在 Azure 门户和 Azure 市场中预配虚拟机 (VM)，将自动安装代理和代理引导加载程序。 如果使用 PowerShell 预配 VM，需要在[使用 PowerShell 创建 Azure 虚拟桌面主机池](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)时手动下载代理和代理引导加载程序 .msi 文件。 安装代理后，该代理会安装 Azure 虚拟桌面并行堆栈和 Geneva 监视代理。 用户需要并行堆栈组件才能安全建立反向服务器与客户端的连接。 Geneva 监视代理监视代理的运行状况。 这三个组件都是让端到端用户连接正常运行的必要组件。

>[!IMPORTANT]
>若要成功安装 Azure 虚拟桌面代理、并排堆栈和 Geneva 监视代理，必须取消阻止[必需 URL 列表](safe-url-list.md#virtual-machines)中列出的所有 URL。 使用 Azure 虚拟桌面服务需要取消阻止这些 URL。

## <a name="agent-update-process"></a>代理更新过程

每当有可用更新时，Azure 虚拟桌面服务就会更新代理。 代理更新可包含针对以前问题的新功能或修补程序。 始终必须安装最新稳定版本的代理，使 VM 的连接或安全性不会受到影响。 安装 Azure 虚拟桌面代理的初始版本后，代理会定期查询 Azure 虚拟桌面服务，以确定是否有可用的更新版本的代理、堆栈或监视组件。 如果部署了更新版本的任何组件，外部测试系统会自动安装更新的组件。

新版代理将以五天为周期定期部署到所有 Azure 订阅。 这些更新周期称为“发布外部测试版”。 单个中介区域中的所有 VM 需要花费 24 小时来接收外部测试版中的代理更新。 因此，发布外部测试版时，你可能会看到主机池中的 VM 在不同时间接收代理更新。 此外，如果 VM 位于不同的区域，它们可能会在五天周期内的不同日期更新。 在部署周期结束时，外部测试版将会更新所有订阅中的所有 VM 代理。 Azure 虚拟桌面外部测试系统通过确保代理更新的稳定性和质量来增强服务可靠性。


应该注意的其他重要事项：

- 代理更新未连接到 Azure 虚拟桌面基础结构内部版本更新。 更新 Azure 虚拟桌面基础结构并不意味着代理会随它一起更新。
- 由于主机池中的 VM 可能会在不同的时间接收代理更新，因此你需要能够辨别外部测试问题与代理更新失败的区别。 如果通过“事件查看器” > “Windows 日志” > “应用程序”转到 VM 的事件日志后看到标记为“ID 3277”的事件，则表示代理更新未正常运行  。 如果未看到该事件，则表示 VM 位于不同的外部测试版中，因此将在以后更新。
- 当 Geneva 监视代理更新到最新版本时，在为新监视代理创建新 GenevaTask 任务之前，系统会找到并禁用旧的任务。 系统不会删除早期版本的监视代理，以便监视代理的最新版本出现问题时可还原到早期版本进行修复。 如果最新版本出现问题，系统将重新启用旧的监视代理来继续传送监视数据。 将从 VM 中删除更新之前安装的最新版本之前的所有监视器版本。
- VM 一次保留三个版本的并行堆栈。 这样，如果更新出现问题，就可以快速恢复。 每当堆栈更新时，都会从 VM 中删除堆栈的最早版本。

代理更新通常在新 VM 上持续 2-3 分钟，应不会导致 VM 断开连接或关闭。 此更新过程适用于 Azure 虚拟桌面（经典）和带有 Azure 资源管理器的最新版 Azure 虚拟桌面。

## <a name="next-steps"></a>后续步骤

现在你已对 Azure 虚拟桌面代理有了更好的了解，接下来请参阅以下有用资源：

- 如果遇到代理或连接相关的问题，请查看 [Azure 虚拟桌面代理问题排查指南](troubleshoot-agent.md)。
