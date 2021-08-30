---
title: 将虚拟网络服务终结点用于语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将语音服务与 Azure 虚拟网络服务终结点结合使用。
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490210"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>通过虚拟网络服务终结点使用语音服务

[Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)有助于通过 Azure 主干网络上的优化路由提供与 Azure 服务的安全和直接连接。 终结点有助于保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 通过服务终结点，虚拟网络中的专用 IP 地址可以到达 Azure 服务的终结点，而无需虚拟网络上的公共 IP 地址。

本文介绍如何设置虚拟网络服务终结点并将其用于 Azure 认知服务中的语音服务。

> [!NOTE]
> 在开始操作之前，请查看[如何将虚拟网络与认知服务结合使用](../cognitive-services-virtual-networks.md)。

本文还介绍[如何在以后删除虚拟网络服务终结点，但仍使用语音资源](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks)。

若要为虚拟网络服务终结点场景设置语音资源，需要执行以下操作：
1. [为语音资源创建自定义域名](#create-a-custom-domain-name)。
1. [为语音资源配置虚拟网络和网络设置](#configure-virtual-networks-and-the-speech-resource-networking-settings)。
1. [调整现有应用程序和解决方案](#adjust-existing-applications-and-solutions)。

> [!NOTE]
> 为语音服务设置和使用虚拟网络服务终结点类似于设置和使用专用终结点。 在本文中，当程序相同时，我们会参考[有关使用专用终结点的文章](speech-services-private-link.md)中的相应部分。

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

本文介绍如何将虚拟网络服务终结点与语音服务结合使用。 有关专用终结点的信息，请参阅[通过专用终结点使用语音服务](speech-services-private-link.md)。

## <a name="create-a-custom-domain-name"></a>创建自定义域名

虚拟网络服务终结点需要[将自定义子域名称用于认知服务](../cognitive-services-custom-subdomains.md)。 按照“专用终结点”一文的[指导](speech-services-private-link.md#create-a-custom-domain-name)创建自定义域。 本部分中的所有警告也适用于虚拟网络服务终结点。

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>配置虚拟网络和语音资源网络设置

需要将允许通过服务终结点访问的所有虚拟网络添加到语音资源网络属性。

> [!NOTE]
> 若要通过虚拟网络服务终结点访问语音资源，需要为虚拟网络的所需子网启用 `Microsoft.CognitiveServices` 服务终结点类型。 这样做会通过专用主干网络路由与认知服务相关的所有子网流量。 如果打算从同一子网访问任何其他认知服务资源，请确保将这些资源配置为允许虚拟网络。 
>
> 如果虚拟网络未以“允许”的形式添加到语音资源网络属性中，它将无法通过服务终结点访问语音资源，即使已为虚拟网络启用了 `Microsoft.CognitiveServices` 服务终结点也是如此。 如果启用了服务终结点但不允许虚拟网络，则无论语音资源的其他网络安全设置是什么，虚拟网络都无法通过公共 IP 地址访问语音资源。 这是因为启用 `Microsoft.CognitiveServices` 终结点会通过专用主干网络路由与认知服务相关的所有流量，在这种情况下，应明确允许虚拟网络访问资源。 本指南适用于所有认知服务资源，而不仅仅适用于语音资源。  
  
1. 转到 [Azure 门户](https://portal.azure.com/)并登录 Azure 帐户。
1. 选择语音资源。
1. 在左窗格的“资源管理”组中，选择“网络” 。
1. 在“防火墙和虚拟网络”选项卡上，选择“选定的网络和专用终结点” 。 

   > [!NOTE]
   > 若要使用虚拟网络服务终结点，需要选择“选定的网络和专用终结点”网络安全选项。 不支持其他选项。 如果你的场景需要“所有网络”选项，请考虑使用支持所有三个网络安全选项的[专用终结点](speech-services-private-link.md)。

5. 选择“添加现有虚拟网络”或“添加新的虚拟网络”并提供所需参数 。 选择“添加”现有虚拟网络或“创建”新的虚拟网络 。 如果添加现有虚拟网络，将自动为所选子网启用 `Microsoft.CognitiveServices` 服务终结点。 此操作最多可能需要 15 分钟。 另请参阅本部分开头的注释。

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>为现有虚拟网络启用服务终结点 

如上一部分所述，将虚拟网络配置为允许用于语音资源时，会自动启用 `Microsoft.CognitiveServices` 服务终结点。 如果稍后禁用服务终结点，则需要手动重新启用它以恢复服务终结点对语音资源（以及其他认知服务资源）的访问权限：

1. 转到 [Azure 门户](https://portal.azure.com/)并登录 Azure 帐户。
1. 选择虚拟网络。
1. 在左窗格的“设置”组中，选择“子网” 。
1. 选择所需子网。
1. 窗口右侧将显示一个新面板。 在此面板的“服务终结点”部分中，从“服务”列表中选择 `Microsoft.CognitiveServices` 。
1. 选择“保存”。

## <a name="adjust-existing-applications-and-solutions"></a>调整现有应用程序和解决方案

启用了自定义域的语音资源以不同的方式与语音服务进行交互。 无论是否配置服务终结点，对于启用了自定义域的语音资源都是如此。 此节中的信息适用于这两种方案。

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>使用具有自定义域名和允许的虚拟网络的语音资源 

在此场景中，在语音资源的网络设置中选择了“选定的网络和专用终结点”选项，并且至少允许一个虚拟网络。 此场景等效于[使用已启用自定义域名和专用终结点的语音资源](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint)。


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>使用具有自定义域名但未允许虚拟网络的语音资源

在此场景中，未启用专用终结点，并且其中一个语句为 true：

- 在语音资源的网络设置中选择了“选定的网络和专用终结点”选项，但未配置允许的虚拟网络。
- 在语音资源的网络设置中选择了“所有网络”选项。

此场景等效于[使用具有自定义域名但没有专用终结点的语音资源](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints)。


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>了解更多信息

* [通过专用终结点使用语音服务](speech-services-private-link.md)
* [Azure 虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure 专用链接](../../private-link/private-link-overview.md)
* [语音 SDK](speech-sdk.md)
* [语音转文本 REST API](rest-speech-to-text.md)
* [文本转语音 REST API](rest-text-to-speech.md)
