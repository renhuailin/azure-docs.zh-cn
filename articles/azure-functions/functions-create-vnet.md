---
title: 使用专用终结点将 Azure Functions 与虚拟网络集成
description: 本教程介绍如何使用专用终结点将函数连接到 Azure 虚拟网络并将其锁定。
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 8007fde1d4aaf52a98bf924f607450462a993c1d
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708427"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>教程：使用专用终结点将 Azure Functions 与 Azure 虚拟网络集成

本教程介绍如何使用专用终结点将 Azure Functions 连接到 Azure 虚拟网络中的资源。 你将使用在虚拟网络后面锁定的存储帐户创建一个函数。 虚拟网络使用服务总线队列触发器。

在本教程中，你将：

> [!div class="checklist"]
> * 在高级计划中创建函数应用。
> * 创建 Azure 资源，例如服务总线、存储帐户和虚拟网络。
> * 锁定专用终结点后面的存储帐户。
> * 锁定专用终结点后面的服务总线。
> * 部署使用服务总线和 HTTP 触发器的函数应用。
> * 锁定专用终结点后面的函数应用。
> * 测试函数应用在虚拟网络中是否安全。
> * 清理资源。

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级计划中创建函数应用。

你将在高级计划中创建 .NET 函数应用，因为本教程使用的是 C#。 Windows 还支持其他语言。 高级计划提供无服务器规模，同时支持虚拟网络集成。

1. 在 Azure 门户菜单上或在门户“主页”中，选择“创建资源”。

1. 在“新建”页面，选择“计算” > “函数应用”。

1. 在“基本信息”页面，使用下表配置函数应用设置。

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **订阅** | 你的订阅 | 要在其下创建此新函数应用的订阅。 |
    | **[资源组](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 要在其中创建函数应用的新资源组的名称。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。  |
    |**发布**| 代码 | 选择发布代码文件或 Docker 容器。 |
    | **运行时堆栈** | .NET | 本教程使用 .NET。 |
    |**区域**| 首选区域 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

1. 在完成时选择“下一步:托管”。 在“托管”页上，输入以下设置。

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | [存储帐户](../storage/common/storage-account-create.md) |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称的长度必须为 3 到 24 个字符。 名称只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](./storage-considerations.md#storage-account-requirements)。 |
    |**操作系统**| Windows | 本教程使用 Windows。 |
    | **[计划](./functions-scale.md)** | Premium | 定义如何将资源分配给 Function App 的托管计划。 默认情况下，选择“高级”时，将创建一个新的应用服务计划。 默认的“SKU 和大小”为 EP1，其中 EP 表示“弹性高级”(elastic premium) 。 有关详细信息，请参阅[高级 SKU](./functions-premium-plan.md#available-instance-skus) 的列表。<br/><br/>在高级计划上运行 JavaScript 函数时，选择 vCPU 数更少的实例。 有关详细信息，请参阅[选择单核心高级计划](./functions-reference-node.md#considerations-for-javascript-functions)。  |

1. 在完成时选择“下一步:监视”。 在“监视”页上，输入以下设置。

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 如果需要更改新资源名称或将数据存储在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)的其他位置，请展开此设置。 |

1. 选择“查看 + 创建”，以便查看应用配置选择。

1. 在“查看 + 创建”页面查看设置。 然后，选择“创建”以预配和部署函数应用。

1. 在门户右上角，选择“通知”图标，留意是否显示了“部署成功”消息。 

1. 选择“转到资源”，查看新的函数应用。 还可选择“固定到仪表板”。 固定可以更轻松地从仪表板返回此函数应用资源。

恭喜！ 你已成功创建高级函数应用。

## <a name="create-azure-resources"></a>创建 Azure 资源

接下来，你将创建存储帐户、服务总线和虚拟网络。 
### <a name="create-a-storage-account"></a>创建存储帐户

虚拟网络需要一个存储帐户，该帐户与使用函数应用创建的帐户不同。

1. 在 Azure 门户菜单上或在门户“主页”中，选择“创建资源”。

1. 在“新建”页面，搜索“存储帐户”。 然后选择“创建”。

1. 在“基本信息”选项卡上，使用下表配置存储帐户设置。 所有其他设置都可以使用默认值。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 用函数应用创建的资源组。 |
    | **名称** | mysecurestorage| 将应用专用终结点的存储帐户的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 创建函数应用的区域。 |

1. 选择“查看 + 创建”。 通过验证后，选择“创建”。

### <a name="create-a-service-bus"></a>创建服务总线

1. 在 Azure 门户菜单上或在门户“主页”中，选择“创建资源”。

1. 在“新建”页面，搜索“服务总线”。 然后选择“创建”。

1. 在“基本信息”选项卡上，使用下表配置服务总线设置。 所有其他设置都可以使用默认值。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 |
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 用函数应用创建的资源组。 |
    | **命名空间名称** | myServiceBus| 将应用专用终结点的服务总线的名称。 |
    | **[位置](https://azure.microsoft.com/regions/)** | myFunctionRegion | 创建函数应用的区域。 |
    | **定价层** | 高级 | 选择此层将专用终结点与 Azure 服务总线一起使用。 |

1. 选择“查看 + 创建”。 通过验证后，选择“创建”。

### <a name="create-a-virtual-network"></a>创建虚拟网络

本教程中的 Azure 资源与虚拟网络集成或在其中提供。 你将使用专用终结点来包含虚拟网络中的网络流量。

本教程将创建以下两个子网：
- 默认：专用终结点的子网。 此子网提供专用 IP 地址。
- 函数：用于 Azure Functions 虚拟网络集成的子网。 此子网被委派到函数应用。

创建函数应用集成到的虚拟网络：

1. 在 Azure 门户菜单上或在门户“主页”中，选择“创建资源”。

1. 在“新建”页面，搜索“虚拟网络”。 然后选择“创建”。

1. 在“基本信息”选项卡上，使用下表配置虚拟网络设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 用函数应用创建的资源组。 |
    | **名称** | myVirtualNet| 函数应用将连接到的虚拟网络的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 创建函数应用的区域。 |

1. 在“IP 地址”选项卡上，选择“添加子网”。 使用下表配置子网设置。

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="创建虚拟网络配置视图的屏幕截图。":::

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **子网名称** | functions | 函数应用将连接到的子网的名称。 | 
    | **子网地址范围** | 10.0.1.0/24 | 子网地址范围。 请注意，在上图中，IPv4 地址空间为 10.0.0.0/16。 如果值为 10.1.0.0/16，则建议的子网地址范围为 10.1.1.0/24。 |

1. 选择“查看 + 创建”。 通过验证后，选择“创建”。

## <a name="lock-down-your-storage-account"></a>锁定存储帐户

Azure 专用终结点用于通过专用 IP 地址连接到特定 Azure 资源。 此连接确保网络流量保留在所选虚拟网络中，并且只能访问特定资源。 

使用存储帐户创建 Azure 文件存储、Azure Blob 存储和 Azure 表存储的专用终结点：

1. 在新的存储帐户中，在左侧菜单中选择“网络”。

1. 在“专用终结点连接”选项卡上，选择“专用终结点”。

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="介绍如何为存储帐户创建专用终结点的屏幕截图。":::

1. 在“基本信息”选项卡上，使用下表中显示的专用终结点设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用函数应用创建的资源组。 |
    | **名称** | file-endpoint | 存储帐户中文件专用终结点的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择创建存储帐户的区域。 |

1. 在“资源”选项卡上，使用下表中显示的专用终结点设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.Storage/storageAccounts | 存储帐户的资源类型。 |
    | **资源** | mysecurestorage | 创建的存储帐户。 |
    | **目标子资源** | 文件 | 将用于存储帐户中的文件的专用终结点。 |

1. 在“配置”选项卡上，在“子网”设置中，选择“默认”。

1. 选择“查看 + 创建”。 通过验证后，选择“创建”。 虚拟网络中的资源现可与存储文件通信。

1. 为 blob 创建另一个专用终结点。 在“资源”选项卡上，使用下表中显示的设置。 对于所有其他设置，请使用与创建文件专用终结点时相同的值。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.Storage/storageAccounts | 存储帐户的资源类型。 |
    | **名称** | blob-endpoint | 存储帐户中 Blob 专用终结点的名称。 |
    | **资源** | mysecurestorage | 创建的存储帐户。 |
    | **目标子资源** | blob | 将用于存储帐户中的 blob 的专用终结点。 |
1. 为表创建另一个专用终结点。 在“资源”选项卡上，使用下表中显示的设置。 对于所有其他设置，请使用与创建文件专用终结点时相同的值。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.Storage/storageAccounts | 存储帐户的资源类型。 |
    | 名称 | table-endpoint | 存储帐户中 Blob 专用终结点的名称。 |
    | **资源** | mysecurestorage | 创建的存储帐户。 |
    | **目标子资源** | 表 | 将用于存储帐户中的表的专用终结点。 |
1. 创建专用终结点后，返回到存储帐户的“防火墙和虚拟网络”部分。  
1. 确保已选择“所选网络”。  不需要添加现有虚拟网络。

虚拟网络中的资源现可使用专用终结点与存储帐户进行通信。
## <a name="lock-down-your-service-bus"></a>锁定服务总线

创建专用终结点以锁定服务总线：

1. 在新的服务总线中，在左侧菜单中选择“网络”。

1. 在“专用终结点连接”选项卡上，选择“专用终结点”。

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="展示如何转到服务总线专用终结点的屏幕截图。":::

1. 在“基本信息”选项卡上，使用下表中显示的专用终结点设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 用函数应用创建的资源组。 |
    | **名称** | sb-endpoint | 存储帐户中文件专用终结点的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 创建存储帐户的区域。 |

1. 在“资源”选项卡上，使用下表中显示的专用终结点设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.ServiceBus/namespaces | 服务总线的资源类型。 |
    | **资源** | myServiceBus | 在本教程前面创建的服务总线。 |
    | **目标子资源** | 命名空间 | 将用于服务总线命名空间的专用终结点。 |

1. 在“配置”选项卡上，在“子网”设置中，选择“默认”。

1. 选择“查看 + 创建”。 通过验证后，选择“创建”。 
1. 创建专用终结点后，返回到服务总线命名空间的“防火墙和虚拟网络”部分。
1. 确保已选择“所选网络”。
1. 选择“+添加现有虚拟网络”，添加最近创建的虚拟网络。
1. 在“添加网络”选项卡上，使用下表中的网络设置：

    | 设置 | 建议的值 | 说明|
    |---------|-----------------|------------|
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 |
    | **虚拟网络** | myVirtualNet | 函数应用将连接到的虚拟网络的名称。 |
    | **子网** | functions | 函数应用将连接到的子网的名称。 |

1. 选择“添加客户端 IP 地址”，为当前客户端 IP 授予对命名空间的访问权限。
    > [!NOTE]
    > 必须允许客户端 IP 地址，这样 Azure 门户才能[在本教程稍后部分将消息发布到队列中](#test-your-locked-down-function-app)。
1. 选择“启用”以启用服务终结点。
1. 选择“添加”，将所选虚拟网络和子网添加到服务总线的防火墙规则中。
1. 选择“保存”以保存更新的防火墙规则。

虚拟网络中的资源现可使用专用终结点与服务总线进行通信。

## <a name="create-a-file-share"></a>创建文件共享

1. 在创建的存储帐户中，在左侧菜单中选择“文件共享”。

1. 选择“+ 文件共享”。 出于本教程的目的，请将文件共享命名为“文件”。

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="介绍如何在存储帐户中创建文件共享的屏幕截图。":::

1. 选择“创建”。

## <a name="get-the-storage-account-connection-string"></a>获取存储帐户连接字符串

1. 在创建的存储帐户中，在左侧菜单中选择“访问密钥”。

1. 选择“显示密钥”。 复制并保存 key1 的连接字符串。 配置应用设置时需要此连接字符串。

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="介绍如何获取存储帐户连接字符串的屏幕截图。":::

## <a name="create-a-queue"></a>创建队列

创建 Azure Functions 服务总线触发器将在其中获取事件的队列：

1. 在服务总线中，在左侧菜单中选择“队列”。

1. 选择“队列”。 对于本教程，请将新队列命名为“队列”。

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="展示如何创建服务总线队列的屏幕截图。":::

1. 选择“创建”  。

## <a name="get-a-service-bus-connection-string"></a>获取服务总线连接字符串

1. 在服务总线中，在左侧菜单中选择“共享访问策略”。

1. 选择“RootManageSharedAccessKey”。 复制并保存“主连接字符串”。 配置应用设置时需要此连接字符串。

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="展示如何获取服务总线连接字符串的屏幕截图。":::

## <a name="integrate-the-function-app"></a>集成函数应用

要在虚拟网络中使用函数应用，需要将其加入子网。 将为 Azure Functions 虚拟网络集成使用特定子网。 将对在本教程中创建的其他专用终结点使用默认子网。

1. 在函数应用中，在左侧菜单中选择“网络”。

1. 在“VNet 集成”下，选择“单击此处进行配置”。 

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="介绍如何转到虚拟网络集成的屏幕截图。":::

1. 选择“添加 VNet”。

1. 在“虚拟网络”下，选择前面创建的虚拟网络。

1. 选择前面创建的“函数”子网。 选择“确定”。  函数应用现已与虚拟网络集成！

    如果虚拟网络和函数应用在不同的订阅中，你需要首先提供对虚拟网络上的服务主体 Microsoft Azure 应用服务的“参与者”访问权限。

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="介绍如何将函数应用连接到子网的屏幕截图。":::

## <a name="configure-your-function-app-settings"></a>配置函数应用设置

1. 在函数应用中，在左侧菜单中选择“配置”。

1. 要在虚拟网络中使用函数应用，请更新下表中显示的应用设置。 若要添加或编辑设置，请选择“+ 新增应用程序设置”或应用设置表最右侧列中的“编辑”图标。 完成后，选择“保存”。

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="介绍如何配置专用终结点的函数应用设置的屏幕截图。":::

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 创建的存储帐户的连接字符串。 此存储连接字符串来自[获取存储帐户连接字符串](#get-the-storage-account-connection-string)部分。 此设置允许函数应用在运行时使用安全的存储帐户进行正常操作。 | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 创建的存储帐户的连接字符串。 此设置允许函数应用使用 Azure 文件存储的安全存储帐户，该帐户在部署期间使用。 |
    | **WEBSITE_CONTENTSHARE** | files | 在存储帐户中创建的文件共享的名称。 将此设置与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 |
    | SERVICEBUS_CONNECTION | myServiceBusConnectionString | 为服务总线的连接字符串创建此应用设置。 此存储连接字符串来自[获取服务总线连接字符串](#get-a-service-bus-connection-string)部分。|
    | **WEBSITE_CONTENTOVERVNET** | 1 | 创建此应用设置。 将存储帐户限制在虚拟网络中时，值 1 允许函数应用进行缩放。 |
    | WEBSITE_DNS_SERVER | 168.63.129.16 | 创建此应用设置。 应用与虚拟网络集成时，它将使用与虚拟网络相同的 DNS 服务器。 函数应用需要此设置才能与 Azure DNS 专用区域配合使用。 使用专用终结点时需要此设置。 此设置和 WEBSITE_VNET_ROUTE_ALL 会将应用中的所有出站呼叫发送到虚拟网络。 |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | 创建此应用设置。 应用与虚拟网络集成时，它将使用与虚拟网络相同的 DNS 服务器。 函数应用需要此设置才能与 Azure DNS 专用区域配合使用。 使用专用终结点时需要此设置。 此设置和 WEBSITE_DNS_SERVER 会将应用中的所有出站呼叫发送到虚拟网络。 |

1. 在“配置”视图中，选择“函数运行时设置”选项卡。

1. 将“运行时规模监视”设置为“启用”。 再选择“保存”。 运行时驱动的缩放使你能够将非 HTTP 触发器函数连接到在虚拟网络中运行的各项服务。

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="介绍如何为 Azure Functions 启用运行时驱动的缩放的屏幕截图。":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>部署服务总线触发器和 HTTP 触发器

1. 在 GitHub 中，转到以下示例存储库。 它包含一个函数应用，还包含两个函数、一个 HTTP 触发器和一个服务总线队列触发器。

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. 在页面顶部选择“分支”，以在自己的 GitHub 帐户或组织中创建此存储库的分支。

1. 在函数应用中，在左侧菜单中选择“部署中心”。 然后选择“设置”。

1. 在“设置”选项卡上，使用下表中显示的部署设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **Source** | GitHub | 应该已为步骤 2 中的示例代码创建了 GitHub 存储库。 | 
    | 组织  | myOrganization | 要签入存储库的组织。 它通常是你的帐户。 |
    | 存储库 | functions-vnet-tutorial | 从 https://github.com/Azure-Samples/functions-vnet-tutorial 分支的存储库。 |
    | 分支 | 主要 | 创建的存储库的主分支。 |
    | **运行时堆栈** | .NET | 示例代码是用 C# 编写的。 |
    | **版本** | .NET Core 3.1 | 运行时版本。 |

1. 选择“保存”。 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="介绍如何通过门户部署 Azure Functions 代码的屏幕截图。":::

1. 初始部署可能需要几分钟。 成功部署应用后，在“日志”选项卡上，可以看到“成功(活动)”状态消息。 如果需要，请刷新页面。

恭喜！ 你已成功部署了示例函数应用。

## <a name="lock-down-your-function-app"></a>锁定函数应用

现在，创建专用终结点以锁定函数应用。 此专用终结点将使用专用 IP 地址将函数应用以私密方式安全地连接到虚拟网络。 

有关详细信息，请参阅[专用终结点文档](../private-link/private-endpoint-overview.md)。

1. 在函数应用中，在左侧菜单中选择“网络”。

1. 在“专用终结点连接”中，选择“配置专用终结点连接” 。

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="介绍如何导航到函数应用专用终结点的屏幕截图。":::

1. 选择 **添加** 。

1. 在打开的窗格中，使用以下专用终结点设置：

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="屏幕截图显示如何创建函数应用专用终结点。终结点名称为 functionapp-endpoint，订阅为“Private Test Sub CACHHAI”，虚拟网络为 MyVirtualNet-tutorial，子网为默认值。":::

1. 选择“确定”以添加专用终结点。 
 
恭喜！ 通过添加专用终结点，你成功保护了函数应用、服务总线和存储帐户的安全！

### <a name="test-your-locked-down-function-app"></a>测试锁定的函数应用

1. 在函数应用中，在左侧菜单中选择“函数”。

1. 选择“ServiceBusQueueTrigger”。

1. 在左侧菜单中选择“监视”。 
 
你会发现你无法监视应用。 你的浏览器无法访问虚拟网络，因此无法直接访问虚拟网络中的资源。 
 
下面是使用 Application Insights 监视函数的另一种方法：

1. 在函数应用中，在左侧菜单中选择“Application Insights”。 然后选择“查看 Application Insights 数据”。

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="介绍如何查看函数应用的应用程序见解的屏幕截图。":::

1. 在左侧菜单中选择“实时指标”。

1. 打开新选项卡，在服务总线中的左侧菜单中选择“队列”。

1. 选择队列。

1. 在左侧菜单中，选择“Service Bus Explorer”。 在“发送”下，为“内容类型”选择“文本/无格式”。 然后输入一条消息。 

1. 选择“发送”发送该消息。

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="展示如何使用门户发送服务总线消息的屏幕截图。":::

1. 在“实时指标”选项卡上，应该会看到服务总线队列触发器已触发。 如果尚未触发，请通过“Service Bus Explorer”重新发送消息。

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="介绍如何使用函数应用的实时指标来查看消息的屏幕截图。":::

恭喜！ 你已成功使用专用终结点测试了函数应用设置。

## <a name="understand-private-dns-zones"></a>了解专用 DNS 区域
你已使用专用终结点连接到 Azure 资源。 你正在连接到专用 IP 地址而不是公共终结点。 现有 Azure 服务配置为使用现有 DNS 连接到公共终结点。 必须覆盖 DNS 配置才能连接到专用终结点。

将为使用专用终结点配置的每个 Azure 资源创建专用 DNS 区域。 将为与专用终结点关联的每个专用 IP 地址创建一个 DNS 记录。

在本教程中创建了以下 DNS 区域：

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了高级函数应用、存储帐户和服务总线。 你保护了专用终结点后面的所有资源。 

使用以下链接了解更多 Azure Functions 网络选项和专用终结点：

- [Azure Functions 中的网络选项](./functions-networking-options.md)
- [Azure Functions 高级计划](./functions-premium-plan.md)
- [服务总线专用终结点](../service-bus-messaging/private-link-service.md)
- [Azure 存储专用终结点](../storage/common/storage-private-endpoints.md)
