---
title: 使用专用终结点将 Azure Functions 与虚拟网络集成
description: 分步教程演示如何将函数连接到 Azure 虚拟网络，以及如何使用专用终结点将其锁定
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200200"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>教程：使用专用终结点将 Azure Functions 与 Azure 虚拟网络集成

本教程介绍如何使用 Azure Functions 通过专用终结点连接到 Azure 虚拟网络中的资源。 你将创建一个函数，该函数具有在使用服务总线队列触发器的虚拟网络后面锁定的存储帐户。

> [!div class="checklist"]
> * 在高级计划中创建函数应用
> *  (Service Bus、存储帐户、虚拟网络) 创建 Azure 资源
> * 锁定专用终结点后面的存储帐户
> * 锁定专用终结点后面的服务总线
> * 使用服务总线和 HTTP 触发器部署函数应用。
> * 锁定私有终结点后面的函数应用
> * 测试以查看函数应用在虚拟网络后是否安全
> * 清理资源

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级计划中创建 function app

首先，在 [高级计划] 中创建 .net 函数应用，因为本教程将使用 c #。 Windows 还支持其他语言。 此计划提供无服务器规模，同时支持虚拟网络集成。

1. 在 Azure 门户菜单或“主页”页中，选择“创建资源” 。

1. 在 **“新建”** 页面，选择 **“计算”**  >  **“函数应用”** 。

1. 在“基本信息”页面上，按照下列所述使用函数应用设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **订阅** | 你的订阅 | 要在其下创建此新函数应用的订阅。 |
    | **[资源组](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | 要在其中创建 Function App 的新资源组的名称。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。  |
    |<bpt id="p1">**</bpt>Publish<ept id="p1">**</ept>| 代码 | 用于发布代码文件或 Docker 容器的选项。 |
    | **运行时堆栈** | .NET | 本教程使用 .NET |
    |**区域**| 首选区域 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

1. 在完成时选择“下一步:托管”。 在“托管”页面上，输入以下设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | [存储帐户](../storage/common/storage-account-create.md) |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](./storage-considerations.md#storage-account-requirements)。 |
    |**操作系统**| Windows | 本教程使用 Windows |
    | **[计划](./functions-scale.md)** | Premium | 定义如何将资源分配给 Function App 的托管计划。 选择“高级”。 默认创建一个新的应用服务计划。 默认的“SKU 和大小”为 EP1，其中 EP 表示“弹性高级”(elastic premium) 。 要了解详细信息，请参阅[高级 SKU 的列表](./functions-premium-plan.md#available-instance-skus)。<br/>在高级计划上运行 JavaScript 函数时，应选择 vCPU 数更少的实例。 有关详细信息，请参阅[选择单核心高级计划](./functions-reference-node.md#considerations-for-javascript-functions)。  |

1. 在完成时选择“下一步:监视”。 在“监视”页面上，输入以下设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 展开此设置即可更改“新建资源名称”，或者在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)选择其他位置来存储你的数据 。 |

1. 选择“查看 + 创建”，以便查看应用配置选择。

1. 在“查看 + 创建”页上查看设置，然后选择“创建”来预配并部署函数应用 。

1. 选择门户右上角的“通知”图标，留意是否显示了“部署成功”消息。 

1. 选择“转到资源”，查看新的函数应用。 还可选择“固定到仪表板”。 固定可以更轻松地从仪表板返回此函数应用资源。

1. 祝贺！ 已成功创建高级函数应用！

## <a name="create-azure-resources"></a>创建 Azure 资源

### <a name="create-a-storage-account"></a>创建存储帐户

虚拟网络需要一个独立的存储帐户，该帐户是最初创建函数应用时创建的帐户。

1. 在 Azure 门户菜单或“主页”页中，选择“创建资源” 。

1. 在 "新建" 页上，搜索 "**存储帐户**" 并选择 "**创建**"

1. 在 " **基本** 信息" 选项卡上，设置下表中指定的设置。 Rest 可以保留为默认值：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用 function app 创建的资源组。 |
    | 名称 - ** | mysecurestorage| 专用终结点将应用到的存储帐户的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择在其中创建函数应用的区域。 |

1. 选择“查看 + 创建”。 验证完成后，选择“创建”。

### <a name="create-a-service-bus"></a>创建服务总线

1. 在 Azure 门户菜单或“主页”页中，选择“创建资源” 。

1. 在 "新建" 页上，搜索 " **服务总线** "，然后选择 " **创建**"。

1. 在 " **基本** 信息" 选项卡上，设置下表中指定的设置。 Rest 可以保留为默认值：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 |
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用 function app 创建的资源组。 |
    | 名称 - ** | myServiceBus| 专用终结点将应用到的服务总线的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择在其中创建函数应用的区域。 |
    | **定价层** | 高级 | 选择此层以将专用终结点用于服务总线。 |

1. 选择“查看 + 创建”。 验证完成后，选择“创建”。

### <a name="create-a-virtual-network"></a>创建虚拟网络

本教程中的 Azure 资源将与或集成在一个虚拟网络中。 你将使用专用终结点来阻止虚拟网络包含的网络流量。

本教程将创建两个子网：
- **默认**：专用终结点的子网。 专用 IP 地址是从此子网中指定的。
- **函数**：用于 Azure Functions 虚拟网络集成的子网。 此子网将委托给 function app。

现在，创建函数应用集成到的虚拟网络。

1. 从 "Azure 门户" 菜单或 "主页" 中，选择 " **创建资源**"。

1. 在 "新建" 页上，搜索 " **虚拟网络** "，然后选择 " **创建**"。

1. 在 " **基本** 信息" 选项卡上，使用以下指定的虚拟网络设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用 function app 创建的资源组。 |
    | 名称 - ** | myVirtualNet| 函数应用将连接到的虚拟网络的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择在其中创建函数应用的区域。 |

1. 在 " **IP 地址** " 选项卡上，选择 " **添加子网**"。 添加子网时，请使用下面指定的设置：

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="&quot;创建虚拟网络配置&quot; 视图的屏幕截图。":::

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **子网名称** | functions | 函数应用将连接到的子网的名称。 | 
    | **子网地址范围** | 10.0.1.0/24 | 请注意，上图中的 IPv4 地址空间为 10.0.0.0/16。 如果上述 10.1.0.0/16，建议的 *子网地址范围* 将是 10.1.1.0/24。 |

1. 选择“查看 + 创建”。 验证完成后，选择“创建”。

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>用专用终结点锁定存储帐户

Azure 专用终结点用于连接到使用专用 IP 地址的特定 Azure 资源。 此连接确保网络流量保留在所选的虚拟网络中，并且访问仅适用于特定资源。 现在，请通过存储帐户创建 Azure 文件存储和 Azure Blob 存储的专用终结点。

1. 在新的存储帐户中，从左侧菜单中选择 " **网络** "。

1. 选择 " **专用终结点连接** " 选项卡，然后选择 " **专用终结点**"。

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="介绍如何导航来为存储帐户创建专用终结点的屏幕截图。":::

1. 在 " **基本** 信息" 选项卡上，使用下面指定的专用终结点设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用 function app 创建的资源组。 | |
    | 名称 - ** | 文件-终结点 | 存储帐户中文件的专用终结点的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择你在中创建存储帐户的区域。 |

1. 在 " **资源** " 选项卡上，使用下面指定的专用终结点设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.Storage/storageAccounts | 这是存储帐户的资源类型。 |
    | **资源** | mysecurestorage | 刚创建的存储帐户 |
    | **目标子资源** | 文件 | 此专用终结点将用于存储帐户中的文件。 |

1. 在 " **配置** " 选项卡上，选择 "子网" 设置的 **默认值** 。

1. 选择“查看 + 创建”。 验证完成后，选择“创建”。 虚拟网络中的资源现可与存储文件通信。

1. 为 blob 创建另一个专用终结点。 对于 " **资源** " 选项卡，请使用以下设置。 对于所有其他设置，请使用刚才遵循的文件专用终结点创建步骤中的相同设置。

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.Storage/storageAccounts | 这是存储帐户的资源类型。 |
    | **资源** | mysecurestorage | 刚创建的存储帐户 |
    | **目标子资源** | blob | 此专用终结点将用于存储帐户中的 blob。 |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>使用专用终结点锁定服务总线

现在，请为 Azure 服务总线创建专用终结点。

1. 在新的服务总线中，在左侧菜单中选择 " **网络** "。

1. 选择 " **专用终结点连接** " 选项卡，然后选择 " **专用终结点**"。

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="介绍如何导航到服务总线专用终结点的屏幕截图。":::

1. 在 " **基本** 信息" 选项卡上，使用下面指定的专用终结点设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择用 function app 创建的资源组。 |
    | 名称 - ** | sb-终结点 | 存储帐户中文件的专用终结点的名称。 |
    | **[区域](https://azure.microsoft.com/regions/)** | myFunctionRegion | 选择你在中创建存储帐户的区域。 |

1. 在 " **资源** " 选项卡上，使用下面指定的专用终结点设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 | 
    | **资源类型**  | Microsoft.ServiceBus/namespaces | 这是服务总线的资源类型。 |
    | **资源** | myServiceBus | 本教程前面创建的服务总线。 |
    | **目标子资源** | namespace | 此专用终结点将用于来自服务总线的命名空间。 |

1. 在 " **配置** " 选项卡上，选择 "子网" 设置的 **默认值** 。

1. 选择“查看 + 创建”。 验证完成后，选择“创建”。 虚拟网络中的资源现可与服务总线通信。

## <a name="create-a-file-share"></a>创建文件共享

1. 在创建的存储帐户中，在左侧菜单中选择 " **文件共享** "。

1. 选择 " **+ 文件共享**"。 出于本教程的目的， **请提供文件** 共享的名称。

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="介绍如何在存储帐户中创建文件共享的屏幕截图。":::

## <a name="get-storage-account-connection-string"></a>获取存储帐户连接字符串

1. 在创建的存储帐户中，在左侧菜单中选择 " **访问密钥** "。

1. 选择 " **显示密钥**"。 复制 key1 的连接字符串，并保存它。 稍后在配置应用程序设置时将需要此连接字符串。

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="如何获取存储帐户连接字符串的屏幕截图。":::

## <a name="create-a-queue"></a>创建队列

这将是 Azure Functions Service Bus 触发器从中获取事件的队列。

1. 在服务总线中，在左侧菜单中选择 " **队列** "。

1. 选择“共享访问策略”。 出于本教程的目的，请将 **queue** 作为队列的名称提供。

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="如何创建服务总线队列的屏幕截图。":::

## <a name="get-service-bus-connection-string"></a>获取服务总线连接字符串

1. 在服务总线中，在左侧菜单中选择 " **共享访问策略** "。

1. 选择“RootManageSharedAccessKey”。 复制 **主连接字符串**，并保存它。 稍后在配置应用程序设置时将需要此连接字符串。

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="如何获取服务总线连接字符串的屏幕截图。":::

## <a name="integrate-function-app-with-your-virtual-network"></a>将 function app 与你的虚拟网络集成

若要将 function app 用于虚拟网络，你需要将其加入子网。 我们使用特定子网进行 Azure Functions 虚拟网络集成，使用本教程中创建的所有其他专用终结点的默认子网。

1. 在函数应用中，选择左侧菜单中的 " **网络** "。

1. 选择 **"单击此处以** 在 VNet 集成下配置"。

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="介绍如何导航到虚拟网络集成的屏幕截图。":::

1. 选择 **添加 VNet**

1. 在 " **虚拟网络**" 下打开的边栏选项卡中，选择前面创建的虚拟网络。

1. 选择我们之前创建的 **子网** **。** 函数应用现已与虚拟网络集成！

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="如何将 function app 连接到子网的屏幕截图。":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>为私有终结点配置 function app 设置

1. 在 function app 中，从左侧菜单中选择 " **配置** "。

1. 若要将 function app 用于虚拟网络，需要更新以下应用设置。 根据需要，选择 " **+ 新建应用程序设置** " 或 "应用设置" 表最右侧列中的 " **编辑** "。 完成后，选择“保存”。

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="如何配置专用终结点的函数应用设置的屏幕截图。":::

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 你创建的存储帐户的连接字符串。 这是 [获取存储帐户连接字符串](#get-storage-account-connection-string)的存储连接字符串。 更改此设置后，在运行时，函数应用将使用安全存储帐户进行正常操作。 | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 你创建的存储帐户的连接字符串。 更改此设置后，函数应用现在将使用用于 Azure 文件的安全存储帐户，这些文件是在部署时使用的。 |
    | **WEBSITE_CONTENTSHARE** | files | 在存储帐户中创建的文件共享的名称。 此应用设置与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | 为服务总线的连接字符串创建应用设置。 这是来自 [获取服务总线连接字符串](#get-service-bus-connection-string)的存储连接字符串。|
    | **WEBSITE_CONTENTOVERVNET** | 1 | 创建此应用设置。 如果将存储帐户限制在虚拟网络中，则值为1时，可以使用值1缩放函数应用。 将存储帐户限制于一个虚拟网络时，应启用此设置。 |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | 创建此应用设置。 应用与虚拟网络集成后，将使用与虚拟网络相同的 DNS 服务器。 这是所需的两个设置之一：函数应用可与 Azure DNS 专用区域一起使用，并且在使用专用终结点时是必需的。 这些设置会将应用中的所有出站呼叫发送到虚拟网络。 |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | 创建此应用设置。 应用与虚拟网络集成后，将使用与虚拟网络相同的 DNS 服务器。 这是所需的两个设置之一：函数应用可与 Azure DNS 专用区域一起使用，并且在使用专用终结点时是必需的。 这些设置会将应用中的所有出站呼叫发送到虚拟网络。 |

1. 在 " **配置** " 视图中，选择 " **函数运行时设置** " 选项卡。

1. 将 **运行时缩放监视** 设置为 **"开**"，然后选择 " **保存**"。 运行时驱动的缩放使你能够将非 HTTP 触发器函数连接到虚拟网络中运行的服务。

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="如何对 Azure Functions 启用运行时驱动缩放的屏幕截图。":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>将服务总线触发器和 http 触发器部署到 function app

1. 在 GitHub 中，浏览到以下示例存储库，其中包含具有两个函数的函数应用： HTTP 触发器和服务总线队列触发器。

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. 在页面顶部，选择 " **分叉** " 按钮，在你自己的 GitHub 帐户或组织中创建此存储库的分支。

1. 在 function app 中，从左侧菜单中选择 " **部署中心** "。 然后选择 " **设置**"。

1. 在 " **设置** " 选项卡上，使用下面指定的部署设置：

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **Source** | GitHub | 你应已使用步骤2中的示例代码创建了一个 GitHub 存储库。 | 
    | **组织**  | myOrganization | 这是您的存储库的组织，通常是您的帐户。 |
    | **存储库** | myRepo | 用示例代码创建的存储库。 |
    | 分支 | 主要 | 这是刚创建的存储库，因此请使用主分支。 |
    | **运行时堆栈** | .NET | 示例代码以 c # 编写。 |

1. 选择“保存”。 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="介绍如何通过门户部署 Azure Functions 代码的屏幕截图。":::

1. 初始部署可能需要几分钟的时间。 成功部署应用后，会在 "**日志**" 选项卡中看到 "**成功" (活动)** 状态消息。 如果需要，请刷新页面。 

1. 祝贺！ 你已成功部署了示例函数应用。

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>使用私有终结点锁定函数应用

现在，为 function app 创建专用终结点。 此专用终结点将使用专用 IP 地址将 function app 私下和安全连接到虚拟网络。 有关专用终结点的详细信息，请参阅 [专用终结点文档](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)。

1. 在函数应用中，选择左侧菜单中的 " **网络** "。

1. 选择 " **单击此处" 可** 在 "专用终结点连接" 下进行配置。

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="介绍如何导航到 Function App 专用终结点的屏幕截图。":::

1. 选择 **添加** 。

1. 在打开的菜单中，使用下面指定的专用终结点设置：

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="如何创建 Function App 专用终结点的屏幕截图。":::

1. 选择 **"确定"** 以添加专用终结点。 祝贺！ 已成功保护函数应用、服务总线和具有专用终结点的存储帐户！

### <a name="test-your-locked-down-function-app"></a>测试锁定的函数应用

1. 在 function app 中，从左侧菜单中选择 " **函数** "。

1. 选择 **ServiceBusQueueTrigger**。

1. 从左侧菜单中选择 " **监视器**"。 你将看到无法监视你的应用程序。 这是因为你的浏览器无权访问虚拟网络，因此它不能直接访问虚拟网络中的资源。 现在，我们将演示另一种方法，通过该方法还可以监视函数，Application Insights。

1. 在 function app 中，从左侧菜单中选择 " **Application Insights** "，然后选择 " **查看 Application Insights 数据**"。

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="如何查看 Function App 的 application insights 的屏幕截图。":::

1. 从左侧菜单中选择 " **实时指标** "。

1. 打开新选项卡。在服务总线中，从左侧菜单中选择 " **队列** "。

1. 选择队列。

1. 从左侧菜单中选择 " **服务总线资源管理器** "。 在 " **发送**" 下，选择 " **文本"/"纯文本** " 作为 " **内容类型** "，然后输入消息。 

1. 选择 " **发送** " 以发送消息。

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="使用门户发送服务总线消息的屏幕截图。":::

1. 在打开 **实时指标** 的选项卡上，你应该会看到你的服务总线队列触发器已触发。 如果没有，请从 **服务总线资源管理器** 重新发送消息

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="介绍如何使用函数应用的实时指标来查看消息的屏幕截图。":::

1. 祝贺！ 已成功测试了通过专用终结点设置的函数应用！

### <a name="private-dns-zones"></a>专用 DNS 区域
使用专用终结点连接到 Azure 资源意味着连接到专用 IP 地址而不是公共终结点。 现有的 Azure 服务配置为使用现有 DNS 连接到公共终结点。 需要重写 DNS 配置，以连接到专用终结点。

已为使用专用终结点配置的每个 Azure 资源创建专用 DNS 区域。 将为与专用终结点关联的每个专用 IP 地址创建 DNS A 记录。

在本教程中创建了以下 DNS 区域：

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了高级函数应用、存储帐户和服务总线，并且你在私有终结点后面保护了它们！ 详细了解下面提供的各种网络功能：

> [!div class="nextstepaction"]
> [详细了解 Functions 中的网络选项](./functions-networking-options.md)

[高级计划]: functions-premium-plan.md
