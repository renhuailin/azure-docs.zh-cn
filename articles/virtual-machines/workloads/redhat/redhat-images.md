---
title: Azure 中的 Red Hat Enterprise Linux 映像概述
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像。
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: ed4df5d747dfcd9d5e50e439be63b7682d6b886a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696760"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映像概述

适用于：:heavy_check_mark: Linux VM 

本文介绍 Azure 市场中可用的 Red Hat Enterprise Linux (RHEL) 映像以及与其命名和保留相关的策略。

有关 RHEL 所有版本的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。 有关定价详细信息，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure 市场中当前提供的 RHEL 映像支持自带订阅 (BYOS) 或即用即付许可模型。 可以通过 [Azure 混合使用权益](../../linux/azure-hybrid-benefit-linux.md)在 BYOS 与即用即付许可之间进行动态切换。

>[!NOTE]
> 对于与 Azure 市场中 RHEL 映像相关的任何问题，请向 Microsoft 提交支持票证。

## <a name="view-images-available-in-azure"></a>查看 Azure 中可用的映像

在 Azure 市场中搜索“Red Hat”或在 Azure 门户 UI 中创建资源时，只会看到所有可用 RHEL 映像中的一部分。 可以使用 Azure CLI、PowerShell 和 API 获取全部的可用 VM 映像。

若要在 Azure 中查看完整的可用 Red Hat 映像集，请运行以下命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名约定

Azure 中的 VM 映像按发布者、产品/服务、SKU 和版本进行整理。 发布者:产品/服务:SKU:版本的组合即为映像 URN，可唯一地标识要使用的映像。

例如，`RedHat:RHEL:8-LVM:8.1.20200318` 表示 2020 年 3 月 18 日生成的 RHEL 8.1 LVM 分区映像。

下面展示了有关如何创建 RHEL 8.1 VM 的示例。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>“latest”名字对象

Azure REST API 允许使用名字对象“latest”作为版本来代替特定版本。 使用“latest”将为给定的发布者、产品/服务和 SKU 预配最新的可用映像。

例如，`RedHat:RHEL:8-LVM:latest` 表示可用的最新 RHEL 8 系列 LVM 分区映像。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 一般情况下，通过比较版本来确定最新版本时应遵循 [CompareTo 方法](/dotnet/api/system.version.compareto#system_version_compareto_system_version_)的规则。
这种映像版本比较是通过以 [Version](/dotnet/api/system.version.-ctor) 对象的形式而不是以字符串的形式来比较值而完成的。

## <a name="rhel-6-image-types"></a>RHEL 6 映像类型

>[!NOTE]
> 从 2020 年 12 月 30 日起，RHEL 6.10 进入生命周期结束时间 (EOL)。 若要继续获得支持，请使得 ELS 成为延长的生命周期支持阶段的一部分，并参阅 [Red HAT ELS 文档](./redhat-extended-lifecycle-support.md)。

下表显示了 RHEL 6.x 映像的映像类型。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本（例如 6.9） | RHEL 次要版本和发布日期的串联值（例如 6.9.2018010506） | 所有标准 RHEL 6.x 映像都遵循此约定。
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本和发布日期的串联值（例如 6.9.20181023） | 此映像是 RHEL 6.9 BYOS 映像。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如 6.8.2017053118） | 此映像是 RHEL 6.8 for SAP Applications 映像。 它有权访问 SAP Applications 存储库和基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如 6.7.2017053121） | 此映像是 RHEL 6.7 for SAP HANA 映像。 它有权访问 SAP HANA 存储库和基本 RHEL 存储库。

## <a name="rhel-7-image-types"></a>RHEL 7 映像类型

RHEL 7.x 映像有几种不同的映像类型。 下表显示了我们提供的不同的映像集。 若要查看完整列表，请使用 Azure CLI 命令 `az vm image list --publisher redhat --all`。

>[!NOTE]
> 除非另有说明，否则将对所有映像进行 LVM 分区，并将其连接到常规 RHEL 存储库。 也就是说，这些存储库不是扩展更新支持 (EUS) 存储库，也不是 SAP 更新服务 (E4S) 存储库。 今后，我们将转变为仅发布 LVM 分区的映像，但乐于倾听大家对于此决定的反馈。 有关扩展更新支持和 SAP 更新服务的详细信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本（例如 7.6） | RHEL 次要版本和发布日期的串联值（例如 7.6.2019102813） | 2019 年 4 月之前发布的映像将附加到标准 RHEL 存储库。 2019 年 4 月之后发布的映像将附加到 Red Hat 的 EUS 存储库，以允许对特定次要版本进行版本锁定。 如果客户需要常规存储库，则应使用 SKU 值中包含 7-LVM 或 7-RAW 的映像（详情如下）。 RHEL 7.7 及更高版本的映像已进行 LVM 分区。 此类别中的所有其他映像都是原始分区的。
|RedHat | RHEL | 7-RAW | RHEL 次要版本和发布日期的串联值（例如 7.6.2019102813） | 这些映像是原始分区的（例如未添加逻辑卷）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本和发布日期的串联值（例如 7.6.2019072418） | 这些映像是原始分区的（例如未添加逻辑卷），并使用 cloud-init 进行预配。
|RedHat | RHEL | 7-LVM | RHEL 次要版本和发布日期的串联值（例如 7.6.2019062414） | 这些映像是 LVM 分区的。
|RedHat | rhel-byos | rhel-{lvm,raw} | RHEL 次要版本和发布日期的串联值（例如 7.7.20190819） | 这些映像是 RHEL 7 BYOS 映像。 它们不会附加到任何存储库，也不会收取 RHEL 高级版费用。 如果你对 RHEL BYOS 映像感兴趣，可以[请求访问权限](https://aka.ms/rhel-byos)。 SKU 值以次要版本结尾，并指示映像是原始分区还是 LVM 分区。 例如，rhel-lvm77 的 SKU 值指示 LVM 分区的 RHEL 7.7 映像。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本和发布日期的串联值（例如 7.6.2019071300） | 这些映像是 RHEL for SAP 映像。 它们有权访问 SAP HANA 和 Applications 存储库以及 RHEL E4S 存储库。 计费包括基础计算费用，以及 RHEL 高级版和 SAP 高级版费用。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本和发布日期的串联值（例如 7.6.2019062320） | 这些映像是具有高可用性的 RHEL for SAP 映像和更新服务映像。 它们有权访问 SAP HANA 和 Applications 存储库、高可用性存储库以及 RHEL E4S 存储库。 计费包括基础计算费用，以及 RHEL 高级版、SAP 高级版和高可用性高级版的费用。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本和发布日期的串联值（例如 7.6.2019062019） | 这些映像是 RHEL 映像，也有权访问高可用性附加产品。 由于使用高可用性附加产品高级版，它们在 RHEL 和基础计算费用的基础上会额外收点费。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如 7.3.2017053118） | 这些映像已过期，因为 SAP Applications 和 SAP HANA 存储库已合并到 SAP 存储库。 这些映像是 RHEL for SAP Applications 映像。 它们有权访问 SAP Applications 存储库和基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如 7.3.2018051421） | 这些映像已过期，因为 SAP Applications 和 SAP HANA 存储库已合并到 SAP 存储库。 这些映像是 RHEL for SAP HANA 映像。 它们有权访问 SAP HANA 存储库和基本 RHEL 存储库。

## <a name="rhel-8-image-types"></a>RHEL 8 映像类型

>[!NOTE]
> Red Hat 建议使用 Grubby 在 RHEL 8+ 中配置内核命令行参数。 有关详细信息，请参阅[第 5 章：配置内核命令行参数 Red Hat Enterprise Linux 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)。

下面是 RHEL 8 映像类型的详细信息。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本和发布日期的串联值（例如 8.0.20191023） | 这些映像是连接到标准 Red Hat 存储库的 RHEL 8 LVM 分区映像。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本和发布日期的串联值（例如 8.0.20191024） | 这些映像是连接到标准 Red Hat 存储库的 Hyper-V 第 2 代 RHEL 8 LVM 分区映像。 有关 Azure 中第 2 代 VM 的详细信息，请参阅[对 Azure 上第 2 代 VM 的支持](../../generation-2.md)。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如 8.1.2021012201） | 这些映像是 RHEL for SAP Applications 映像。 它们有权访问 SAP Applications 存储库和基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本和发布日期的串联值（例如 8.1.2021010602） | 这些映像是具有高可用性的 RHEL for SAP 映像和更新服务映像。 它们有权访问 SAP Solutions 和 Applications 存储库、高可用性存储库以及 RHEL E4S 存储库。 计费包括基础计算费用，以及 RHEL 高级版、SAP 高级版和高可用性高级版的费用。

## <a name="rhel-extended-support-add-ons"></a>RHEL 外延支持附加产品

### <a name="extended-life-cycle-support"></a>扩展生命周期支持

扩展生命周期支持 (ELS) 外接产品属于可选订阅，它可为已达到生命结束时间 (EOL) 的版本启用关键和重要的安全修补程序。 有关 RHEL EUS 的详细信息，请参阅 [Red Hat 的文档](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support)。

ELS 目前仅适用于 RHEL 6.10。 对于即用即付映像，可以按照 [Red HAT ELS 文档](./redhat-extended-lifecycle-support.md)中的步骤启用 ELS。

如果运行的是较旧版本，则需要升级到 RHEL 6.10，然后才能启用 ELS。

### <a name="extended-update-support"></a>扩展更新支持

从 2019 年 4 月起，将提供默认附加到 EUS 存储库的 RHEL 映像。 有关 RHEL EUS 详细信息，请参阅 [Red Hat 的文档](https://access.redhat.com/articles/rhel-eus)。

切换到 EUS 存储库是可行并且受支持的。 有关如何将 VM 切换到 EUS 的说明，以及有关 EUS 支持结束日期的详细信息，请参阅 [RHEL EUS 和版本锁定 RHEL VM](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)。

>[!NOTE]
> RHEL Extras 不支持 EUS。 这意味着，如果要安装通常可从 RHEL Extras 渠道获得的包，则在 EUS 上将无法这样做。 有关 Red Hat Extras 产品生命周期的详细信息，请参阅 [Red Hat Enterprise Linux Extras 生命周期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>区分常规映像和 EUS 映像

如果客户想要使用附加到 EUS 存储库的映像，则应使用 SKU 中包含 RHEL 次要版本号的 RHEL 映像。

例如，可能看到下面两个 RHEL 7.4 映像可用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在这种情况下，`RedHat:RHEL:7.6:7.6.2019102813` 默认附加到 EUS 存储库。 SKU 值是 7.4。 而 `RedHat:RHEL:7-LVM:7.6.2019062414` 默认附加到非 EUS 存储库。 SKU 值是 7-LVM。

若要使用常规（非 EUS）存储库，请使用 SKU 中不包含次要版本号的映像。

#### <a name="rhel-images-with-eus"></a>具有 EUS 的 RHEL 映像

下表中的信息适用于连接到 EUS 存储库的 RHEL 映像。

>[!NOTE]
> 撰写本文时，只有 RHEL 7.4 及更高版本的次要版本具有 EUS 支持。 RHEL <=7.3 不再支持 EUS。
>
> 有关 RHEL EUS 可用性的详细信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

次要版本 |EUS 映像示例              |EUS 状态                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 默认情况下，2019 年 4 月及之后发布的映像是 EUS 映像。|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 默认情况下，2019 年 6 月及之后发布的映像是 EUS 映像。 |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 默认情况下，2019 年 5 月及之后发布的映像是 EUS 映像。 |
RHEL 8.0      |不适用                            | Red Hat 中没有可用的 EUS。                               |

### <a name="update-services-for-sap"></a>SAP 更新服务

最新的 RHEL for SAP 映像将连接到 SAP Solutions 更新服务订阅 (E4S)。 有关 E4S 详细信息，请参阅 Red Hat [文档](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>具有 E4S 的 RHEL 映像

2019 年 12 月之后创建的以下产品/服务的映像连接到 E4S 存储库：

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA（具有高可用性和更新服务的 RHEL for SAP）

## <a name="other-available-offers-and-skus"></a>其他可用产品/服务和 SKU

可用产品/服务和 SKU 的完整列表可能还包括除前表所列映像以外的其他映像。 示例为 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 这些产品/服务可用于为特定市场解决方案提供支持。 也可将其发布以供预览和测试之用。 它们可能随时更改或删除，而不会向你发出警告。 除非 Microsoft 或 Red Hat 已将它们公开记录在案，否则请勿使用它们。

## <a name="publishing-policy"></a>发布策略

Microsoft 和 Red Hat 会在以下情况下更新映像：新的次要版本发布时，根据需要解决特定的常见漏洞和透露 (CVE) 时，或者偶尔进行配置更改或更新时。 我们会在发布或提供 CVE 修补程序后的三个工作日内尽快提供更新的映像。

我们只更新给定映像系列中的当前次要版本。 随着较新次要版本的发布，我们将停止更新较旧的次要版本。 例如，随着 RHEL 7.6 的发布，RHEL 7.5 映像将不再更新。

>[!NOTE]
> 从 RHEL 即用即付映像预配的活动 Azure VM 会连接到 Azure RHUI，并且可以在 Red Hat 发布更新和修补程序并在这些更新和修补程序复制到 Azure RHUI 后便立即收到它们。 时间通常是在 Red Hat 正式发布后的 24 小时内。 这些 VM 不需要新的发布映像即可获取更新。 客户可以完全控制何时启动更新。

## <a name="image-retention-policy"></a>映像保留策略

目前的策略是保留以前发布的所有映像。 我们保留删除已知会导致任何问题的映像的权利。 例如，可能删除由于后续平台或组件更新而具有不正确配置的映像。 可能被删除的映像遵循当前 Azure 市场策略，在映像删除前的 30 天内提供通知。

## <a name="next-steps"></a>后续步骤

* 若要查看 Azure 中 RHEL 映像的完整列表，请参阅 [Azure 中提供的 Red Hat Enterprise Linux (RHEL) 映像](./redhat-imagelist.md)。
* 若要了解有关 Azure Red Hat 更新基础结构的详细信息，请参阅 [Azure 中按需 RHEL VM 的 Red Hat 更新基础结构](./redhat-rhui.md)。
* 若要了解有关 RHEL BYOS 产品/服务的详细信息，请参阅 [Azure 中的 Red Hat Enterprise Linux 自带订阅黄金映像](./byos.md)。
* 有关 RHEL 所有版本的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。
