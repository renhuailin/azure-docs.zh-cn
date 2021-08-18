---
title: 注册表服务层级和功能
description: 了解 Azure 容器注册表的基本、标准和高级服务层 (SKU) 中的功能和限制（配额）。
ms.topic: article
ms.date: 06/24/2021
ms.openlocfilehash: 8c27426cae6d80e31aef3d7ef9b75d28a14bd923
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437533"
---
# <a name="azure-container-registry-service-tiers"></a>Azure 容器注册表服务层级

Azure 容器注册表分为多个服务层级（也称为 SKU）。 这些层级提供可预测的定价和多个选项，用来适应你在 Azure 中的专用 Docker 注册表的容量和使用模式。

| 层 | 说明 |
| --- | ----------- |
| **基本** | 供开发者了解 Azure 容器注册表的入口点（已优化过成本）。 基本注册表的编程功能（如 Azure Active Directory [身份验证集成](container-registry-authentication.md#individual-login-with-azure-ad)、[映像删除][container-registry-delete]和 [Webhook][container-registry-webhook]）与标准注册表和高级注册表相同。 但其附带的存储和映像吞吐量最适合使用较少的场景。 |
| **Standard** | 标准注册表的功能与基本注册表相同。不同之处在于，前者附带更多的存储和映像吞吐量。 标准注册表应能够满足大部分生产方案的需求。 |
| **高级** | 高级注册表附带的存储和并发操作数最多，支持大容量方案。 除增加了映像吞吐容量之外，高级注册表还增添了其他功能，例如用于跨多个区域管理一个注册表的[异地复制][container-registry-geo-replication]、用于映像标记签名的[内容信任](container-registry-content-trust.md)、用于限制注册表访问的[具有专用终结点的专用链接](container-registry-private-link.md)。 |

“基本”、“标准”和“高级”层级全都提供相同的编程功能。 它们还全都受益于完全由 Azure 托管的[映像存储][container-registry-storage]。 选择的层级等级越高，性能和可缩放性就越高。 使用多个服务层级，你可以从“基本”层级开始，然后随着注册表使用量增长转换到“标准”和“高级”层级。

## <a name="service-tier-features-and-limits"></a>服务层级功能和限制

下表详细介绍了“基本”、“标准”和“高级”服务层级的功能和注册表限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>注册表吞吐量和限制

### <a name="throughput"></a>吞吐量 

生成高速率的注册表操作时，请使用服务层级对读写操作和带宽的限制作为指导，以实现预期最大吞吐量。 这些限制会影响数据平面操作，包括列出、删除、推送和拉取映像和其他项目。

要准确估计映像拉取和推送的吞吐量，请考虑注册表限制和以下因素： 

* 映像层的数量和大小
* 跨映像重用层或基础映像
* 每次拉取或推送可能需要的额外 API 调用

有关详细信息，请参阅 [Docker HTTP API V2](https://docs.docker.com/registry/spec/api/) 的文档。

在对注册表吞吐量进行评估或故障排除时，还要考虑客户端环境的配置：

* 用于并发操作的 Docker 守护程序配置
* 与注册表的数据终结点的网络连接（如果注册表是[异地复制](container-registry-geo-replication.md)的）。

如果你遇到注册表吞吐量的问题，请参阅[对注册表性能进行故障排除](container-registry-troubleshoot-performance.md)。 

#### <a name="example"></a>示例

要将单个 133 MB 的 `nginx:latest` 映像推送到 Azure 容器注册表需要对映像的五个层执行多个读写操作： 

* 读取映像清单的读取操作（如果映像清单存在于注册表中）
* 写入映像的配置 blob 的写入操作
* 写入映像清单的写入操作

### <a name="throttling"></a>限制

当注册表确定请求速率超出注册表的服务层级允许的限制时，你可能会遇到拉取或推送操作的限制。 你可能会看到类似于 `Too many requests` 的 HTTP 429 错误。

当你在非常短的时间内生成大量映像拉取或推送操作时，即使读写操作的平均速率未超出注册表限制，也可能会暂时出现限制。 你可能需要在代码中通过一些回退操作来实现重试逻辑，或者降低对注册表的请求的最大速率。

## <a name="changing-tiers"></a>更改层级

可以通过 Azure CLI 或在 Azure 门户中更改注册表的服务层级。 你可以自由地在各种层级之间切换，只要你要切换到的层级具有所需的最大存储容量即可。 

在服务层级之间移动时，不会出现注册表故障，也不会影响注册表操作。

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 在各种服务层级之间切换，请使用 [az acr update][az-acr-update] 命令。 例如，若要切换到高级 SKU，请使用以下命令：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中的容器注册表“概述”中，选择“更新”，然后从“SKU”下拉列表中选择一个新 SKU。

![在 Azure 门户中更新容器注册表 SKU][update-registry-sku]

## <a name="pricing"></a>定价

有关每个 Azure 容器注册表服务层级的定价信息，请参阅[容器注册表定价][container-registry-pricing]。

有关数据传输定价的详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。 

## <a name="next-steps"></a>后续步骤

**Azure 容器注册表路线图**

请访问 GitHub 上的 [ACR 路线图][acr-roadmap]，了解服务即将推出的新功能。

**Azure 容器注册表 UserVoice**

请在 [ACR UserVoice][container-registry-uservoice] 中提交新功能建议，并对这些功能建议投票。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
