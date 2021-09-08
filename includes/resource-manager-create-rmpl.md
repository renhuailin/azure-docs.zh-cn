---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/29/2021
ms.author: tomfitz
ms.openlocfilehash: 735f19f1c9f66db6d311c42528b36a44bf924ec0
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227282"
---
使用专用链接，可以通过虚拟网络中的专用终结点访问 Azure 服务。 将专用链接与 Azure 资源管理器的操作相结合时，可阻止不在特定终结点的用户管理资源。 如果恶意用户获取订阅中帐户的凭据，则该用户会因未在特定终结点而无法管理资源。

专用链接提供以下安全优势：

* **专用访问** - 用户可以通过专用终结点从专用网络管理资源。
* **数据外泄** - 拒绝用户访问范围内未包含的资源。

## <a name="understand-architecture"></a>了解体系结构

对于此版本，只能在根[管理组](../articles/governance/management-groups/overview.md)级别应用专用链接管理访问。 此限制意味着在租户范围应用专用链接访问。

通过专用链接实现管理时，将使用两种资源类型。

* 资源管理专用链接 (Microsoft.Authorization/resourceManagementPrivateLinks)
* 专用链接关联 (Microsoft.Authorization/privateLinkAssociations)

下图显示了如何构造一个解决方案，用于限制对资源的管理访问权限。

:::image type="content" source="./media/resource-manager-create-rmpl/resource-management-private-link.svg" alt-text="资源管理专用链接关系图":::

专用链接关联扩展了根管理组。 专用链接关联和专用终结点引用资源管理专用链接。

## <a name="workflow"></a>工作流

若要为资源设置专用链接，请使用以下步骤。 本文稍后将更详细地介绍这些步骤。

1. 创建资源管理专用链接。
1. 创建专用链接关联。 专用链接关联扩展了根管理组。 它还引用资源管理专用链接的资源 ID。
1. 添加一个引用资源管理专用链接的专用终结点。

完成这些步骤后，你可以管理范围层次结构中的 Azure 资源。 使用连接到子网的专用终结点。

可以监视对专用链接的访问。 有关详细信息，请参阅[记录和监视](../articles/private-link/private-link-overview.md#logging-and-monitoring)。

## <a name="required-permissions"></a>所需的权限

若要设置用于资源管理的专用链接，需要以下访问权限：

* 订阅的所有者。 需要此访问权限才能创建资源管理专用链接资源。
* 根管理组的所有者或参与者。 需要此访问权限才可创建专用链接关联资源。
* Azure Active Directory 的全局管理员不会自动拥有在根管理组分配角色的权限。 为了能够创建资源管理专用链接，全局管理员必须有权读取根管理组并[提升访问权限](../articles/role-based-access-control/elevate-access-global-admin.md)，以便对租户中所有订阅和管理组具有用户访问管理员权限。 获取用户访问管理员权限后，全局管理员必须在根管理组中向创建专用链接关联的用户授予所有者或参与者权限。