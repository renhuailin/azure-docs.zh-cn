---
title: IT 服务管理连接器-安全导出 Azure Monitor-Azure 配置
description: 本文介绍如何配置 Azure 以便使用 Azure Monitor 中的安全导出来连接 ITSM 产品/服务，以集中监视和管理 ITSM 工作项。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037534"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>使用安全导出配置 Azure 以连接 ITSM 工具

本文介绍如何配置 Azure 以便使用 "安全导出"。
若要使用 "安全导出"，请执行以下步骤：

1. [将你的应用程序注册到 Azure AD。](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [定义服务主体。](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [创建安全 Webhook 操作组。](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. 配置合作伙伴环境。
    安全导出支持与以下 ITSM 工具的连接：
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>注册到 Azure Active Directory

请按照以下步骤将应用程序注册到 Azure AD：

1. 按照[将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)中的步骤进行操作。
2. 在 Azure AD 中，选择 " **公开应用程序**"。
3. 选择 "为 **应用程序 ID URI****设置**"。

   [![用于设置应用程序 I D 的 U R I 的选项的屏幕截图。](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. 选择“保存”。

## <a name="define-service-principal"></a>定义服务主体

操作组服务是第一方应用程序，因此它有权从 AAD 应用程序获取身份验证令牌，以便立即使用服务进行身份验证。
作为一个可选步骤，你可以在创建的应用的清单中定义应用程序角色，这样就可以进一步限制，只需具有特定角色的特定应用程序就可以发送消息。 必须将此角色分配给操作组服务主体 (需要) 租户管理员权限。

可以通过相同的 [PowerShell 命令](../alerts/action-groups.md#secure-webhook-powershell-script)来执行此步骤。

## <a name="create-a-secure-webhook-action-group"></a>创建安全 Webhook 操作组

在将应用程序注册到 Azure AD 后，可以使用操作组中的安全 Webhook 操作，基于 Azure 警报在 ITSM 工具中创建工作项。

操作组为 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中将操作组与指标警报、活动日志警报和 Azure Log Analytics 警报一起使用。
若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../alerts/action-groups.md)。

若要将 webhook 添加到操作，请按照以下安全 Webhook 说明操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。
2. 选择 "**警报**" "  >  **管理操作**"。
3. 选择“添加操作组”，并填写字段。
4. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。
5. 选择 " **安全 Webhook**"。
6. 选择以下详细信息：
   1. 选择您注册的 Azure Active Directory 实例的对象 ID。
   2. 在 "URI" 中，粘贴从 [ITSM 工具环境](#configure-the-itsm-tool-environment)复制的 webhook URL。
   3. 将 **"启用公用警报架构** " 设置为 **"是"**。 

   下图显示了示例安全 Webhook 操作的配置：

   ![显示安全 Webhook 操作的屏幕截图。](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>配置 ITSM 工具环境

此配置包括两个步骤：

1. 获取安全导出定义的 URI。
2. 根据 ITSM 工具的流定义。

## <a name="next-steps"></a>后续步骤

* [ServiceNow 安全导出配置](./itsmc-secure-webhook-connections-servicenow.md)
* [BMC 安全导出配置](./itsmc-secure-webhook-connections-bmc.md)
