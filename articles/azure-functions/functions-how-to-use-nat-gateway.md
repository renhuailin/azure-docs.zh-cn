---
title: 使用 Azure 虚拟网络 NAT 网关控制 Azure Functions 出站 IP
description: 介绍如何为连接到 Azure 虚拟网络的函数配置 NAT 的分步教程
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 9b91ccd725fbd6ae1e3a974ecb70b4c01201adb4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432259"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>教程：使用 Azure 虚拟网络 NAT 网关控制 Azure Functions 出站 IP

虚拟网络地址转换 (NAT) 简化了虚拟网络仅限出站的 Internet 连接。 在子网中配置后，所有出站连接将使用指定的静态公共 IP 地址。 NAT 对于需要使用第三方服务（使用 IP 地址允许列表作为安全措施）的 Azure Functions 或 Web 应用非常有用。 若要了解详细信息，请参阅[什么是虚拟网络 NAT？](../virtual-network/nat-gateway/nat-overview.md)。

本教程介绍如何使用虚拟网络 NAT 从 HTTP 触发的函数路由出站流量。 此函数可让你检查其自身的出站 IP 地址。 在本教程中，你将：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建高级计划函数应用
> * 创建公共 IP 地址
> * 创建 NAT 网关
> * 配置函数应用以通过 NAT 网关路由出站流量

## <a name="topology"></a>拓扑

下图显示了你创建的解决方案的体系结构：

![NAT 网关集成的 UI](./media/functions-how-to-use-nat-gateway/topology.png)

高级计划中运行的函数与 Azure 应用服务中的 Web 应用具有相同的承载功能，其中包括 VNet 集成功能。 若要了解有关 VNet 集成的详细信息（包括故障排除和高级配置），请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>先决条件

若要学习本教程，必须了解 IP 寻址和子网划分。 可以从[这篇介绍了寻址和子网划分基础知识的文章](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)入手。 网上还有其他许多相关文章和视频。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果已完成[将 Functions 与 Azure 虚拟网络集成](./functions-create-vnet.md)教程，可以跳至[创建 HTTP 触发器函数](#create-function)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在 Azure 门户菜单中，选择“创建资源”。 在 Azure 市场中，选择“网络” > “虚拟网络” 。

1. 在“创建虚拟网络”中，按照下表中显示的说明输入或选择设置：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | 名称 | 输入 *myResourceGroup-vnet*。 |
    | 位置 | 选择“美国东部”。|

1. 选择 **下一步：IP 地址**，并输入 *10.10.0.0/16* 作为 **IPv4 地址空间**。

1. 选择“添加子网”，输入 *Tutorial-Net* 作为“子网名称”，输入 *10.10.1.0/24* 作为“子网地址范围”  。

    ![用于创建 VNet 的“IP 地址”选项卡](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. 选择“添加”，然后选择“查看 + 创建”。  将其余的设置保留默认值，然后选择“创建”。

1. 在“创建虚拟网络”中，选择“创建”。 

接下来，在[高级计划](functions-premium-plan.md)中创建一个函数应用。 此计划提供无服务器规模，同时支持虚拟网络集成。

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级计划中创建函数应用。

本教程介绍如何在[高级计划](functions-premium-plan.md)中创建函数应用。 使用[专用（应用服务）计划](dedicated-plan.md)时也可以使用相同的功能。

> [!NOTE]  
> 为在本教程中获得最佳体验，请选择“.NET”作为运行时堆栈，选择“Windows”作为操作系统。 此外，在虚拟网络所在的同一区域中创建函数应用。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>将函数应用连接到虚拟网络

现在可以将函数应用连接到虚拟网络。

1. 在函数应用中，选择左侧菜单中的“网络”，然后在“VNet 集成”下选择“单击此处进行配置”  。

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="在函数应用中选择“网络”":::

1. 在“VNET 集成”页上，选择“添加 VNet” 。

1. 在“网络功能状态”中，使用插图下面的表格中的设置：

    ![定义函数应用虚拟网络](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **虚拟网络** | MyResourceGroup-vnet | 此虚拟网络就是你先前创建的网络。 |
    | **子网** | 创建新子网 | 在虚拟网络中创建一个子网供函数应用使用。 必须将 VNet 集成配置为使用空子网。 |
    | **子网名称** | Function-Net | 新子网的名称。 |
    | **虚拟网络地址块** | 10.10.0.0/16 | 只应定义一个地址块。 |
    | **子网地址块** | 10.10.2.0/24   | 子网大小限制高级计划函数应用可以横向扩展到的实例总数。 此示例使用具有 254 个可用主机地址的 `/24` 子网。 此子网进行了超量预配，但易于计算。 |

1. 选择“确定”以添加子网。 关闭“VNet 集成”和“网络功能状态”页，返回到函数应用页 。

函数应用现在可以访问虚拟网络。 接下来，将 HTTP 触发的函数添加到函数应用中。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>创建 HTTP 触发器函数

1. 从“Functions”窗口的左侧菜单中选择“Functions”，然后从顶部菜单中选择“添加”。 
 
1. 在“新建函数”窗口中，选择“Http 触发器”并接受 **新函数** 的默认名称，或输入新名称 。 

1. 在“代码 + 测试”中，将模板生成的 C# 脚本 (.csx) 代码替换为以下代码： 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    此代码将调用一个外部网站，该网站返回调用方的 IP 地址，在本例中为此函数。 此方法可让你轻松确定函数应用使用的出站 IP 地址。

现已准备好运行函数并检查当前的出站 IP。

## <a name="verify-current-outbound-ips"></a>验证当前出站 IP

现在可以运行该函数。 但首先请在门户中查看函数应用正在使用哪些出站 IP。  

1. 在函数应用中，选择“属性”并查看“出站 IP 地址”字段 。

    ![查看函数应用的出站 IP 地址](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. 现在，返回到你的 HTTP 触发器函数，选择“代码 + 测试”，然后选择“测试/运行” 。

    ![测试函数](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. 选择“运行”以执行该函数，然后切换到“输出” 。 

    ![测试函数输出](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. 验证 HTTP 响应正文中的 IP 地址是否为先前查看的出站 IP 地址中的值之一。

现在，可以创建一个公共 IP，并使用 NAT 网关来修改此出站 IP 地址。

## <a name="create-public-ip"></a>创建公共 IP

1. 在你的资源组中选择“添加”，在 Azure 市场中搜索“公共 IP 地址”，然后选择“创建”  。 使用插图下面的表格中的设置：

    ![创建公共 IP 地址](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | 设置      | 建议的值  |
    | ------------ | ---------------- |
    | **IP 版本** | IPv4 |
    | **SKU** | Standard |
    | **层** | 区域 |
    | **名称** | 出站 IP |
    | **订阅** | 确保已显示你的订阅 | 
    | **资源组** | myResourceGroup（或分配给你的资源组的名称） |
    | **位置** | 美国东部（或已分配给其他资源的位置） |
    | **可用性区域** | 无区域 |

1. 选择“创建”以提交部署。

1. 部署完成后，导航到新建的“公共 IP 地址”资源，并查看“概述”中的 IP 地址。

    ![查看公共 IP 地址](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>创建 NAT 网关

现在，让我们创建 NAT 网关。 开始学习[前面的虚拟网络教程](functions-create-vnet.md)时，`Function-Net` 是该教程中建议的子网名称，`MyResourceGroup-vnet` 是该教程中建议的虚拟网络名称。

1. 在你的资源组中选择“添加”，在 Azure 市场中搜索“NAT 网关”，然后选择“创建”  。 使用插图下面的表格中的设置来填充“基本信息”选项卡：

    ![创建 NAT 网关](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | 设置      | 建议的值  |
    | ------------ | ---------------- |  
    | **订阅** | 你的订阅 | 
    | **资源组** | myResourceGroup（或分配给你的资源组的名称） |
    | **NAT 网关名称** | myNatGateway |
    | **区域** | 美国东部（或已分配给其他资源的位置） |
    | **可用性区域** | 无 |

1. 选择“下一步: 出站 IP”。 在“公共 IP 地址”字段中，选择前面创建的公共 IP 地址。 将“公共 IP 前缀”保持未选中状态。

1. 选择“下一步: 子网”。 在“虚拟网络”字段中选择“myResourceGroup-vnet”资源，并选择“Function-Net”子网。

    ![选择子网](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. 依次选择“查看 + 创建”和“创建”来提交部署 。

部署完成后，NAT 网关便可以将流量从你的函数应用子网路由到 Internet。

## <a name="update-function-configuration"></a>更新函数配置

现在，必须添加设置为 `1` 值的应用程序设置 `WEBSITE_VNET_ROUTE_ALL`。  此设置会强制出站流量通过虚拟网络和关联的 NAT 网关。 如果没有此设置，Internet 流量不会通过集成虚拟网络进行路由，你会看到相同的出站 IP。 

1. 在 Azure 门户中导航到你的函数应用，从左侧菜单中选择“配置”。

1. 在“应用程序设置”下，选择“+ 新建应用程序设置”，然后使用以下值填充字段 ：

    |字段名称  |值 |
    |---|---|
    |**名称**    |WEBSITE_VNET_ROUTE_ALL|
    |**值**   |1|

1. 选择“确定”以关闭“新建应用程序设置”对话框。

1. 选择“保存”，然后选择“继续”以保存设置 。

函数应用现已配置为通过其关联的虚拟网络路由流量。

## <a name="verify-new-outbound-ips"></a>验证新的出站 IP

重复[上述步骤](#verify-current-outbound-ips)再次运行该函数。 现在，在函数输出中应会看到你在 NAT 中配置的出站 IP 地址。

## <a name="clean-up-resources"></a>清理资源

你已创建了完成本教程所需的资源。 你需要为这些资源付费，具体取决于你的[帐户状态](https://azure.microsoft.com/account/)和[服务定价](https://azure.microsoft.com/pricing/)。 为了避免产生额外的成本，请在不再需要这些资源时将其删除。 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 网络选项](functions-networking-options.md)
