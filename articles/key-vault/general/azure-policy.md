---
title: 将 Azure Key Vault 与 Azure Policy 集成
description: 了解如何将 Azure Key Vault 与 Azure Policy 集成
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: dcbbe63754bdcfc4ded249720b58940e0c219bf9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861557"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>将 Azure Key Vault 与 Azure Policy 集成

[Azure Policy](../../governance/policy/index.yml) 是一种管理工具，使用户能够大规模审核和管理其 Azure 环境。 Azure Policy 提供将护栏放置在 Azure 资源上的功能，以确保它们符合分配的策略规则。 它允许用户对其 Azure 环境执行审核、实时强制和修正。 策略执行的审核的结果将显示在合规性仪表板中，用户可以在其中查看合规和不合规的资源和组件的明细。  有关详细信息，请参阅 [Azure Policy 服务概述](../../governance/policy/overview.md)。

使用方案示例：

- 你想通过在公司的 Key Vault 中实现最小密钥大小和最大证书有效期的要求来提高你公司的安全态势，但是你不知道哪些团队合规而哪些不合规。
- 当前没有解决方案可在组织中执行审核，或者你通过要求组织内各个团队报告其符合性来对环境进行手动审核。 你正在寻找一种方法来自动执行此任务，实时执行审核，并保证审核的准确性。
- 你需要强制实施公司安全策略，并阻止个人创建自签名证书，但你不能通过自动方式来阻止其创建。 
- 你希望放宽对测试团队的一些要求，但想要对生产环境保持严格控制。 你需要一种简单的自动化方式来分隔你的资源的强制实施。
- 你需要确保在发生实时站点问题时可以回滚新策略的强制实施。 需要一键式解决方案才能关闭策略的强制实施。 
- 你依赖于第三方解决方案来审核你的环境，并且希望使用 Microsoft 内部产品/服务。

## <a name="types-of-policy-effects-and-guidance"></a>策略影响和指南的类型

**审核**：当策略的影响设置为“审核”时，策略不会对环境造成任何中断性变更。 它只会通过在策略合规性仪表板中将组件（例如在指定范围内不符合策略定义的证书）标记为不合规，提醒你注意这些组件。 如果未选择任何策略影响，则“审核”为默认值。

**拒绝**：当策略的效果设置为“拒绝”时，策略将阻止创建新组件（如证书），并阻止不符合策略定义的现有组件的新版本。 Key Vault 中现有的不合规资源不受影响。 “审核”功能将继续运行。

## <a name="available-built-in-policy-definitions"></a>可用的“内置”策略定义

Key Vault 已创建可用于管理密钥保管库及其密钥、证书和机密对象的一组策略。 这些策略是“内置的”，这意味着不需要你编写任何自定义 JSON 来启用它们，它们在 Azure 门户中可供给你分配。 你仍可以自定义某些参数以满足你组织的需求。

# <a name="certificate-policies"></a>[证书策略](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>证书应具有指定的最长有效期（预览版）

此策略可用于管理 Key Vault 中存储的证书的最长有效期。 最好的安全做法是限制证书的最长有效期。 如果证书的私钥在未检测的情况下被泄露，则使用短期证书将最大限度地缩短持续伤害的时间，并减少证书对于攻击者的价值。

### <a name="certificates-should-use-allowed-key-types-preview"></a>证书应使用允许的密钥类型（预览版）

此策略允许你限制可位于 Key Vault 中的证书类型。 你可以使用此策略来确保证书私钥为 RSA、ECC 或支持 HSM。 你可以从以下列表中选择允许使用的证书类型。

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>证书应具有指定的生存期操作触发器（预览版）

此策略可用于管理为证书指定的生存期操作，这些证书要么处于有效期的特定天数内，要么已达到其使用寿命的一定百分比。

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>证书应由指定的集成证书颁发机构颁发（预览版）

如果你使用 Key Vault 集成证书颁发机构（Digicert 或 GlobalSign），并且希望用户使用其中一个提供程序，则可以使用此策略审核或强制执行你的选择。 此策略将评估在证书颁发策略中选择的 CA 及密钥保管库中定义的 CA 提供程序。 此策略还可用于审核或拒绝在 Key Vault 中创建自签名证书。

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>证书应由指定的非集成证书颁发机构颁发（预览版）

如果你使用的是内部证书颁发机构或未与 Key Vault 集成的证书颁发机构，并且你希望用户从你提供的列表中使用证书颁发机构，则可以使用此策略按颁发者名称创建允许的证书颁发机构列表。 此策略还可用于审核或拒绝在 Key Vault 中创建自签名证书。

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>使用椭圆曲线加密的证书应使用允许的曲线名称（预览版）

如果使用椭圆曲线加密（简称为 ECC）证书，则可以从以下列表自定义允许的曲线名称列表。 默认选项允许所有以下曲线名称。

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>使用 RSA 加密的证书 管理 RSA 证书的最小密钥大小（预览版）

如果使用 RSA 证书，则可以选择证书必须具有的最小密钥大小。 你可以从以下列表中选择一个选项。

- 2048 位
- 3072 位
- 4096 位

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>管理处于指定过期天数内的证书（预览版）

如果未充分监视的证书未在过期前旋转，则服务可能会发生中断。 此策略对于确保监视 Key Vault 中存储的证书非常重要。 建议使用不同的到期阈值（例如 180、90、60 和 30 天阈值）多次应用此策略。 此策略可用于监视和会审你的组织中的证书过期。

# <a name="key-policies"></a>[密钥策略](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>密钥的已生效时间不应超过指定的天数（预览版）

如果要确保密钥的已生效时间不超过指定的天数，可使用此策略审核密钥已生效的时长。

如果密钥已设置激活日期，此策略将计算从该密钥的激活日期到当前日期所经过的天数 。 如果天数超出你设置的阈值，该密钥将被标记为不符合策略。

如果密钥未设置激活日期，此策略将计算从该密钥的创建日期到当前日期所经过的天数 。 如果天数超出你设置的阈值，该密钥将被标记为不符合策略。

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>密钥应为指定的加密类型 RSA 或 EC（预览版）

此策略允许你限制可位于 Key Vault 中的密钥类型。 你可以使用此策略来确保密钥受 RSA、ECC 或 HSM 支持。 你可以从以下列表中选择允许使用的证书类型。

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>使用椭圆曲线加密的密钥应使用指定的曲线名称（预览版）

如果使用椭圆曲线加密（简称 ECC）密钥，则可以自定义以下列表中允许的曲线名称列表。 默认选项允许所有以下曲线名称。

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>密钥应设置到期日期（预览版）

此策略将审核密钥保管库中的所有密钥，并将未设置到期日期的密钥标记为不合规。 还可使用此策略阻止创建未设置到期日期的密钥。

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>密钥的剩余有效期应超过指定的天数（预览版）

如果密钥临近到期，组织延迟轮换密钥可能会导致服务中断。 应在密钥到期前指定的天数轮换密钥，以提供足够的时间来应对故障。 此策略将审核临近到期日期的密钥，并允许你以天为单位设置此阈值。 还可使用此策略阻止创建临近其到期日期的新密钥。

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>密钥应由硬件安全模块提供支持（预览版）

HSM 是存储密钥的硬件安全模块。 HSM 为加密密钥提供物理保护层。 加密密钥不能离开物理 HSM，物理 HSM 提供比软件密钥更高的安全级别。 某些组织具有强制使用 HSM 密钥的合规性要求。 使用此策略审核密钥保管库中存储的不受 HSM 支持的任何密钥。 还可使用此策略阻止创建不受 HSM 支持的新密钥。 此策略将应用于所有密钥类型（RSA 和 ECC）。

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>使用 RSA 加密的密钥应具有指定的最小密钥大小（预览版）

使用较小的 RSA 密钥是不安全的设计实践。 可能需要遵守强制使用最小密钥大小的审核和认证标准。 通过以下策略，可以对密钥保管库设置最小密钥大小要求。 你可以审核不满足这一最低要求的密钥。 此策略还可用于阻止创建不满足最小密钥大小要求的新密钥。

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>密钥应具有指定的最长有效期（预览版）

通过指定密钥在密钥保管库中的最长有效期（以天为单位），管理组织的合规性要求。 有效期超出你设置的阈值的密钥将被标记为不合规。 还可使用此策略阻止创建设置了超过你所指定最长有效期的到期日期的新密钥。

# <a name="secret-policies"></a>[机密策略](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>机密的已生效时间不应超过指定的天数（预览版）

如果要确保机密的已生效时间不超过指定的天数，可使用此策略审核机密已生效的时长。

如果机密已设置激活日期，此策略将计算从该机密的激活日期到当前日期所经过的天数 。 如果天数超出你设置的阈值，该机密将被标记为不符合策略。

如果机密未设置激活日期，此策略将计算从该机密的创建日期到当前日期所经过的天数 。 如果天数超出你设置的阈值，该机密将被标记为不符合策略。

### <a name="secrets-should-have-content-type-set-preview"></a>机密应设置内容类型（预览版）

任何纯文本或编码文件都可以存储为密钥保管库机密。 但是，你的组织可能想要对密码、连接字符串或存储为密钥的证书设置不同的轮换策略和限制。 内容类型标记可帮助用户查看机密对象中存储的内容，而无需读取机密的值。 可使用此策略审核未设置内容类型标记的机密。 还可使用此策略阻止创建未设置内容类型标记的新机密。

### <a name="secrets-should-have-expiration-date-set-preview"></a>机密应设置到期日期（预览版）

此策略将审核密钥保管库中的所有机密，并将未设置到期日期的机密标记为不合规。 还可使用此策略阻止创建未设置到期日期的机密。

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>机密的剩余有效期应超过指定的天数（预览版）

如果机密临近到期，组织延迟轮换机密可能会导致服务中断。 应在密钥到期前指定的天数轮换机密，以提供足够的时间来应对故障。 此策略将审核临近到期日期的机密，并允许你以天为单位设置此阈值。 还可使用此策略阻止创建临近其到期日期的新机密。

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>机密应具有指定的最长有效期（预览版）

通过指定机密在密钥保管库中的最长有效期（以天为单位），管理组织的合规性要求。 有效期超出你设置的阈值的机密将被标记为不合规。 还可使用此策略阻止创建设置了超过你所指定最长有效期的到期日期的新机密。

# <a name="key-vault-policies"></a>[密钥保管库策略](#tab/keyvault)

### <a name="key-vault-should-use-a-virtual-network-service-endpoint"></a>Key Vault 应使用虚拟网络服务终结点

此策略审核任何未配置为使用虚拟网络服务终结点的 Key Vault。

### <a name="resource-logs-in-key-vault-should-be-enabled"></a>应启用 Key Vault 中的资源日志

对启用资源日志进行审核。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索

### <a name="key-vaults-should-have-purge-protection-enabled"></a>密钥保管库应启用清除保护

恶意删除密钥保管库可能会导致永久丢失数据。 你组织中的恶意内部人员可能会删除和清除密钥保管库。 清除保护通过强制实施软删除密钥保管库的强制保留期来保护你免受内部攻击。 你的组织内的任何人都无法在软删除保留期内清除你的密钥保管库。

---

## <a name="example-scenario"></a>示例方案

你管理多个团队使用的包含 100 个证书的 Key Vault，你想要确保 Key Vault 中所有证书有效期都不超过两年。

1. 分配“证书应具有指定的最长有效期”策略，指定证书的最长有效期为 24 个月，并将策略的影响设置为“审核”。 
1. 你查看 [Azure 门户上的相容性报告](#view-compliance-results)，发现 20 个证书不合规且有效期超过两年，而剩余的证书符合要求。 
1. 你与这些证书的所有者联系，传达证书的有效期不能超过两年的新安全要求。 某些团队做出了响应，续订了 15 个最长有效期为两年或更短的证书。 其他团队不做出响应，你的 Key Vault 中仍有 5 个不合规的证书。
1. 你将分配的策略的影响改为“拒绝”。 5 个不合规证书未被吊销，且继续工作。 但是，续订的有效期不能用长于两年。 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>通过 Azure 门户启用和管理 Key Vault 策略

### <a name="select-a-policy-definition"></a>选择策略定义

1. 登录到 Azure 门户。 
1. 在搜索栏中搜索“策略”，然后选择“策略”。

    ![显示“搜索栏”的屏幕截图。](../media/policy-img1.png)

1. 在“策略”窗口中，选择“定义”。

    ![突出显示“定义”选项的屏幕截图。](../media/policy-img2.png)

1. 在“类别筛选器”中，取消选择“全选”，然后选择“Key Vault”。 

    ![显示“类别筛选器”和所选的 Key Vault 类别的屏幕截图。](../media/policy-img3.png)

1. 现在，你应该能够查看适用于 Azure Key Vault 公共预览版的所有策略。 确保你已阅读并理解上述策略指南部分，并选择要分配给某个范围的策略。  

    ![显示处于公共预览状态的策略的屏幕截图。](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>将策略分配到范围 

1. 选择要应用的策略，在此示例中，显示“管理证书有效期”策略。 单击左上角的“分配”按钮。

    ![显示“管理证书有效期”策略的屏幕截图。](../media/policy-img5.png)
  
1. 选择要应用策略的订阅。 你可以选择将范围限制为一个订阅中的单个资源组。 如果要将策略应用于整个订阅并排除某些资源组，还可以配置排除列表。 如果希望产生策略的影响（审核或拒绝），则将策略强制实施选择器设置为“启用”；如果希望关闭影响（审核或拒绝），则将策略强制实施选择器设置为“禁用”。 

    ![屏幕截图，显示可在其中选择将范围限制为一个订阅中的单个资源组的位置。](../media/policy-img6.png)

1. 单击屏幕顶部的“参数”选项卡，以指定所需的最长有效期（以月为单位）。 按照以上部分中的指导，选择“审核”或“拒绝”作为策略的影响。 选择“查看 + 创建”按钮。 

    ![显示“参数”选项卡的屏幕截图，可在其中指定所需的最长有效期（以月为单位）。](../media/policy-img7.png)

### <a name="view-compliance-results"></a>查看合规性结果

1. 返回到“策略”边栏选项卡，然后选择“合规性”选项卡。单击要查看其合规结果的策略分配。

    ![显示“合规性”选项卡的屏幕截图，可在其中选择要查看其合规性结果的策略分配。](../media/policy-img8.png)

1. 从此页中，可以按合规或不合规的保管库筛选结果。 可在此处查看策略分配范围内不合规的 Key Vault 的列表。 如果保管库中的任何组件（证书）不合规，则将保管库视为不合规。 可以选择单独的保管库来查看各个不合规组件（证书）。 


    ![屏幕截图，显示了策略分配范围内不合规的 Key Vault 的列表。](../media/policy-img9.png)

1. 查看保管库中不合规的组件的名称


    ![屏幕截图，显示了可在其中查看保管库中不合规的组件名称的位置。](../media/policy-img10.png)

1. 如果需要检查是否拒绝用户在 Key Vault 中创建资源，你可以单击“组件事件(预览版)”选项卡，以查看已拒绝的证书操作的摘要以及请求的请求者和时间戳。 


    ![Azure 密钥保管库的工作原理概述](../media/policy-img11.png)

## <a name="feature-limitations"></a>功能限制

分配具有“拒绝”影响的策略可能需要多达 30 分钟（平均情况）和 1 小时（最坏情况），才能开始拒绝创建不合规的资源。 保管库中现有组件的策略评估可能需要长达 1 小时（平均情况）和 2 小时（最坏情况），才能在门户 UI 中查看合规性结果。 如果合规性结果显示为“未启动”，则可能是由于以下原因：
- 尚未完成策略评估。 在最坏情况下，初始评估延迟可能长达 2 小时。 
- 策略分配的范围中没有 Key Vault。
- 策略分配的范围中没有带证书的 Key Vault。

> [!NOTE]
> Azure Policy [资源提供程序模式](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)（如 Azure Key Vault 的模式）提供了有关[组件合规性](../../governance/policy/how-to/get-compliance-data.md#component-compliance)页上合规性的信息。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure Policy 服务](../../governance/policy/overview.md)的详细信息
- 请参阅 Key Vault 示例：[Key Vault 内置策略定义](../../governance/policy/samples/built-in-policies.md#key-vault)
- 了解有关[密钥保管库的 Azure 安全基准指南](/security/benchmark/azure/baselines/key-vault-security-baseline?source=docs#network-security)
