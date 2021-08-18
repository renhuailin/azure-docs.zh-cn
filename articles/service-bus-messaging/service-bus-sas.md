---
title: 使用共享访问签名进行 Azure 服务总线访问控制
description: 根据如何使用共享访问签名进行服务总线访问控制，并详细介绍如何使用 Azure 服务总线进行 SAS 授权。
ms.topic: article
ms.date: 04/27/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5659e159604c80ffe19cbc4b57dea5d99c1acb98
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379809"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>使用共享访问签名进行服务总线访问控制

本文介绍共享访问签名 (SAS)、其工作原理以及如何以平台无关的方式使用它们。

SAS 可以根据授权规则来保护对服务总线的访问。 可以在命名空间或消息传递实体（队列或主题）中配置这些保护。 授权规则具有与特定权限关联的名称，并包含一个加密密钥对。 通过服务总线 SDK 或者在自己的代码中使用规则名称和密钥可以生成 SAS 令牌。 然后，客户端可将令牌传递给服务总线，以证明请求的操作获得授权。

> [!NOTE]
> Azure 服务总线支持使用 Azure Active Directory (Azure AD) 授予对服务总线命名空间及其实体的访问权限。 使用 Azure AD 返回的 OAuth 2.0 令牌授权用户或应用程序可提供比共享访问签名 (SAS) 更高的安全性和易用性。 使用 Azure AD，不需要在代码中存储令牌，也不需要冒潜在的安全漏洞风险。
>
> Microsoft 建议尽量对 Azure 服务总线应用程序使用 Azure AD。 有关详细信息，请参阅以下文章：
> - [使用 Azure Active Directory 对应用程序进行身份验证和授权，使之能够访问 Azure 服务总线实体](authenticate-application.md)。
> - [使用 Azure Active Directory 对托管标识进行身份验证，以便访问 Azure 服务总线资源](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>SAS 概述

共享访问签名是使用简单令牌的基于声明的授权机制。 使用 SAS 时，永远不会通过网络传递密钥。 密钥用于以加密方式将信息签名，以后，服务可以验证这些信息。 可以像使用用户名和密码一样使用 SAS。在用户名和密码方案中，客户端直接拥有授权规则名称和匹配的密钥。 此外，还可以像在联合安全模型中一样使用 SAS。在此模型中，客户端从安全令牌服务接收限时且经过签名的访问令牌，而无需拥有签名密钥。

服务总线中的 SAS 身份验证配置了指定的[共享访问授权策略](#shared-access-authorization-policies)，这些规则具有关联的访问权限，以及一对主要和辅助加密密钥。 密钥是采用 Base64 表示法的 256 位值。 在服务总线的[队列](service-bus-messaging-overview.md#queues)和[主题](service-bus-messaging-overview.md#topics)上，可以在命名空间级别配置规则。

共享访问签名令牌包含所选授权策略的名称、应访问的资源的 URI、即时过期时间，以及使用所选授权规则的主要或辅助加密密钥基于这些字段计算的 HMAC-SHA256 加密签名。

## <a name="shared-access-authorization-policies"></a>共享访问授权策略

每个服务总线命名空间和服务总线实体都有一个由规则构成的共享访问授权策略。 命名空间级别的策略应用到该命名空间中的所有实体，不管这些实体各自的策略配置如何。

对于每个授权策略规则，需要确定三个信息片段：**名称**、**范围** 和 **权限**。 **名称** 只是该范围内的唯一名称。 范围也很简单：它是相关资源的 URI。 服务总线命名空间的范围是完全限定的命名空间，比如 `https://<yournamespace>.servicebus.windows.net/`。

策略规则授予的权限可以是以下各项的组合：

* “发送”- 授予向实体发送消息的权限
* “侦听”- 授予接收（队列、订阅）和所有相关消息处理的权限
* “管理”- 授予管理命名空间的拓扑的权限，包括创建和删除实体

“管理”权限包括“发送”和“接收”权限。

一个命名空间或实体策略最多可以包含 12 个共享访问授权规则，为三组规则提供空间。其中每个规则涵盖了基本权限，以及“发送”和“侦听”权限的组合。 根据此项限制，很明显 SAS 策略存储并不适合用作用户或服务帐户存储。 如果应用程序需要根据用户或服务标识授予服务总线的访问权限，应实现安全令牌服务，以便在执行身份验证和访问检查后颁发 SAS 令牌。

将为授权规则分配主要密钥和辅助密钥。  它们是加密形式的强密钥。 请不要遗失或透漏这些密钥 - 在 [Azure 门户][Azure portal]中总要用到它们。 可以使用其中一个生成的密钥，并且随时可以重新生成密钥。 如果重新生成或更改策略中的密钥，以前基于该密钥颁发的所有令牌会立即失效。 但是，基于此类令牌创建的现有连接将继续工作，直到该令牌过期。

创建服务总线命名空间时，系统会自动为该命名空间创建名为 **RootManageSharedAccessKey** 的策略规则。 此策略具有整个命名空间的“管理”权限。 建议将此规则视为 **root** 管理帐户，且不要在应用程序中使用它。 可以通过门户上命名空间的“配置”选项卡、PowerShell 或 Azure CLI 创建更多策略规则。

## <a name="best-practices-when-using-sas"></a>使用 SAS 的最佳实践
在应用程序中使用共享访问签名时，需要知道以下两个可能的风险：

- 如果 SAS 泄露，则获取它的任何人都可以使用它，这可能会使服务总线资源遭到入侵。
- 如果提供给客户端应用程序的 SAS 到期并且应用程序无法从服务检索新 SAS，则可能会影响该应用程序的功能。

下面这些针对使用共享访问签名的建议可帮助降低这些风险：

- **如果需要，让客户端自动续订 SAS**：客户端应在到期时间之前很久就续订 SAS，这样，即使提供 SAS 的服务不可用，客户端也有时间重试。 如果 SAS 旨在用于少量即时的短期操作，这些操作应在到期时间内完成，则上述做法可能是不必要的，因为不应续订 SAS。 但是，如果客户端定期通过 SAS 发出请求，则有效期可能就会起作用。 需要考虑的主要方面就是在以下两者间进行权衡：对短期 SAS 的需求（如前文所述）以及确保客户端尽早请求续订（以免在成功续订前因 SAS 到期而中断）。
- **要注意 SAS 开始时间**：如果将 SAS 的开始时间设置为“现在”，则由于时钟偏移（根据不同计算机，当前时间的差异），在前几分钟将会间歇地观察到失败。 通常，将开始时间至少设置为 15 分钟前。 或者根本不设置，这会使它在所有情况下都立即生效。 同样的原则也适用于过期时间。 请记住，对于任何请求，在任一方向你可能会观察到最多 15 分钟的时钟偏移。 
- **对要访问的资源要具体**：一种安全性最佳做法是向用户提供所需最小权限。 如果某一用户仅需要对单个实体的读取访问权限，则向该用户授予对该单个实体的读取访问权限，而不要授予针对所有实体的读取/写入/删除访问权限。 如果 SAS 泄露，这也有助于降低损失，因为攻击者手中掌握的 SAS 的权限较为有限。
- **不要总是使用 SAS**：有时，与针对事件中心的特定操作相关联的风险要超过 SAS 所带来的好处。 对于此类操作，应创建一个中间层服务，该服务在执行业务规则验证、身份验证和审核后写入事件中心。
- **始终使用 HTTPs**：始终使用 HTTPs 创建或分发 SAS。 如果某一 SAS 通过 HTTP 传递并且被截取，则执行中间人攻击的攻击者将能够读取 SAS、并使用它，就像目标用户本可执行的操作一样，这可能会暴露敏感数据或者使恶意用户能够损坏数据。

## <a name="configuration-for-shared-access-signature-authentication"></a>共享访问签名身份验证的配置

可以在服务总线命名空间、队列或者主题上配置共享访问授权策略。 目前不支持在服务总线订阅上进行配置，但你可以使用命名空间或主题上配置的规则来保护对订阅的访问。 

![SAS](./media/service-bus-sas/service-bus-namespace.png)

在此图中，manageRuleNS、sendRuleNS 和 listenRuleNS 授权规则适用于队列 Q1 和主题 T1，而 listenRuleQ 和 sendRuleQ 仅适用于队列 Q1，sendRuleT 仅适用于主题 T1。

## <a name="generate-a-shared-access-signature-token"></a>生成共享访问签名令牌

有权访问授权规则的名称及其某个签名密钥的任何客户端都可以生成 SAS 令牌。 令牌是通过采用以下格式编写一个字符串而生成的：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` - 令牌即时过期时间。 一个整数，反映自 1970 年 1 月 1 日令牌过期的时期 `00:00:00 UTC`（UNIX 时期）以来的秒数。
- `skn` - 授权规则的名称。
- `sr` - 要访问的资源的 URL 编码 URI。
- `sig` - URL 编码的 HMACSHA256 签名。 哈希计算类似于以下伪代码，并返回原始二进制输出的 base64。

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

> [!IMPORTANT]
> 有关使用不同编程语言生成 SAS 令牌的示例，请参阅[生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)。 


令牌包含非哈希值，使接收方可以使用相同的参数重新计算哈希，并验证颁发者是否拥有有效的签名密钥。

资源 URI 是向其声明访问权限的服务总线资源的完整 URI。 例如，`http://<namespace>.servicebus.windows.net/<entityPath>` 或 `sb://<namespace>.servicebus.windows.net/<entityPath>`；即，`http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`。 

URI 必须采用[百分比编码格式](/dotnet/api/system.web.httputility.urlencode)。

用于签名的共享访问授权规则必须在此 URI 指定的实体上，或由其分层父级之一进行配置。 例如，之前示例中的 `http://contoso.servicebus.windows.net/contosoTopics/T1` 或 `http://contoso.servicebus.windows.net`。

SAS 令牌对于以 `signature-string` 中使用的 `<resourceURI>` 为前缀的所有资源有效。


## <a name="regenerating-keys"></a>重新生成密钥

建议定期重新生成在共享访问身份验证策略使用的密钥。 提供了主要和辅助密钥槽，以便可以逐步轮换密钥。 如果应用程序通常使用主要密钥，则可将主要密钥复制到辅助密钥槽，并只在完成此过程后才重新生成主要密钥。 然后，可以在客户端应用程序（可以使用辅助槽中的旧主要密钥继续访问）中配置新的主要密钥值。 更新所有客户端后，可以重新生成辅助密钥，以便最终停用旧主要密钥。

如果你知道或怀疑密钥被盗用并需要撤销该密钥，则可以重新生成共享访问身份验证策略的主要和辅助密钥来代替之前的密钥。 此过程将使得由旧密钥签名的所有令牌失效。

## <a name="shared-access-signature-authentication-with-service-bus"></a>服务总线的共享访问签名身份验证

下面所述的方案包括配置授权规则、生成 SAS 令牌和客户端授权。

有关演示配置和使用 SAS 授权的服务总线应用程序的示例，请参阅[服务总线的共享访问签名身份验证](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/ManagingEntities/SASAuthorizationRule)。

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>访问实体上的共享访问授权规则

对[服务总线管理库](service-bus-management-libraries.md)中的队列或主题使用获取/更新操作来访问/更新相应的共享访问授权规则。 也可以在使用这些库创建队列或者主题的时候添加规则。

## <a name="use-shared-access-signature-authorization"></a>使用共享访问签名授权

在任何官方支持的语言（例如 .NET、Java、JavaScript 和 Python）中使用任何服务总线 SDK 的应用程序可以通过传递给客户端构造函数的连接字符串来利用 SAS 授权。

连接字符串可以包含规则名称 (*SharedAccessKeyName*) 和规则密钥 (*SharedAccessKey*) 或以前颁发的令牌 (*SharedAccessSignature*)。 如果传递给接受连接字符串的任何构造函数或工厂方法中包含这些参数，则系统会自动创建并填充 SAS 令牌提供程序。

要使用服务总线订阅的 SAS 授权，可以使用服务总线命名空间或主题上配置的 SAS 密钥。

## <a name="use-the-shared-access-signature-at-http-level"></a>使用共享访问签名（在 HTTP 级别）

在了解如何为服务总线中的任何实体创建共享访问签名后，便可以执行 HTTP POST 了：

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

请记住，这适用于所有情况。 可以为队列、主题或订阅创建 SAS。

如果为发送方或客户端提供 SAS 令牌，它们不会直接获取密钥，并且他们无法逆向改编哈希来获取它。 因此，可以控制它们有权访问的项以及访问时长。 要记住的一个重点是，如果更改策略中的主密钥，基于该密钥创建的所有共享访问签名都将失效。

## <a name="use-the-shared-access-signature-at-amqp-level"></a>使用共享访问签名（在 AMQP 级别）

在前一部分中，已了解如何使用 SAS 令牌配合 HTTP POST 请求将数据发送到服务总线。 如你所了解，可以使用高级消息队列协议 (AMQP) 来访问服务总线。在许多方案中，都会出于性能原因而将该协议用作首选协议。 文档[基于 AMQP 声明的安全性版本 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)（自 2013 年以来以有效草案版推出，现已得到 Azure 的支持）中介绍了如何配合 AMQP 使用 SAS 令牌。

开始将数据发送到服务总线之前，发布者必须将 AMQP 消息中的 SAS 令牌发送到正确定义的名为 $cbs 的 AMQP 节点（可以将它视为一个由服务使用的“特殊”队列，用于获取和验证所有 SAS 令牌）。 发布者必须在 AMQP 消息中指定 **ReplyTo** 字段；这是服务向发布者回复令牌验证结果（发布者与服务之间的简单请求/回复模式）时所在的节点。 根据 AMQP 1.0 规范中有关“动态创建远程节点”的论述，此回复节点是“在运行中”创建的。 在检查 SAS 令牌是否有效之后，发布者可以继续将数据发送到服务。

下面的步骤演示如何使用 [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) 库通过 AMQP 协议发送 SAS 令牌。 如果无法使用官方的服务总线 SDK（例如，在 WinRT、.NET Compact Framework、.NET Micro Framework 和 Mono 中）进行 C\# 开发，则这很有用。 当然，此库对于帮助了解基于声明的安全性如何在 AMQP 级别工作非常有用，就如同可以了解它如何在 HTTP 级别工作一样（使用 HTTP POST 请求并在“Authorization”标头内部发送 SAS 令牌）。 如果你不需要对 AMQP 有这么深入的了解，可以在任何支持的语言（例如 .NET、Java、JavaScript、Python 和 Go）中使用官方的服务总线 SDK，该 SDK 会为你执行这些操作。

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()` 方法接收代表服务的 TCP 连接的 connection（[AMQP .NET Lite 库](https://github.com/Azure/amqpnetlite)提供的 AMQP Connection 类实例），以及表示要发送的 SAS 令牌的 sasToken 参数。

> [!NOTE]
> 请务必在 **SASL 身份验证机制设置为 ANONYMOUS** 的情况下创建连接（而不是在不需要发送 SAS 令牌时使用的包含用户名与密码的默认 PLAIN）。
>
>

接下来，发布者将创建两个 AMQP 链接来发送 SAS 令牌和接收来自服务的回复（此令牌验证结果）。

AMQP 消息包含一组属性，比简单消息包含更多信息。 SAS 令牌是消息的正文（使用其构造函数）。 “ReplyTo”属性设置为用于在接收方链接上接收验证结果的节点名称（可以根据需要更改其名称，该节点将由服务动态创建）。 服务使用最后三个应用程序/自定义属性来指示它需要执行哪种类型的操作。 如 CBS 草案规范中所述，这些属性必须是 **操作名称** ("put-token")、**令牌类型**（在本例中为 `servicebus.windows.net:sastoken`），以及要应用令牌的 **受众的“名称”** （整个实体）。

在发送方链接上发送 SAS 令牌后，发布者必须在接收方链接上读取回复。 回复是一个简单的 AMQP 消息，其中包含一个名为 **"status-code"** 的应用程序属性，该属性可以包含与 HTTP 状态代码相同的值。

## <a name="rights-required-for-service-bus-operations"></a>服务总线操作所需的权限

下表显示对服务总线资源进行各种操作所需的访问权限。

| 操作 | 所需声明 | 声明范围 |
| --- | --- | --- |
| **命名空间** | | |
| 在命名空间上配置授权规则 |管理 |任何命名空间地址 |
| **服务注册表** | | |
| 枚举私有策略 |管理 |任何命名空间地址 |
| 开始在命名空间上侦听 |侦听 |任何命名空间地址 |
| 将消息发送到命名空间中的侦听器 |发送 |任何命名空间地址 |
| **队列** | | |
| 创建队列 |管理 |任何命名空间地址 |
| 删除队列 |管理 |任何有效队列地址 |
| 枚举队列 |管理 |/$Resources/Queues |
| 获取队列说明 |管理 |任何有效队列地址 |
| 在队列上配置授权规则 |管理 |任何有效队列地址 |
| 发送到队列 |发送 |任何有效队列地址 |
| 从队列接收消息 |侦听 |任何有效队列地址 |
| 在速览-锁定模式下接收消息后放弃或完成消息 |侦听 |任何有效队列地址 |
| 推迟消息以供将来检索 |侦听 |任何有效队列地址 |
| 将消息放入死信队列 |侦听 |任何有效队列地址 |
| 获取与消息队列会话关联的状态 |侦听 |任何有效队列地址 |
| 设置与消息队列会话关联的状态 |侦听 |任何有效队列地址 |
| 将消息计划为以后传送 |侦听 | 任何有效队列地址
| **主题** | | |
| 创建主题 |管理 |任何命名空间地址 |
| 删除主题 |管理 |任何有效主题地址 |
| 枚举主题 |管理 |/$Resources/Topics |
| 获取主题描述 |管理 |任何有效主题地址 |
| 在主题上配置授权规则 |管理 |任何有效主题地址 |
| 发送到主题 |发送 |任何有效主题地址 |
| **订阅** | | |
| 创建订阅 |管理 |任何命名空间地址 |
| 删除订阅 |管理 |../myTopic/Subscriptions/mySubscription |
| 枚举订阅 |管理 |../myTopic/Subscriptions |
| 获取订阅说明 |管理 |../myTopic/Subscriptions/mySubscription |
| 在速览-锁定模式下接收消息后放弃或完成消息 |侦听 |../myTopic/Subscriptions/mySubscription |
| 推迟消息以供将来检索 |侦听 |../myTopic/Subscriptions/mySubscription |
| 将消息放入死信队列 |侦听 |../myTopic/Subscriptions/mySubscription |
| 获取与主题会话关联的状态 |侦听 |../myTopic/Subscriptions/mySubscription |
| 设置与主题会话关联的状态 |侦听 |../myTopic/Subscriptions/mySubscription |
| **规则** | | |
| 创建规则 |管理 |../myTopic/Subscriptions/mySubscription |
| 删除规则 |管理 |../myTopic/Subscriptions/mySubscription |
| 枚举规则 |管理或侦听 |../myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [如何使用服务总线队列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
