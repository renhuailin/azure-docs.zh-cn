---
title: Azure Red Hat OpenShift 支持生命周期
description: 了解 Azure Red Hat OpenShift 的支持生命周期和受支持版本
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 2c1eeb97fecac449e85aa0a5d1987dc6ef2c4b4f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614263"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>支持 Azure Red Hat OpenShift 4 的生命周期

Red Hat 大约每三个月发布一次 Red Hat OpenShift 容器平台 (OCP) 的次要版本。 这些版本包括新增功能和改进。 补丁发布更为频繁（通常每周都会发布），并且仅适用于次要版本中的关键 bug 修复。 这些补丁发布可能包括针对安全漏洞或主要 bug 的修复。

Azure Red Hat OpenShift 是通过 OCP 的特定发布进行构建。 本文介绍了 Azure Red Hat OpenShift 支持的 OCP 版本以及有关升级、弃用和支持策略的详细信息。

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift 版本

Red Hat OpenShift 容器平台使用语义化版本控制。 语义化版本控制使用不同级别的版本号指定不同级别的版本控制。 下表说明了语义化版本号的不同部分，在此例中使用示例版本号 4.4.11。

|主版本 (x)|次要版本 (y)|补丁 (z)|
|-|-|-|
|4|4|11|

版本中的每个编写表示与前一版本的一般兼容性：

* 主版本：目前没有计划发布主版本。 如果发生不兼容的 API 更改或者后向兼容性受到破坏，主要版本将会更改。
* 次要版本：大约每三个月发布一次。 次要版本升级可包括新添功能、增强功能、弃用、删除、bug 修复、安全增强以及其他改进。
* 补丁：通常每周发布一次，或根据需要发布。 补丁版本升级可包括 bug 修复、安全增强以及其他改进。

客户的目标应是运行他们所运行的主版本的最新次要版本。 例如，如果生产群集为 4.4，而 4.5 是 4 系列的最新正式发布次要版本，则应尽快升级到 4.5。 

### <a name="upgrade-channels"></a>升级通道

升级通道与 Red Hat OpenShift 容器平台 (OCP) 的次要版本关联。 例如，OCP 4.4 升级通道从不会包含到 4.5 版本的升级。 升级通道仅控制版本选择，不会影响群集的版本。

Azure Red Hat OpenShift 4 仅支持稳定通道。 例如：stable-4.4。

可以使用 stable-4.5 通道从以前的 Azure Red Hat OpenShift 次要版本升级。 不支持使用快速、预发布和候选通道升级的群集。

如果更改为不包含当前版本的通道，则会显示警报，无法推荐更新，但是可以在任何时候安全地更改回原始通道。

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift 容器平台版本支持策略

Azure Red Hat OpenShift 支持 Red Hat OpenShift 容器平台的两个正式发布 (GA) 次要版本：
* 在 Azure Red Hat OpenShift 中发布的最新 GA 次要版本（我们将它称为 N）
* 前一个次要版本 (N-1)

如果在稳定升级通道中提供，则也可能支持在上游 OCP 中提供的较新次要版本（N+1、N+2）。

关键补丁更新由 Azure Red Hat OpenShift 站点可靠性工程师 (SRE) 自动应用于群集。 希望事先安装补丁更新的客户可随意执行此操作。

例如，如果 Azure Red Hat OpenShift 今天引入 4.5.z，则会为以下版本提供支持：

|新的次要版本|支持的版本列表|
|-|-|
|4.5.z|4.5.z、4.4.z|

“.z”代表补丁版本。 如果在稳定升级通道中提供，则客户还可以升级到 4.6.z。

引入新的次要版本后，最早次要版本会被弃用并删除。 例如，假设当前支持的版本列表为 4.5.z 和 4.4.z。 当 Azure Red Hat OpenShift 发布 4.6.z 时，4.4.z 版本会被删除并在 30 天后终止支持。

> [!NOTE]
> 请注意，如果客户在运行不受支持的 Red Hat OpenShift 版本，则在请求群集支持时，系统可能会要求他们升级。 运行不受支持的 Red Hat OpenShift 版本的群集不在 Azure Red Hat OpenShift SLA 涵盖范围内。

## <a name="release-and-deprecation-process"></a>发布和弃用流程

可以在 Azure Red Hat OpenShift 发布日历上参考即将推出的版本发布和弃用。

对于 Red Hat OpenShift 容器平台的新次要版本：
* Azure Red Hat OpenShift SRE 团队会至少在删除前 30 天在 [Azure Red Hat OpenShift 发行说明](https://github.com/Azure/OpenShift/releases)中发布预告，其中包含新版本发布的计划日期以及相应的旧版本弃用。
* Azure Red Hat OpenShift SRE 团队会发布向具有 Azure Red Hat OpenShift 和门户访问权限的所有客户提供的服务运行状况通知，并向订阅管理员发送电子邮件，其中包含计划版本删除日期。
* 自版本删除起，客户有 30 天的时间升级到受支持的次要版本发布，以继续获得支持。

对于 Red Hat OpenShift 容器平台的新补丁版本：
* 由于补丁版本的紧急性质，可以在补丁变为可用时由 Azure Red Hat OpenShift SRE 团队将其引入到服务中。
* 通常，对于新补丁版本的安装，Azure Red Hat OpenShift SRE 团队不会执行广泛的通信。 但是，该团队会持续监视和验证可用的 CVE 补丁，以便及时支持它们。 如果需要客户操作，该团队会向客户通知有关升级的信息。

## <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外情况

Azure Red Hat OpenShift SRE 团队保留相关权利来添加或删除新的/现有版本，或是延迟被确定为具有一个或多个关键生产影响 bug 或安全问题且未提前通知的即将推出次要发布版本。

特定补丁发布可能会被跳过或者加速推出，具体取决于 bug 或安全问题的严重性。

## <a name="azure-portal-and-cli-versions"></a>Azure 门户和 CLI 版本

在门户中或使用 Azure CLI 部署 Azure Red Hat OpenShift 群集时，群集会默认为最新 (N) 次要版本和最新关键补丁。 例如，如果 Azure Red Hat OpenShift 支持 4.5.z 和 4.4.z，则新安装的默认版本为 4.5.z。 希望使用最新上游 OCP 次要版本（N+1、N+2）的客户可以随时将其群集升级到稳定升级通道中提供的任何版本。

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift 发布日历

请参阅以下指南以了解[过去的 Red Hat OpenShift 容器平台（上游）发布历史记录](https://access.redhat.com/support/policy/updates/openshift/#dates)。

|OCP 版本|上游版本|Azure Red Hat OpenShift 正式发布|生命周期终止|
|-|-|-|-|
|4.4|2020 年 5 月|2020 年 7 月|4.6 GA|
|4.5|2020 年 7 月| 2020 年 11 月|4.7 GA|
|4.6|2020 年 10 月| 2021 年 2 月|4.8 GA|
|4.7|2021 年 2 月| 2021 年 7 月 15 日|4.9 GA|
|4.8|2021 年 7 月| 2021 年 9 月 15 日|4.10 GA|

## <a name="faq"></a>FAQ

用户升级的 OpenShift 群集具有不受支持的次要版本时，会发生什么情况？

如果你当前使用的是 N-2 版本或更低版本，则意味着你不再可接受支持，系统会要求你升级才能获得支持。 从版本 N-2 成功升级到 N-1 后，你将重新获得支持。 从版本 N-3 或更低版本升级到受支持的版本可能会很困难，有些情况下甚至不可能。 建议将群集保留在最新的 OpenShift 版本上，以避免潜在的升级问题。 例如：
* 如果最早的受支持 Azure Red Hat OpenShift 版本为 4.4.z，而你使用的是 4.3.z 或更低版本，则你不再可接受支持。
* 从 4.3.z 升级到 4.4.z 或更高后续版本之后，你将重新涵盖在我们的支持策略中。 

不支持将群集还原到以前的版本或进行回滚。 仅支持升级到较新版本。

“不在支持范围内”是什么意思？

如果 ARO 群集运行的 OpenShift 版本不在受支持的版本列表中，或者正在使用[不受支持的群集配置](./support-policies-v4.md)，则表明群集“不受支持”。 因此：
- 为群集创建支持票证时，系统将要求你将群集升级到受支持的版本。 在收到支持之前，除非你还在版本支持结束后的 30 天宽限期内。 
- “不受支持”的群集的任何运行时或 SLA 保证都将失效。
- “不受支持”的群集只能按照“尽最大努力”的原则来修补。
- 不会监视“不受支持”的群集。
