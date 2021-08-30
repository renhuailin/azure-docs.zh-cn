---
author: baanders
description: 介绍如何配置 Azure 函数以使用 Azure 数字孪生的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748613"
---
可以使用 Azure CLI 或 Azure 门户设置函数应用的安全性访问。 按照适用于你首选项的步骤进行操作。

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

#### <a name="assign-an-access-role"></a>分配访问角色

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

通过使用系统分配的托管标识，Azure 资源无需在代码中存储凭据即可对云服务（如 Azure Key Vault）进行身份验证。 启用系统分配的托管标识后，可以通过 Azure 基于角色的访问控制授予所有必要的权限。 

此类托管标识的生命周期与此资源的生命周期关联。 此外，每个资源只能具有一个系统分配的托管标识。

1. 在 [Azure 门户](https://portal.azure.com/)的搜索框中，键入函数应用的名称以搜索函数应用。 选择结果中的应用。 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图。函数应用的名称处于门户搜索栏中，并突出显示了搜索结果。":::

1. 在函数应用页的左侧菜单中，选择“标识”以使用函数的托管标识。 在“系统分配”页中，验证“状态”是否设置为“开启” 。 如果不是，请立即设置，然后“保存”更改。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Azure 门户的屏幕截图。在函数应用的“标识”页中，“状态”选项设置为“开启”。" lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. 选择“Azure 角色分配”。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Azure 门户的屏幕截图。在 Azure Functions“标识”页中“权限”下，突出显示了“Azure 角色分配”按钮。" lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    选择“+ 添加角色分配(预览版)”。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Azure 门户的屏幕截图。在“Azure 角色分配”页中，突出显示了“添加角色分配(预览版)”按钮。" lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. 在“添加角色分配(预览版)”页上，选择以下值：

    * 范围：资源组
    * **订阅**：选择 Azure 订阅。
    * 资源组：选择你的资源组。
    * 角色：Azure 数字孪生数据所有者

    选择“保存”以保存详细信息。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="Azure 门户的屏幕截图，演示如何添加新角色分配。该对话框显示“范围”、“订阅”、“资源组”和“角色”的字段。":::

#### <a name="configure-application-settings"></a>配置应用程序设置

若要使函数可以访问 Azure 数字孪生实例的 URL，可以设置环境变量。 应用程序设置以环境变量的形式公开，以便允许访问 Azure 数字孪生实例。 有关环境变量的详细信息，请参阅管理函数应用。 

若要使用实例的 URL 设置环境变量，请先查找实例的主机名： 

1. 在 [Azure 门户](https://portal.azure.com)中搜索实例。 
1. 在左侧菜单中选择“概述”。 
1. 复制“主机名”值。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text="Azure 门户的屏幕截图。在实例的“概述”页中，突出显示了主机名值。":::

现在可以创建应用程序设置：

1. 在门户搜索栏中搜索函数应用，然后在结果中将其选中。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图。正在门户搜索栏中搜索函数应用的名称。突出显示了搜索结果。":::

1. 在左侧选择“配置”。 在“应用程序设置”选项卡上，选择“+ 新建应用程序设置” 。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Azure 门户的屏幕截图。在函数应用的“配置”选项卡中，突出显示了用于新建应用程序设置的按钮。":::

1. 在打开的窗口中，使用复制的主机名值创建应用程序设置。
    * **名称**：`ADT_SERVICE_URL`
    * **值**：`https://<your-Azure-Digital-Twins-host-name>`
    
    选择“确定”，以创建应用程序设置。
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Azure 门户的屏幕截图。在“添加/编辑应用程序设置”页中，填写了“名称”和“值”字段。突出显示了“确定”按钮。":::

1. 创建设置后，它应出现在“应用程序设置 ”选项卡上。验证 ADT_SERVICE_URL 是否出现在列表中。 然后通过选择“保存”来保存新应用程序设置。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Azure 门户的屏幕截图。在应用程序设置选项卡上，突出显示了新的 ADT SERVICE URL 设置和“保存”按钮。":::

1. 对应用程序设置进行的任何更改都需要重启应用程序，因此请在系统提示时选择“继续”以重启应用程序。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Azure 门户的屏幕截图。一个备注指出对应用程序设置进行的任何更改都会重启应用程序。":::

---
