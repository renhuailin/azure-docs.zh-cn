---
title: 使用 Azure 安全基线和 Azure 安全中心强化 Windows 和 Linux OS
description: 了解 Azure 安全中心如何使用来宾配置来将 OS 强化与 Azure 安全基准中的指导进行比较
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/04/2021
ms.author: memildin
ms.openlocfilehash: 9a15a87eb5f00316f88109e05e069032de795807
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129435962"
---
# <a name="apply-azure-security-baselines-to-machines"></a>将 Azure 安全基线应用于计算机

要减少计算机的攻击面并避免已知风险，请务必尽可能安全地配置操作系统 (OS)。

Azure 安全基准包含有关 OS 强化的指导，为 [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) 和 [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md) 生成了安全基线文档。

使用本文中所述的安全建议评估环境中的计算机，并执行以下操作：

- 确定安全配置中的缺口
- 了解如何修正这些缺口

## <a name="availability"></a>可用性
|方面|详细信息|
|----|:----|
|发布状态：|预览版。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|定价：|免费|
|先决条件：|计算机必须 (1) 成为工作组成员，(2) 具有来宾配置扩展，(3) 具有系统分配的托管标识，(4) 正在运行受支持的 OS：<br>• Windows Server 2012、2012r2、2016 或 2019<br>• Ubuntu 14.04、16.04、17.04、18.04 或 20.04<br>• Debian 7、8、9 或 10<br>• CentOS 7 或 8<br>• Red Hat Enterprise Linux (RHEL) 7 或 8<br>• Oracle Linux 7 或 8<br>• SUSE Linux Enterprise Server 12|
|所需角色和权限：|要安装来宾配置扩展及其先决条件，需要在相关计算机上具有写入权限。<br>要查看建议并浏览 OS 基线数据，需要在订阅级别上具有读取权限 。|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/no-icon.png"::: 国家/地区/主权云（Azure 政府、Azure 中国世纪互联）|
|||

## <a name="what-are-the-hardening-recommendations"></a>什么是强化建议？

Azure 安全中心包含两个建议，用于检查环境中 Windows 和 Linux 计算机的配置是否符合 Azure 安全基线配置：

- 对于 Windows 计算机，[应修正 Windows 计算机上安全配置中的漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)将配置与 [Windows 安全基线](../governance/policy/samples/guest-configuration-baseline-windows.md)进行比较。
- 对于 Linux 计算机，[应修正 Linux 计算机上安全配置中的漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)将配置与 [Linux 安全基线](../governance/policy/samples/guest-configuration-baseline-linux.md)进行比较。

这些建议使用 Azure Policy 的来宾配置功能，将计算机的 OS 配置与 [Azure 安全基准](/security/benchmark/azure/overview)中定义的基线进行比较。

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>将订阅中的计算机与 OS 安全基线进行比较

将计算机与 OS 安全基线进行比较：
 
1. 在安全中心的门户页中，打开“建议”页。 
1. 搜索相关建议：
    - 对于 Windows 计算机，[应修正 Windows 计算机上的安全配置漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
    - 对于 Linux 计算机，[应修正 Linux 计算机上的安全配置漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="有关将计算机的 OS 配置与相关的 Azure 安全基线进行比较的两个建议。" lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. 在建议详细信息页上，可看到：
    1. 受影响的资源。
    1. 失败的特定安全检查。

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="有关 Windows 计算机的基线配置中的漏洞的 Windows 建议的建议详细信息页。" lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. 要了解有关特定发现结果的详细信息，请选择此发现结果。

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="详细了解有关 OS 配置与定义的安全基线的来宾配置比较中的特定发现结果。" lightbox="media/apply-security-baseline/finding-details.png":::

1. 其他调查可能性：

    - 要查看已评估的计算机的列表，请打开“受影响的资源”。
    - 要查看一台计算机的发现结果列表，请从“运行不正常的资源”选项卡中选择一台计算机。将打开一个页面，只列出该计算机的发现结果。


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>常见问题解答 - 根据安全基线强化 OS

- [如何实现部署安全配置建议的先决条件？](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [为什么计算机显示为“不适用”？](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>如何实现部署安全配置建议的先决条件？

部署具有以下先决条件的来宾配置扩展：

- 对于选定的计算机，请遵循“实施安全最佳做法”安全控制中的安全建议“应在计算机上安装来宾配置扩展” 。

- 大规模分配策略计划“部署先决条件以在虚拟机上启用来宾配置策略”。


### <a name="why-is-a-machine-shown-as-not-applicable"></a>为什么计算机显示为“不适用”？

“不适用”选项卡中的资源列表包含“原因”列 。 一些常见原因包括：

| Reason                                                            | 详细信息                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 计算机上没有可用的扫描数据                         | 在 Azure Resource Graph 中，此计算机没有任何符合性结果。 所有符合性结果均由来宾配置扩展写入 Azure Resource Graph。 可使用 [Azure 策略来宾配置-示例 ARG 查询](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration)中的示例查询来检查 Azure Resource Graph 中的数据。|
| 计算机上未安装来宾配置扩展 | 计算机缺少来宾配置扩展，这是评估 Azure 安全基线的符合性的先决条件。                               |
| 计算机上未配置系统托管标识      | 必须在计算机上部署系统分配的托管标识。                                                                                                           |
| 策略中禁用了建议                      | 在包含相关计算机的作用域上禁用评估 OS 基线的策略定义。                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>后续步骤
在本文档中，已了解如何使用安全中心的来宾配置建议将 OS 的强化与 Azure 安全基线进行比较。

要详细了解这些配置设置，请参阅：

- [Windows 安全基线](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Linux 安全基线](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Azure 安全基准](/security/benchmark/azure/overview)