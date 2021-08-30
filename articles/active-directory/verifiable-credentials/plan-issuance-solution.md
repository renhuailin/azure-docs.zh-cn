---
title: 规划 Azure Active Directory 可验证凭据颁发解决方案（预览版）
description: 了解如何规划端到端的颁发解决方案。
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: d724c41fd16ea762fc96c36e67fe4a00a5b5e72d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728164"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>规划 Azure Active Directory 可验证凭据颁发解决方案（预览版）

 >[!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对颁发解决方案进行规划很重要，这样除了凭据的颁发外，还可以全面了解解决方案的体系结构和业务影响。 如果还未这样做，建议查看 [Azure Active Directory 可验证凭据体系结构概述](introduction-to-verifiable-credentials-architecture.md)，了解基础信息。

## <a name="scope-of-guidance"></a>指南范围

本文介绍有关规划可验证凭据颁发解决方案的技术方面的内容，该解决方案使用 Microsoft 产品与身份覆盖网络 (ION) 进行互操作。 用于可验证凭据的 Microsoft 解决方案遵循万维网联合会 (World Wide Web Consortium, W3C) [可验证凭据数据模型 1.0](https://www.w3.org/TR/vc-data-model/) 和[分散式身份识别符 (Decentralized Identifier, DID) V1.0](https://www.w3.org/TR/did-core/) 标准，可以与非 Microsoft 服务进行互操作。 但是，此内容中的示例反映了可验证凭据的 Microsoft 解决方案堆栈。 

与颁发解决方案不直接相关的支持性技术的相关主题不在此内容的介绍范围内。 例如，可验证凭据颁发解决方案中会用到网站，但不会详细介绍如果规划网站部署。

## <a name="components-of-the-solution"></a>解决方案组件

在规划颁发解决方案时，你必须设计一个使颁发者、用户和验证程序之间能够交互的解决方案。 可以颁发多个可验证凭据。 下图显示了颁发体系结构的组件。

### <a name="microsoft-vc-issuance-solution-architecture"></a>Microsoft VC 颁发解决方案体系结构

![一个颁发解决方案组件](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Azure Active Directory 租户

运行 Azure AD 可验证凭据服务的先决条件是其托管在 Azure Active Directory (Azure AD) 租户中。 Azure AD 租户为解决方案中的 Azure 资源提供了一个标识和访问管理 (Identity and Access Management, IAM) 控制平面。

每个租户都拥有一个 Azure AD 可验证凭据服务实例和一个分散式身份识别符 (DID)。 分散式身份识别符可证明颁发者拥有分散式身份识别符中包含的域。 使用者和验证者使用分散式身份识别来验证颁发者。 

### <a name="microsoft-azure-services"></a>Microsoft Azure 服务

![一种颁发解决方案的组件，侧重 Azure 服务](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

Azure 密钥保管库服务存储颁发者密钥，这些密钥是在启动 Azure AD 可验证凭据颁发服务时生成的。 密钥和元数据用于执行凭据管理操作并提供消息安全性。

每个颁发者都有用于签名、更新和恢复的单个密钥集。 此密钥集用于生成的每个可验证凭据的每个颁发。 

Azure 存储用于存储凭据元数据和定义；具体而言，即凭据的规则及其显示文件。 

* 显示文件确定哪些声明存储在 VC 中，以及如何在持有人的钱包中显示。 显示文件还包括品牌打造和其他元素。 规则文件的大小上限为 50 KB，而显示文件为 150 KB。 请参阅[如何自定义可验证凭据](../verifiable-credentials/credential-design.md)。

* 规则是一个由颁发者定义的模型，用于描述可验证凭据的所需输入、输入的受信任源以及输入声明到输出声明的映射。 

   * **输入** - 是规则文件中供客户端使用的模型的子集。 子集必须描述该组输入，在何处获取输入，以及为获取可验证凭据要调用的终结点。

* 不同凭据的规则和显示文件可以配置为使用不同的容器、订阅和存储。 例如，可以将权限委托给负责管理特定 VC 的不同团队。 

### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD 可验证凭据服务

![Microsoft Azure AD 可验证凭据服务](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

Azure AD 可验证凭据服务使你能够根据配置来颁布和撤销 VC。 服务：

* 预配分散式身份识别 (DID)，并将 DID 文档写入 ION，以便使用者和验证者使用。 每个颁发者有每个租户的相应的 DID。 

* 向 Key Vault 预配密钥集。 

* 存储供颁发服务和 Microsoft Authenticator 使用的配置元数据。

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft 使用[身份覆盖网络 (ION)](https://identity.foundation/ion/)，这是一个[基于 Sidetree 的网络](https://identity.foundation/sidetree/spec/)，使用比特币的区块链来实现分散式身份识别符 (DID)。 颁发者的 DID 文档存储在 ION 中，并被交易各方用来执行加密签名检查。

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator 应用程序

![Microsoft Authenticator 应用程序](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator 是一种移动应用程序，它用于协调用户、Azure AD 可验证凭据服务以及用于颁发 VC 的协定中描述的依赖方之间的交互。 它相当于一个数字钱包，VC 的持有者将 VC 存储在其中，包括 VC 使用者的私钥。 Authenticator 也是用于展示 VC 的验证机制。

### <a name="issuance-business-logic"></a>颁发业务逻辑 

![颁发业务逻辑](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

你的颁发解决方案包含一个 Web 前端，用户在其中请求 VC、身份存储和或其他属性存储以获取有关使用者的声明的值，以及其他后端服务。 

Web 前端通过生成深层链接或 QR 码向使用者的钱包提供颁发请求。 基于协定的配置，可能还需其他组件才能满足创建 VC 的要求。

这些服务提供的支持角色不一定需要与 ION 或 Azure AD 可验证凭据颁发服务集成。 此层通常包括：

* **与 Open ID Connect (OIDC) 兼容的服务** 用于获取颁发 VC 所需的 id_token。 现有标识系统（如 Azure AD 或 Azure AD B2C）可提供 OIDC 兼容服务，与标识服务器等自定义解决方案可以提供一样。

* **属性存储** - 它们可能在目录服务之外，提供颁发 VC 所需的属性。 例如，一个学生信息系统可能会提供与所获学历有关的声明。 

* **额外中间层服务** 包含有关查找、验证、账单的业务规则以及颁发凭据所需的其他运行时检查和工作流等。

有关设置 Web 前端的详细信息，请参阅教程[配置 Azure AD 以颁发可验证凭据](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md)。 

## <a name="credential-design-considerations"></a>凭据设计注意事项

凭据设计根据你的特定用例而定。 用例决定了：

* 互操作性要求

* 用户进行身份证明以获取其 VC 的方式

* 凭据中所需的声明

* 是否需要撤销凭据

 

### <a name="credential-use-cases"></a>凭据用例

对于 Azure AD 可验证凭据，最常见的凭据用例如下：

**身份验证**：基于多个条件颁发凭据。 这可能包括验证由政府颁发的文档（如护照或驾照）的真实性、将文档中的信息与其他信息关联，例如：

* 用户的自拍 

* 有效性验证

这种凭据适用于身份加入方案，包括新员工、合作伙伴、服务提供商、学生方案以及身份验证至关重要的其他方案。

 

![身份验证用例](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**聘用/成员证明**：颁发凭据以证明用户与机构之间的关系。 这种凭据适用于访问关联度不大的企业对企业应用程序，例如向员工或学生提供折扣的零售商。 VC 的一个主要价值是可移植性：颁发后，用户可以在许多方案中使用 VC。 

![聘用证明用例](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

有关更多用例，请参阅[可验证凭据用例 (w3.org)](https://www.w3.org/TR/vc-use-cases/)。

### <a name="credential-interoperability"></a>凭据互操作性

在设计过程中，研究特定于行业的架构、命名空间，以及可一致化的身份识别符，旨在实现可能的最大互操作性和使用率。 可在 [Schema.org](https://schema.org/) 和 [DIF - 声明和凭据工作组](https://identity.foundation/working-groups/claims-credentials.html)中找到示例。

请注意，通用架构领域的标准尚处于萌芽阶段。 此类工作的一个示例是[适用于教育任务组的可验证凭据](https://github.com/w3c-ccg/vc-ed)。 我们鼓励你进行调研，为你组织所在行业中萌芽阶段的标准的完善作出贡献。

### <a name="credential-attributes"></a>凭据属性 

建立凭据用例后，还需要确定凭据中要包含的属性。 验证者可读取用户展示的 VC 中的声明。

除了你的方案可能适用的行业特定标准和架构外，还应考虑以下方面：

* **最小化专用信息量**：使用最少量的必要专用信息来满足用例需求。 例如，对于一个为员工和校友提供折扣的电子商务网站，要满足该网站的 VC 要求，所需展示的凭据只需含有名字和姓氏声明。 不需含有招聘日期、职位、部门等信息。

* **优先使用抽象化声明**：每个声明都应满足需求，同时尽量减少详细信息。 例如，对于名为“ageOver”的声明来说，离散值（如“13”、“21”、“60”）比出生日期更加抽象。

* **规划可撤销性**：建议定义一个索引声明，以支持凭据查找和撤销机制。 每个协定只能定义一个索引声明。 需要注意的是，索引声明的值不存储在后端，只存储声明值的哈希。 有关详细信息，请参阅[撤销之前颁发的可验证凭据](../verifiable-credentials/how-to-issuer-revoke.md)。

有关凭据属性的其他注意事项，请参阅[可验证凭据数据模型 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/) 规范。

## <a name="plan-quality-attributes"></a>规划质量属性

### <a name="plan-for-performance"></a>规划性能

与任何解决方案一样，你必须规划性能。 需要关注的重要因素是延迟、吞吐量存储和可伸缩性。 在发布周期的初始阶段，不应担心性能的问题。 但是，当颁发解决方案的采用会导致颁发许多可验证凭据时，性能规划对于解决方案可能就很关键了。

下面提供了规划性能时需要考虑的方面：

* 部署了 Azure AD 可验证凭据颁发服务的 Azure 区域有欧洲西部、欧洲北部、美国西部 2 和美国中西部。 无需选择服务的部署区域。 

* 若要限制延迟，请在前面所列区域中最靠近请求发起位置的区域中部署颁发前端网站、密钥保管库和存储。

基于吞吐量的模型：
* VC 颁发服务遵循 [Azure 密钥保管库服务限制](../../key-vault/general/service-limits.md)。 

*  对于 Azure 密钥保管库，每个 VC 颁发都涉及三个签名操作：

      * 一个用于来自网站的颁发请求

      * 一个用于创建的 VC

      * 还有一个用于协定下载

* 密钥保管库的最大签名性能是在约每 10 秒的时间内进行 2000 次签名。 也就是每分钟约 12,000 次签名。 这意味着解决方案每分钟可至多支持 4,000 个 VC 颁发。

* 你无法控制限制；但我们建议阅读 [Azure 密钥保管库限制指南](../../key-vault/general/overview-throttling.md)。 

* 如果计划大规模推出和载入 VC，请考虑批量创建 VC，以确保不超过限制。

* 颁发服务遵循 Azure 存储限制。 在典型用例中，一般不用担心存储的问题。 但如果认为可能超出存储限制或认为存储可能是瓶颈，请查看以下内容： 

   * 建议在规划过程中阅读 [Blob 存储的可伸缩性和性能目标](../../storage/blobs/scalability-targets.md)。 Azure AD 可验证凭据颁发服务读取规则和显示文件，并缓存结果。

   * 还建议查看 [Blob 存储的性能和可伸缩性清单 - Azure 存储](../../storage/blobs/storage-performance-checklist.md)。 

在规划性能时，需要确定要监视的内容，以便更好地了解解决方案的性能。 在定义 VC 颁发监视策略时，除了应用程序级网站监视之外，还应考虑以下事项：

对于可伸缩性，考虑实现以下各项的指标：

   * 定义颁发过程的逻辑阶段。 例如：

   * 初始请求 

   * QR 码或深层链接的提供

   * 属性查找

   * Azure AD 可验证凭据颁发服务调用

   * 凭据颁发

   * 基于阶段定义指标：

      * 请求总数（容量）

      * 每个时间单位的请求数（吞吐量）

      * 所花时间（延迟）

* 使用以下项监视 Azure 密钥保管库和存储：

   * [Azure 密钥保管库监视和警报](../../key-vault/general/alert.md)

   * [监视 Azure Blob 存储](../../storage/blobs/monitor-blob-storage.md)

* 监视用于业务逻辑层的组件。 

### <a name="plan-for-reliability"></a>规划可靠性

若要规划可靠性，我们建议：

* 定义了可用性和冗余目标后，请使用以下指南了解如何实现目标：

   * [Azure 密钥保管库可用性和冗余 - Azure 密钥保管库](../../key-vault/general/disaster-recovery-guidance.md)

   * [灾难恢复和存储帐户故障转移 - Azure 存储](../../storage/common/storage-disaster-recovery-guidance.md)

* 对于前端和业务层，解决方案可以通过无限多种方式来展现。 与其他解决方案一样，对于标识的依赖项，请确保依赖项可复原并受到监视。 

如果发生了 Azure AD 可验证凭据颁发服务、Azure 密钥保管库或 Azure 存储服务不可用这种很少发生的情况，整个解决方案都会变得不可用。

### <a name="plan-for-compliance"></a>规划合规性

组织可能有与行业、交易类型或运营国家/地区相关的特定合规性需求。 

**数据驻留**：Azure AD 可验证凭据颁发服务会部署到 Azure 区域的一个子集中。 该服务仅用于计算功能。 我们不会在 Microsoft 系统中存储可验证凭据的值。 但是，在颁发 VC 时，在颁发过程中会发送和使用个人数据。 VC 服务的使用不应影响数据驻留要求。 如果在身份验证过程中要存储任何个人信息，应采用符合合规性要求的方式和区域进行存储。 如需查看 Azure 相关指南，请访问 Microsoft 信任中心网站。 

**撤销凭据**：确认组织是否会需要撤销凭据。 例如，当员工离开公司时，管理员可能需要撤销其凭据。 或者，如果颁发了驾驶证的凭据，而其持有人被发现做了一些会导致驾照被暂停使用的事，则可能需要撤销 VC。 有关详细信息，请参阅[撤销之前颁发的可验证凭据](how-to-issuer-revoke.md)。

**过期凭据**：确定是否要为凭据设置过期时间，如果是，会在哪种情况下过期。 例如，如果颁发 VC 作为驾照证明，那么该 VC 可能会在几年后过期。 如果颁发 VC 作为与某个用户存在关联的证明，你可能会希望 VC 每年过期一次，以确保用户每年返回以获取最新版本的 VC。

## <a name="plan-for-operations"></a>规划操作

规划操作时，开发一个用于故障排除、报告和区分你支持的各种客户的架构是至关重要的。 此外，如果由运营团队负责执行 VC 吊销，则必须定义该流程。 流程的每个步骤都应具有关联性，以便可以确定可以将哪些日志项与每个唯一的颁发请求相关联。 对于审核，建议分别捕获每次凭据颁发尝试。 具体而言：

* 生成客户和支持工程师可根据其需要引用的唯一事务 ID。

* 设计一种机制，将 Azure 密钥保管库事务日志与解决方案颁发部分的事务 ID 相关联。

* 如果你是代表多个客户颁发 VC 的身份验证服务，请根据客户或合同 ID，针对面向客户的报告和计费，进行监视并缓解相关问题。

* 如果你是代表多个客户颁发 VC 的身份验证服务，请在面向客户的报告和计费、监视和缓解中使用客户或合同 ID。 

## <a name="plan-for-security"></a>规划安全性

在考虑与设计相关的一些注意事项时（侧重于安全性），建议执行以下操作：

* 对于密钥管理：

   * 创建用于 VC 颁发的专用密钥保管库。 限制 Azure AD 可验证凭据颁发服务和颁发服务前端网站服务主体的 Azure 密钥保管库权限。 

   * 将 Azure 密钥保管库视为高特权系统 - Azure 密钥保管库为客户颁发凭据。 建议将 Azure 密钥保管库权限设置为最高，即任何人的权限都不得高于此权限。 管理员只能在指定时间访问密钥保管库。 有关使用 Azure 密钥保管库的最佳做法的详细信息，请参阅[密钥保管库的 Azure 安全性基线](/security/benchmark/azure/baselines/key-vault-security-baseline)。

* 对于代表颁发前端网站的服务主体：

   * 定义一个专用服务主体以授权访问 Azure 密钥保管库。 如果网站位于 Azure 上，建议使用 [Azure 托管标识](../managed-identities-azure-resources/overview.md)。 

   * 将代表网站和用户的服务主体视为单个信任边界。 虽然可以创建多个网站，但颁发解决方案只有一个密钥集。 

对于安全性记录和监视，建议执行以下操作：

* 启用针对 Azure 密钥保管库的日志记录和警报，以跟踪凭据颁发操作、密钥提取尝试、权限更改，以及监视和发送配置更改警报。 有关详细信息，请参阅[如何启用密钥保管库的日志记录](../../key-vault/general/howto-logging.md)。 

* 启用 Azure 存储帐户的日志记录，以监视和发送配置更改警报。 有关详细信息，请参阅[监视 Azure Blob 存储](../../storage/blobs/monitor-blob-storage.md)。

* 在安全信息和事件管理 (security information and event management, SIEM) 系统中存档日志，例如在 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel) 中长期保留日志。

* 通过执行以下操作来缓解欺骗风险

   * 实施 DNS 验证，以帮助客户识别颁发者品牌。

   * 对最终用户有意义的域名。

   * 最终用户认可的受信任品牌。

* 减轻分布式拒绝服务 (distributed denial of service, DDOS) 和密钥保管库资源耗尽的风险。 触发 VC 颁发请求的每一个请求都会生成密钥保管库签名操作，操作量的累积会趋近服务限制。 建议在生成颁发请求之前，先通过包含身份验证或验证码来保护流量。

有关管理 Azure 环境的指南，建议查看 [Azure 安全基准](/security/benchmark/azure/)以及[使用 Azure Active Directory 保护 Azure 环境](https://aka.ms/AzureADSecuredAzure)。 这些指南提供了管理基础 Azure 资源（包括 Azure 密钥保管库、Azure 存储、网站和其他 Azure 相关服务和功能）的最佳做法。

## <a name="additional-considerations"></a>其他注意事项

完成 POC 后，收集生成的所有信息和文档，并考虑删除颁发者配置。 这有助于避免在 POC 期限过期后颁发可验证凭据。 

有关实现密钥保管库和操作的详细信息，请参阅[使用密钥保管库的最佳做法](../../key-vault/general/best-practices.md)。 有关使用 Active Directory 保护 Azure 环境的详细信息，请参阅[使用 Azure Active Directory 保护 Azure 环境](https://aka.ms/AzureADSecuredAzure)。 

## <a name="next-steps"></a>后续步骤

[阅读体系结构概述](introduction-to-verifiable-credentials-architecture.md)

[规划验证解决方案](plan-verification-solution.md)

[开始使用可验证凭据](get-started-verifiable-credentials.md)
