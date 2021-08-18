---
title: 使用 Azure Defender 扫描注册表映像
description: 了解如何使用适用于容器注册表的 Azure Defender 来扫描 Azure 容器注册表中的映像
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: d00e23ffa7e3bd2fc1084ba6253274d14031d624
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113552995"
---
# <a name="scan-registry-images-with-azure-defender"></a>使用 Azure Defender 扫描注册表映像

若要扫描 Azure 容器注册表映像中的漏洞，可以集成一个可用的 Azure 市场解决方案，或者，如果想要使用 Azure 安全中心，可以选择在订阅级别启用“适用于容器注册表的 Azure Defender”。 

* 详细了解[适用于容器注册表的 Azure Defender](../security-center/defender-for-container-registries-introduction.md)
* 详细了解 [Azure 安全中心中的容器安全性](../security-center/container-security.md)

## <a name="registry-operations-by-azure-defender"></a>Azure Defender 执行的注册表操作

Azure Defender 会扫描推送到注册表或导入到注册表中的映像，以及在过去 30 天内拉取的任何映像。 如果检测到漏洞，则 Azure 安全中心中会显示[建议修正](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings)。

 执行修复安全问题的建议步骤后，请替换注册表中的映像。 Azure Defender 会重新扫描映像，以确认是否修正了漏洞。 

有关详细信息，请参阅[将 Azure Defender用于容器注册表](../security-center/defender-for-container-registries-usage.md)。

> [!TIP]
> Azure Defender 将向注册表进行身份验证，以拉取映像进行漏洞扫描。 如果系统收集了注册表的[资源日志](monitor-service-reference.md#resource-logs)，你将看到 Azure Defender 生成的注册表登录事件和映像拉取事件。 这些事件与字母数字 ID（例如 `b21cb118-5a59-4628-bab0-3c3f0e434cg6`）相关联。

## <a name="scanning-a-network-restricted-registry"></a>扫描受网络限制的注册表

Azure Defender 可以扫描可公开访问的容器注册表中的映像或受网络访问规则保护的注册表中的映像。 如果配置了网络规则（也就是说，禁用公共注册表访问、配置 IP 访问规则或创建专用终结点），请确保启用网络设置来[让受信任的 Microsoft 服务](allow-access-trusted-services.md)可以访问注册表。 默认情况下，在新的容器注册表中启用此设置。

## <a name="next-steps"></a>后续步骤

* 详细了解[受信任服务](allow-access-trusted-services.md)的注册表访问。
* 要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。
* 若要设置注册表防火墙规则，请参阅[配置公共 IP 网络规则](container-registry-access-selected-networks.md)。
