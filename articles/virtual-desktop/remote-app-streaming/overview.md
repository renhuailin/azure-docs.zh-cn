---
title: 什么是 Azure 虚拟桌面远程应用流式处理？ - Azure
description: Azure 虚拟桌面远程应用流式处理的概述。
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 88bc4c94612a69be8a9d7aad7bbad983b687235a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798721"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>什么是 Azure 虚拟桌面远程应用流式处理？

Azure 虚拟桌面是一种桌面和应用虚拟化服务，它在云中运行，并让你能随时随地访问远程桌面。 但是，你是否知道还可以将 Azure 虚拟桌面用作平台即服务 (PaaS) 来向客户提供组织的应用作为软件即服务 (SaaS)？ 通过 Azure 虚拟桌面远程应用流式处理，现在可以使用 Azure 虚拟桌面通过虚拟机的安全网络将应用交付给客户。

如果你不熟悉 Azure 虚拟桌面（或通常不熟悉应用虚拟化），我们在此处收集了一些资源，可以帮助你启动和运行部署。

## <a name="requirements"></a>要求

在开始使用之前，建议先查看 [Azure 虚拟桌面概述](../overview.md)，获取运行 Azure 虚拟桌面的系统要求的更详细列表。 在浏览时，如果想要了解更多服务专注于 IT 的内容，可以浏览 Azure 虚拟桌面文档的其他部分，因为大部分文章也适用于 Azure 虚拟桌面的远程应用流式处理。 了解基础后，可以更高效地使用远程应用流式处理文档。

若要为对组织外部客户可用的自定义应用设置 Azure 虚拟桌面部署，需要以下各项：

- 你的自定义应用。 请参阅[如何使用 Azure 虚拟桌面托管自定义应用](custom-apps.md)，了解 Azure 虚拟桌面支持的应用类型以及如何向客户提供它们。

- 你的域加入凭据。 如果你没有与 Azure 虚拟桌面兼容的标识管理系统，则需要为主机池设置标识管理。

- Azure 订阅。 如果还没有订阅，请务必[创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="get-started"></a>开始使用

准备就绪后，让我们看看如何设置 Azure 虚拟桌面部署。

可以按照以下教程手动设置部署：

1. [使用 Azure 门户创建主机池](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [管理应用组](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [创建主机池以验证服务更新](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [设置服务警报](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>自定义和管理 Azure 虚拟桌面

设置 Azure 虚拟桌面后，可以使用许多选项自定义部署以满足组织或客户的需求。 以下文章有助于入门：

- [如何使用 Azure 虚拟桌面托管应用](custom-apps.md)
- [按用户访问定价注册订阅](per-user-access-pricing.md)
- [如何使用 Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [将 Windows 10 虚拟机与 Intune 配合使用](/mem/intune/fundamentals/windows-10-virtual-machines)
- [如何使用 MSIX 应用附加部署应用](msix-app-attach.md)
- [使用 Azure Monitor for Azure Virtual Desktop 监视部署](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [设置业务连续性和灾难恢复计划](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [使用 Azure 自动化来改变会话主机规模](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [设置通用打印](/universal-print/fundamentals/universal-print-getting-started)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>了解 Azure 虚拟桌面部署

阅读以下文章，了解创建和管理 Azure 虚拟桌面部署的基本概念：

- [了解授权和每用户访问定价](licensing.md)
- [跨组织应用的安全指南](security.md)
- [Azure 虚拟桌面安全最佳做法](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Monitor for Windows Virtual Desktop 术语表](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [适用于企业的 Azure 虚拟桌面](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [预计总部署成本](total-costs.md)
- [体系结构建议](architecture-recs.md)

## <a name="next-steps"></a>后续步骤

如果已准备好开始手动设置部署，请转至以下教程。

> [!div class="nextstepaction"]
> [使用 Azure 门户创建主机池](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
