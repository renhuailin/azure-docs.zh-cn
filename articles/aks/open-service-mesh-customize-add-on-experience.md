---
title: 自定义 OSM CLI 体验
description: 使用 osmconfig 文件自定义 OSM CLI 体验。
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1e5b39424b8e162feda65783eecc3a1a95fdaa7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535486"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>使用 OSM_CONFIG 文件配置 OSM CLI 变量

用户可以替代默认的 OSM CLI 配置来增强加载项体验。 这可以通过创建类似于 `kubeconfig` 的配置文件来完成。 配置文件可以在 `$HOME/.osm/config.yaml` 中创建，也可以在使用 `OSM_CONFIG` 环境变量导出的不同路径创建。

该文件必须包含以下 YAML 格式的内容：

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

如果未在 `$HOME/.osm/config.yaml` 中创建文件，请记得将 `OSM_CONFIG` 环境变量设置为指向创建配置文件的路径。

设置 OSM_CONFIG 后，`osm env` 命令的输出应如下所示： 

```console
$ osm env
--- 
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```