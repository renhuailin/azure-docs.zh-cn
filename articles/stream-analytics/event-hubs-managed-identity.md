---
title: 使用托管标识从 Azure 流分析作业访问事件中心
description: 本文介绍如何使用托管标识通过 Azure 事件中心输入和输出对 Azure 流分析作业进行身份验证。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 4b470ff5336807fd92ae14af2548cd3a4305c6ed
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113485898"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job"></a>使用托管标识从 Azure 流分析作业访问事件中心

Azure 流分析支持针对 Azure 事件中心的输入和输出进行托管标识身份验证。 托管标识消除了基于用户的身份验证方法的限制，例如，由于每隔 90 天进行一次密码更改（否则用户令牌会过期），需要重新进行身份验证。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。  

托管标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序。 托管应用程序用于向目标资源（包括位于防火墙或虚拟网络 (VNet) 后面的事件中心）进行身份验证。 若要详细了解如何绕过防火墙，请参阅[允许通过专用终结点访问 Azure 事件中心命名空间](../event-hubs/private-link-service.md#trusted-microsoft-services)。

本文介绍如何通过 Azure 门户为流分析作业的事件中心输入或输出启用托管标识。在启用托管标识之前，必须先有一个流分析作业和事件中心资源。

## <a name="create-a-managedidentity"></a>创建托管标识  

首先，创建 Azure 流分析作业的托管标识。  

1. 在 Azure 门户中，打开 Azure 流分析作业。  

1. 从左侧导航菜单中，选择“配置”下的“托管标识” ****   ** 。 然后，选中“使用系统分配的托管标识”旁的框，然后选择“保存” ****   **** 。

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="系统分配托管标识":::  

1. 在 Azure Active Directory 中为流分析作业的标识创建服务主体。 新创建的标识的生命周期由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。  

   保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示：  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="主体 ID":::

   服务主体与流分析作业同名。 例如，如果作业的名称是 `MyASAJob`，则服务主体的名称也是 `MyASAJob`。  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>向流分析作业授予访问事件中心的权限

为了使流分析作业能够使用托管标识访问事件中心，你创建的服务主体必须具有访问事件中心的特殊权限。

1. 在事件中心内转到“访问控制(IAM)”。

1. 选择“+添加”和“添加角色分配” 。

1. 在“添加角色分配”页上，输入以下选项：

   |参数|值|
   |---------|-----|
   |角色|Azure 事件中心数据所有者|
   |将访问权限分配到|用户、组或服务主体|
   |Select|输入流分析作业的名称|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="添加角色分配":::

1. 选择“保存”，并等待大约一分钟，以便传播所做的更改。

还可以在事件中心命名空间级别授予此角色，这样就会将权限自然地传播到在该级别下创建的所有事件中心。 也就是说，某个命名空间下的所有事件中心都可以用作流分析作业中的托管标识身份验证资源。

> [!NOTE]
> 由于全局复制或缓存延迟，在撤销或授予权限时可能会有延迟。 更改应在 8 分钟内反映出来。

## <a name="create-anevent-hub-input-or-output"></a>创建事件中心输入或输出  

现在已经配置了托管标识，可以将事件中心资源作为输入或输出添加到流分析作业了。  

### <a name="add-the-event-hub-as-an-input"></a>添加事件中心作为输入 

1. 转到流分析作业，然后导航到“作业拓扑”下的“输入”页 。

1. 选择“添加流输入”>“事件中心”。 在输入属性窗口中，搜索并选择事件中心，然后从“身份验证模式”下拉菜单中选择“托管标识”。

1. 填写其余属性并选择“保存”。

### <a name="add-the-event-hub-as-an-output"></a>添加事件中心作为输出

1. 转到流分析作业，然后导航到“作业拓扑”下的“输出”页 。

1. 选择“添加”>“事件中心”。 在输出属性窗口中，搜索并选择事件中心，然后从“身份验证模式”下拉菜单中选择“托管标识”。

1. 填写其余属性并选择“保存”。

## <a name="next-steps"></a>后续步骤

* [来自 Azure 流分析的事件中心输出](event-hubs-output.md)
* [从事件中心对数据进行流式传输](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
