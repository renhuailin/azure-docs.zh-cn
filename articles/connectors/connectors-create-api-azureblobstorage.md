---
title: 连接到 Azure Blob 存储
description: 通过使用 Azure 逻辑应用在 Azure 存储账户中创建和管理 Blob。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/23/2021
tags: connectors
ms.openlocfilehash: e3d8730976d623b5304a50341bcf257f35047e25
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359544"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Azure Blob 存储中创建和管理 Blob

可以在 Azure 逻辑应用中使用 [Azure Blob 存储连接器](/connectors/azureblobconnector/)访问和管理 Azure 存储帐户中以 Blob 形式存储的文件。 此连接器为逻辑应用工作流中的 Blob 操作提供了触发器以及相应操作。 可以使用这些操作来让任务和工作流自动化以便管理存储账户中的文件。 [可用的连接器操作](/connectors/azureblobconnector/#actions)包括检查、删除、读取和上传 Blob。 添加或修改 Blob 时，将触发[可用触发器](/connectors/azureblobconnector/#triggers)。 

可以同时从标准型和消耗型逻辑应用资源类型连接到 Blob 存储。 可以将连接器与单租户、多租户或集成服务环境 (ISE) 中的逻辑应用一起使用。 对于单租户环境中的逻辑应用，Blob 存储提供内置操作以及托管连接器操作。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本使用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

了解更多有关此连接器的触发器、操作和限制等技术信息，请参阅[连接器的参考页面](/connectors/azureblobconnector/)。

还可以[使用具有 HTTP 触发器或操作的托管标识来执行 Blob 操作](#access-blob-storage-with-managed-identities)，而不是使用 Blob 存储连接器。

> [!IMPORTANT]
> 如果逻辑应用和防火墙后的存储帐户位于同一区域，则逻辑应用无法直接访问这些存储帐户。 解决方法是将逻辑应用和存储帐户放在不同的区域。 有关如何使 Azure 逻辑应用能够访问防火墙后的存储帐户的详细信息，请参阅本主题稍后的[访问防火墙后的存储帐户](#access-storage-accounts-behind-firewalls)部分。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。
- [Azure 存储帐户和存储容器](../storage/blobs/storage-quickstart-blobs-portal.md)
- 想要借此访问 Blob 存储账户的逻辑应用工作流。 若要通过 Azure Blob 存储触发器启动逻辑应用，你需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="limits"></a>限制

- 默认情况下，Azure Blob 存储操作可以读取或写入小于或等于 50 MB 的文件。 若要处理大于 50 MB 但不超过 1024 MB 的文件，Azure Blob 存储操作支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 “获取 Blob 内容”[操作](/connectors/azureblobconnector/#get-blob-content)隐式使用分块。
- Blob 存储触发器不支持分块。 请求文件内容时，触发器仅选择 50 MB 或更小的文件。 若要获取大于 50 MB 的文件，请遵循以下模式：
  - 使用返回文件属性的 Blob 存储触发器，比如 [**当添加或者修改 Blob 时（仅属性）**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only))。
  - 在触发器后面添加 Azure Blob 存储“获取 Blob 内容”[操作](/connectors/azureblobconnector/#get-blob-content)，该操作会读取完整文件并隐式使用分块。

## <a name="add-blob-storage-trigger"></a>添加 Blob 存储触发器

在 Azure 逻辑应用中，每个逻辑应用都必须以[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或满足特定条件时触发。 

此连接器有一个可用的触发器，“在 Azure 存储中添加或修改 Blob 时”[，或“在添加或修改 Blob 时（仅属性）”](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only))调用。 在存储容器中添加或更新 Blob 的属性时，会触发触发器。 每当触发器触发时，Azure 逻辑应用引擎就会创建一个逻辑应用实例并开始运行工作流。

### <a name="single-tenant"></a>[单租户](#tab/single-tenant)

若要在使用标准计划的单租户逻辑应用中添加 Blob 存储操作，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在设计器中打开工作流。
1. 在搜索框中，输入 `Azure blob` 作为筛选器。 从触发器列表中，选择名为“在 Azure 存储中添加或修改 blob 时”的触发器。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="设计器中标准逻辑应用的屏幕截图，其中显示了名为“在 Azure 存储中添加或修改 blob 时”的触发器选择。":::
1. 如果系统提示输入连接详细信息，请[立即创建 Blob 存储连接](#connect-to-storage-account)。
1. 提供触发器的必要信息。
    1. 在“参数”选项卡下，将“Blob 路径”添加到要监视的 Blob 。
        若要查找 Blob 路径，请打开 Azure 门户中的存储帐户。 在导航菜单中的“数据存储”下，选择“容器” 。 选择你的 Blob 容器。 在容器导航菜单上的“设置”下，选择“属性” 。 复制 URL 值，这是 Blob 的路径。 路径类似于 `https://{your-storage-account}.blob.core.windows.net/{your-blob}`。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="设计器中标准逻辑应用的屏幕截图，其中显示了 Blob 存储触发器的参数配置。":::
    1. 根据需要配置其他触发器设置。
    1. 选择“完成”。
1. 向工作流添加一个或多个操作。
1. 若要保存更改，请在设计器工具栏上选择“保存”。

### <a name="multi-tenant"></a>[多租户](#tab/multi-tenant)

若要在使用消耗型计划的多租户逻辑应用中添加 Blob 存储操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在逻辑应用设计器中打开工作流。
1. 在搜索框中，输入“Azure Blob”作为筛选器。 在“触发器”列表中，选择触发器“添加或修改 Blob 时（仅属性）”。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="设计器中消耗型逻辑应用屏幕截图，显示所选的 Blob 存储触发器。":::
1. 如果系统提示输入连接详细信息，请[立即创建 Blob 存储连接](#connect-to-storage-account)。
1. 提供触发器的必要信息。
    1. 对于“容器”，选择“文件夹”图标以选择你的 Blob 存储容器。 或者，手动输入路径。
    1. 根据需要配置其他触发器设置。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="设计器中消耗型逻辑应用屏幕截图，显示 Blob 存储触发器的参数配置。":::
1. 向工作流添加一个或多个操作。
1. 若要保存更改，请在设计器工具栏上选择“保存”。

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>添加 Blob 存储操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中跟在某个触发器或另一操作后面的步骤。

### <a name="single-tenant"></a>[单租户](#tab/single-tenant)

对于单租户环境中的逻辑应用：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在逻辑应用设计器中打开工作流。
1. 添加触发器。 此示例开始使用“定期”[触发器](../connectors/connectors-native-recurrence.md)。
1. 向工作流添加新步骤。 在搜索框中，输入“Azure Blob”作为筛选器。 然后，选择要使用的 Blob 存储操作。 此示例使用“从 Azure 存储中读取 Blob”。 
   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="设计器中标准逻辑应用的屏幕截图，其中显示了可用 Blob 存储操作的列表。":::
1. 如果系统提示输入连接详细信息，请[创建到 Blob 存储账户的连接](#connect-to-storage-account)。
1. 提供操作的所需信息。
    1. 对于“容器名称”，请输入想要使用的 Blob 容器的路径。
    1. 对于“Blob 名称”，请输入想要使用的 Blob 的路径。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="设计器中标准逻辑应用的屏幕截图，其中显示了“Blob 存储”触发器选择。":::
    1. 根据需要配置其他操作设置。
1. 在设计器工具栏上选择“保存”。 
1. 测试逻辑应用，确保所选容器包含 Blob。 

> [!TIP]
> 此示例仅读取 Blob 的内容。 若要查看这些内容，请使用另一连接器添加另一操作，以便创建包含此 Blob 的文件。 例如，添加一个 OneDrive 操作，以便根据 Blob 内容创建文件。

### <a name="multi-tenant"></a>[多租户](#tab/multi-tenant)

对于多租户环境中的逻辑应用：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在逻辑应用设计器中打开工作流。
1. 添加触发器。 此示例开始使用“定期”[触发器](../connectors/connectors-native-recurrence.md)。
1. 向工作流添加新步骤。 在搜索框中，输入“Azure Blob”作为筛选器。 然后，选择要使用的 Blob 存储操作。 此示例使用“获取 Blob 内容”。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="设计器中的消耗型逻辑应用屏幕截图，显示可用 Blob 存储操作的列表。":::
1. 如果系统提示输入连接详细信息，请[创建到 Blob 存储账户的连接](#connect-to-storage-account)。
1. 提供操作的所需信息。
    1. 对于“Blob”，请选择“文件夹”图标以选择你的 Blob 存储容器。 或者，手动输入路径。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="设计器中的消耗型逻辑应用屏幕截图，显示 Blob 存储操作的配置。":::
    1. 根据需要配置其他操作设置。

---

## <a name="connect-to-storage-account"></a>连接到存储帐户

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

你需要连接到存储帐户，然后才能配置 [Blob 存储触发器](#add-blob-storage-trigger) 或 [Blob 存储操作](#add-blob-storage-action)。 连接需要以下属性。

| 属性 | 必须 | 值 | 说明 |
|----------|----------|-------|-------------|
| **连接名称** | 是 | <*connection-name*> | 将要为连接创建的名称 |
| **Azure Blob 存储连接字符串** | 是 | <*storage-account*> | 从列表中选择存储帐户，或提供一个字符串。 |

> [!TIP]
> 要查找连接字符串，请参阅存储帐户页面。 在导航菜单上的“安全性 + 网络”下，选择“访问密钥” 。 选择“显示密钥”。 复制两个可用连接字符串值的其中一个。

### <a name="single-tenant"></a>[单租户](#tab/single-tenant)

对于单租户环境中的逻辑应用：

1. 对于“连接名称”，请为连接输入一个名称。
1. 对于“Azure Blob 存储连接字符串”，请输入想要使用的存储帐户的连接字符串。
1. 选择“创建”以建立连接。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="设计器中标准逻辑应用的屏幕截图，其中显示了将新连接添加到 Blob 存储步骤的提示。":::

如果已有连接，但想要选择其他连接，请在步骤的编辑器中选择“更改连接”。

如果在连接到存储帐户时遇到问题，请参阅[如何访问防火墙后的存储帐户](#access-storage-accounts-behind-firewalls)。

### <a name="multi-tenant"></a>[多租户](#tab/multi-tenant)

对于多租户环境中的逻辑应用：

1. 对于“连接名称”，请为连接输入一个名称。
1. 对于“存储帐户”，请选择你的 Blob 容器所在的存储帐户。 或者，选择“手动输入连接信息”以自行提供路径。
1. 选择“创建”以建立连接。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="设计器中消耗型逻辑应用的屏幕截图，显示向 Blob 存储步骤添加新连接的提示。":::

---

## <a name="access-storage-accounts-behind-firewalls"></a>访问防火墙后的存储帐户

通过[使用防火墙和防火墙规则限制访问](../storage/common/storage-network-security.md)，可以提高 Azure 存储帐户的网络安全性。 但是，此设置会给需要访问存储帐户的 Azure 和其他 Microsoft 服务带来挑战。 数据中心内的本地通信会提取内部 IP 地址，因此无法设置带 IP 限制的防火墙规则。 

要使用 Blob 存储连接器访问防火墙后面的存储账户：

- [访问其他区域中的存储帐户](#access-storage-accounts-in-other-regions)
- [通过受信任的虚拟网络访问存储帐户](#access-storage-accounts-through-trusted-virtual-network)

访问防火墙后面的存储账户的其他解决方案：

- [使用托管标识访问用作受信任服务的存储帐户](#access-blob-storage-with-managed-identities)
- [通过 Azure API 管理访问存储帐户](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>访问其他区域中的存储帐户

如果逻辑应用和防火墙后的存储帐户位于同一区域，则逻辑应用无法直接访问这些存储帐户。 一种解决方案是将逻辑应用放在与存储帐户不同的区域。 然后，允许访问[区域中托管连接器的出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。

> [!NOTE]
> 此解决方案不适用于 Azure 表存储连接器和 Azure 队列存储连接器。 相反的，若要访问表存储或者队列存储，[请使用内置的 HTTP 触发器和操作](../logic-apps/logic-apps-http-endpoint.md)。

若要将出站 IP 地址添加到存储帐户防火墙：

1. 请注意逻辑应用所在地区的[出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开存储帐户的页面。 在导航菜单中的“安全性 + 网络”下，选择“网络” 。 
1. 在“允许的访问来源”下，选择“所选网络”选项。 相关设置现在会显示在页面上。
1. 在“防火墙”下，添加需要访问的 IP 地址或者范围。 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Azure 门户中 blob 存储帐户网络页的屏幕截图，显示将 IP 地址和范围添加到允许列表的防火墙设置。":::

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>通过受信任的虚拟网络访问存储帐户

可以将存储帐户放置在你管理的 Azure 虚拟网络中，然后将相应虚拟网络添加到受信任的虚拟网络列表中。 为了让逻辑应用通过[受信任的虚拟网络](../virtual-network/virtual-networks-overview.md)访问存储帐户，你需要将相应逻辑应用部署到[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，此环境可以连接到虚拟网络中的资源。 然后，可以将相应 ISE 中的子网添加到受信任列表。 Azure 存储连接器（如 Blob 存储连接器）可以直接访问存储容器。 这种设置与从 ISE 使用服务终结点的体验相同。

### <a name="access-storage-accounts-through-azure-api-management"></a>通过 Azure API 管理访问存储帐户

如果使用 [API 管理](../api-management/api-management-key-concepts.md)的专用层，则可支持存储 API，方法是使用 API 管理并允许其 IP 地址通过防火墙。 简单而言，将 API 管理使用的 Azure 虚拟网络添加到存储帐户的防火墙设置， 然后使用 API 管理操作或 HTTP 操作调用 Azure 存储 API 即可。 但是，如果选择此选项，则需自行处理身份验证过程。 有关详细信息，请参阅[简单的企业集成体系结构](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)。

## <a name="access-blob-storage-with-managed-identities"></a>使用托管标识访问 Blob 存储

如果要在不使用此逻辑应用连接器的情况下访问 Blob 存储，可以改为使用[托管标识进行身份验证](../active-directory/managed-identities-azure-resources/overview.md)。 你可以创建一个异常，让 Microsoft 受信任的服务（如托管标识）可以穿过防火墙访问存储帐户。

若要在逻辑应用中使用托管标识访问 Blob 存储：

1. [配置访问存储账户的权限](#configure-storage-account-access)
1. [为逻辑应用创建角色分配](#create-role-assignment-for-logic-app)
1. [在逻辑应用中启用对托管标识的支持](#enable-support-for-managed-identity-in-logic-app)

> [!NOTE]
> 此解决方案的的限制：
> - 只能在工作流中使用 HTTP 触发器或操作。
> - 必须设置托管标识，才能对存储帐户连接进行身份验证。
> - 如果使用托管标识进行身份验证，则不能使用内置 Blob 存储操作。
> - 对于单租户环境中的逻辑应用，仅系统分配的托管标识可用并受支持，而不是用户分配的托管标识。

### <a name="configure-storage-account-access"></a>配置存储帐户访问权限

若要设置异常和托管标识支持，请先为存储帐户配置适当访问权限：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开存储帐户的页面。 在导航菜单中的“安全性 + 网络”下，选择“网络” 。 
1. 在“允许的访问来源”下，选择“所选网络”选项。 相关设置现在会显示在页面上。
1. 如果需要从计算机访问存储帐户，请在“防火墙”下启用“添加客户端 IP 地址 。
1. 在“异常”下，启用“允许受信任的 Microsoft 服务访问此存储帐户” 。 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Azure 门户中 Blob 存储帐户网络页的屏幕截图，其中显示了允许所选网络、客户端 IP 地址和受信任的 Microsoft 服务的设置。":::
1. 选择“保存”。

> [!TIP]
> 如果尝试从工作流连接到存储帐户时收到“403 禁止”错误，可能有多个原因。 在继续执行其他步骤之前，请尝试以下解决方法。 首先，禁用“允许受信任的 Microsoft 服务访问此存储帐户”设置，并保存所做的更改。 然后重新启用此设置，并再次保存更改。 

### <a name="create-role-assignment-for-logic-app"></a>为逻辑应用创建角色分配

接下来，在逻辑应用上[启用托管标识支持](../logic-apps/create-managed-service-identity.md)。

1. 在 Azure 门户中，打开逻辑应用。
1. 在导航菜单中的“设置”下，选择“标识” 。
1. 在“系统分配”下，将“状态”设置为“开”。 此设置可能已启用。
1. 在“权限”下，选择“Azure 角色分配” 。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Azure 门户中逻辑应用菜单的屏幕截图，显示标识设置窗格，具有可添加 Azure 角色分配权限的按钮。":::
1. 在“Azure 角色分配”窗格中，选择“添加角色分配” 。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="逻辑应用角色分配窗格的屏幕截图，显示所选订阅和用于添加新角色分配的按钮。":::
1. 按如下所示配置新角色分配。
    1. 对于“范围”，选择“存储” 。
    1. 对于“订阅”，选择存储帐户所在的订阅。
    1. 对于“资源”，选择要从逻辑应用访问的存储帐户。
    1. 对于“角色”，选择适合方案的恰当权限。 此示例使用“存储 Blob 数据参与者”，该角色允许对 Blob 容器和日期的读取、写入和删除的权限。 将鼠标悬停在下拉菜单中某个角色旁边的信息图标上，以获取权限详细信息。
    1. 选择“保存”以完成角色分配的创建。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="角色分配配置窗格的屏幕截图，显示范围、订阅、资源和角色的设置。":::

### <a name="enable-support-for-managed-identity-in-logic-app"></a>在逻辑应用中启用对托管标识的支持

接下来，在工作流中添加 [HTTP 触发器或操作](connectors-native-http.md)。 请确保[将身份验证类型设置为使用托管标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 

对于单租户环境和多租户环境中的逻辑应用，步骤是相同的。

1. 在逻辑应用设计器中打开工作流。
1. 使用“HTTP”触发器或操作向工作流中添加一个新步骤，具体取决于你的方案。
1. 为“HTTP”触发器或操作配置所有必需的参数。
    1. 为请求选择一个“方法”。 此示例使用 HTTP PUT 方法。
    1. 输入 Blob 的“URI”。 路径类似于 `https://{your-storage-account}.blob.core.windows.net/{your-blob}`。
    1. 在“标头”下，添加值为 `BlockBlob` 的 Blob 类型标头 `x-ms-blob-type`。
    1. 在“标头”下，还添加具有适当值的 API 版本标头 `x-ms-version`。 有关详细信息，请参阅[使用托管标识对访问进行身份验证](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)和 [Azure 存储服务的版本控制](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="逻辑应用设计器的屏幕截图，显示所需的 HTTP PUT 操作参数。":::
1. 选择“添加新参数”，并选择“身份验证”[以配置托管标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 
    1. 在“身份验证”下，为“身份验证类型”选择“托管标识”。  
    1. 对于“托管标识”，选择系统分配的托管标识 。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="逻辑应用设计器的屏幕截图，显示用于托管标识的 HTTP 操作身份验证参数设置。":::
1. 在逻辑应用设计器工具栏上，选择“保存”。

现在，可以调用 [Blob 服务 REST API](/rest/api/storageservices/blob-service-rest-api) 来运行任何必要的存储操作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解逻辑应用连接器的详细信息](../connectors/apis-list.md)
