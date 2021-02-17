---
title: 部署和配置 Azure 防火墙 Premium 预览版
description: 了解如何部署和配置 Azure 防火墙高级版。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ec8fc4473669b0c056d0b22ff44e5818b87ba3fa
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549584"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>部署和配置 Azure 防火墙 Premium 预览版

> [!IMPORTANT]
> Azure 防火墙高级版目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 Azure 防火墙高级预览版是下一代防火墙，其中包含高度敏感和管控环境所需的功能。 它包括以下功能：

- **TLS 检查** -解密出站流量，处理数据，然后加密数据并将数据发送到目标。
- **Idp** -网络入侵检测和防护系统 (idp) 使你可以监视恶意活动的网络活动，记录有关此活动的信息，报告它，还可以选择尝试阻止它。
- **URL 筛选** -扩展 Azure 防火墙的 FQDN 筛选功能，以考虑整个 URL。 例如， `www.contoso.com/a/c` 而不是 `www.contoso.com` 。
- **Web 类别** -管理员可以允许或拒绝用户访问网站类别，如赌博网站、社交媒体网站等。

有关详细信息，请参阅 [Azure 防火墙高级版功能](premium-features.md)。

将使用模板部署具有以下三个子网的中央 VNet (10.0.0.0/16) 的测试环境：
- 辅助角色子网 (10.0.10.0/24) 
- Azure 堡垒子网 (10.0.20.0/24) 
- 防火墙子网 (10.0.100.0/24) 

为了简单起见，在此测试环境中使用单个中心 VNet。 对于生产目的，具有对等互连 Vnet 的 [中心和辐射拓扑](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 更为常见。

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="中心 VNet 拓扑":::

辅助角色虚拟机是通过防火墙发送 HTTP/S 请求的客户端。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="deploy-the-infrastructure"></a>部署基础结构

该模板使用 IDP、TLS 检查、URL 筛选和 Web 类别为启用了 Azure 防火墙高级版部署了完整的测试环境：

- 新的 Azure 防火墙高级和防火墙策略，其中包含预定义的设置，以允许简单验证其核心功能 (IDP、TLS 检查、URL 筛选和 Web 类别) 
- 部署所有依赖项，包括 Key Vault 和托管标识。 在生产环境中，这些资源可能已在同一模板中创建，不需要。
- 生成自签名根 CA 并将其部署在生成的 Key Vault
-  生成派生的中间 CA 并将其部署在 Windows 测试虚拟机上 (WorkerVM) 
- 还会部署 (BastionHost) 的堡垒主机，并可用于连接到 Windows 测试计算机 (WorkerVM) 



[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>测试防火墙

现在，可以测试 IDP、TLS 检查、Web 筛选和 Web 类别。

### <a name="add-firewall-diagnostics-settings"></a>添加防火墙诊断设置

若要收集防火墙日志，需要添加诊断设置以收集防火墙日志。

1. 选择 **DemoFirewall** 并在 " **监视**" 下选择 " **诊断设置**"。
2. 选择“添加诊断设置”。
3. 对于 " **诊断设置名称**"，请键入 " *fw-诊断*"。
4. 在 " **日志**" 下，选择 " **AzureFirewallApplicationRule**" 和 " **AzureFirewallNetworkRule**"。
5. 在 " **目标详细信息**" 下，选择 " **发送到 Log Analytics 工作区**"。
6. 选择“保存”。

### <a name="idps-tests"></a>IDP 测试

若要测试 IDP，需要使用适当的服务器证书部署自己的内部 Web 服务器。 有关 Azure 防火墙高级预览版证书要求的详细信息，请参阅 [Azure 防火墙高级预览版证书](premium-certificates.md)。

可以使用 `curl` 控制各种 HTTP 标头并模拟恶意流量。

#### <a name="to-test-idps-for-http-traffic"></a>测试 HTTP 流量的 IDP：

1. 在 WorkerVM 虚拟机上，打开管理员命令提示符窗口。
2. 在命令提示符处键入以下命令：

   `curl -A "BlackSun" <your web server address>`
3. 你将看到你的 Web 服务器响应。
4. 在 Azure 门户上，请参阅防火墙网络规则日志，查找类似于以下消息的警报：

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="警报消息":::

   > [!NOTE]
   > 可能需要一段时间才能开始在日志中显示数据。 至少为20分钟，允许日志开始显示数据。
5. 为签名2008983添加签名规则：

   1. 选择 " **DemoFirewallPolicy** "，然后在 " **设置** " 下选择 " **idp (预览")**。
   1. 选择 " **签名规则** " 选项卡。
   1. 在 " **签名 ID**" 下的 "打开" 文本框中，键入 *2008983*。
   1. 在 " **模式**" 下，选择 " **拒绝**"。
   1. 选择“保存”。
   1. 等待部署完成，然后继续。



6. 在 WorkerVM 上，再次运行该 `curl` 命令：

   `curl -A "BlackSun" <your web server address>`

   由于 HTTP 请求现在被防火墙阻止，因此在连接超时过期后，你将看到以下输出：

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. 中转到 Azure 门户中的监视器日志，并查找被阻止的请求的消息。
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>测试 HTTPS 流量的 IDP

使用 HTTPS 而不是 HTTP 重复这些测试。 例如：

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

你应看到与 HTTP 测试相同的结果。

### <a name="tls-inspection-with-url-filtering"></a>TLS 检查与 URL 筛选

使用以下步骤通过 URL 筛选来测试 TLS 检查。

1. 编辑防火墙策略应用程序规则，并向规则集合添加一个名 `AllowURL` 为的新规则 `AllowWeb` 。 配置 "目标 URL `www.nytimes.com/section/world` "、"源 IP 地址 **\* *"、"目标类型 _* URL (预览")**，选择 " **TLS 检查" (预览)**，并选择协议 " **http，https"**。

3. 部署完成后，在 WorkerVM 上打开浏览器，并在 `https://www.nytimes.com/section/world` 浏览器中打开并验证 HTML 响应是否按预期方式显示。
4. 在 Azure 门户中，可以在应用程序规则监视日志中查看整个 URL：

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="显示 URL 的警报消息":::

某些 HTML 页可能看起来不完整，因为它们引用的其他 Url 已被拒绝。 若要解决此问题，可以采取以下方法：

- 如果 HTML 页面包含指向其他域的链接，则可以使用 "允许访问这些 Fqdn" 将这些域添加到新的应用程序规则。
- 如果 HTML 页面包含指向子 Url 的链接，则可以修改规则并将星号添加到 URL。 例如： `targetURLs=www.nytimes.com/section/world*`

   或者，可以向规则添加新的 URL。 例如： 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Web 类别测试

让我们创建一个应用程序规则，以允许访问体育网站。
1. 在门户中打开资源组，并选择 " **DemoFirewallPolicy**"。
2. 选择 " **应用程序规则**"，然后 **添加规则集合**。
3. 对于 " **名称**"，键入 *GeneralWeb*， **优先级** *103*，" **规则收集组** "，选择 **DefaultApplicationRuleCollectionGroup**。
4. 在 "**名称** 类型 *AllowSports*"、"**源**"、"协议 http"、"https" 的 "**规则**" 下， *\** 选择 "  **TLS 检查**"、"**目标类型**" "选择 *Web 类别 (预览")*   
5. 选择 **添加** 。

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="体育 web 类别":::
6. 部署完成后，请转到  **WorkerVM** 并打开 web 浏览器并浏览到 `https://www.nfl.com` 。

   应会看到 "NFL" 网页，应用程序规则日志显示 " **Web 类别：体育** " 规则已匹配并且允许该请求。

## <a name="next-steps"></a>后续步骤

- [Azure 门户中的 Azure 防火墙高级预览版](premium-portal.md)
