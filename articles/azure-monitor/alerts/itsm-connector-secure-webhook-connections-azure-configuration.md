---
title: IT 服务管理连接器 - Azure Monitor 中的安全导出 - Azure 配置
description: 本文介绍如何配置 Azure 以将 ITSM 产品/服务与 Azure Monitor 中的安全导出连接，从而集中监视和管理 ITSM 工作项。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037534"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>配置 Azure 以使用安全导出连接 ITSM 工具

本文提供有关如何配置 Azure 以使用“安全导出”的信息。
若要使用“安全导出”，请执行以下步骤：

1. [将应用注册到 Azure AD。](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [定义服务主体。](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [创建安全 Webhook 操作组。](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. 配置合作伙伴环境。
    安全导出支持使用以下 ITSM 工具建立的连接：
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>注册到 Azure Active Directory

按照以下步骤将应用注册到 Azure AD：

1. 按照[将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)中的步骤进行操作。
2. 在 Azure AD 中，选择“公开应用程序”。
3. 针对“应用程序 ID URI”选择“设置”。

   [![应用程序 ID URI 设置选项的屏幕截图。](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. 选择“保存”。

## <a name="define-service-principal"></a>定义服务主体

操作组服务是第一方应用程序，因此它有权从 AAD 应用程序获取身份验证令牌，以便使用 ServiceNow 进行身份验证。
作为一个可选步骤，你可以在创建的应用部件清单中定义应用程序角色，以便进一步以某种方式限制访问，即仅具有特定角色的某些应用程序才能发送消息。 之后必须将此角色分配给操作组服务主体（需要租户管理员特权）。

此步骤可以通过同一 [PowerShell 命令](../alerts/action-groups.md#secure-webhook-powershell-script)完成。

## <a name="create-a-secure-webhook-action-group"></a>创建安全 Webhook 操作组

将应用程序注册到 Azure AD 后，可以使用操作组中的安全 Webhook 操作，基于 Azure 警报在 ITSM 工具中创建工作项。

操作组对 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户结合指标警报、活动日志警报和 Azure Log Analytics 警报使用操作组。
若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../alerts/action-groups.md)。

若要将 Webhook 添加到操作，请按照以下安全 Webhook 说明进行操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。
2. 选择“警报” > “管理操作”。
3. 选择“添加操作组”，并填写字段。
4. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。
5. 选择“安全 Webhook”。
6. 选择以下详细信息：
   1. 选择注册的 Azure Active Directory 实例的对象 ID。
   2. 对于 URI，粘贴从 [ITSM 工具环境](#configure-the-itsm-tool-environment)中复制的 Webhook URL。
   3. 将“启用常见警报架构”设置为“是”。 

   下图显示了安全 Webhook 操作示例的配置：

   ![显示安全 Webhook 操作的屏幕截图。](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>配置 ITSM 工具环境

配置包括两个步骤：

1. 获取安全导出定义的 URI。
2. 根据 ITSM 工具流进行定义。

## <a name="next-steps"></a>后续步骤

* [ServiceNow 安全导出配置](./itsmc-secure-webhook-connections-servicenow.md)
* [BMC 安全导出配置](./itsmc-secure-webhook-connections-bmc.md)
