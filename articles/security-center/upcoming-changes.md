---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/10/2021
ms.author: memildin
ms.openlocfilehash: 6a98a5dfbdf47ad9e2b74415786c240b32b3b499
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754684"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改       | 预计更改日期 |
|----------------------|---------------------------|
| [即将弃用预览版警报：ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | 2021 年 10 月|
| [正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| 2021 年 10 月|
| [关于管理 Endpoint Protection 解决方案的更改建议](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | 2021 年 11 月| 
| [清单中显示本地计算机时将使用不同的资源名称模板](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | 2021 年 11 月    |
| [针对标识建议进行了多项更改](#multiple-changes-to-identity-recommendations)                                                                                          | 2021 年 11 月    |
| [针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)                              | 2022 年第 1 季度    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>即将弃用预览版警报：ARM.MCAS_ActivityFromAnonymousIPAddresses

**预计更改日期：** 2021 年 10 月

我们即将弃用以下预览版警报：

|警报名称| 说明|
|----------------------|---------------------------|
|预览 - 来自有风险的 IP 地址的活动<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|检测到来自已被标识为匿名代理 IP 地址的 IP 地址的用户活动。<br>这些代理被想要隐藏其设备 IP 地址的用户使用，并可能用于恶意目的。 此项检测使用可以减少误报的机器学习算法，例如在组织中被用户广泛使用的错误标记的 IP 地址。<br>需要有效的 Microsoft Cloud App Security 许可证。|
|||

我们已创建新的警报，它们可提供此信息并为其做出补充。 此外，较新的警报（ARM_OperationFromSuspiciousIP、ARM_OperationFromSuspiciousProxyIP）不需要 Microsoft Cloud App Security 的许可证。

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013

**预计更改日期：** 2021 年 10 月

将从安全中心的监管合规性仪表板中删除 ISO 27001 的旧版实现。 如果要跟踪 ISO 27001 与安全中心的合规性，请针对所有相关管理组或订阅加入新的 ISO 27001:2013 标准，很快将会从仪表板中删除当前的旧版 ISO 27001。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="安全中心的监管合规性仪表板，其中显示了有关删除 ISO 27001 的旧版实现的消息。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>关于管理 Endpoint Protection 解决方案的更改建议

**预计更改日期：** 2021 年 11 月

2021 年 8 月，我们就如何在计算机上部署和维护 Endpoint Protection 解决方案，增加了两项新预览建议。 有关完整的详细信息，请参阅[发行说明](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)。

正式发布这些建议时，它们将替换以下现有建议：

- **应在计算机上安装 Endpoint Protection** 将替换：
    - [在虚拟机上安装终结点保护解决方案（密钥：83f577bd-a1b6-b7e1-0891-12ca19d1e6df）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [在计算机上安装终结点保护解决方案（密钥：383cf3bc-fdf9-4a02-120a-3e7e36c6bfee）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **应在计算机上解决 Endpoint protection 运行状况问题** 将替换具有相同名称的现有建议。 这两个建议有不同的评估密钥：
    - 预览版建议的评估密钥：37a3689a-818e-4a0e-82ac-b1392b9bb000
    - GA 建议的评估密钥：3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

了解详细信息：
- [安全中心的受支持 Endpoint Protection 解决方案](security-center-services.md#endpoint-supported)
- [这些建议如何评估部署的解决方案的状态](security-center-endpoint-protection.md)



### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>清单中显示本地计算机时使用不同的资源名称模板

**预计更改日期：** 2021 年 11 月

为了改进[资产清单](asset-inventory.md)中资源的表示形式，我们将从用于命名本地计算机的模板中删除 "source-computer-IP" 元素。

- 当前格式：``machine-name_source-computer-id_VMUUID``
- 更新后：``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>针对标识建议进行了多项更改

**预计更改日期：** 2021 年 11 月

安全中心包含多项用于改进用户和帐户管理的建议。 我们将在 11 月做出如下更改。

- 改进刷新间隔 - 当前，标识建议的刷新间隔为 24 小时。 更新后间隔将减少到 12 小时。

- 帐户豁免功能 - 安全中心具有许多功能，可用于进行体验自定义并确保安全功能分数反映组织的安全优先级。 安全建议的豁免选项就是其中一项功能。 如需完整的概述和说明，请参阅[从安全功能分数中豁免资源和建议](exempt-resource.md)。 通过此更新，你将能够豁免下表中列出的八项建议对特定帐户的评估。

    通常，将从 MFA 建议中豁免紧急“破窗”帐户，因为此类帐户通常被有意排除在组织的 MFA 要求之外。 或者，你可能拥有想要允许其访问 MFA 但未为其启用 MFA 的外部帐户。

    > [!TIP]
    > 豁免某个帐户时，它不会显示为运行不正常，也不会导致订阅看起来不正常。

    |建议| 评估密钥|
    |-|-|
    |[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/94290b00-4d0c-d7b4-7cea-064a9554e681)|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/151e82c5-5341-a74b-1eb0-bc38d2c84bb5)|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |应在对订阅拥有写入权限的帐户上启用 MFA|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/c3b6ae71-f1f0-31b4-e6c1-d5951285d03d)|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |[应从订阅中删除拥有读取权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b)|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |[应从订阅中删除具有写入权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/04e7147b-0deb-9796-2e5c-0336343ceb3d)|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)|e52064aa-6853-e252-a11e-dffc675689c2|
    |[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- 建议重命名 - 更新将重命名两条建议。 我们还将修改建议说明。 评估密钥保持不变。 


    |属性  |当前值  | 更新后|
    |---------|---------|---------|
    |评估密钥     | e52064aa-6853-e252-a11e-dffc675689c2        | 不变|
    |名称     |[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)         |应清除在 Active Directory 中被阻止且具有所有者权限的帐户的订阅        |
    |说明     |应从订阅中删除已被阻止登录的用户帐户。<br>这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。|应从订阅中删除被阻止登录到 Active Directory 的用户帐户。 这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br>在 [Azure 标识管理和访问控制安全最佳做法](/azure/security/fundamentals/identity-management-best-practices.md)中详细了解如何保护标识外围。|
    |相关策略     |[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)         |应清除在 Active Directory 中被阻止且具有所有者权限的帐户的订阅 |
    |||

    |属性  |当前值  | 更新后|
    |---------|---------|---------|
    |评估密钥     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | 不变|
    |名称     |[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|应清除在 Active Directory 中被阻止且具有读写权限的帐户的订阅|
    |说明     |应从订阅中删除已被阻止登录的用户帐户。<br>这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。|应从订阅中删除被阻止登录到 Active Directory 的用户帐户。 这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br>在 [Azure 标识管理和访问控制安全最佳做法](/azure/security/fundamentals/identity-management-best-practices.md)中详细了解如何保护标识外围。|
    |相关策略     |[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)|应清除在 Active Directory 中被阻止且具有读写权限的帐户的订阅|
    |||
 




### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强

**预计更改日期：** 2022 年第 1 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。


## <a name="next-steps"></a>后续步骤

有关安全中心的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)
