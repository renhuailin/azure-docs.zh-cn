---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: c29c7abf0964edb7a5427d8c2cfe62e066d13bfb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900777"
---
在开始之前，请确保：

* 已为 Azure Stack Edge 资源启用 Microsoft Azure 订阅。 确保使用了受支持的订阅，例如 [Microsoft 企业协议 (EA)](https://azure.microsoft.com/overview/sales-number/)、[云解决方案提供商 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。
* 你在资源组级别拥有对 Azure Stack Edge/Azure 存储网关、IoT 中心和 Azure 存储资源的所有者或参与者访问权限。

  * 若要创建任何 Azure Stack Edge 资源，你应该在资源组级别范围内具有参与者（或更高级别）权限。 你还需要确保已注册 `Microsoft.DataBoxEdge` 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 若要创建任何 IoT 中心资源，请确保已注册 Microsoft.Devices 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 若要创建存储帐户资源，你同样需要资源组级别范围内的参与者或更高级别访问权限。 Azure 存储在默认情况下是已注册的资源提供程序。
* 你拥有对 Azure Active Directory 图形 API 的管理员或用户访问权限。 有关详细信息，请参阅 [Azure Active Directory 图形 API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。
* 具有 Microsoft Azure 存储帐户和访问凭据。