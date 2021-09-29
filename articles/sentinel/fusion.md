---
title: Azure Sentinel 中的高级多阶段攻击检测
description: 使用 Azure Sentinel 中的 Fusion 技术来减轻警报疲劳，并创建基于高级多阶段攻击检测的可操作事件。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2021
ms.author: yelevin
ms.openlocfilehash: 090e82a3efd27629633772b0549ee6d2452e159d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818936"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的高级多阶段攻击检测

> [!IMPORTANT]
> 某些 Fusion 检测（如下文所示）目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

使用基于机器学习的 Fusion 技术，Azure Sentinel 可以通过识别在不同终止链阶段观测到的异常行为与可疑活动的组合，来自动检测多阶段攻击。 Azure Sentinel 根据这些发现结果生成事件，否则很难捕获这些事件。 这些事件由两个或者更多个警报或活动构成。 根据设计，这些事件具有数量少、保真度高和严重性高的特点。

此项检测技术已根据你的环境进行自定义，不仅可以减少[误报](false-positives.md)率，而且还能在信息有限或缺失的情况下检测到攻击。



## <a name="configuration-for-advanced-multistage-attack-detection"></a>高级多阶段攻击检测的配置

### <a name="enable-fusion-rule"></a>启用 Fusion 规则

此项检测默认已在 Azure Sentinel 中启用。 若要检查状态，或者要在使用替代解决方案创建基于多个警报的事件时禁用此项检测，请使用以下说明：

1. 如果尚未这样做，请登录到 [Azure 门户](https://portal.azure.com)并输入 Azure Sentinel。

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 选择“活动规则”选项卡，然后通过筛选“Fusion”规则类型列表，在“名称”列中找到“高级多阶段攻击检测”   。 检查“状态”列确认此项检测是已启用还是已禁用。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}" lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. 若要更改状态，请选择此条目，然后在“高级多阶段攻击检测”预览边栏选项卡上选择“编辑” 。

1. 在“规则创建向导”边栏选项卡上，系统已自动选择状态更改，因此请依次选择“下一步: 查看”、“保存”  。 

 由于“Fusion”规则类型仅包含一个不可修改的规则，因此规则模板不适用于此规则类型。

> [!NOTE]
> Azure Sentinel 目前使用 30 天的历史数据来训练机器学习系统。 此数据在通过机器学习管道时，始终会使用 Microsoft 的密钥进行加密。 但是，如果在 Azure Sentinel 工作区中启用了 CMK，则不会使用[客户管理的密钥 (CMK)](customer-managed-keys.md) 加密训练数据。 若要选择禁用 Fusion，请导航到“Azure Sentinel”\>“配置”\>“分析”\>“活动规则”\>“高级多阶段攻击检测”，然后在“状态”列中选择“禁用”    。

### <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>为 Fusion 检测配置计划分析规则

> [!IMPORTANT]
>
> - 使用分析规则警报的基于 Fusion 的检测目前以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Fusion 可以使用一组[计划分析规则](detect-threats-custom.md)生成的警报来检测多阶段攻击。 建议执行以下步骤来配置并启用这些规则，以充分利用 Azure Sentinel 的 Fusion 功能。

1. 使用以下计划分析规则模板（可在“分析”边栏选项卡中的“规则模板”选项卡中找到）创建新规则  。 单击模板库中的规则名称，然后单击预览窗格中的“创建规则”：

    - [Cisco - 被防火墙阻止但已成功登录到 Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml)
    - [Fortinet - 检测到信标模式](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml)
    - [多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml)
    - [用户多次重置密码](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml)
    - [出现了以往未曾出现过的新管理员帐户活动](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Hunting%20Queries/OfficeActivity/new_adminaccountactivity.yaml)
    - [罕见的应用程序同意](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml)
    - [通过以前未曾看到过的 IP 运行 SharePointFileOperation](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml)
    - [可疑的资源部署](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml)

    > [!NOTE]
    > 对于 Fusion 使用的计划分析规则集，ML 算法将对模板中提供的 KQL 查询执行模糊匹配。 重命名模板不会影响 Fusion 检测。

1. 查看这些计划规则的实体映射。 使用[实体映射配置部分](map-data-fields-to-entities.md)将查询结果中的参数映射到 Azure Sentinel 识别的实体。 由于 Fusion 基于实例（例如用户帐户或 IP 地址）关联警报，因此 ML 算法在没有实体信息的情况下无法执行警报匹配 。

1. 请查看分析规则详细信息中的策略。 Fusion ML 算法使用 MITRE ATT&CK 策略信息来检测多阶段攻击，用于标记分析规则的策略将显示在生成的事件中。 如果传入的警报缺少策略信息，Fusion 计算可能会受到影响。

1. 根据需要调整警报阈值。 Fusion 基于计划分析规则引发的警报生成事件。 如果你想要减少特定分析规则的 Fusion 事件数，请根据需要调整警报阈值。 如果你不想要接收任何基于该规则的事件，也可以禁用特定的分析规则。

## <a name="attack-detection-scenarios"></a>攻击检测方案

以下部分列出了 Azure Sentinel 寻求在其中使用 Fusion 技术的关联方案类型（按威胁分类分组）。

如前所述，由于 Fusion 会关联各种产品中的多个安全警报来检测高级多阶段攻击，因此成功的 Fusion 检测将显示为 Azure Sentinel“事件”页上的“Fusion 事件”，而不是显示为“日志”中“安全警报”表内的“警报”    。

若要启用这些由 Fusion 支持的攻击检测方案，必须使用关联的 Azure Sentinel 数据连接器引入列出的任何数据源。

> [!NOTE]
> 其中一些方案目前为预览版。 它们都带有这样的标示。

## <a name="compute-resource-abuse"></a>计算资源滥用

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>以可疑方式登录到 Azure Active Directory 后执行多次 VM 创建活动
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、造成影响 

MITRE ATT&CK 方法：有效帐户 (T1078)、资源劫持 (T1496)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，在单个会话中创建了异常数量的 VM。 这种类型的警报以较高的可信度表明，在 Fusion 事件说明中提到的帐户已遭入侵，并已用于创建新的 VM 来实现未经授权的目的，例如运行加密货币挖矿操作。 可疑 Azure AD 登录警报和多个 VM 创建活动警报的排列方式如下：

- 无法前往非典型位置，进而执行多次 VM 创建活动

- 从不熟悉的位置发起登录事件，进而执行多次 VM 创建活动

- 从受感染的设备发起登录事件，进而执行多次 VM 创建活动

- 从匿名 IP 地址发起登录事件，进而执行多次 VM 创建活动

- 从凭据已泄露的用户发起登录事件，进而执行多次 VM 创建活动

## <a name="credential-access"></a>凭据访问
（新的威胁分类）

### <a name="new-multiple-passwords-reset-by-user-following-suspicious-sign-in"></a>新功能！ 用户在以可疑方式登录后重置了多个密码
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、凭据访问

MITRE ATT&CK 方法：有效帐户 (T1078)、暴力破解 (T1110)

数据连接器源：Azure Sentinel（计划分析规则）、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户在以可疑方式登录到 Azure AD 帐户后重置了多个密码。 此证据表明 Fusion 事件说明中提到的帐户已遭入侵，并已用于执行多次密码重置，以获取对多个系统和资源的访问权限。 帐户操控（包括密码重置）有助于攻击者保持对凭据的访问权限，以及在环境中保持特定的权限级别。 可疑 Azure AD 登录警报和重置多个密码警报的排列方式如下：

- 无法前往非典型位置，进而重置多个密码

- 从不熟悉的位置发起登录事件，进而重置多个密码

- 从受感染的设备发起登录事件，进而重置多个密码

- 从匿名 IP 发起登录事件，进而重置多个密码

- 从凭据已泄露的用户发起登录事件，进而重置多个密码

### <a name="new-suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>新功能！ 通过一个多次无法登录到 Azure AD 的 IP 以可疑方式登录时，碰巧成功登录到 Palo Alto VPN
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、凭据访问

MITRE ATT&CK 方法：有效帐户 (T1078)、暴力破解 (T1110)

数据连接器源：Azure Sentinel（计划分析规则）、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人通过一个在类似时间范围内多次无法登录到 Azure AD 的 IP 地址以可疑方式登录到 Azure AD 帐户时，碰巧成功通过 Palo Alto VPN 登录。 尽管这不能算作多阶段攻击的证据，但将这两个低保真警报相关联会得到一个高保真事件，表明有人对组织网络进行恶意的初始访问。 或者，这可能表明攻击者尝试使用暴力破解方法来访问 Azure AD 帐户。 可疑 Azure AD 登录警报和“多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN”警报的排列方式如下：

- 无法前往非典型位置，但碰巧使用一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN

- 从不熟悉的位置发起登录事件，并碰巧使用一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN

- 从受感染的设备发起登录事件，并碰巧使用一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN

- 从匿名 IP 发起登录事件，并碰巧使用一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN

- 从凭据已泄露的用户发起登录事件，并碰巧使用一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN

## <a name="credential-harvesting"></a>凭据搜集
（新的威胁分类）
### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>以可疑方式登录后执行恶意的凭据盗窃工具

MITRE ATT&CK 策略：初始访问、凭据访问

MITRE ATT&CK 方法：有效帐户 (T1078)、OS 凭据转储 (T1003)

数据连接器源：Azure Active Directory 标识保护、Microsoft Defender for Endpoint

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 后，执行了某个已知的凭据盗窃工具。 此证据以较高的可信度表明，警报说明中提到的用户帐户已遭入侵，并且可能已成功使用 Mimikatz 之类的工具从系统中搜集了密钥、明文密码和/或密码哈希等凭据。 攻击者可以凭借搜集到的凭据访问敏感数据、提升特权，并/或在整个网络中横向移动。 可疑 Azure AD 登录警报和恶意凭据盗窃工具警报的排列方式如下：

- 无法前往非典型位置，进而执行恶意的凭据盗窃工具

- 从不熟悉的位置发起登录事件，进而执行恶意的凭据盗窃工具

- 从受感染的设备发起登录事件，进而执行恶意的凭据盗窃工具

- 从匿名 IP 地址发起登录事件，进而执行恶意的凭据盗窃工具

- 从凭据已泄露的用户发起登录事件，进而执行恶意的凭据盗窃工具

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>以可疑方式登录后执行可疑的凭据盗窃活动

MITRE ATT&CK 策略：初始访问、凭据访问

MITRE ATT&CK 方法：有效帐户 (T1078)、密码存储中的凭据 (T1555)、OS 凭据转储 (T1003)

数据连接器源：Azure Active Directory 标识保护、Microsoft Defender for Endpoint

说明：这种类型的 Fusion 事件表明，在发生可疑的 Azure AD 登录后，发生了与凭据盗窃模式关联的活动。 此证据以较高的可信度表明，警报说明中提到的用户帐户已遭入侵，并已用于窃取密钥、明文密码、密码哈希等凭据。 攻击者可以凭借盗取到的凭据访问敏感数据、提升特权，并/或在整个网络中横向移动。 可疑 Azure AD 登录警报和凭据盗窃活动警报的排列方式如下：

- 无法前往非典型位置，进而执行可疑的凭据盗窃活动

- 从不熟悉的位置发起登录事件，进而执行可疑的凭据盗窃活动

- 从受感染的设备发起登录事件，进而执行可疑的凭据盗窃活动

- 从匿名 IP 地址发起登录事件，进而执行可疑的凭据盗窃活动

- 从凭据已泄露的用户发起登录事件，进而执行可疑的凭据盗窃活动

## <a name="crypto-mining"></a>加密货币挖矿
（新的威胁分类）

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>在以可疑方式登录后执行加密货币挖矿活动

MITRE ATT&CK 策略：初始访问、凭据访问

MITRE ATT&CK 方法：有效帐户 (T1078)、资源劫持 (T1496)

数据连接器源：Azure Active Directory 标识保护、Azure Defender（Azure 安全中心）

说明：这种类型的 Fusion 事件表明发生了与 Azure AD 帐户的可疑登录关联的加密货币挖矿活动。 此证据以较高的可信度表明，警报说明中提到的用户帐户已遭入侵，并已用于劫持环境中的资源来挖掘加密货币。 这可能会导致算力资源枯竭，并/或导致云使用费远远超过预期。 可疑 Azure AD 登录警报和加密货币挖矿活动警报的排列方式如下：  

- 无法前往非典型位置，进而执行加密货币挖矿活动

- 从不熟悉的位置发起登录事件，进而执行加密货币挖矿活动

- 从受感染的设备发起登录事件，进而执行加密货币挖矿活动

- 从匿名 IP 地址发起登录事件，进而执行加密货币挖矿活动

- 从凭据已泄露的用户发起登录事件，进而执行加密货币挖矿活动

## <a name="data-destruction"></a>数据销毁

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后批量删除文件

MITRE ATT&CK 策略：初始访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、数据销毁 (T1485)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户在以可疑方式登录到 Azure AD 帐户后删除了异常数量的唯一文件。 此证据表明，Fusion 事件说明中提到的帐户可能已遭入侵，并已用于销毁数据以实现恶意目的。 可疑 Azure AD 登录警报和批量文件删除警报的排列方式如下：  

- 无法前往非典型位置，进而执行批量文件删除

- 从不熟悉的位置发起登录事件，进而执行批量文件删除

- 从已受感染的设备发起登录事件，进而执行批量文件删除

- 从匿名 IP 地址发起登录事件，进而执行批量文件删除

- 从凭据已泄露的用户发起登录事件，进而执行批量文件删除

### <a name="new-mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>新功能！ 通过 Cisco 防火墙设备阻止的 IP 成功登录到 Azure AD 后批量删除文件
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、数据销毁 (T1485)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，尽管用户的 IP 地址已被 Cisco 防火墙设备阻止，但该用户仍然通过该 IP 地址成功登录到了 Azure AD，然后删除了异常数量的唯一文件。 此证据表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于销毁数据以实现恶意目的。 由于防火墙已阻止该 IP，因此，通过该 IP 成功登录到 Azure AD 可能表示出现了可疑活动，并可能表明用户帐户的凭据已泄露。

### <a name="new-mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>新功能！ 通过一个多次无法登录到 Azure AD 的 IP 成功登录到 Palo Alto VPN 后批量删除文件
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、凭据访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、暴力破解 (T1110)、数据销毁 (T1485)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，某个用户使用一个在类似时间范围内多次无法登录到 Azure AD 的 IP 地址，通过 Palo Alto VPN 成功登录后删除了异常数量的唯一文件。 此证据表明，可能有人通过暴力破解方法入侵了 Fusion 事件说明中提到的用户帐户，并将其用于销毁数据以实现恶意目的。

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行可疑的电子邮件删除活动
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、造成影响 

MITRE ATT&CK 方法：有效帐户 (T1078)、数据销毁 (T1485)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，在单个会话中删除了异常数量的电子邮件。 此证据表明，Fusion 事件说明中提到的帐户可能已遭入侵，并已用于销毁数据以实现恶意目的，例如，给组织造成损害，或者隐藏与垃圾邮件相关的电子邮件活动。 可疑 Azure AD 登录警报和可疑电子邮件删除活动警报的排列方式如下：   

- 无法前往非典型位置，进而执行可疑的电子邮件删除活动

- 从不熟悉的位置发起登录事件，进而执行可疑的电子邮件删除活动

- 从已受感染的设备发起登录事件，进而执行可疑的电子邮件删除活动

- 从匿名 IP 地址发起登录事件，进而执行可疑的电子邮件删除活动

- 从凭据已泄露的用户发起登录事件，进而执行可疑的电子邮件删除活动

## <a name="data-exfiltration"></a>数据外泄

### <a name="new-mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>新功能！ 在执行最近未曾出现过的新管理帐户活动后执行邮件转发活动
此方案属于此列表中的两种威胁分类：“数据外泄”和“恶意管理活动” 。 为清晰起见，它在两个部分中显示。

此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、收集、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、电子邮件收集 (T1114)、通过 Web 服务外泄 (T1567)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，已创建新的 Exchange 管理员帐户，或者现有的 Exchange 管理员帐户在过去两周内首次执行了某种管理操作，然后该帐户执行了一些对于管理员帐户而言不常见的邮件转发操作。 此证据表明，Fusion 事件说明中提到的用户帐户已遭入侵或受到操控，并已用于从组织网络中外泄数据。

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后批量下载文件

MITRE ATT&CK 策略：初始访问、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户在以可疑方式登录到 Azure AD 帐户后下载了异常数量的文件。 这以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于从组织网络中外泄数据。 可疑 Azure AD 登录警报和批量文件下载警报的排列方式如下：  

- 无法前往非典型位置，进而执行批量文件下载

- 从不熟悉的位置发起登录事件，进而执行批量文件下载

- 从已受感染的设备发起登录事件，进而执行批量文件下载

- 从匿名 IP 发起登录事件，进而执行批量文件下载

- 从凭据已泄露的用户发起登录事件，进而执行批量文件下载

### <a name="new-mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>新功能！ 通过 Cisco 防火墙设备阻止的 IP 成功登录到 Azure AD 后批量下载文件
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、通过 Web 服务外泄 (T1567)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，尽管用户的 IP 地址已被 Cisco 防火墙设备阻止，但该用户仍然通过该 IP 地址成功登录到了 Azure AD，然后下载了异常数量的文件。 这可能表示攻击者在入侵用户帐户后试图从组织网络中外泄数据。 由于防火墙已阻止该 IP，因此，通过该 IP 成功登录到 Azure AD 可能表示出现了可疑活动，并可能表明用户帐户的凭据已泄露。

### <a name="new-mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip"></a>新功能！ 通过未见过的 IP 批量下载文件并碰巧执行了 SharePoint 文件操作
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：外泄

MITRE ATT&CK 方法：通过 Web 服务外泄 (T1567)、数据传输大小限制 (T1030)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，某个通过以往未曾见过的 IP 地址建立连接的用户下载了异常数量的文件。 尽管这不是多阶段攻击的证据，但将这两个低保真警报相关联可以得到一个高保真度事件，表明攻击者试图通过一个可能已遭入侵的用户帐户从组织网络中外泄数据。 在稳定的环境中，通过以前未曾见过的 IP 建立这种连接可能是未经授权的操作，尤其是与下载量高峰关联时，这可能与大规模的文档外泄有关。

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行批量文件共享

MITRE ATT&CK 策略：初始访问、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、通过 Web 服务外泄 (T1567)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，与其他人共享了数量超过特定阈值的文件。 这以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于通过与未经授权的用户共享文档、电子表格等文件，出于恶意目的从组织网络中外泄数据。 可疑 Azure AD 登录警报和批量文件共享警报的排列方式如下：  

- 无法前往非典型位置，进而执行批量文件共享

- 从不熟悉的位置发起登录事件，进而执行批量文件共享

- 从已受感染的设备发起登录事件，进而执行批量文件共享

- 从匿名 IP 地址发起登录事件，进而执行批量文件共享

- 从凭据已泄露的用户发起登录事件，进而执行批量文件共享

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行多个 Power BI 报表共享活动 
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、外泄 

MITRE ATT&CK 方法：有效帐户 (T1078)、通过 Web 服务外泄 (T1567)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，在单个会话中共享了异常数量的 Power BI 报表。 这以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于通过与未经授权的用户共享 Power BI 报表，出于恶意目的从组织网络中外泄数据。 可疑 Azure AD 登录警报和多个 Power BI 报表共享活动的排列方式如下：  

- 无法前往非典型位置，进而执行多个 Power BI 报表共享活动

- 从不熟悉的位置发起登录事件，进而执行多个 Power BI 报表共享活动

- 从已受感染的设备发起登录事件，进而执行多个 Power BI 报表共享活动

- 从匿名 IP 地址发起登录事件，进而执行多个 Power BI 报表共享活动

- 从凭据已泄露的用户发起登录事件，进而执行多个 Power BI 报表共享活动

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行 Office 365 邮箱外泄

MITRE ATT&CK 策略：初始访问、外泄、收集

MITRE ATT&CK 方法：有效帐户 (T1078)、电子邮件收集 (T1114)、自动外泄 (T1020)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，对用户的收件箱设置了可疑的收件箱转发规则。 这以较高的可信度表明，Fusion 事件说明中提到的用户帐户已遭入侵，并已用于在真实用户不知情的情况下，通过启用邮箱转发规则从组织网络中外泄数据。 可疑 Azure AD 登录警报和 Office 365 邮箱外泄警报的排列方式如下：

- 无法前往非典型位置，进而执行 Office 365 邮箱外泄

- 从不熟悉的位置发起登录事件，进而执行 Office 365 邮箱外泄

- 从已受感染的设备发起登录事件，进而执行 Office 365 邮箱外泄

- 从匿名 IP 地址发起登录事件，进而执行 Office 365 邮箱外泄

- 从凭据已泄露的用户发起登录事件，进而执行 Office 365 邮箱外泄

### <a name="new-sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection"></a>新功能！ 检测到恶意软件后发现有人通过以前未曾见过的 IP 执行 SharePoint 文件操作
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：外泄、防御规避

MITRE ATT&CK 方法：数据传输大小限制 (T1030)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，攻击者试图使用恶意软件通过 SharePoint 下载或共享大量数据，以此外泄数据。 在稳定的环境中，通过以前未曾见过的 IP 建立这种连接可能是未经授权的操作，尤其是与下载量高峰关联时，这可能与大规模的文档外泄有关。 

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后设置可疑收件箱操作规则
此方案属于此列表中的两种威胁分类：“数据外泄”和“横向移动” 。 为清晰起见，它在两个部分中显示。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、横向移动、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、内部鱼叉式网络钓鱼 (T1534)、自动外泄 (T1020)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，对用户的收件箱设置了异常的收件箱规则。 此证据以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于操控用户的电子邮件收件箱规则以实现恶意目的，攻击者可能是想要从组织网络中外泄数据。 或者，可能是攻击者试图从组织内部生成网络钓鱼电子邮件（绕过针对来自外部源的电子邮件实施的网络钓鱼检测机制），以便能够通过获取对其他用户和/或特权帐户的访问权限来进行横向移动。 可疑 Azure AD 登录警报和可疑收件箱操作规则警报的排列方式如下：

- 无法前往非典型位置，进而设置可疑收件箱操作规则

- 从不熟悉的位置发起登录事件，进而设置可疑收件箱操作规则

- 从已受感染的设备发起登录事件，进而设置可疑收件箱操作规则

- 从匿名 IP 地址发起登录事件，进而设置可疑收件箱操作规则

- 从凭据已泄露的用户发起登录事件，进而设置可疑收件箱操作规则

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行可疑的 Power BI 报表共享
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、外泄 

MITRE ATT&CK 方法：有效帐户 (T1078)、通过 Web 服务外泄 (T1567)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，执行了可疑的 Power BI 报告共享活动。 该共享活动之所以标识为可疑，是因为 Power BI 报表包含使用自然语言处理标识的敏感信息，并且该报表是使用外部电子邮件地址共享的、已发布到 Web，或者以快照形式传递到了外部订阅的电子邮件地址。 这条警报以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于通过与未经授权的用户共享 Power BI 报表，出于恶意目的从组织中外泄敏感数据。 可疑 Azure AD 登录警报和可疑 Power BI 报表共享的排列方式如下：  

- 无法前往非典型位置，进而执行可疑的 Power BI 报表共享

- 从不熟悉的位置发起登录事件，进而执行可疑的 Power BI 报表共享

- 从已受感染的设备发起登录事件，进而执行可疑的 Power BI 报表共享

- 从匿名 IP 地址发起登录事件，进而执行可疑的 Power BI 报表共享

- 从凭据已泄露的用户发起登录事件，进而执行可疑的 Power BI 报表共享

## <a name="denial-of-service"></a>拒绝服务

### <a name="multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行多个 VM 删除活动
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、终结点拒绝服务 (T1499)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，在单个会话中删除了异常数量的 VM。 这以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于试图干扰或破坏组织的云环境。 可疑 Azure AD 登录警报和多个 VM 删除活动警报的排列方式如下：  

- 无法前往非典型位置，进而执行多次 VM 删除活动

- 从不熟悉的位置发起登录事件，进而执行多次 VM 删除活动

- 从已受感染的设备发起登录事件，进而执行多次 VM 删除活动

- 从匿名 IP 地址发起登录事件，进而执行多次 VM 删除活动

- 从凭据已泄露的用户发起登录事件，进而执行多次 VM 删除活动

## <a name="lateral-movement"></a>横向移动

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行 Office 365 模拟

MITRE ATT&CK 策略：初始访问、横向移动

MITRE ATT&CK 方法：有效帐户 (T1078)、内部鱼叉式网络钓鱼 (T1534)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户在以可疑方式登录到 Azure AD 帐户后执行了异常数量的模拟操作。 在某些软件中，有些选项可让用户模拟其他用户。 例如，电子邮件服务允许用户授权其他用户代表他们发送电子邮件。 此警报以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于执行模拟活动来实现恶意目的，例如，发送网络钓鱼电子邮件来分发恶意软件，或实现横向移动。 可疑 Azure AD 登录警报和 Office 365 模拟警报的排列方式如下：  

- 无法前往非典型位置，进而执行 Office 365 模拟

- 从不熟悉的位置发起登录事件，进而执行 Office 365 模拟

- 从已受感染的设备发起登录事件，进而执行 Office 365 模拟

- 从匿名 IP 地址发起登录事件，进而执行 Office 365 模拟

- 从凭据已泄露的用户发起登录事件，进而执行 Office 365 模拟
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后设置可疑收件箱操作规则
此方案属于此列表中的两种威胁分类：“横向移动”和“数据外泄” 。 为清晰起见，它在两个部分中显示。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、横向移动、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、内部鱼叉式网络钓鱼 (T1534)、自动外泄 (T1020)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式登录到 Azure AD 帐户后，对用户的收件箱设置了异常的收件箱规则。 此证据以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于操控用户的电子邮件收件箱规则以实现恶意目的，攻击者可能是想要从组织网络中外泄数据。 或者，可能是攻击者试图从组织内部生成网络钓鱼电子邮件（绕过针对来自外部源的电子邮件实施的网络钓鱼检测机制），以便能够通过获取对其他用户和/或特权帐户的访问权限来进行横向移动。 可疑 Azure AD 登录警报和可疑收件箱操作规则警报的排列方式如下：

- 无法前往非典型位置，进而设置可疑收件箱操作规则

- 从不熟悉的位置发起登录事件，进而设置可疑收件箱操作规则

- 从已受感染的设备发起登录事件，进而设置可疑收件箱操作规则

- 从匿名 IP 地址发起登录事件，进而设置可疑收件箱操作规则

- 从凭据已泄露的用户发起登录事件，进而设置可疑收件箱操作规则

## <a name="malicious-administrative-activity"></a>恶意管理活动

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行可疑的云应用管理活动

MITRE ATT&CK 策略：初始访问、持久保留、防御规避、横向移动、收集、外泄和造成影响

MITRE ATT&CK 方法：不适用

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，有人在以可疑方式从同一个帐户登录到 Azure AD 帐户后，在一个会话中执行了异常数量的管理活动。 此证据表明，Fusion 事件说明中提到的帐户可能已遭入侵，并已用于恶意执行任意数量的未经授权的管理操作。 这也表明拥有管理特权的帐户可能已遭入侵。 可疑 Azure AD 登录警报和可疑云应用管理活动警报的排列方式如下：  

- 无法前往非典型位置，进而执行可疑的云应用管理活动

- 从不熟悉的位置发起登录事件，进而执行可疑的云应用管理活动

- 从已受感染的设备发起登录事件，进而执行可疑的云应用管理活动

- 从匿名 IP 地址发起登录事件，进而执行可疑的云应用管理活动

- 从凭据已泄露的用户发起登录事件，进而执行可疑的云应用管理活动

### <a name="new-mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>新功能！ 在执行最近未曾出现过的新管理帐户活动后执行邮件转发活动
此方案属于此列表中的两种威胁分类：“恶意管理活动”和“数据外泄” 。 为清晰起见，它在两个部分中显示。

此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、收集、外泄

MITRE ATT&CK 方法：有效帐户 (T1078)、电子邮件收集 (T1114)、通过 Web 服务外泄 (T1567)

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，已创建新的 Exchange 管理员帐户，或者现有的 Exchange 管理员帐户在过去两周内首次执行了某种管理操作，然后该帐户执行了一些对于管理员帐户而言不常见的邮件转发操作。 此证据表明，Fusion 事件说明中提到的用户帐户已遭入侵或受到操控，并已用于从组织网络中外泄数据。

## <a name="malicious-execution-with-legitimate-process"></a>使用合法进程执行恶意活动

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 建立可疑的网络连接，然后 Palo Alto Networks 防火墙标记了异常流量。
此方案目前为预览版。

MITRE ATT&CK 策略：执行

MITRE ATT&CK 方法：命令和脚本解释器 (T1059)

数据连接器源：Microsoft Defender for Endpoint（以前称为 Microsoft Defender 高级威胁防护，简称 MDATP）、Palo Alto Networks 

说明：这种类型的 Fusion 事件表明，有人通过 PowerShell 命令发出了出站连接请求，然后，Palo Alto Networks 防火墙检测到了异常入站活动。 此证据表明攻击者可能获取了对你网络的访问权限，并试图执行恶意操作。 PowerShell 做出遵循此模式的连接尝试可能表明存在恶意软件命令和控制活动、请求下载其他恶意软件，或者攻击者正在建立远程交互式访问。 与所有“离地”攻击一样，此活动有可能合法使用了 PowerShell。 但是，如果在执行 PowerShell 命令之后接着执行可疑的入站防火墙活动，则可以更确信 PowerShell 被恶意使用，应该进一步展开调查。 在 Palo Alto 日志中，Azure Sentinel 注重[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，当威胁受到允许时，会将流量视为可疑（可疑的数据、文件、泛洪、数据包、扫描、间谍软件、URL、病毒、漏洞、野火病毒、野火）。 另请参考对应于 Fusion 事件说明中列出的[威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)的 Palo Alto 威胁日志，以了解更多警报详细信息。

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>远程执行可疑的 WMI 命令后 Palo Alto Networks 防火墙标记了异常流量
此方案目前为预览版。

MITRE ATT&CK 策略：执行、发现

MITRE ATT&CK 方法：Windows Management Instrumentation (T1047)

数据连接器源：Microsoft Defender for Endpoint（以前称为 MDATP）、Palo Alto Networks 

说明：这种类型的 Fusion 事件表明，在系统上远程执行了 Windows 管理接口 (WMI) 命令，然后 Palo Alto Networks 防火墙检测到了可疑的入站活动。 此证据表明攻击者可能已获取对你网络的访问权限，并且正在尝试横向移动、提升特权和/或执行恶意有效负载。 与所有“离地”攻击一样，此活动有可能合法使用了 WMI。 但是，如果在远程执行 WMI 命令之后接着执行可疑的入站防火墙活动，则可以更确信 WMI 被恶意使用，应该进一步展开调查。 在 Palo Alto 日志中，Azure Sentinel 注重[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，当威胁受到允许时，会将流量视为可疑（可疑的数据、文件、泛洪、数据包、扫描、间谍软件、URL、病毒、漏洞、野火病毒、野火）。 另请参考对应于 Fusion 事件说明中列出的[威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)的 Palo Alto 威胁日志，以了解更多警报详细信息。

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>以可疑方式登录后执行可疑的 PowerShell 命令行

MITRE ATT&CK 策略：初始访问、执行

MITRE ATT&CK 方法：有效帐户 (T1078)、命令和脚本解释器 (T1059)

数据连接器源：Azure Active Directory 标识保护、Microsoft Defender for Endpoint（以前称为 MDATP）

说明：这种类型的 Fusion 事件表明，用户在以可疑方式登录到 Azure AD 帐户后执行了潜在恶意的 PowerShell 命令。 此证据以较高的可信度表明，警报说明中提到的帐户已遭入侵，并且执行了其他恶意操作。 攻击者经常使用 PowerShell 在内存中执行恶意有效负载而不会在磁盘上留下任何项目，以规避基于磁盘的安全机制（例如病毒扫描程序）的检测。 可疑 Azure AD 登录警报和可疑 PowerShell 命令警报的排列方式如下：

- 无法前往非典型位置，进而执行可疑的 PowerShell 命令行

- 从不熟悉的位置发起登录事件，进而执行可疑的 PowerShell 命令行

- 从已受感染的设备发起登录事件，进而执行可疑的 PowerShell 命令行

- 从匿名 IP 地址发起登录事件，进而执行可疑的 PowerShell 命令行

- 从凭据已泄露的用户发起登录事件，进而执行可疑的 PowerShell 命令行

## <a name="malware-c2-or-download"></a>恶意软件 C2 或下载

### <a name="new-beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service"></a>新功能！ 在用户登录某个服务多次失败后，Fortinet 检测到信标模式

此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、命令和控制

MITRE ATT&CK 方法：有效帐户 (T1078)、非标准端口 (T1571)、T1065（已停用）

数据连接器源：Azure Sentinel（计划分析规则）、Microsoft Cloud App Security

说明：这种类型的 Fusion 事件表明，用户通过某个相关内部实体登录某个服务多次失败后，发生了与信标模式一致的从内部 IP 地址到外部 IP 地址的通信模式。 这两个事件相结合可能表明受到恶意软件的感染，或者遭到入侵的主机正在进行数据外泄。 

### <a name="new-beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in"></a>新功能！ 发生可疑的 Azure AD 登录后 Fortinet 检测到信标模式

此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、命令和控制

MITRE ATT&CK 方法：有效帐户 (T1078)、非标准端口 (T1571)、T1065（已停用）

数据连接器源：Azure Sentinel（计划分析规则）、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，用户以可疑方式登录到 Azure AD 后，发生了与信标模式一致的从内部 IP 地址到外部 IP 地址的通信模式。 这两个事件相结合可能表明受到恶意软件的感染，或者遭到入侵的主机正在进行数据外泄。 Fortinet 检测到信标模式警报和可疑 Azure AD 登录警报的排列方式如下：   

- 无法前往非典型位置，导致 Fortinet 检测到信标模式

- 从不熟悉的位置发起登录事件，导致 Fortinet 检测到信标模式

- 从受感染的设备发起登录事件，导致 Fortinet 检测到信标模式

- 从匿名 IP 地址发起登录事件，导致 Fortinet 检测到信标模式

- 从凭据已泄露的用户发起登录事件，导致 Fortinet 检测到信标模式

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>向 TOR 匿名化服务发出网络请求，然后 Palo Alto Networks 防火墙标记了异常流量。
此方案目前为预览版。

MITRE ATT&CK 策略：命令和控制

MITRE ATT&CK 方法：加密通道 (T1573)、代理 (T1090)

数据连接器源：Microsoft Defender for Endpoint（以前称为 MDATP）、Palo Alto Networks 

说明：这种类型的 Fusion 事件表明，有人向 TOR 匿名化服务发出了出站连接请求，然后，Palo Alto Networks 防火墙检测到了异常入站活动。 此证据表明攻击者可能获取了对你网络的访问权限，并试图掩盖其行动和意图。 遵循此模式连接到 TOR 网络可能表明存在恶意软件命令和控制活动、请求下载其他恶意软件，或者攻击者正在建立远程交互式访问。 在 Palo Alto 日志中，Azure Sentinel 注重[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，当威胁受到允许时，会将流量视为可疑（可疑的数据、文件、泛洪、数据包、扫描、间谍软件、URL、病毒、漏洞、野火病毒、野火）。 另请参考对应于 Fusion 事件说明中列出的[威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)的 Palo Alto 威胁日志，以了解更多警报详细信息。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>与曾经有人试图未经授权访问的 IP 建立了出站连接，然后 Palo Alto Networks 防火墙标记了异常流量
此方案目前为预览版。

MITRE ATT&CK 策略：命令和控制

MITRE ATT&CK 方法：不适用

数据连接器源：Microsoft Defender for Endpoint（以前称为 MDATP）、Palo Alto Networks 

说明：这种类型的 Fusion 事件表明，与曾经有人试图未经授权访问的 IP 地址建立了出站连接，然后，Palo Alto Networks 防火墙检测到了异常活动。 此证据表明攻击者可能获取了对你的网络的访问权限。 遵循此模式的连接企图可能表明存在恶意软件命令和控制活动、请求下载其他恶意软件，或者攻击者正在建立远程交互式访问。 在 Palo Alto 日志中，Azure Sentinel 注重[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，当威胁受到允许时，会将流量视为可疑（可疑的数据、文件、泛洪、数据包、扫描、间谍软件、URL、病毒、漏洞、野火病毒、野火）。 另请参考对应于 Fusion 事件说明中列出的[威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)的 Palo Alto 威胁日志，以了解更多警报详细信息。

## <a name="persistence"></a>持久性
（新的威胁分类）

### <a name="new-rare-application-consent-following-suspicious-sign-in"></a>新功能！ 发生可疑的登录后授予了罕见的应用程序同意

此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：持久性、初始访问

MITRE ATT&CK 方法：创建帐户 (T1136)、有效帐户 (T1078)

数据连接器源：Azure Sentinel（计划分析规则）、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户在以可疑方式登录到 Azure AD 帐户后授予了应用程序同意，但该用户过去从未或者极少授予这种同意。 此证据表明，Fusion 事件说明中提到的帐户可能已遭入侵，并已用于访问或操控该应用程序以实现恶意目的。  同意应用程序、添加服务主体和添加 OAuth2PermissionGrant 通常是罕见的事件。 攻击者可以使用此类配置更改在系统上建立或保留其据点。 可疑 Azure AD 登录警报和罕见的应用程序同意警报的排列方式如下：

- 无法前往非典型位置，进而授予罕见的应用程序同意

- 从不熟悉的位置发起登录事件，进而授予罕见的应用程序同意

- 从受感染的设备发起登录事件，进而授予罕见的应用程序同意

- 从匿名 IP 发起登录事件，进而授予罕见的应用程序同意

- 从凭据已泄露的用户发起登录事件，进而授予罕见的应用程序同意

## <a name="ransomware"></a>勒索软件

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>以可疑方式登录到 Azure AD 后执行勒索软件

MITRE ATT&CK 策略：初始访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、加密数据以造成影响 (T1486)

数据连接器源：Microsoft Cloud App Security、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，已检测到有人在可疑方式登录到 Azure AD 帐户后做出了异常的用户行为，该行为表示勒索软件攻击。 这以较高的可信度表明，Fusion 事件说明中提到的帐户已遭入侵，并已用于加密数据，以勒索数据所有者或者拒绝数据所有者访问其数据。 可疑 Azure AD 登录警报和勒索软件执行警报的排列方式如下：  

- 无法前往非典型位置，进而在云应用中执行勒索软件

- 从不熟悉的位置发起登录事件，进而在云应用中执行勒索软件

- 从已受感染的设备发起登录事件，进而在云应用中执行勒索软件

- 从匿名 IP 地址发起登录事件，进而在云应用中执行勒索软件

- 从凭据已泄露的用户发起登录事件，进而在云应用中执行勒索软件

### <a name="multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview"></a>可能与检测到的勒索软件活动相关的多个警报（公共预览版）

Azure Sentinel 从以下数据源检测到多个不同类型的警报，并且这些警报可能与勒索软件活动相关时，则会生成事件：

- [Azure Defender（Azure 安全中心）](connect-azure-security-center.md)
- [用于终结点的 Microsoft Defender](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Azure Sentinel 计划分析规则](detect-threats-built-in.md#scheduled)。 Fusion 仅考虑包含策略信息的计划分析规则。

此类 Fusion 事件命名为“多个警报可能与检测到的勒索软件活动相关”，这些事件在特定时间范围内检测到相关警报时生成，并与攻击的“执行”和“防御规避”阶段相关联。

例如，如果在特定时间范围内在同一主机上触发以下警报，则 Azure Sentinel 会为可能的勒索软件活动生成事件：

- Azure Sentinel 计划警报(信息性)：Windows 错误和警告事件
- Azure Defender (中)：“GandCrab”勒索软件已被阻止
- Microsoft Defender for Endpoint (信息性)：检测到“Emotet”恶意软件
- Azure Defender (低)：检测到“Tofsee”后门程序
- Microsoft Defender for Endpoint (信息性)：检测到“Parite”恶意软件

## <a name="remote-exploitation"></a>远程恶意利用

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>以可疑方式使用攻击框架，然后 Palo Alto Networks 防火墙标记了异常流量
此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、执行、横向移动、特权提升

MITRE ATT&CK 方法：恶意利用面向公众的应用程序 (T1190)、恶意利用客户端执行 (T1203)、恶意利用远程服务 (T1210)、恶意利用特权提升 (T1068)

数据连接器源：Microsoft Defender for Endpoint（以前称为 MDATP）、Palo Alto Networks 

说明：这种类型的 Fusion 事件表明，已检测到有人以非常规方式使用协议，它类似于使用 Metasploit 之类的攻击框架；然后，Palo Alto Networks 防火墙检测到了可疑的入站活动。 这可能初步表明攻击者已恶意利用某个服务来获取对你网络资源的访问权限，或者攻击者已获取访问权限，并试图进一步恶意利用可用的系统/服务来横向移动和/或提升特权。 在 Palo Alto 日志中，Azure Sentinel 注重[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，当威胁受到允许时，会将流量视为可疑（可疑的数据、文件、泛洪、数据包、扫描、间谍软件、URL、病毒、漏洞、野火病毒、野火）。 另请参考对应于 Fusion 事件说明中列出的[威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)的 Palo Alto 威胁日志，以了解更多警报详细信息。

## <a name="resource-hijacking"></a>资源劫持
（新的威胁分类）

### <a name="new-suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in"></a>新功能！ 以前未曾见过的调用方以可疑方式登录到 Azure AD 后执行可疑的资源/资源组部署
此方案利用计划分析规则生成的警报。

此方案目前为预览版。

MITRE ATT&CK 策略：初始访问、造成影响

MITRE ATT&CK 方法：有效帐户 (T1078)、资源劫持 (T1496)

数据连接器源：Azure Sentinel（计划分析规则）、Azure Active Directory 标识保护

说明：这种类型的 Fusion 事件表明，某个用户使用最近未见过的属性以可疑方式登录到 Azure AD 帐户后，部署了一个 Azure 资源或资源组 - 这是一个罕见的活动。 这可能表明，攻击者在入侵 Fusion 事件说明中提到的用户帐户后，试图出于恶意目的部署资源或资源组。
可疑 Azure AD 登录警报和以前未曾见过的调用方执行可疑资源/资源组部署警报的排列方式如下：

- 无法前往非典型位置，导致以前未曾见过的调用方执行可疑资源/资源组部署

- 从不熟悉的位置发起登录事件，导致以前未曾见过的调用方执行可疑资源/资源组部署

- 从受感染的设备发起登录事件，导致以前未曾见过的调用方执行可疑资源/资源组部署

- 从匿名 IP 发起登录事件，导致以前未曾见过的调用方执行可疑资源/资源组部署

- 从凭据已泄露的用户发起登录事件，导致以前未曾见过的调用方执行可疑资源/资源组部署

## <a name="next-steps"></a>后续步骤

现在你已详细了解高级多阶段攻击检测，接下来你可能对以下快速入门感兴趣，其中介绍了如何洞察数据和潜在威胁：[Azure Sentinel 入门](get-visibility.md)。

如果你已准备好调查系统为你创建的事件，请参阅以下教程：[使用 Azure Sentinel 调查事件](investigate-cases.md)。