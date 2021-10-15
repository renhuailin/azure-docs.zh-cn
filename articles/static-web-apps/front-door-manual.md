---
title: 教程：手动为 Azure Static Web Apps 配置 Azure Front Door
description: 了解如何为 Azure Static Web Apps 设置 Azure Front Door
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400679"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>教程：手动为 Azure Static Web Apps 配置 Azure Front Door

了解如何为 Static Web Apps 将 [Azure Front Door](../frontdoor/front-door-overview.md) 添加为 CDN。  Azure Front Door 是一个可缩放的安全入口点，用于快速交付 Web 应用程序。

在本教程中，你将了解：

> [!div class="checklist"]
>
> - 创建 Azure Front Door 实例
> - 将 Azure Font Door 与 Azure Static Web Apps 站点关联

> [!NOTE]
> 本教程需要 Azure Static Web Apps 标准计划。

## <a name="copy-url"></a>复制 URL

1. 导航到 Azure 门户。

1. 打开要应用 Azure Front Door 的 Static Web Apps。

1. 转到“概述”部分。

1. 将 URL 复制到剪贴板供以后使用。

## <a name="add-azure-front-door"></a>添加 Azure Front Door

1. 导航到 Azure 门户。

1. 选择“创建资源”。 

1. 搜索 Azure Front Door。

1. 选择“Front Door”。

    请确保不要选择标记为“Front Door Standard/Premium”的服务，因为 Standard/Premium 服务的步骤与本教程中介绍的步骤不同。

1. 选择“创建”  。

1. 在“基本信息”选项卡中，输入以下值：

    | 设置 | 值 |
    |---|---|
    | 订阅 | 选择 Azure 订阅。 |
    | 资源组 | 输入资源组名称。 此名称通常与 Static Web Apps 使用的组名相同。 |
    | 资源组位置 | 如果创建新资源组，请输入离你最近的位置。 |

    选择“下一步: 配置>”。

1. 在“配置”选项卡中，选择“前端/域”旁边的加号，然后输入以下值：

    | 设置 | 值 |
    |---|---|
    | 主机名 | 为 Front Door 主机输入一个唯一名称。 |

    接受窗体其余部分的默认值，然后选择“添加”。

1. 选择“后端池”旁边的加号，然后输入以下值：

    | 设置 | 值 |
    |---|---|
    | 名称 | 输入“my-static-web-app-pool”。 |

1. 选择“+ 添加后端”，然后输入以下值：

    | 设置 | 值 |
    |---|---|
    | 后端主机类型 | 选择“自定义主机”。 |
    | 后端主机名 | 输入 Static Web Apps 的主机名。 请确保值不包含尾部斜杠或协议。 （例如：`desert-rain-04056.azurestaticapps.net`）  |
    | 后端主机头 | 输入 Static Web Apps 的主机名。 请确保值不包含尾部斜杠协议。 例如：`desert-rain-04056.azurestaticapps.net` |

    接受窗体其余部分的默认值，然后选择“添加”。

1. 选择“添加”。

1. 选择“传递规则”旁边的加号，然后输入以下值：

    | 设置 | 值 |
    |---|---|
    | 名称 | 输入“my-routing-rule”。 |

    接受窗体其余部分的默认值，然后选择“添加”。

1. 选择“查看 + 创建”。

1. 选择“创建”  。

    创建过程可能需要几分钟才能完成。

1. 选择“转到资源”。

1. 选择“概述”。 

1. 选择标记为“前端主机”的链接。

    选择此链接时，如果站点未完全传播，则可能会看到 404 错误。 请等待几分钟，然后返回“概览”窗口并选择标记为“前端主机”的链接，而不要刷新页面 。

1. 从“概览”窗口中，复制标记为“Front Door ID”的值，并将其粘贴到文件中供以后使用。

> [!IMPORTANT]
> 默认情况下，Azure Front Door 配置可能影响流量统计信息的[运行状况探测](../frontdoor/front-door-health-probes.md)。 你可能希望编辑[运行状况探测](../frontdoor/front-door-health-probes.md)的默认值。

## <a name="update-static-web-app-configuration"></a>更新 Static Web Apps 配置

要完成与 Front Door 的集成，需要将应用程序配置文件更新为：

- 将站点流量限制为仅通过 Front Door
- 将站点流量限制为仅来自 Front Door 实例
- 定义哪些域可以访问站点

为站点打开 [staticwebapp.config.json](configuration.md) 文件，并做出以下更改。

1. 通过将以下部分添加到配置文件，将流量限制为仅使用 Front Door：

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. 要定义哪些 Azure Front Door 实例和域可以访问站点，请添加 `forwardingGateway` 部分。

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    首先，将应用配置为仅允许来自 Front Door 实例的流量。 在每个后端请求中，Front Door 自动添加包含 Front Door 实例 ID 的 `X-Azure-FDID` 标头。 通过将 Static Web Apps 配置为需要此标头，它将以独占方式将流量限制为 Front Door 实例。 在配置文件的 `forwardingGateway` 部分中，添加 `requiredHeaders` 部分并定义 `X-Azure-FDID` 标头。 将 `<YOUR-FRONT-DOOR-ID>` 替换为之前预留的 Front Door ID。

    接下来，将 Azure Front Door 主机名（而不是 Azure Static Web Apps 主机名）添加到 `allowedForwardedHosts` 阵列中。 如果在 Front Door 实例中配置了自定义域，请也将其包含在此列表中。

    此示例将 `my-sitename.azurefd.net` 替换为站点的 Azure Front Door 主机名。

使用此配置，站点不再通过生成的 `*.azurestaticapps.net` 主机名提供，而是只能通过 Front Door 实例中配置的主机名提供。

> [!NOTE]
> 将更新部署到 Static Web Apps 中的现有文件时，Azure Front Door 可能会继续提供旧版本的文件，直到其[生存时间](https://wikipedia.org/wiki/Time_to_live)到期。 为受影响路径[清除 Azure Front Door 缓存](../frontdoor/front-door-caching.md#cache-purge)，以确保提供最新文件。

现在 Front Door 管理站点，无需使用 Azure Static Web Apps 自定义域功能。 Azure Front Door 有一个单独的过程用于添加自定义域。 请参阅[将自定义域添加到 Front Door](../frontdoor/front-door-custom-domain.md)。 将自定义域添加到 Front Door 时，需要更新 Static Web Apps 配置文件，以将其包含在 `allowedForwardedHosts` 列表中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](apis.md)
