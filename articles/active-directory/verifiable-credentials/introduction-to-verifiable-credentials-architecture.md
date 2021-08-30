---
title: Azure Active Directory 体系结构概述（预览版）
description: 了解有关规划和设计解决方案的基础信息
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.openlocfilehash: 45dd0be664474b22a24354903233e5729479f095
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723121"
---
# <a name="azure-ad-verifiable-credentials-architecture-overview-preview"></a>Azure AD 可验证凭据体系结构概述（预览版）

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对可验证凭据解决方案进行规划很重要，这样除了凭据的颁发和验证外，还可以全面了解解决方案的体系结构和业务影响。 建议查看 [Azure Active Directory 可验证凭据简介](decentralized-identifier-overview.md)和[常见问题解答](verifiable-credentials-faq.md)（如果尚未查看），然后完成[入门](get-started-verifiable-credentials.md)教程。 

本体系结构概述介绍 Azure Active Directory 可验证凭据服务的功能和组件。 有关颁发和验证的更多详细信息，请参阅 

* [规划颁发解决方案](plan-issuance-solution.md)

* [规划验证解决方案](plan-verification-solution.md)

## <a name="approaches-to-identity"></a>有关身份识别的方法

现今大多数组织都使用集中式身份识别系统来提供员工凭据。 它们还使用多种方法将客户、合作伙伴、供应商和信赖方纳入组织的信任边界内。 这些方法包括：联合身份验证、通过 Azure AD B2B 等系统创建和管理来宾帐户，以及建立与信赖方之间的明确信任。 大多数业务合作关系存在一个数字化的构成部分，这使得在组织间实现某种形式的信任需要付出巨大的努力。 

### <a name="centralized-identity-systems"></a>集中式身份识别系统

集中式方法仍适合许多情况，比如当应用程序、服务和设备依赖于某个域或信任边界内使用的信任机制时。 

在集中式身份识别系统中，由身份提供者 (IDP) 控制凭据的生命周期和使用。 

![集中式身份识别系统的示例](./media/introduction-to-verifiable-credentials-architecture/centralized-identity-architecture.png)


然而，在某些方案中，将分散式体系结构与可验证凭据结合使用可增强重要方案的效果，从而带来价值，例如以下这些重要方案：

* 确保员工和其他人的身份启用的安全性，包括远程方案。

* 基于特定条件对组织信任边界内的资源进行访问。

* 使用组织颁发的可移植凭据访问信任边界外的资源，例如访问合作伙伴的资源。

 

### <a name="decentralized-identity-systems"></a>分散式身份识别系统

在分散式身份识别系统中，由颁发者、持有者和使用凭据的信赖方共同控制凭据的生命周期和使用。

请看下面关系图中的方案，其中，电子商务网站 Proseware 希望为 Woodgrove 员工提供企业折扣。 

 ![分散式身份识别系统的示例](media/introduction-to-verifiable-credentials-architecture/decentralized-architecture.png)


如果你不熟悉可验证凭据 (VC)，可能会不理解 VC 术语。 以下定义来自[可验证凭据数据模型 1.0](https://www.w3.org/TR/vc-data-model/) 术语部分。 我们会在每个术语的解释后面将术语与前述图表中的实体相关联。

 “‘颁发者’是实体可通过以下方式扮演的一个角色：对有关一个或多个使用者的声明进行断言，然后根据这些声明创建可验证凭据，并将可验证凭据传输给持有者。”

* 在前述图表中，Woodgrove 是向其员工颁发可验证凭据的颁发者。 

 “‘持有者’是实体可通过以下方式扮演的一个角色：拥有一个或多个可验证凭据并通过这些凭据生成展示。 持有者通常是（但不总是）其持有的可验证凭证的使用者。 持有者将其凭证存储在凭证存储库中。” 

* 在前述图表中，Alice 是 Woodgrove 的一名员工。 他们从 Woodgrove 颁发者获取可验证凭据，并成为该凭据的持有者。

 “‘验证者’是实体可通过以下方式扮演的一个角色：接收一个或多个可验证凭据，并选择在某个可验证展示中进行处理。 在其他规范中，此概念可能称为信赖方。”

* 在前述图表中，Proseware 是由 Woodgrove 颁发的凭据的验证者。 

“‘凭据’是由颁发者发出的一个或多个声明的集合。 可验证凭据是一种防篡改的凭据，其作者身份可通过密码方式进行验证。 可验证凭据可用于生成可验证展示，还可通过密码方式进行验证。 一个凭据的不同声明中所关联的使用者可能不同。”

 “‘分散式身份识别符’是基于 URI 的可移植身份识别符（也称为 DID），与实体关联。 这些身份识别符通常用于可验证凭据中，并与使用者、颁发者和验证者相关联。”

* 前述图表中显示，参与者的 DID 的公钥存储在分散式帐本 (ION) 中。位于分散式身份识别符文档中。

 “‘分散式身份识别符文档’，也称为‘DID 文档’，是一种可使用可验证数据注册表访问的文档，并包含与某个特定的分散式身份识别符相关的信息，如关联的存储库和公钥信息。” 

* 在上述方案中，颁发者和验证者都有一个 DID 和一个 DID 文档。 DID 文档包含公钥和与 DID 关联的 DNS Web 域的列表（也称为链接域）。

* Woodgrove（颁发者）使用其公钥给员工的 VC 签名，类似的，Proseware（验证者）使用其密钥来为 VC 展示请求签名，此密钥也与 DID 关联。

 “‘分布式帐本’是用于记录事件的非集中式系统。 这些系统使参与者有足够的信心来依赖其他人记录的数据做出与操作相关的决定。 它们通常使用分布式数据库，其中不同的节点使用一个共识协议来确认以密码方式签名的事务的顺序。 随时间推移，数字签名的事务链可有效使账本的历史记录变得无法改变。”

* Microsoft 解决方案使用“身份覆盖网络 (ION)”提供分散式公钥基础结构 (PKI) 功能。

 

### <a name="combining-centralized-and-decentralized-identity-architectures"></a>结合使用集中式和分散式身份识别体系结构

在研究某个可验证凭据解决方案时，了解分散式与集中式身份识别体系结构的结合方式，会非常有帮助，这样有利于得到可降低风险并提供巨大操作优势的解决方案。

## <a name="the-user-journey"></a>用户旅程

此体系结构方面的概述基于一个申请并接受了某一组织的工作的求职者暨员工的经历。 介绍了该员工及其组织所经历的由可验证凭据带来的变化，在这一过程中，可验证凭据增强了组织的集中式流程。

 

### <a name="actors-in-these-use-cases"></a>这些用例中的参与者

* **Alice**，一个申请并接受了 Woodgrove 公司聘用的人。

* **Woodgrove** 公司，一家虚构的公司。

* **Adatum**，Woodgrove 的虚构的身份验证合作伙伴。

* **Proseware**，Woodgrove 的虚构的合作伙伴组织。

Woodgrove 同时使用集中式和分散式这两种身份识别体系结构。

### <a name="steps-in-the-user-journey"></a>用户旅程中的步骤

* Alice 申请、接受 Woodgrove 公司的一个职位并入职。

* 访问 Woodgrove 信任边界内的数字资源。

* 访问 Woodgrove 信任边界外的数字资源，但不扩展 Woodgrove 或合作伙伴的信任边界。

随着 Woodgrove 持续运营其业务，该公司必须持续管理身份。 本指南中的用例介绍了 Alice 如何使用自助服务功能获取和维护其身份识别符，以及 Woodgrove 如何添加、修改和结束具有不同信任需求的业务对业务关系。 

这些用例演示了如何结合使用集中式和分散式身份识别，以提供更可靠、更有效的身份识别、信任策略和生命周期。 


## <a name="user-journey-onboarding-to-woodgrove"></a>用户旅程：入职 Woodgrove

![用户的 Woodgrove 入职旅程](media/introduction-to-verifiable-credentials-architecture/onboarding-journey.png)

 **认知**：Alice 有意向为 Woodgrove 公司工作，并访问了 Woodgrove 的招聘网站。 

**激活**：Woodgrove 网站向 Alice 显示了一种证明其身份的方法：向她显示 QR 码或深度链接，提示她访问该公司信任的身份验证合作伙伴 Adatum 来验证自身身份。

**请求和上传**：Adatum 要求 Alice 提供身份证明。 Alice 拍了一张自拍照和一张驾驶执照的照片，并上传到 Adatum。 

**颁发**：Adatum 验证 Alice 的身份后，Adatum 颁发给她一个证明其身份的可验证凭据 (VC)。

**展示**：然后，Alice（凭据的持有者和使用者）可访问 Woodgrove 招聘门户，完成申请流程。 当 Alice 使用 VC 访问门户时，Woodgrove 将充当验证者和信赖方的角色，信任来自 Adatum 的证明。


### <a name="distributing-initial-credentials"></a>分配初始凭据

Alice 接受了 Woodgrove 的聘用。 在 Alice 的入职过程中，公司将为她创建一个 Azure Active Directory (AD) 帐户，供其在 Woodgrove 信任边界内使用。 Alice 的管理者必须解决如何使远程办公的 Alice 以安全的方式接收初始登录信息这一问题。 过去，IT 部门可能会将这些凭据提供给入职人员的管理者，由管理者将其打印出来交给 Alice。 但这种方式对远程办公的员工不适用。

VC 可通过增强凭据分发流程，为集中式系统增值。 Alice 可使用其 VC 作为身份证明来接收初始用户名名和凭据，用于访问集中式系统，而无需管理者提供凭据。 Alice 在入职流程中展示其添加到钱包中的身份证明。 

 

在该入职用例中，系统会在颁发者、验证者和持有者之间分配信任关系角色。 

* 颁发者负责验证其颁发的 VC 中的声明。 Adatum 验证 Alice 的身份并颁发 VC。 在本例中，颁发 VC 时无需考虑验证者或信赖方。

* 持有者拥有 VC，并发起 VC 展示以进行验证。 只有 Alice 才能展示她持有的 VC。

* 验证者接受来自他们信任的颁发者的 VC 中的声明，并使用可验证凭据数据模型中所述的分散式账本功能验证 VC。 Woodgrove 信任 Adatum 关于 Alice 身份的声明。

 

通过在入职中结合使用集中式和分散式身份识别体系结构，Woodgrove 无需存储对 Alice 进行身份验证时所必需的权限信息（如政府 ID 号），因为他们相信身份验证合作伙伴 (Adatum) 为 Alice 颁发的 VC 可证明其身份。 这样最大程度地减少了重复工作量，还可以对初始入职任务采用一种编程和可预测的方法。 

 

## <a name="user-journey-accessing-resources-inside-the-trust-boundary"></a>用户旅程：访问信任边界内的资源

![访问信任边界内的资源](media/introduction-to-verifiable-credentials-architecture/inside-trust-boundary.png)

Alice 作为员工，是在 Woodgrove 的信任边界内进行操作。 Alice 在 Woodgrove 信任边界内使用应用进行交互，而 Woodgrove 充当身份提供者 (IDP)，并保持对身份和她所使用的应用的配置的完全控制。 若要使用 Azure AD 信任边界中的资源，Alice 有可能需要提供多种形式的身份证明才能登录到 Woodgrove 的信任边界内和访问 Woodgrove 技术环境中的资源。 这是可以很好地利用集中式身份识别体系结构的一种典型方案。 

* Woodgrove 管理信任边界，并实行良好的安全做法，根据所执行的作业，向 Alice 提供所需的最小级别的访问权限。 为保持高度安全的状态，以及合规性方面的考虑，Woodgrove 还需要能够追踪员工的权限和对资源的访问权限，并且需要能够在员工聘用终止时撤回这些权限。

* Alice 只能使用 Woodgrove 维护的凭据来访问 Woodgrove 资源。 Alice 无需记录凭据的使用时间，因为凭据由 Woodgrove 管理且仅用于 Woodgrove 资源。 只有在需要访问 Woodgrove 资源时，该身份在 Woodgrove 信任边界内才有效，因此 Alice 无需拥有凭据。 

### <a name="using-vcs-inside-the-trust-boundary"></a>在信任边界内使用 VC

员工的身份需求会不断变化，而 VC 可增强集中式系统来管理这些变化。 

* 成为 Woodgrove 的员工后，Alice 可能还会需要更多的资源访问权限来满足某些特定需求。 例如，当 Alice 完成隐私培训时，公司可能会向她颁发一个具有相关声明的新员工 VC，她可以使用此 VC 访问设置了限制的资源。

* 可以使用 VC 在信任边界内恢复帐户。 例如，如果员工丢失了自己的电话和计算机，他们可从 Woodgrove 信任的身份验证服务获取一个新 VC，重获访问权限，再使用该 VC 获取新凭据。 

 ## <a name="user-journey-accessing-external-resources"></a>用户旅程：访问外部资源

Woodgrove 与 Proseware 协定产品购买折扣。 Woodgrove 所有员工都可以享受该折扣。 Woodgrove 希望为 Alice 提供一种访问 Proseware 网站并在购买产品时获得折扣的方法。 如果 Woodgrove 使用集中式身份识别体系结构，则可通过两种方法为 Alice 提供折扣： 

* Alice 可提供个人信息来创建 Proseware 帐户，然后 Proseware 需要验证 Alice 的 Woodgrove 员工身份。

* Woodgrove 可扩展其信任边界，将 Proseware 作为信赖方包含在内，这样 Alice 可以使用扩展的信任边界来接收折扣。 

利用分散式身份识别符，Woodgrove 可为 Alice 提供可验证凭据 (VC)，她可使用该凭据访问 Proseware 网站和其他外部资源。 

![访问信任边界外的资源](media/introduction-to-verifiable-credentials-architecture/external-resources.png)

 

通过向 Alice 提供 VC，Woodgrove 可证明 Alice 是公司的一名员工。 在 Proseware 的验证解决方案中，Woodgrove 是受信任的 VC 颁发者。 Proseware 对 Woodgrove 颁发流程的这种信任使其能够接受电子版 VC 作为 Alice 是 Woodgrove 员工的证明，并为 Alice 提供折扣。 在对 Alice 展示的 VC 进行验证时，Proseware 使用分布式账本检查 VC 的有效性。 本解决方案的内容：

* Woodgrove 使 Alice 能够在 Woodgrove 不扩展其信任边界的情况下向 Proseware 提供聘用证明。 

* Proseware 也无需扩展其信任边界就能验证 Alice 是 Woodgrove 的员工。 Proseware 可通过使用 Woodgrove 提供的 VC 来实现这一目的。 由于未扩展信任边界，因此管理信任关系会更容易，而且 Proseware 可通过不再接受 VC 来很轻易地结束这种关系。

* Alice 无需向 Proseware 提供个人信息，如电子邮件。 Alice 可通过个人设备上的钱包应用程序来维护 VC。 Alice 是唯一能够使用此 VC 的人员，且 Alice 需要启用此凭据。 钱包应用程序会记录 VC 的每一次使用，因此 Alice 会有一个关于 VC 使用时间和位置的记录。 

 

通过结合使用集中式和分散式身份识别体系结构来实现信任边界内部和外部的操作，可以降低复杂性和风险，且由于所需的关系有限，而使关系更易于管理。 

### <a name="changes-over-time"></a>随时间推移的变化

Woodgrove 会增加和终止与其他组织之间的业务关系，并需要确定何时使用集中式和分散式身份识别体系结构。

通过结合使用集中式和分散式身份识别体系结构，将分配与身份和身份证明相关的职责和工作量，风险会降低，用户不会面临需要经常或向许多未知的验证者披露其私人信息的风险。 具体而言：

* 在集中式身份识别体系结构中，由 IDP 颁发凭据，并对颁发的凭据进行验证。 所有身份信息均由 IDP 处理，或将其存储在目录下，或从目录中进行检索。 IDP 可能还会动态接受来自其他 IDP 系统（如社交登录或业务合作伙伴）的安全令牌。 信赖方若要在 IDP 信任边界内使用身份，须进行配置以接受 IDP 颁发的令牌。

## <a name="how-decentralized-identity-systems-work"></a>分散式身份识别系统工作原理

在分散式身份识别体系结构中，为建立和确保各方彼此间持续可靠的凭据交换，每个颁发者、用户和信赖方 (RP) 都有一个角色。 在 ION 中，参与者的 DID 的公钥是可解析的，这可支持签名验证，从而为任何项目提供信任，包括可验证凭据。 信赖方无需与颁发者建立信任关系即可使用可验证凭据。 而是由颁发者向使用者提供一个作为证明信息向信赖方展示的凭据。 参与者之间的所有消息都使用参与者的 DID 进行签名；来自颁发者和验证者的 DID 还需拥有生成请求的 DNS 域。 

例如：当 VC 持有者需访问资源时，须向信赖方展示 VC。 他们可通过使用钱包应用程序来读取 RP 请求来展示 VC。 在读取请求的过程中，钱包应用程序通过 ION 使用 RP 的 DID 来查找 RP 公钥，并验证出示 VC 的请求没有被篡改。 为证明域所有权，钱包还会检查 RP 的 DNS 域中托管的元数据文档中是否引用了 DID。 

 

![分散式身份识别系统的工作原理](media/introduction-to-verifiable-credentials-architecture/how-decentralized-works.png)

### <a name="flow-1-verifiable-credential-issuance"></a>流 1：可验证凭据的颁发

在此流中，凭据持有者与颁发者进行交互，以请求可验证凭据，如下图所示

![可验证凭据的颁发](media/introduction-to-verifiable-credentials-architecture/issuance.png)

1. 持有者启动此流的方法是使用浏览器或本机应用程序访问颁发者的 Web 前端。 在那里，颁发者网站会让用户收集数据，并执行特定于颁发者的逻辑，来确定是否可以颁发凭据并确定凭据内容。） 

1. 颁发者 Web 前端调用 Azure AD VC 服务，以生成 VC 颁发请求。 

1. Web 前端以 QR 码或设备特定的深度链接的形式（取决于设备）呈现请求链接。

1. 持有者使用 Microsoft Authenticator 等钱包应用扫描步骤 3 中的 QR 码或深度链接

1. 钱包从链接下载请求。 请求包含：

   * 颁发者的 DID。 钱包应用将它用于 ION 中的解析，以获取公钥和链接域。

   * URL 和 VC 清单，清单指定了颁发 VC 的协定要求。 这可能包括 id_token、必须提供的自证明属性或其他 VC 的展示。 

   * VC 的外观（日志文件的 URL、颜色等）。

1. 钱包验证颁发请求并处理协定要求：

   1. 验证颁发请求消息是否使用颁发者的密钥进行了签名，可在 ION 中解析的 DID 文档中找到密钥。 这可确保消息未被篡改。

   1. 验证颁发者 DID 文档中引用的 DNS 域是否为颁发者所拥有。 

   1. 根据 VC 协定要求，钱包还可能要求持有人收集其他信息，如请求自颁发的属性或通过浏览 OIDC 流获取 id_token。

1. 将协定要求的项目提交到 Azure AD VC 服务。 Azure AD VC 服务返回使用颁发者的 DID 密钥签了名的 VC，之后钱包安全存储 VC。

有关如何构建颁发解决方案和有关体系结构注意事项的详细信息，请参阅[规划 Azure Active Directory 可验证凭据颁发解决方案](plan-issuance-solution.md)。

### <a name="flow-2-verifiable-credential-presentation"></a>流 2：可验证凭据的展示

![可验证凭据的展示](media/introduction-to-verifiable-credentials-architecture/presentation.png)

在此流中，持有者与信赖方 (RP) 交互，根据信赖方的授权要求出示 VC。

1. 持有者启动此流的方法是使用浏览器或本机应用程序访问信赖方的 Web 前端。

1. Web 前端调用 Azure AD VC 服务，以生成 VC 展示请求。

1. Web 前端以 QR 码或设备特定的深度链接的形式（取决于设备）呈现请求链接。

1. 持有者使用 Microsoft Authenticator 等钱包应用扫描步骤 3 中的 QR 码或深度链接

1. 钱包从链接下载请求。 请求包含：

   * 一个架构或凭据类型的[基于标准的凭据请求](https://identity.foundation/presentation-exchange/)。 

   * RP 的 DID，钱包在 ION 中查找它。


1. 钱包验证展示请求并查找满足请求的存储的 VC。 钱包会根据所需的 VC 指导使用者选择并同意使用该 VC。

   * 在使用者同意使用 VC 后，钱包会在使用者和 RP 之间生成唯一的成对 DID。 

   然后，钱包向由使用者签名的 Azure AD VC 服务发送展示响应有效负载。 该结构包含： 

   * 使用者同意使用的 VC。

   * 作为负载的“使用者”生成的成对 DID。

   * RP DID 充当有效负载的“受众”。

1. Azure AD VC 服务验证钱包发送的响应。 根据步骤 2 中创建原始展示请求的方式，此验证可包括向 VC 颁发者核实所出示的 VC 的状态，以确定是否存在吊销等情况。

1. 验证后，Azure AD VC 服务将回调带结果的 RP。 

有关如何生成验证解决方案和有关体系结构注意事项的详细信息，请参阅[规划 Azure Active Directory 可验证凭据验证解决方案](plan-verification-solution.md)。

## <a name="key-takeaways"></a>关键点

分散式体系结构可用于增强现有解决方案和提供新功能。 

若要实现[分散式身份识别基础](https://identity.foundation/) (DIF) 和 W3C [设计目标](https://www.w3.org/TR/did-core/)，在创建可验证凭据解决方案时应考虑以下因素：

* 系统中的参与者之间没有集中的信任点。 也就是说，信任边界不会通过联合身份验证进行扩展，因为参与者信任特定的 VC

   * 通过 ION 可以发现任何参与者的分散式身份符 (DID)。

   * 此解决方案使验证者能够验证来自任何颁发者的任何可验证凭据 (VC)。

   * 该解决方案不支持由颁发者控制使用者或验证者（信赖方）的授权。

* 参与者独立操作（分离式），每个参与者都能完成其角色下的任务。

   * 颁发者为每个 VC 请求提供服务，且不会区别对待每个请求。

   * 将 VC 颁发给使用者后，使用者就拥有此 VC，并可向任何验证者出示它。

   * 验证者可验证来自任何使用者或颁发者的任何 VC。

## <a name="next-steps"></a>后续步骤

详细了解有关可验证凭据的体系结构的信息

* [规划颁发解决方案](plan-issuance-solution.md)

* [规划验证解决方案](plan-verification-solution.md)

* [Azure Active Directory 可验证凭据入门](get-started-verifiable-credentials.md)
