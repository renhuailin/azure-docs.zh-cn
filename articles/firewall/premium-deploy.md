---
title: 部署和配置 Azure 防火墙高级版
description: 了解如何部署和配置 Azure 防火墙高级版。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: 9358852dd8b16f495d5123b59ea3ae0f4c29b00f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441141"
---
# <a name="deploy-and-configure-azure-firewall-premium"></a>部署和配置 Azure 防火墙高级版


 Azure 防火墙高级版是新一代防火墙，具备了高度敏感环境和受管制环境所需的功能。 它包括以下功能：

- TLS 检查 - 解密出站流量、处理数据，然后加密数据并将数据发送到目的地。
- IDPS - 网络入侵检测和防护系统 (IDPS) 使你可以监视网络活动是否出现恶意活动，记录有关此活动的信息，予以报告，选择性地尝试阻止。
- URL 筛选 - 将 Azure 防火墙的 FQDN 筛选功能扩展到考虑整个 URL。 例如，`www.contoso.com/a/c`，而非 `www.contoso.com`。
- Web 类别 - 管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。

有关详细信息，请参阅 [Azure 防火墙高级版功能](premium-features.md)。

你可使用模板来部署具有一个中心 VNet (10.0.0.0/16) 和三个子网的测试环境：
- 辅助角色子网 (10.0.10.0/24)
- Azure Bastion 子网 (10.0.20.0/24)
- 防火墙子网 (10.0.100.0/24)

为简单起见，此测试环境使用了一个中心 VNet。 对于生产环境，具有对等互连 Vnet 的[中心辐射型拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)更为常见。

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="中心 VNet 拓扑":::

辅助角色虚拟机是通过防火墙发送 HTTP/S 请求的客户端。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="deploy-the-infrastructure"></a>部署基础结构

模板将为 Azure 防护墙高级版部署一个完整的测试环境，并对其启用 IDPS、TLS 检查、URL 筛选和 Web 类别：

- 包含预定义设置的新 Azure 防火墙高级版和防火墙策略，以便轻松验证其核心功能（IDPS、TLS 检查、URL 筛选和 Web 类别）
- 部署所有依赖项，包括 Key Vault 和托管标识。 在生产环境中，这些资源可能已创建，在同一模板中不需要。
- 生成自签名根 CA 并将其部署到生成的 Key Vault 上
-  生成派生的中间 CA 并将其部署到 Windows 测试虚拟机 (WorkerVM) 上
- 还会部署一个堡垒主机 (BastionHost)，可用于连接到 Windows 测试计算机 (WorkerVM)



[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>测试防火墙

现在，可以测试 IDPS、TLS 检查、Web 筛选和 Web 类别。

### <a name="add-firewall-diagnostics-settings"></a>添加防火墙诊断设置

要收集防火墙日志，需要添加诊断设置以收集防火墙日志。

1. 选择“DemoFirewall”，并在“监视”下选择“诊断设置”。
2. 选择“添加诊断设置”。
3. 对于“诊断设置名称”，请键入“fw-diag”。
4. 在“日志”下，选择“AzureFirewallApplicationRule”和“AzureFirewallNetworkRule”。
5. 在“目标详细信息”下选择“发送到 Log Analytics 工作区”。 
6. 选择“保存”。

### <a name="idps-tests"></a>IDPS 测试

要测试 IDPS，需要为自己的内部 Web 服务器部署适当的服务器证书。 有关 Azure 防火墙高级版证书要求的详细信息，请参阅 [Azure 防火墙高级版证书](premium-certificates.md)。

可以使用 `curl` 来控制各种 HTTP 头并模拟恶意流量。

#### <a name="to-test-idps-for-http-traffic"></a>测试用于 HTTP 流量的 IDPS：

1. 在 WorkerVM 虚拟机上，打开管理员命令提示符窗口。
2. 在命令提示符处键入以下命令：

   `curl -A "BlackSun" <your web server address>`
3. 你将看到自己的 Web 服务器响应。
4. 转到 Azure 门户上的防火墙网络规则日志，查找类似以下消息的警报：

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="警报消息":::

   > [!NOTE]
   > 可能需要一段时间日志中才会开始显示数据。 至少等待 20 分钟，以便日志开始显示数据。
5. 为签名 2008983 添加签名规则：

   1. 选择“DemoFirewallPolicy”，然后在“设置”下选择“IDPS”。
   1. 选择“签名规则”选项卡。
   1. 在“签名 ID”下，在打开的文本框中键入“2008983”。
   1. 在“模式”下，选择“拒绝”。
   1. 选择“保存”。
   1. 等待部署完成，然后再继续。



6. 在 WorkerVM 上，再次运行 `curl` 命令：

   `curl -A "BlackSun" <your web server address>`

   由于 HTTP 请求现已被防火墙阻止，因此在连接超时过期后，你将看到以下输出：

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. 转到 Azure 门户中的监视日志，查找被阻止的请求的消息。
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>测试用于 HTTPS 流量的 IDPS

使用 HTTPS（而不是 HTTP）重复这些 curl 测试。 例如：

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

你应会看到与 HTTP 测试相同的结果。

### <a name="tls-inspection-with-url-filtering"></a>TLS 检查和 URL 筛选

请按照以下步骤测试 TLS 检查和 URL 筛选。

1. 编辑防火墙策略的应用规则，并向 `AllowWeb` 规则集合中添加一项名为 `AllowURL` 的新规则。 配置目标 URL `www.nytimes.com/section/world`、源 IP 地址 **\* *、目标类型“URL”***，选择“TLS 检查”和协议“http、https”。

3. 部署完成后，在 WorkerVM 上打开浏览器，转到 `https://www.nytimes.com/section/world`，然后在浏览器中验证 HTML 响应是否按预期方式显示。
4. 在 Azure 门户中，可以在应用规则监视日志中查看完整 URL：

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="显示 URL 的警报消息":::

有些 HTML 页可能看起来不完整，因为它们引用的是其他已被拒绝的 URL。 要解决此问题，可以采取以下方法：

- 如果 HTML 页面包含指向其他域的链接，则可以将这些域添加到一个对这些 FQDN 具有允许访问权限的新应用规则。
- 如果 HTML 页面包含指向子 URL 的链接，则可以修改规则并向 URL 中添加一个星号。 例如：`targetURLs=www.nytimes.com/section/world*`

   或者，可以向规则中添加一个新 URL。 例如： 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Web 类别测试

让我们创建一个应用规则，以允许访问体育网站。
1. 在门户中打开资源组，并选择“DemoFirewallPolicy”。
2. 选择“应用规则”，然后选择“添加规则集合”。
3. 对于“名称”键入“GeneralWeb”，“优先级”设为 103，对于“规则集合组”选择“DefaultApplicationRuleCollectionGroup”。
4. 在“规则”下，对于“名称”键入 AllowSports，对于“源”选择 *\** ，对于“协议”选择“http、https”，选择“TLS 检查”，对于“目标类型”选择“Web 类别” ，对于“目标” 选择“体育” 。
5. 选择 **添加** 。

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="体育 Web 类别":::
6. 部署完成后，转到“WorkerVM”，打开 Web 浏览器并浏览到 `https://www.nfl.com`。

   你应会看到 NFL 网页，应用规则日志将显示一个匹配的“Web 类别：体育”规则，并且允许该请求。

## <a name="next-steps"></a>后续步骤

- [Azure 门户中的 Azure 防火墙高级版](premium-portal.md)