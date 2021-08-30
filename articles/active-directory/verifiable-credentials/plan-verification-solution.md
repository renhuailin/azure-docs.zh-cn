---
title: 规划 Azure Active Directory 可验证凭据验证解决方案（预览版）
description: 了解规划和设计验证解决方案的基础信息
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: 0bc7f6a01dc2e537e91f162ba76eb6e505518747
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746012"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials--verification-solution-preview"></a>规划 Azure Active Directory 可验证凭据验证解决方案（预览版）

>[!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Microsoft 的 Azure Active Directory 可验证凭据 (Azure AD VC) 服务让你可以在不通过创建帐户或与其他标识提供者进行联合来扩展信任边界的情况下，信任用户身份证明。 通过使用基于开放标准的可验证凭据，验证交换让应用程序能够请求未绑定到特定域的凭据。 这样可以更轻松地大规模请求和验证凭据。

如果尚未使用此服务，建议你查看 [Azure AD 可验证凭据体系结构概述](introduction-to-verifiable-credentials-architecture.md)。 你可能还想要查看[规划 Azure AD 可验证凭据颁发解决方案](plan-issuance-solution.md)。

## <a name="scope-of-guidance"></a>指南范围

此内容包含规划使用 Microsoft 产品和服务的可验证凭据 (VC) 验证解决方案的多个技术方面的内容。 该解决方案与身份覆盖网络 (ION) 连接，身份覆盖网络充当分散式公钥基础结构 (DPKI)。 

本内容不包含非特定于验证解决方案的支持技术。 例如，在可验证凭据验证解决方案中使用了网站，但没有详细介绍网站部署规划。

规划验证解决方案时，必须考虑要添加或修改哪些业务功能，以及可以利用哪些 IT 功能或者必须添加哪些 IT 功能才能创建解决方案。 还必须考虑业务流程中涉及的人员以及支持解决方案最终用户和员工的人员所需的培训。 此内容未涉及这些主题。 建议回顾一下 [Microsoft Azure 架构良好的框架](/azure/architecture/framework/)，获取涵盖这些主题的信息。

## <a name="components-of-the-solution"></a>解决方案组件

作为验证解决方案计划的一部分，必须启用验证方、使用者和颁发者之间的交互。 在本文中，术语“信赖方”和“验证方”可互换使用。 下图显示了验证体系结构的组件。

![验证解决方案的组件](media/plan-verification-solution/verification-solution-architecture.png)


### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD 可验证凭据服务

在验证方解决方案的上下文中，Azure AD 可验证凭据服务是解决方案的 Microsoft 组件与 ION 之间的接口。 服务将对 Key Vault 密钥集进行预配，对分散式身份识别 (DID) 进行预配，并将 DID 文档写入 ION，使用者和颁发者可在 ION 中使用它。 

### <a name="azure-active-directory-tenant"></a>Azure Active Directory 租户 

该服务需要 Azure AD 租户，Azure AD 租户为解决方案中的 Azure 资源提供标识和访问管理 (IAM) 控制平面。 租户内存在 Azure AD VC 服务的单个实例，它可以颁发代表验证方的单个 DID 文档。 如果有多个使用验证服务的信赖方，则它们都使用相同的验证方 DID。 验证方 DID 提供了指向公钥的指针，让使用者和颁发者可以验证来自信赖方的消息。

### <a name="azure-key-vault"></a>Azure Key Vault

![Azure Key Vault](./media/plan-verification-solution/verification-solution-key-vault.png)

Azure Key Vault 服务存储验证方密钥，这些密钥是在启用 Azure AD 可验证凭据颁发服务时生成的。 密钥用于提供消息安全性。 每个验证方都有用于签名、更新和恢复 VC 的单个密钥集。 每次处理验证请求时，都将使用此密钥集。 Microsoft 密钥集目前使用椭圆曲线加密 (ECC) [SECP256k1](https://en.bitcoin.it/wiki/Secp256k1)。 我们将探索更广泛的 DID 社区所采用的其他加密签名架构。

### <a name="azure-ad-vc-apis-and-sdks"></a>Azure AD VC API 和 SDK

![Azure AD VC API 和 SDK](./media/plan-verification-solution/verification-solution-tools.png)

应用程序编程接口 (API) 和软件开发工具包 (SDK) 为开发人员提供一种方法来抽象解决方案组件之间的交互，以执行验证操作。 

### <a name="ion"></a>ION 

![Azure AD VC ION](./media/plan-verification-solution/verification-solution-ion.png)

可验证凭据解决方案使用分散式账本系统来记录事务。 Microsoft 使用[身份覆盖网络 (ION)](https://identity.foundation/ion/)，这是一个[基于 Sidetree 的网络](https://identity.foundation/sidetree/spec/)，它用比特币作为其区块链风格的账本来实现分散式身份识别 (DID)。 颁发者的 DID 文档存储在 ION 中，事务各方用它来执行加密签名检查。

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator 应用程序

![Microsoft Authenticator 应用程序](media/plan-verification-solution/verification-solution-authenticator.png)

Microsoft Authenticator 是一种移动应用程序，它用于协调依赖方、用户、Azure AD 可验证凭据颁发服务，以及用于颁发 VC 的协定中描述的依赖项之间的交互。 它相当于一个数字钱包，VC 的持有者将 VC 存储在其中。 它也是一种用于提供 VC 进行验证的机制。

### <a name="relying-party-rp"></a>信赖方 (RP) 

![信赖方组件](media/plan-verification-solution/verification-solution-relying-party.png)

#### <a name="web-front-end"></a>Web 前端

信赖方 Web 前端使用 Azure AD VC API 或 SDK 来验证 VC，方法是生成使用者的钱包使用的深层链接或 QR 码。 前端可以是可公开访问的网站，也可以是内部网站，这取决于具体方案，以实现需要验证的最终用户体验。 但是，钱包访问的终结点必须可公开访问。 具体而言，它可以使用特定请求参数控制重定向到钱包。 这是使用 Microsoft 提供的 API 和 SDK 实现的。

#### <a name="business-logic"></a>业务逻辑 

可以创建新的逻辑，或使用特定于信赖方的现有逻辑，并通过呈现 VC 来增强该逻辑。

## <a name="scenario-specific-designs"></a>特定于方案的设计

以下为用于满足特定用例的设计示例。 第一种适用于帐户加入，用于减少与加入新员工相关的时间、成本和风险。 第二种适用于帐户恢复，使最终用户能够使用自助服务机制来恢复或解锁其帐户。 第三种适用于访问高价值的应用程序和资源，尤其适用于企业到企业的用例，在这些用例中，将向为其他公司工作的人员提供访问权限。 

### <a name="account-onboarding"></a>帐户加入

还可以通过替换一些人工交互，使用可验证凭据来实现更快的帐户加入。 VC 可用于加入员工、学生、公民或其他人员以访问服务。 例如，员工可以使用 VC 验证身份来激活向其远程提供的识别证，而不用前往总部激活员工识别证。 居民可以使用 VC 来证明自己的身份并获取访问权限，而不是接收一个代码，他们必须进行兑换才能访问政府服务。 

![帐户加入方案](media/plan-verification-solution/verification-solution-onboarding.png)

#### <a name="additional-elements"></a>其他元素 

**加入门户**：这是一个 Web 前端，它可以编排用于 VC 呈现和验证的 Azure AD VC API/SDK 调用，以及加入帐户的逻辑。

**自定义逻辑/工作流**：包含在更新用户帐户前后执行的特定于组织的步骤的特定逻辑。 这可能包括审批工作流、其他验证、日志记录、通知等。

**目标身份识别系统**：在加入使用者时，加入门户需要与之交互的特定于组织的身份识别存储库。 要集成的系统是根据要加入 VC 验证的身份类型来确定的。 针对加入的身份验证的常见方案包括：

* 外部标识（例如，销售商、合作伙伴、供应商和客户），在集中式身份识别系统中使用 API 发出企业对企业 (B2B) 邀请或对包的权利管理分配加入 Azure AD。 

* 员工标识，在集中式身份识别系统中已通过人力资源 (HR) 系统加入。 在这种情况下，可以将身份验证集成为 HR 工作流的现有阶段的一部分。 

#### <a name="design-considerations"></a>设计注意事项

* **颁发者**：作为 VC 颁发者，帐户加入非常适合外部身份证明服务。 检查加入的示例包括：运行情况检查、政府颁发的文档验证、地址或电话号码确认等。

* **存储 VC 属性**：尽可能不要将 VC 中的属性存储在特定于应用的存储区中。 请特别注意个人数据。 如果应用程序内的特定流需要此信息，请考虑要求提供 VC 以按需检索声明。 

* **与后端系统关联的 VC 属性**：与颁发者一起定义 VC 的属性时，建立一种机制，以便在用户提供 VC 后，将后端系统中的信息关联起来。 这通常会在 RP 的上下文中结合收到的声明使用时间限定的唯一标识符。 下面是一些示例：

   * **新员工**：当 HR 工作流需要身份证明时，RP 可以生成具有时间限制的唯一标识符的链接，并将其发送到 HR 系统上应聘者的电子邮件地址。 此唯一标识符应足以将来自 VC 验证请求的信息（例如，名字、姓氏）与 HR 记录或基础数据关联起来。 VC 中的属性可用于在 HR 系统中完成用户属性，或验证有关员工的用户属性的准确性。

   * **外部标识** - 邀请：如果组织中的现有用户邀某个外部用户加入到目标系统中，则 RP 可以生成具有代表邀请事务的唯一标识符的链接，并将其发送到该外部用户的电子邮件地址。 此唯一标识符应足以将 VC 验证请求与邀请记录或基础数据关联起来，然后继续预配工作流。 VC 中的属性可用于验证或完成外部用户属性。

   * **外部标识** - 自助服务：如果外部标识通过自助服务（例如，B2C 应用程序）注册到目标系统，则 VC 中的属性可用于填充用户帐户的初始属性。 VC 属性还可用于查明是否已存在配置文件。

* **与目标身份识别系统交互**：Web 前端和目标身份识别系统之间的服务到服务通信需要作为高特权系统进行保护，因为它可以创建帐户。 向 Web 前端授予尽可能最低特权的角色。 示例包括：

   * 若要在 Azure AD 中创建新用户，RP 网站可以使用授予 MS Graph 范围 User.ReadWrite.All 的服务主体来创建用户，并使用范围 UserAuthenticationMethod.ReadWrite.All 来重置身份验证方法

   * 若要使用 B2B 协作将用户邀请到 Azure AD，RP 网站可以使用授予 MS Graph 范围 User.Invite.All 的服务主体来创建邀请。

   * 如果 RP 在 Azure 中运行，请使用托管标识调用 Microsoft Graph；这将消除在代码或配置文件中管理服务主体凭据的风险。 若要了解托管标识的详细信息，请参阅 [Azure 资源的托管标识。](../managed-identities-azure-resources/overview.md)

### <a name="accessing-high-value-applications-inside-organizations"></a>访问组织中的高价值应用程序 

可验证凭据可以用作访问组织内部敏感应用程序的附加证明。 例如，VC 还可用于根据实现特定标准（如认证）为员工提供对业务线应用程序的访问权限。

![在信任边界内访问](media/plan-verification-solution/inside-trust-boundary-access.png)

#### <a name="additional-elements"></a>其他元素 

**信赖方 Web 前端**：这是应用程序的 Web 前端，可以根据业务要求，通过 Azure AD 可验证凭据 SDK 或 API 调用来进行 VC 呈现和验证，从而增强它。

**用户访问授权逻辑**：应用程序中的逻辑层，它可用于授权用户进行访问，并经过增强以使用 VC 中的用户属性来做出授权决策。 

**其他后端服务和依赖项**：表示应用程序的其余部分逻辑，通过 VC 包含身份证明通常不会使其发生改变。

#### <a name="design-considerations"></a>设计注意事项

* **目标**：方案的目标决定了需要哪种凭据和颁发者。 典型方案包括：

   * **授权**：在此方案中，用户提供 VC 以做出授权决策。 旨在证明完成训练或持有特定认证的 VC 非常适合此方案。 VC 属性应包含有助于做出授权决策和进行审核的细粒度信息。 例如，如果 VC 用于验证个人是否经过培训并可以访问敏感财务应用，则应用逻辑可以检查部门声明是否具有细粒度的授权，并使用员工 ID 进行审核。 

   * **确认身份验证**：此方案的目标是确认最初加入的人和正在尝试访问高价值应用程序的人是同一个人。 来自身份验证颁发者的凭据正合适，应用程序逻辑应验证 VC 中的属性是否与登录应用程序的用户一致。 

* **检查吊销**：使用 VC 访问敏感资源时，通常与原始颁发者一起检查 VC 的状态，并拒绝访问已吊销的 VC。 与颁发者合作时，请确保在方案设计过程中对吊销进行明确讨论。 

* **用户体验**：使用 VC 访问敏感资源时，可以考虑以下两种模式。 

   * **升级身份验证**：用户使用现有的身份验证机制启动与应用程序的会话。 用户必须为应用程序中的特定高价值操作（例如，业务工作流审批）提供 VC。 这正好适用于在应用程序流中能轻易识别和更新此类高价值操作的各种方案。

   * **会话建立**：用户在启动与应用程序的会话过程中必须提供 VC。 当整个应用程序的性质为高价值时，此方案正好适用。 

### <a name="accessing-applications-outside-organization-boundaries"></a>访问组织边界外的应用程序

希望根据不同组织的成员身份或雇佣关系授予访问权限或权益的信赖方也可使用可验证凭据。 例如，电子商务门户可以提供权益（例如，为特定公司的员工、指定机构的学生等提供折扣）。 

在不建立联合关系的情况下，可验证凭据的分散性质可实现此方案。 

![在信任边界外进行访问](media/plan-verification-solution/outside-trust-boundary-access.png)

#### <a name="additional-elements"></a>其他元素 

**信赖方 Web 前端**：这是应用程序的 Web 前端，可以根据业务要求，通过 Azure AD 可验证凭据 SDK 或 API 调用来进行 VC 呈现和验证，从而增强它。

**用户访问授权逻辑**：应用程序中的逻辑层，它可用于授权用户进行访问，并经过增强以使用 VC 中的用户属性来做出授权决策。

**其他后端服务和依赖项**：表示应用程序的其余部分逻辑，通过 VC 包含身份证明通常不会使其发生改变。

#### <a name="design-considerations"></a>设计注意事项

* **目标**：方案的目标决定了需要哪种凭据和颁发者。 典型方案包括：

   * **身份验证**：在此方案中，用户必须拥有 VC 才能证明与特定组织的雇佣关系或其他关系。 在这种情况下，RP 应配置为接受目标组织颁发的 VC。 

   * **授权**：根据应用程序要求，应用程序可能会使用 VC 属性做出细粒度的授权决策和进行审核。 例如，如果电子商务网站为特定位置的组织员工提供折扣，则他们可基于 VC 中的国家/地区声明（如果存在）对此进行验证。

* **检查吊销**：使用 VC 访问敏感资源时，通常与原始颁发者一起检查 VC 的状态，并拒绝访问已吊销的 VC。 与颁发者合作时，请确保在方案设计过程中对吊销进行明确讨论。 

* **用户体验**：用户可在启动与应用程序的会话过程中提供 VC。 通常，应用程序还会提供另一种方法来启动会话，以应对用户没有 VC 的情况。 


### <a name="account-recovery"></a>帐户恢复

可验证凭据可以用作恢复帐户的方法。 例如，当用户需要恢复其帐户时，他们可能会访问一个网站，该网站要求他们提供 VC，并调用 MS Graph API 来启动 Azure AD 凭据重置，如下图所示。

注意：虽然本部分中介绍的方案特定于恢复 Azure AD 帐户，但此方法还可用于恢复其他系统中的帐户。

![帐户恢复解决方案](media/plan-verification-solution/account-recovery.png)

#### <a name="additional-elements"></a>其他元素

**帐户门户**：这是一个 Web 前端，用于编排 API 或 SDK 调用以进行 VC 呈现和验证。 此编排可以包括 Microsoft Graph 调用，以恢复 Azure AD 中的帐户。

**自定义逻辑或工作流**：包含在更新用户帐户前后执行的特定于组织的步骤的逻辑。 这可能包括审批工作流、其他验证、日志记录、通知等。

**Microsoft Graph**：公开表述性状态转移 (REST) API 和客户端库来访问用于执行帐户恢复的 Azure AD 数据。 

**Azure AD 企业目录**：这是包含通过帐户门户创建或更新的帐户的 Azure AD 租户。

#### <a name="design-considerations"></a>设计注意事项

**VC 属性与 Azure AD 的关联**：与颁发者协作定义 VC 的属性时，请建立一种基于 VC 中的声明和用户输入将信息与内部系统进行关联的机制。 例如，如果在加入员工之前通过身份验证提供程序 (IDV) 进行了身份验证，请确保颁发的 VC 包含同样存在于内部系统（如人力资源系统）中的声明以进行关联。 这可能是电话号码、地址或出生日期。 除了 VC 中的声明外，RP 还可以在此过程中请求提供一些信息，例如其社会安全号码 (SSN) 的最后 4 位数字。

**具有现有 AZURE AD 凭据重置功能的 VC 角色**：Azure AD 具有内置的自助式密码重置 (SSPR) 功能。 可验证凭据可用于提供另一种恢复方式，尤其是在用户对 SSPR 方法无法访问或失去控制的情况下（例如，用户同时丢失了计算机和移动设备）。 在此方案中，用户可以从身份证明颁发者重新获取 VC，并提供它来恢复自己的帐户。 

同样，可以使用 VC 生成临时访问密码，让用户能够在没有密码的情况下重置其 MFA 身份验证方法。 

**授权**：创建授权机制（例如，在继续凭据恢复之前 RP 检查的安全组）。 例如，只有特定组中的用户才可能有资格使用 VC 恢复帐户。

**与 Azure AD 进行交互**：Web 前端与 Azure AD 之间的服务到服务通信必须作为高特权系统进行保护，因为它可以重置员工的凭据。 向 Web 前端授予尽可能最低特权的角色。 示例包括：

* 向 RP 网站授权，使其能够使用授予了 MS Graph 范围 UserAuthenticationMethod.ReadWrite.All 的服务主体来重置身份验证方法。 不要授予 User.ReadWrite.All，这会启用创建和删除用户的功能。

* 如果 RP 在 Azure 中运行，请使用托管标识调用 Microsoft Graph。 这消除了在代码或配置文件中管理服务主体凭据的风险。 有关详细信息，请参阅 [Azure 资源的托管标识。](../managed-identities-azure-resources/overview.md)

## <a name="plan-for-identity-management"></a>规划标识管理 

下面是将 VC 合并到信赖方时需要考虑的一些 IAM 注意事项。 信赖方通常是应用程序。 

### <a name="authentication"></a>身份验证

* VC 的使用者必须是人类。 

* VC 的呈现必须由人类 VC 持有者以交互方式执行，该持有者将 VC 放在钱包中。 不支持代理等非交互式流。 

### <a name="authorization"></a>授权 

* 成功呈现 VC 本身就可被视为粗粒度授权入口。 VC 属性还可用于做出细粒度的授权决策。

* 确定已过期的 VC 在应用程序中是否有用；如果有用，请在授权检查过程中检查 VC 的“exp”声明值（过期时间）。 例如，以下情况与过期不相关：请求政府颁发的文件（如驾照）来验证使用者年龄是否在 18 岁以上。 即使 VC 已过期，出生日期声明也有效。 

* 确定已吊销的 VC 对授权决策是否有用。 

   * 如果不相关，请跳过对检查状态 API 的调用（默认为打开状态）。 

   * 如果相关，请在应用程序中添加正确的异常处理方式。

### <a name="user-profiles"></a>用户配置文件

可以使用已提供的 VC 中的信息来生成用户配置文件。 如果要使用属性生成配置文件，请考虑以下事项。

* 颁发 VC 时，它包含颁发时的属性快照。 VC 的有效期可能很长，你必须确定自己将接受的属性具有足够长的有效期，可以用作配置文件的一部分。 

* 如果每次使用者启动与 RP 的会话时都需要提供 VC，请考虑使用 VC 呈现的输出来生成具有各种属性的非持久性用户配置文件。 这有助于降低与静态存储用户属性相关的隐私风险。 如果应用程序需要将使用者的属性保存在本地，则仅存储应用程序所需的最小声明集（而不是存储 VC 的全部内容）。

* 如果应用程序需要永久性用户配置文件存储：

   * 请考虑使用“sub”声明作为用户的不可变标识符。 这是一个不透明的唯一属性，对于给定使用者/RP 对，它将是常量。 

   * 定义从应用程序撤销用户配置文件的机制。 由于 Azure AD 可验证凭据系统的分散性质，因此没有应用程序用户预配生命周期。

   * 不要存储 VC 令牌中返回的个人数据声明。

   * 仅存储信赖方逻辑所需的声明。 

## <a name="plan-for-performance"></a>规划性能

与任何解决方案一样，你必须规划性能。 重点领域包括延迟、吞吐量、存储和可伸缩性。 在发布周期的初始阶段，不应担心性能的问题。 但是，当采用颁发解决方案会导致颁发许多可验证凭据时，性能规划可能会成为解决方案的关键部分。

下面提供了规划性能时需要考虑的方面：

* Azure AD 可验证凭据颁发服务部署在欧洲西部、欧洲北部、美国西部 2 和美国中西部的 Azure 区域。 若要限制延迟，请在前文列出的最靠近请求来源的区域中部署验证前端（网站）和密钥保管库。

* 基于吞吐量的模型：

   * VC 验证容量受 [Azure Key Vault 服务限制](../../key-vault/general/service-limits.md)的限制。 

   * 每次验证 VC 都需要一个 Key Vault 签名操作。

   * Key Vault 的最大签名性能是在约 10 秒的时间内进行 2000 次签名。 这意味着解决方案每分钟最多支持 12,000 个 VC 验证请求。

   * 你无法控制限制；但是，建议阅读 [Azure Key Vault 限制指南](../../key-vault/general/overview-throttling.md)，以便你了解限制对性能的影响。 

## <a name="plan-for-reliability"></a>规划可靠性

为针对高可用性和灾难恢复进行最佳规划，建议执行以下操作：

* Azure AD 可验证凭据服务部署在欧洲西部、欧洲北部、美国西部 2 和美国中西部的 Azure 区域。 请考虑将受支持的 Web 服务器和受支持的应用程序部署在这些区域之一，尤其是你期望大多数验证流量源自其中的那些区域。 

* 在设计可用性和冗余目标时，请查看并整合 [Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md)中的最佳做法。

## <a name="plan-for-security"></a>规划安全性

在设计安全性时，请考虑以下事项：

* 单个租户中的所有信赖方 (RP) 都具有相同的信任边界，因为它们共享相同的 DID。

* 为访问 Key Vault 的网站定义专用服务主体。

* 只有 Azure AD 可验证凭据服务和网站服务主体才应该有权使用 Key Vault 来通过私钥对消息进行签名。 

* 不要向 Key Vault 分配任何人工标识管理权限。 有关 Key Vault 最佳做法的详细信息，请参阅[适用于 Key Vault 的 Azure 安全基线](../../key-vault/general/security-baseline.md)。

* 查看[使用 Azure Active Directory 保护 Azure 环境](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)，了解管理解决方案受支持的服务的最佳做法。 

* 通过以下操作减轻欺骗风险：

   * 实现 DNS 验证，帮助客户识别颁发者品牌。

   * 使用对最终用户有意义的域。

* 减轻分布式拒绝服务 (DDOS) 和 Key Vault 资源限制风险。 每个 VC 呈现请求都会生成 Key Vault 签名操作，这些操作会逐渐增加服务限制。 建议在生成颁发请求之前，先通过合并备用身份验证或验证码来保护流量。

## <a name="plan-for-operations"></a>规划操作

在规划操作时，建议在审核过程中计划捕获每次凭据验证尝试。 使用该信息进行审核和故障排除。 此外，请考虑生成客户和支持工程师可根据需要引用的唯一事务标识符 (ID)。 

作为操作规划的一部分，请考虑对以下内容进行监视：

* **可伸缩性**：

   * 监视失败的 VC 验证，作为应用程序的端到端安全指标的一部分。

   * 监视凭据验证的端到端延迟。

* **可靠性和依赖项**：

   * 监视验证解决方案使用的基础依赖项。 

   * 遵循 [Azure Key Vault 监视和警报](../../key-vault/general/alert.md)。

* **安全性**：

   * 启用 Key Vault 日志记录来跟踪签名操作，以及监视配置更改并发出警报。 有关详细信息，请参阅[如何启用 Key Vault 日志记录](../../key-vault/general/howto-logging.md)。

   * 存档安全信息和事件管理 (SIEM) 系统（例如，[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)）中的日志进行长期保留。

 

## <a name="next-steps"></a>后续步骤

详细了解如何构建 VC 解决方案

   * [Azure AD 可验证凭据概述](introduction-to-verifiable-credentials-architecture.md)

   * [规划 Azure AD 可验证凭据颁发解决方案](plan-issuance-solution.md)

实现可验证凭据

   * [Azure Active Directory 可验证凭据简介](decentralized-identifier-overview.md) 

   * [可验证凭据入门指南](get-started-verifiable-credentials.md)

[常见问题](verifiable-credentials-faq.md)