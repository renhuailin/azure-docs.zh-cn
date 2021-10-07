---
title: 使用共享访问签名 (SAS) 授予对数据的有限访问权限
titleSuffix: Azure Storage
description: 了解使用共享访问签名 (SAS) 委托对 Azure 存储资源（包括 Blob、队列、表和文件）的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 1ea3f9181e1ebbbe4ae71c3e2505490361f066a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560264"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限

共享访问签名 (SAS) 提供对存储帐户中资源的安全委托访问。 使用 SAS 可以精细控制客户端访问数据的方式。 例如：

- 客户端可以访问哪些资源。

- 它们对这些资源拥有哪些权限。

- SAS 的有效期是多长。

## <a name="types-of-shared-access-signatures"></a>共享访问签名的类型

Azure 存储支持三种类型的共享访问签名：

- 用户委托 SAS

- 服务 SAS

- 帐户 SAS

### <a name="user-delegation-sas"></a>用户委托 SAS

用户委托 SAS 使用 Azure Active Directory (Azure AD) 凭据以及为 SAS 指定的权限进行保护。 用户委托 SAS 仅适用于 Blob 存储。

有关用户委托 SAS 的详细信息，请参阅[创建用户委托 SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)。

### <a name="service-sas"></a>服务 SAS

使用存储帐户密钥保护的服务 SAS。 一个服务 SAS 只会委托对一个 Azure 存储服务中的资源的访问权限：Blob 存储、队列存储、表存储或 Azure 文件。

有关服务 SAS 的详细信息，请参阅[创建服务 SAS (REST API)](/rest/api/storageservices/create-service-sas)。

### <a name="account-sas"></a>帐户 SAS

帐户 SAS 使用存储帐户密钥进行保护。 帐户 SAS 可委派对一个或多个存储服务中的资源的访问权限。 通过服务或用户委托 SAS 提供的所有操作也可以通过帐户 SAS 提供。

还可将访问权限委派给以下操作：

- 服务级别操作（例如“获取/设置服务属性”和“获取服务统计信息”操作 ）。

- 读取、写入和删除服务 SAS 不允许的操作。

有关帐户 SAS 的详细信息，请参阅[创建帐户 SAS (REST API)](/rest/api/storageservices/create-account-sas)。

> [!NOTE]
> Microsoft 建议你尽可能使用 Azure AD 凭据，而不要使用更容易受到安全威胁的帐户密钥，这是安全性方面的最佳做法。 当应用程序设计需要共享访问签名以访问 Blob 存储时，请尽可能使用 Azure AD 凭据创建用户委托 SAS 以提高安全性。 有关详细信息，请参阅[授权访问 Azure 存储中的数据](authorize-data-access.md)。

共享访问签名可以采取以下两种形式的一种：

- **临时 SAS**。 创建临时 SAS 时，将在 SAS URI 中指定开始时间、到期时间和权限。 任何类型的 SAS 都可以用作临时 SAS。

- **具有存储访问策略的服务 SAS**。 存储访问策略是针对资源容器（可以是 Blob 容器、表、队列或文件共享）定义的。 可以使用存储访问策略来管理一个或多个服务共享访问签名的约束。 将某个服务 SAS 与某个存储访问策略关联时，该 SAS 将继承对该存储访问策略定义的约束 &mdash; 开始时间、过期时间和权限。

> [!NOTE]
> 用户委托 SAS 或帐户 SAS 必须是临时 SAS。 用户委托 SAS 或帐户 SAS 不支持存储的访问策略。

## <a name="how-a-shared-access-signature-works"></a>共享访问签名的工作方式

共享访问签名是指向一个或多个存储资源的已签名 URI。 该 URI 包括的令牌包含一组特殊查询参数。 该令牌指示客户端可以如何访问资源。 作为一个查询参数，签名是基于 SAS 参数构造的，已通过用来创建该 SAS 的密钥签名。 Azure 存储使用该签名授予对存储资源的访问权限。

> [!NOTE]
> 无法审核 SAS 令牌的生成。 任何有权通过帐户密钥或 Azure 角色分配生成 SAS 令牌的用户都可以在存储帐户所有者不知情的情况下生成 SAS 令牌。 注意限制那些允许用户生成 SAS 令牌的权限。 若要防止用户生成使用 blob 和队列工作负荷的帐户密钥签名的 SAS，可以禁止共享密钥访问存储帐户。 有关详细信息，请参阅[阻止通过共享密钥进行授权](shared-key-authorization-prevent.md)。

### <a name="sas-signature-and-authorization"></a>SAS 签名和授权

可使用用户委托密钥或使用存储帐户密钥（共享密钥）对 SAS 令牌进行签名。

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>使用用户委托密钥对 SAS 令牌进行签名

可使用通过 Azure Active Directory (Azure AD) 凭据创建的用户委托密钥对 SAS 令牌进行签名。 用户委托 SAS 是使用用户委托密钥进行签名的。

若要获取密钥并创建 SAS，必须为 Azure AD 安全主体分配一个包含 `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` 操作的 Azure 角色。 有关详细信息，请参阅[创建用户委托 SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)。

#### <a name="signing-a-sas-token-with-an-account-key"></a>使用帐户密钥对 SAS 令牌进行签名

服务 SAS 和帐户 SAS 都是使用存储帐户密钥签名的。 若要创建使用帐户密钥签名的 SAS，应用程序必须有权访问该帐户密钥。

当请求包含 SAS 令牌时，将根据此 SAS 令牌的签名方式对该请求进行授权。 Azure 存储还使用用于创建 SAS 令牌的访问密钥或凭据来向拥有 SAS 的客户端授予访问权限。

下表总结了每种类型的 SAS 令牌的授权方式。

| SAS 类型 | 授权类型 |
|-|-|
| 用户委托 SAS（仅限 Blob 存储） | Azure AD |
| 服务 SAS | 共享密钥 |
| 帐户 SAS | 共享密钥 |

Microsoft 建议尽可能使用用户委托 SAS 以提高安全性。

### <a name="sas-token"></a>SAS 令牌

SAS 令牌是在客户端生成的字符串，例如，使用某个 Azure 存储客户端库生成。 Azure 存储不会以任何方式跟踪 SAS 令牌。 可以在客户端上创建不限数量的 SAS 令牌。 创建 SAS 后，可将其分发到需要访问存储帐户中的资源的客户端应用程序。

客户端应用程序将 SAS URI 作为请求的一部分提供给 Azure 存储。 然后，服务将检查 SAS 参数和签名，以验证其是否有效。 如果服务确认签名有效，则请求获得授权。 否则，请求被拒绝，错误代码为 403（禁止访问）。

下面是服务 SAS URI 的一个示例，其中显示了资源 URI 和 SAS 令牌。 由于 SAS 令牌包含 URI 查询字符串，因此，资源 URI 的后面必须先跟一个问号，然后是 SAS 令牌：

![服务 SAS URI 的组成部分](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>何时使用共享访问签名

使用 SAS 向任何无权访问这些资源的客户端授予对存储帐户中资源的安全访问权限。

SAS 通常适用于用户需要在存储帐户中读取和写入其数据的服务情形。 在存储帐户存储用户数据的情形中，有两种典型的设计模式：

1. 客户端通过执行身份验证的前端代理服务上传和下载数据。 此前端代理服务允许验证业务规则。 但对于大量数据或大量事务，创建可扩展以匹配需求的服务可能成本高昂或十分困难。

   ![方案示意图：前端代理服务](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. 轻型服务按需对客户端进行身份验证，并生成 SAS。 客户端应用程序接收到 SAS 后，可以直接访问存储帐户资源。 访问权限由 SAS 定义，并定义为 SAS 允许的时间间隔。 SAS 减少了通过前端代理服务路由所有数据的需要。

   ![方案示意图：SAS 提供程序服务](./media/storage-sas-overview/sas-storage-provider-service.png)

许多实际服务可能会混合使用这两种方法。 例如，一些数据可能通过前端代理进行处理和验证。 其他数据使用 SAS 直接保存和/或读取。

此外，在某些方案的复制操作中，需要使用 SAS 来授权访问源对象：

- 将一个 Blob 复制到驻留在不同存储帐户中的另一个 Blob 时。

  还可以选择使用 SAS 授予对目标 Blob 的访问权限。

- 将一个文件复制到驻留在不同存储帐户中的另一个文件时。

  还可以选择使用 SAS 授予对目标文件的访问权限。

- 将 Blob 复制到文件，或将文件复制到 Blob 时。

  即使源对象和目标对象驻留在同一存储帐户中，也必须使用 SAS。

## <a name="best-practices-when-using-sas"></a>使用 SAS 的最佳实践

在应用程序中使用共享访问签名时，需要知道以下两个可能的风险：

- 如果 SAS 泄露，则获取它的任何人都可以使用它，这可能会损害存储帐户。

- 如果提供给客户端应用程序的 SAS 到期并且应用程序无法从服务检索新 SAS，则可能会影响该应用程序的功能。

下面这些针对使用共享访问签名的建议可帮助降低这些风险：

- 始终使用 HTTPS  创建或分发 SAS。 如果 SAS 通过 HTTP 传递并被截取，则执行中间人攻击的攻击者能够读取 SAS。 然后，他们可以像预期用户一样使用该 SAS。 这可能会危害敏感数据或允许恶意用户损坏数据。

- **尽可能使用用户委托 SAS。** 用户委托 SAS 比服务 SAS 或帐户 SAS提供的安全性更高。 用户委托 SAS 是使用 Azure AD 凭据保护的，这样便不需要使用你的代码存储帐户密钥。

- **为 SAS 准备好吊销计划。** 确保已做好在 SAS 透露时的应对准备。

- **定义服务 SAS 的存储访问策略。** 存储访问策略可让你选择撤消服务 SAS 的权限，且无需重新生成存储帐户密钥。 将针对 SAS 的到期时间设置为很久之后的某一时间（或者无限远），并且确保定期对其进行更新以便将到期时间移到将来的更远时间。

- **对临时 SAS 服务 SAS 或帐户 SAS 使用短期过期时间。** 这样，即使某一 SAS 泄露，它也只会在短期内有效。 如果无法参照某一存储访问策略，该行为尤其重要。 临时到期时间还通过限制可用于上传到它的时间来限制可以写入 Blob 的数据量。

- **如果需要，让客户端自动续订 SAS。** 客户端应在到期时间之前很久就续订 SAS，这样，即使提供 SAS 的服务不可用，客户端也有时间重试。 在某些情况下，这可能是不必要的。 例如，你可能想要将 SAS 用于少量即时、生存期较短的操作。 这些操作预计将在有效期内完成。 因此，你不想续订 SAS。 但是，如果客户端定期通过 SAS 发出请求，那么“SAS 可能会过期”就将开始起作用了。

- 要注意 SAS 开始时间。  如果将 SAS 的开始时间设置为当前时间，则在前几分钟内可能会间歇性发生故障。 这是因为不同计算机的当前时间略有不同（称为时钟偏差）。 通常，将开始时间至少设置为 15 分钟前。 或者根本不设置，这会使它在所有情况下都立即生效。 同样原则也适用于到期时间 - 请记住，对于任何请求，在任一方向可能会观察到最多 15 分钟的时钟偏移。 对于使用 2012-02-12 之前的 REST 版本的客户端，未参照某一存储访问策略的 SAS 的最大持续时间是 1 小时。 任何指定时间超过 1 小时的策略都将失败。

- **请注意 SAS 日期/时间格式。** 对于某些实用工具（如 AzCopy），你需要将其日期时间格式设置为“+%Y-%m-%dT%H:%M:%SZ”。 此格式具体包含秒。

- **对要访问的资源要具体。** 一种安全性最佳做法是向用户提供所需最小权限。 如果某一用户仅需要对单个实体的读取访问权限，则向该用户授予对该单个实体的读取访问权限，而不要授予针对所有实体的读取/写入/删除访问权限。 如果 SAS 泄露，这也有助于降低损失，因为攻击者手中掌握的 SAS 的权限较为有限。

- **知道每次使用都会对帐户收费，包括通过 SAS 使用。** 如果你提供某个 Blob 的写入访问权限，则用户可以选择上传 200 GB 的 Blob。 如果还向用户提供了对 Blob 的读访问权限，他们可能会选择下载 Blob 10 次，对你产生 2 TB 的传出费用。 此外，提供受限权限，帮助降低恶意用户的潜在操作威胁。 使用短期 SAS 以便减少这一威胁（但要注意结束时间上的时钟偏移）。

- **验证使用 SAS 写入的数据。** 在某一客户端应用程序将数据写入存储帐户时，请记住对于这些数据可能存在问题。 如果计划验证数据，请在写入数据之后，在应用程序使用数据之前执行该验证。 这一实践还有助于防止损坏的数据或恶意数据写入帐户，这些数据可能是正常要求 SAS 的用户写入的，也可能是利用泄露的 SAS 的用户写入的。

- **知道何时不使用 SAS。** 有时，针对存储帐户执行特定操作所带来的风险超过了 SAS 所带来的好处。 对于此类操作，应创建一个中间层服务，该服务在执行业务规则验证、身份验证和审核后写入存储帐户。 此外，有时候以其他方式管理访问会更简单。 例如，如果想要使某一容器中的所有 Blob 都可以公开读取，则可以使该容器成为公共的，而不是为每个客户端都提供 SAS 来进行访问。

- **使用 Azure Monitor 和 Azure 存储日志来监视应用程序。** 授权可能会因为 SAS 提供程序服务中断而失败。 也可能由于无意中删除了存储访问策略而失败。 你可使用 Azure Monitor 和存储分析日志记录来观察这些类型的授权失败的任何峰值。 有关详细信息，请参阅 [Azure Monitor 中的 Azure 存储指标](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)和 [Azure 存储分析日志记录](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 存储不会跟踪为存储帐户生成的共享访问签名的数量，并且没有 API 可以提供此详细信息。 如果需要知道已为存储帐户生成的共享访问签名的数目，则必须手动跟踪该数目。

## <a name="get-started-with-sas"></a>开始使用 SAS

若要开始使用共享访问签名，请参阅以下适用于每种 SAS 类型的文章。

### <a name="user-delegation-sas"></a>用户委托 SAS

- [使用 PowerShell 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [使用 Azure CLI 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [使用 .NET 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>服务 SAS

- [使用 .NET 为容器或 Blob 创建服务 SAS](../blobs/sas-service-create.md)

### <a name="account-sas"></a>帐户 SAS

- [使用 .NET 创建帐户 SAS](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>后续步骤

- [使用共享访问签名委托访问权限 (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [创建用户委托 SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [创建服务 SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [创建帐户 SAS (REST API)](/rest/api/storageservices/create-account-sas)
