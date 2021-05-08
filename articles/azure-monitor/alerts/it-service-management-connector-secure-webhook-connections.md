---
title: IT 服务管理连接器 - Azure Monitor 中的安全导出
description: 本文介绍如何将 ITSM 产品/服务与 Azure Monitor 中的安全导出连接，以集中监视和管理 ITSM 工作项。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 330b97e66ba1a9b95f2ef7a34a7ea046414b8bd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037505"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>使用安全导出将 Azure 连接到 ITSM 工具

本文介绍如何使用安全导出配置 IT 服务管理 (ITSM) 产品或服务之间的连接。

安全导出是 [IT 服务管理连接器 (ITSMC)](./itsmc-overview.md) 的更新版本。 这两个版本都允许你在 Azure Monitor 发送警报时在 ITSM 工具中创建工作项。 此功能包括指标、日志和活动日志警报。

ITSMC 使用用户名和密码凭据。 安全导出提供更强的身份验证，因为它使用 Azure Active Directory (Azure AD)。 Azure AD 是 Microsoft 基于云的标识和访问管理服务。 它帮助用户登录和访问内部或外部资源。 将 Azure AD 与 ITSM 配合使用有助于识别发送到外部系统的 Azure 警报（通过 Azure AD 应用程序 ID）。

> [!NOTE]
> 通过使用安全导出将 Azure 连接到 ITSM 工具的功能尚处于预览阶段。

## <a name="secure-export-architecture"></a>安全导出体系结构

安全导出体系结构引入了以下新功能:

* **新操作组**：警报通过安全 Webhook 操作组发送到 ITSM 工具，而不是通过 ITSMC 使用的 ITSM 操作组。
* **Azure AD 身份验证**：通过 Azure AD 而不是用户名/密码凭据进行身份验证。

## <a name="secure-export-data-flow"></a>安全导出数据流

安全导出数据流的步骤如下：

1. Azure Monitor 发送已配置为使用安全导出的警报。
2. 警报有效负载由安全 Webhook 操作发送到 ITSM 工具。
3. ITSM 应用程序使用 Azure AD 检查警报是否有权进入 ITSM 工具。
4. 如果警报已获得授权，则应用程序：
   
   1. 在 ITSM 工具中创建工作项（例如事件）。
   2. 将配置项 (CI) 的 ID 绑定到客户管理数据库 (CMDB)。

![关系图显示了 ITSM 工具如何与 Azure AD、Azure 警报和操作组通信。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>安全导出的优点

集成的主要优点是：

* **更佳的身份验证**：Azure AD 提供了更安全的身份验证，而不会发生 ITSMC 中经常出现的超时。
* **ITSM 工具中解决的警报**：指标警报实现“已触发”和“已解决”状态。 当条件满足时，警报状态为“已触发”。 当条件不再满足时，警报状态为“已解决”。 在 ITSMC 中，无法自动解决警报。 通过安全导出，已解决状态流向 ITSM 工具，因此会自动进行更新。
* **[常见警报架构](./alerts-common-schema.md)** ：在 ITSMC 中，警报有效负载的架构因警报类型而有所差异。 在安全导出中，提供适用于所有警报类型的常见架构。 此常见架构包含适用于所有警报类型的 CI。 所有警报类型都能够将其 CI 与 CMDB 绑定。

## <a name="next-steps"></a>后续步骤

* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-overview.md)