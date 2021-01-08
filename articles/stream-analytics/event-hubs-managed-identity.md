---
title: '使用托管标识从 Azure 流分析作业 (预览访问事件中心) '
description: 本文介绍如何使用托管标识对 azure 流分析作业进行 azure 事件中心输入和输出的身份验证。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018745"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>使用托管标识从 Azure 流分析作业 (预览访问事件中心) 

Azure 流分析支持 Azure 事件中心输入和输出的托管标识身份验证。 托管标识消除了基于用户的身份验证方法的限制，例如由于密码更改而需要重新进行身份验证，或者每90天发生一次用户令牌过期。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。  

托管标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序。 托管应用程序用于向目标资源进行身份验证，包括位于防火墙或虚拟网络后面 (VNet) 的事件中心。 有关如何绕过防火墙的详细信息，请参阅 [允许通过专用终结点访问 Azure 事件中心命名空间](../event-hubs/private-link-service.md#trusted-microsoft-services)。

本文介绍如何通过 Azure 门户为事件中心输入或流分析作业的输出启用托管标识。启用托管标识之前，必须先拥有流分析作业和事件中心资源。

### <a name="limitation"></a>限制
在预览期间，在使用托管标识身份验证模式时，Azure 门户上的事件中心的采样输入将不起作用。

## <a name="create-a-managedidentity"></a>创建托管标识  

首先，创建 Azure 流分析作业的托管标识。  

1. 在 Azure 门户中，打开 Azure 流分析作业。  

1. 从左侧导航菜单中，选择 ****   " *配置*" 下的 "托管标识"。 然后，选中 " **使用系统分配的托管标识**" 旁边的框，   然后选择 " **保存**"。

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="系统分配的托管标识":::  

1. 在 Azure Active Directory 中创建流分析作业标识的服务主体。 新创建的标识的生命周期由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。  

   保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示：  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="主体 ID":::

   服务主体与流分析作业同名。 例如，如果作业的名称为  `MyASAJob` ，则服务主体的名称也是  `MyASAJob` 。  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>向流分析作业授予访问事件中心的权限

为了使流分析作业能够使用托管标识访问事件中心，你创建的服务主体必须对事件中心具有特殊权限。

1. 在事件中心中转到 **(IAM) 的访问控制** 。

1. 选择 " **+ 添加** 并 **添加角色分配**"。

1. 在 " **添加角色分配** " 页上，输入以下选项：

   |参数|值|
   |---------|-----|
   |角色|Azure 事件中心数据所有者|
   |将访问权限分配到|用户、组或服务主体|
   |选择|输入流分析作业的名称|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="添加角色分配":::

1. 选择 " **保存** " 并等待一分钟，以传播更改。

你还可以在事件中心命名空间级别授予此角色，这会将权限自然传播到在其下创建的所有事件中心。 也就是说，一个命名空间下的所有事件中心都可以用作流分析作业中的托管身份验证资源。

## <a name="create-anevent-hub-input-or-output"></a>创建事件中心输入或输出  

配置托管标识后，可以将事件中心资源作为输入或输出添加到流分析作业。  

### <a name="add-the-event-hub-as-an-input"></a>添加事件中心作为输入 

1. 转到流分析作业，并导航到 "**作业拓扑**" 下的 "**输入**" 页。

1. 选择 " **添加流输入 > 事件中心**"。 在 "输入属性" 窗口中，搜索并选择 "事件中心"，然后从 "*身份验证模式*" 下拉菜单中选择 "**托管标识**"。

1. 填写其余属性，然后选择 " **保存**"。

### <a name="add-the-event-hub-as-an-output"></a>添加事件中心作为输出

1. 转到流分析作业，并导航到 "**作业拓扑**" 下的 "**输出**" 页。

1. 选择 " **添加 > 事件中心**"。 在 "输出属性" 窗口中，搜索并选择事件中心，然后从 "*身份验证模式*" 下拉菜单中选择 "**托管标识**"。

1. 填写其余属性，然后选择 " **保存**"。

## <a name="next-steps"></a>后续步骤

* [来自 Azure 流分析的事件中心输出](event-hubs-output.md)
* [从事件中心对数据进行流式传输](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
