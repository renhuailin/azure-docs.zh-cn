---
title: 连接到 Azure Blob 存储
description: 使用 Azure 逻辑应用在 Azure 存储帐户中创建和管理 Blob
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: 1703e206bb13aa6f239346f7a724004a00ddfeca
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111568911"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Azure Blob 存储中创建和管理 Blob

本文介绍如何使用 Azure Blob 存储连接器通过逻辑应用在 Azure 存储帐户中访问和管理作为 Blob 存储的文件。 可以通过这种方式创建逻辑应用，以便自动完成进行文件管理所需的任务和工作流。 例如，可以生成用于在存储帐户中创建、获取、更新和删除文件的逻辑应用。

假定你有一个在 Azure 网站上进行更新的工具。 它充当逻辑应用的触发器。 当此事件发生时，可以让逻辑应用更新 Blob 存储容器中的某些文件，这是逻辑应用中的一项操作。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需特定于连接器的技术信息，请参阅 [Azure Blob 存储连接器参考](/connectors/azureblobconnector/)。

> [!IMPORTANT]
> 如果逻辑应用和防火墙后的存储帐户位于同一区域，则逻辑应用无法直接访问这些存储帐户。 解决方法是将逻辑应用和存储帐户放在不同的区域。 有关如何使 Azure 逻辑应用能够访问防火墙后的存储帐户的详细信息，请参阅本主题稍后的[访问防火墙后的存储帐户](#storage-firewalls)部分。

<a name="blob-storage-limits"></a>

## <a name="limits"></a>限制

* 默认情况下，Azure Blob 存储操作可以读取或写入 50 MB 或更小的文件。  若要处理大于 50 MB 但不超过 1024 MB 的文件，Azure Blob 存储操作支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 “获取 Blob 内容”操作隐式使用分块。 

* Azure Blob 存储触发器不支持分块。 请求文件内容时，触发器仅选择 50 MB 或更小的文件。 若要获取大于 50 MB 的文件，请遵循以下模式：

  * 使用返回文件属性的 Azure Blob 存储触发器，例如“添加或修改 Blob 时(仅属性)”。 

  * 在触发器后面添加 Azure Blob 存储“获取 Blob 内容”操作，该操作读取完整文件并隐式使用分块。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [Azure 存储帐户和存储容器](../storage/blobs/storage-quickstart-blobs-portal.md)

* 需在其中访问 Azure Blob 存储帐户的逻辑应用。 若要通过 Azure Blob 存储触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>添加 Blob 存储触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

此示例演示在存储容器中添加或更新 Blob 的属性时，如何使用“添加或修改 Blob 时(仅属性)”触发器来启动逻辑应用工作流。 

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 中创建一个空白的逻辑应用，这会打开逻辑应用设计器。 此示例使用 Azure 门户。

2. 在搜索框中，输入“azure blob”作为筛选器。 在触发器列表中，选择所需的触发器。

   此示例使用以下触发器：“添加或修改 Blob 时(仅属性)” 

   ![选择 Azure Blob 存储触发器](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. 如果系统提示输入连接详细信息，请[立即创建 Blob 存储连接](#create-connection)。 或者，如果连接已存在，请提供触发器所需的信息。

   对于此示例，请选择要监视的容器和文件夹。

   1. 在“容器”框中，选择文件夹图标。 

   2. 在文件夹列表中选择右尖括号 ( **>** )，然后以浏览方式查找并选择所需的文件夹。

      ![选择要与触发器配合使用的存储文件夹](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 选择你希望触发器以多大时间间隔和频率来检查文件夹中的更改。

4. 完成后，请在设计器工具栏上选择“保存”  。

5. 现在请继续向逻辑应用添加一个或多个操作，以便完成需对触发器结果执行的任务。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>添加 Blob 存储操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 就此示例来说，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)。

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

2. 在逻辑应用设计器的触发器或操作下，选择“新建步骤”。 

   ![向逻辑应用工作流添加新步骤](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 依次选择出现的加号 ( **+** ) 和“添加操作”  。

3. 在搜索框中，输入“azure blob”作为筛选器。 从操作列表中选择所需的操作。

   此示例使用以下操作：“获取 Blob 内容” 

   ![选择 Azure Blob 存储操作](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. 如果系统提示输入连接详细信息，请[立即创建 Azure Blob 存储连接](#create-connection)。
或者，如果连接已存在，请提供操作所需的信息。

   对于此示例，请选择所需的文件。

   1. 从“Blob”框中选择文件夹图标。 
  
      ![选择要与操作配合使用的存储文件夹](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. 根据 Blob 的 ID 编号查找并选择所需的文件。  可以在上述 Blob 存储触发器返回的 Blob 元数据中找到此 ID 编号。 

5. 完成后，请在设计器工具栏上选择“保存”  。
若要测试逻辑应用，请确保所选文件夹包含一个 Blob。

此示例仅获取 Blob 的内容。 若要查看这些内容，请使用另一连接器添加另一操作，以便创建包含此 Blob 的文件。 例如，添加一个 OneDrive 操作，以便根据 Blob 内容创建文件。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>连接到存储帐户

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 当系统提示创建连接时，请提供以下信息：

   | 属性 | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **连接名称** | 是 | <*connection-name*> | 将要为连接创建的名称 |
   | **存储帐户** | 是 | <*storage-account*> | 从列表中选择存储帐户。 |
   ||||

   例如：

   ![创建 Azure Blob 存储帐户连接](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. 准备就绪后，选择“创建”。 

1. 创建连接后，继续[添加 Blob 存储触发器](#add-trigger)或[添加 Blob 存储操作](#add-action)。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/azureblobconnector/)。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本使用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>访问防火墙后的存储帐户

使用[防火墙和防火墙规则](../storage/common/storage-network-security.md)限制访问可以提高 Azure 存储帐户的网络安全性。 但是，此设置会给需要访问存储帐户的 Azure 和其他 Microsoft 服务带来挑战。 数据中心内的本地通信会抽取内部 IP 地址，因此无法设置实施 IP 限制的防火墙规则。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)。

下面是使用 Azure Blob 存储连接器或其他解决方案从 Azure 逻辑应用访问防火墙后的存储帐户的各种选项：

* Azure 存储 Blob 连接器

  * [访问其他区域中的存储帐户](#access-other-regions)
  * [通过受信任的虚拟网络访问存储帐户](#access-trusted-virtual-network)

* 其他解决方案

  * [使用托管标识访问用作受信任服务的存储帐户](#access-trusted-service)
  * [通过 Azure API 管理访问存储帐户](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>访问同一区域中的存储帐户时出现问题

如果逻辑应用和防火墙后的存储帐户位于同一区域，则逻辑应用无法直接访问这些存储帐户。 解决方法是将逻辑应用与存储帐户放在不同的区域，并为[你所在区域中的托管连接器授予对出站 IP 地址的访问权限](../logic-apps/logic-apps-limits-and-config.md#outbound)。

> [!NOTE]
> 此解决方案不适用于 Azure 表存储连接器和 Azure 队列存储连接器。 若要访问表存储或队列存储，请改用内置的 HTTP 触发器和操作。

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>通过受信任的虚拟网络访问存储帐户

可以将存储帐户放置在你管理的 Azure 虚拟网络中，然后将相应虚拟网络添加到受信任的虚拟网络列表中。 为了让逻辑应用通过[受信任的虚拟网络](../virtual-network/virtual-networks-overview.md)访问存储帐户，你需要将相应逻辑应用部署到[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，此环境可以连接到虚拟网络中的资源。 然后，可以将相应 ISE 中的子网添加到受信任列表。 Azure 存储连接器（如 Blob 存储连接器）可以直接访问存储容器。 这种设置与从 ISE 使用服务终结点的体验相同。

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>使用托管标识访问用作受信任服务的存储帐户

若要授予 Microsoft 受信任服务通过防火墙访问某个存储帐户的权限，可在该存储帐户中为这些服务设置一个例外。 此解决方案允许支持[使用托管标识进行身份验证](../active-directory/managed-identities-azure-resources/overview.md)的 Azure 服务访问防火墙后用作受信任服务的存储帐户。 具体而言，要使全球多租户 Azure 中的逻辑应用能够访问这些存储帐户，首先请在该逻辑应用中[启用托管标识支持](../logic-apps/create-managed-service-identity.md)。 然后，在逻辑应用中使用 HTTP 操作或触发器，并[将其身份验证类型设置为使用该逻辑应用的托管标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 对于此方案，只能使用 HTTP 操作或触发器。 

> [!NOTE]
> 如果使用托管标识功能对访问存储帐户进行身份验证，则不能使用内置的 Azure Blob 存储操作。 必须使用设置了托管标识的 HTTP 触发器或操作对存储帐户连接进行身份验证。 要运行必要的存储操作，必须调用 Azure Blob 存储的 相应 REST API。 有关详细信息，请查看 [Blob 服务 REST API](/rest/api/storageservices/blob-service-rest-api)。

若要设置例外和托管标识支持，请执行以下常规步骤：

1. 在存储帐户的“设置”下，选择“防火墙和虚拟网络”。   在“允许从以下位置访问”下，选择“选定的网络”选项，以显示相关设置。  

1. 在“例外”下，选择“允许受信任的 Microsoft 服务访问此存储帐户”，然后选择“保存”。   

   ![选择允许 Microsoft 受信任服务的例外](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. 在逻辑应用的设置中，[启用对托管标识的支持](../logic-apps/create-managed-service-identity.md)。

1. 在逻辑应用的工作流中，添加并设置 HTTP 操作或触发器以访问存储帐户或实体。

   > [!IMPORTANT]
   > 若要对 Azure 存储帐户执行传出 HTTP 操作或触发器调用，请确保请求标头包含要对存储帐户运行的操作的 `x-ms-version` 属性和 API 版本。 有关详细信息，请参阅[使用托管标识对访问进行身份验证](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)和 [Azure 存储服务的版本控制](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)。

1. 在该操作中，[选择用于身份验证的托管标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>通过 Azure API 管理访问存储帐户

如果使用 [API 管理](../api-management/api-management-key-concepts.md)的专用层，则可支持存储 API，方法是使用 API 管理并允许其 IP 地址通过防火墙。 简单而言，将 API 管理使用的 Azure 虚拟网络添加到存储帐户的防火墙设置， 然后使用 API 管理操作或 HTTP 操作调用 Azure 存储 API 即可。 但是，如果选择此选项，则需自行处理身份验证过程。 有关详细信息，请参阅[简单的企业集成体系结构](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
