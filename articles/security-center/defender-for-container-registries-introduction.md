---
title: 适用于容器注册表的 Azure Defender - 优点和功能
description: 了解容器注册表的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5c9407e33cefc9c604192252a119db5a42851949
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541398"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>适用于容器注册表的 Azure Defender 简介

Azure 容器注册表 (ACR) 是一种托管的专用 Docker 注册表服务，它在中心注册表中存储和管理用于 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

若要保护订阅中所有基于 Azure 资源管理器的注册表项，请在订阅级别启用适用于容器注册表的 Azure Defender。 然后，Azure Defender 将扫描推送到注册表、导入注册表或在过去 30 天内拉取的所有映像。 将按扫描的每个映像收费，每个映像收费一次。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|适用于容器注册表的 Azure Defender 按[定价页](security-center-pricing.md)中的定价计费|
|支持的注册表和映像：|可通过 shell 从公共 internet 访问的 ACR 注册表中的 Linux 映像<br>[受 Azure 专用链接保护的 ACR 注册表](../container-registry/container-registry-private-link.md)|
|不支持的注册表和映像：|Windows 映像<br>“专用”注册表（除非授予对[可信服务](../container-registry/allow-access-trusted-services.md#trusted-services)的访问权限）<br>超级简单的映像，例如 [Docker 暂存](https://hub.docker.com/_/scratch/)映像或仅包含应用程序及其运行时依赖项而无包管理器、shell 或 OS 的“无分发版”映像<br>包含[开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)的映像|
|所需角色和权限：|安全读取者和 [Azure 容器注册表角色和权限](../container-registry/container-registry-roles.md)|
|云：|:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: US Gov 和 China Gov - 当前仅支持“推送扫描”功能。 参阅[何时扫描映像？](#when-are-images-scanned)了解详细信息|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>适用于容器注册表的 Azure Defender 有哪些优点？

安全中心会标识订阅中基于 Azure 资源管理器的 ACR 注册表，并无缝为注册表映像提供 Azure 原生漏洞评估和管理。

**适用于容器注册表的 Azure Defender** 包含一个漏洞扫描程序，可扫描基于 Azure 资源管理器的 Azure Container Registry 注册表中的映像，使你能够更深入地了解映像漏洞。 该集成的扫描程序由业界领先的漏洞扫描供应商 Qualys 提供支持。

当 Qualys 或安全中心发现问题时，你将在安全中心仪表板中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-container)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心针对每个报告的漏洞提供相关详细信息和严重性类别。 此外，它还提供有关如何修正每个映像上发现的特定漏洞的指导内容。

安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。


> [!TIP]
> 若要详细了解安全中心的容器安全功能，请参阅：
>
> - [Azure 安全中心和容器安全性](container-security.md)
> - [适用于 Kubernetes 的 Azure Defender 简介](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>何时扫描映像？

映像扫描有三个触发器：

- **在推送时** - 每当向注册表推送映像时，安全中心会自动扫描该映像。 若要触发映像扫描，请将该映像推送到存储库。

- 最近拉取 - 由于每天都会发现新漏洞，适用于容器注册表的 Azure Defender 每周还会扫描过去 30 天内拉取的任何映像 。 重新扫描不产生额外费用；如前所述，你只需为每个映像支付一次扫描费用。

- **导入时** - Azure 容器注册表具有导入工具，用于将 Docker 中心、Microsoft 容器注册表或其他 Azure 容器注册表中的映像引入你的注册表。 **适用于容器注册表的 Azure Defender** 扫描导入的任何受支持的映像。 请参阅[将容器映像导入容器注册表](../container-registry/container-registry-import-images.md)，了解详细信息。
 
扫描通常在 2 分钟内完成，最多可能需要 15 分钟。 扫描结果以“安全中心建议”的形式提供，如下所示：

[![有关在 Azure 容器注册表 (ACR) 托管映像中发现的漏洞的 Azure 安全中心建议示例。](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>安全中心如何使用 Azure 容器注册表

下面是有关使用安全中心保护注册表的组件和优点的概括性关系图。

![Azure 安全中心和 Azure 容器注册表 (ACR) 综合概述。](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq---azure-container-registry-image-scanning"></a>常见问题解答 - Azure 容器注册表映像扫描

### <a name="how-does-security-center-scan-an-image"></a>安全中心如何扫描图像？
安全中心会从注册表中拉取映像，并使用 Qualys 扫描程序在一个隔离的沙盒中运行它。 扫描程序会提取已知漏洞的列表。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可以通过 REST API 获取扫描结果？
是的。 结果位于[子评估 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，还可以对所有资源使用 Azure Resource Graph (ARG)，一个类似于 Kusto 的 API：查询可以提取特定扫描。

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>会扫描哪些类型的注册表？ 会对哪些类型计费？
有关适用于容器注册表的 Azure Defender 支持的容器注册表类型的列表，请参阅[可用性](#availability)。

如果将不支持的注册表连接到 Azure 订阅，Azure Defender 不会对其进行扫描，也不会为此向你收取费用。

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>是否可以自定义漏洞扫描程序中的发现结果？
是的。 如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

[了解如何创建规则来禁用集成漏洞评估工具中的发现结果](defender-for-container-registries-usage.md#disable-specific-findings-preview)

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>有个映像不在我的注册表中，它存在漏洞。为什么安全中心要就这些漏洞向我发送警报？
安全中心会对在注册表中拉取或推送的每个映像提供漏洞评估。 某些映像可能会重复使用来自已扫描的某个映像的标记。 例如，每次你向摘要添加一张映像时，都可能重新分配名为“最新”的标记。 在这种情况下，“旧”映像仍然在注册表中，可能仍会被其摘要拉取。 如果发现该映像存在安全问题，而它被拉取了，那么就将暴露安全漏洞。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [扫描映像中的漏洞](defender-for-container-registries-usage.md)
