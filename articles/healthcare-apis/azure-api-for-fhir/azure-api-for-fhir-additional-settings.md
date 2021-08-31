---
title: Azure API for FHIR 的其他设置
description: 可为 Azure API for FHIR 设置的其他设置的概述
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778250"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Azure API for FHIR 的其他设置

在本指南中，我们将介绍你可能需要在 Azure API for FHIR 中设置的其他设置。 还提供了一些页面，你可以深入了解更多详细信息。

## <a name="configure-database-settings"></a>配置数据库设置

Azure API for FHIR 使用数据库来存储其数据。 基础数据库的性能取决于在服务预配期间或已预配服务后在数据设置中选择的请求单位 (RU) 数。

必须预配吞吐量才能确保始终为数据库提供足够的系统资源。 应用程序需要多少 RU 取决于你执行的操作。 操作可以是简单的读取和写入，也可以是较复杂的查询。

有关如何更改默认设置的详细信息，请参阅[配置数据库设置](configure-database.md)。

## <a name="access-control"></a>访问控制

Azure API for FHIR 仅允许授权用户访问 FHIR API。 你可以通过两种不同的机制来配置授权用户。 配置访问控制的主要推荐方法是使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/index.yml)，可通过“访问控制(IAM)”边栏选项卡进行访问。 只有在你想要使用与订阅关联的 Azure Active Directory 租户来保护数据平面的访问安全性时，Azure RBAC 才会起作用。 如果你想使用不同的租户，Azure API for FHIR 提供了一个本地 FHIR 数据平面访问控制机制。 使用本地 RBAC 机制时，配置选项并没有那么丰富。 有关详细信息，请选择以下选项之一：

* [FHIR 的 Azure RBAC 数据平面](configure-azure-rbac.md)。 当你使用与订阅关联的 Azure Active Directory 租户时，这是首选选项。
* [本地 FHIR 数据平面访问控制](configure-local-rbac.md)。 只有在需要将外部 Azure Active Directory 租户用于数据平面访问控制时，才使用此选项。 

## <a name="enable-diagnostic-logging"></a>启用诊断记录
你可能需要在设置过程中启用诊断日志记录，以便能够监视服务，并出于合规性目的提供准确的报告。 有关如何设置诊断日志记录的详细信息，请参阅相关的[操作指南](enable-diagnostic-logging.md)，以及一些示例查询。 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>使用自定义标头将数据添加到审核日志
在 Azure API for FHIR 中，你可能希望在日志中包含来自调用系统的更多信息。 要包含这些信息，可以使用自定义标头。

可以使用自定义标头来捕获多种类型的信息。 例如：

* 标识或授权信息
* 调用方的源点
* 发起组织
* 客户端系统详细信息（电子病历、患者门户）

若要将此数据添加到审核日志，请参阅[使用自定义 HTTP 头将数据添加到审核日志](use-custom-headers.md)操作指南。

## <a name="next-steps"></a>后续步骤

在此操作指南中，你将为 Azure API for FHIR 设置其他设置。

接下来，请学习系列教程，创建一个读取 FHIR 数据的 Web 应用。

>[!div class="nextstepaction"]
>[部署 JavaScript 应用程序](tutorial-web-app-fhir-server.md)