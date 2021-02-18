---
title: IT 服务管理连接器-安全导出 Azure Monitor
description: 本文介绍如何使用 Azure Monitor 中的安全导出来连接 ITSM 产品/服务，以集中监视和管理 ITSM 工作项。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 6b273b864c8329547ef91eb7063a064e0f5cb77d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608884"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>使用安全导出将 Azure 连接到 ITSM 工具

本文说明如何使用安全导出配置 IT Service Management (ITSM) 产品或服务之间的连接。

安全导出是 [IT 服务管理连接器 (ITSMC) ](../platform/itsmc-overview.md)的更新版本。 这两个版本都允许在 Azure Monitor 发送警报时在 ITSM 工具中创建工作项。 此功能包括指标、日志和活动日志警报。

ITSMC 使用用户名和密码凭据。 安全导出具有更强的身份验证，因为它使用 Azure Active Directory (Azure AD) 。 Azure AD 是 Microsoft 基于云的标识和访问管理服务。 它可帮助用户登录和访问内部或外部资源。 将 Azure AD 与 ITSM 结合使用有助于通过发送到外部系统的 Azure AD 应用程序 ID) 来识别 Azure 警报 (。

> [!NOTE]
> 使用安全导出功能将 Azure 连接到 ITSM 工具的功能处于预览阶段。

## <a name="secure-export-architecture"></a>安全导出体系结构

安全导出体系结构引入了以下新功能：

* **新操作组**：通过安全 Webhook 操作组（而不是 ITSMC 使用的 ITSM 操作组）将警报发送到 ITSM 工具。
* **Azure AD 身份验证**：通过 Azure AD 而不是用户名/密码凭据进行身份验证。

## <a name="secure-export-data-flow"></a>安全导出数据流

安全导出数据流的步骤如下：

1. Azure Monitor 将发送配置为使用安全导出的警报。
2. 警报负载由安全 Webhook 操作发送到 ITSM 工具。
3. 如果警报有权进入 ITSM 工具，ITSM 应用程序会检查 Azure AD。
4. 如果警报已获得授权，则应用程序：
   
   1. 创建工作项 (例如，ITSM 工具中) 事件。
   2.  (CMDB) 将配置项目的 ID （ (CI) 绑定到客户管理数据库）。

![显示 ITSM 工具如何与 Azure A D、Azure 警报和操作组通信的示意图。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>安全导出的优点

集成的主要优点是：

* **更好的身份验证**： Azure AD 提供更安全的身份验证，而不会发生 ITSMC 中经常发生的超时。
* **ITSM 工具中解决的警报**：指标警报实现 "已触发" 和 "已解决" 状态。 满足条件时，警报状态为 "已激发"。 当不再满足条件时，警报状态为 "已解决"。 在 ITSMC 中，无法自动解决警报。 通过安全导出，已解决状态流向 ITSM 工具，因此会自动更新。
* **[常见警报架构](./alerts-common-schema.md)**：在 ITSMC 中，警报负载的架构根据警报类型的不同而异。 在安全导出中，所有警报类型都有一个通用的架构。 此常见架构包含适用于所有警报类型的 CI。 所有警报类型都可以与 CMDB 绑定其 CI。

## <a name="next-steps"></a>后续步骤

* [根据 Azure 警报日志创建 ITSM 工作项](../platform/itsmc-overview.md)
