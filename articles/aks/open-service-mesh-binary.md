---
title: 下载 OSM 客户端库
description: 下载 Open Service Mesh (OSM) 客户端库
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 83eec8682291a74bc9b23bf4ff1326cee7134e7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534940"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>下载 Open Service Mesh (OSM) 客户端库
本文将讨论如何下载 OSM 客户端库，以便用于操作和配置 AKS 的 OSM 外接程序。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> 不要尝试使用 `osm install` 通过二进制文件安装 OSM。 这将导致 OSM 安装未作为附加产品与 AKS 集成。

> [!NOTE]
> 建议在安装二进制文件之后，在使用 OSM CLI 之前，将 OSM CLI 配置为[自定义 OSM AKS 加载项体验](/open-service-mesh-customize-addon-experience.md)。 