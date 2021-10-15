---
title: Azure Database for MySQL - 灵活服务器的新增功能
description: 了解 Azure Database for MySQL - 灵活服务器（Microsoft 云中基于 MySQL Community Edition 的关系数据库服务）的最新更新。
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 377c9fc994c4d26b67791e3eb525c7fba75a9d78
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388881"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - 灵活服务器（预览版）有哪些新增功能？

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database for MySQL - 灵活服务器](./overview.md#azure-database-for-mysql---flexible-server-preview)是一种部署模式，已设计为在数据库管理功能和配置设置方面提供比单一服务器部署模式更高的控制粒度和灵活性。 该服务目前支持 MySQL 5.7 和 8.0 的社区版本。

本文汇总了自 2021 年 1 月至今 Azure Database for MySQL - 灵活服务器的新版本和功能。 列表按时间倒序排列，最新更新排在最前。
## <a name="october-2021"></a>2021 年 10 月
- **创建只读副本时的可用性区域选择**

    创建只读副本时，可以选择所选的可用性区域位置。 可用性区域是一种高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是 Azure 区域中独特的物理位置。 [了解详细信息](../flexible-server/concepts-read-replicas.md)

- **Azure Database for MySQL 中的只读副本 - 可突发 SKU 将不再提供灵活服务器**
    
    你将无法在可突发层服务器上创建新的或维护现有的只读副本。 鉴于为可突发 SKU 层提供良好的查询和开发体验，将不再支持创建和维护可突发定价层中的服务器的只读副本。 

    如果现有的 Azure Database for MySQL - 灵活服务器已启用只读副本，则必须将服务器扩展到“常规用途”或“内存优化”定价层，或者在 60 天内删除只读副本。 在 60 天后，虽然你可以继续使用主服务器进行读写操作，但将停止复制到只读副本服务器。 对于新创建的服务器，只读副本选项仅适用于“常规用途”和“内存优化”定价层。  

 

## <a name="september-2021"></a>2021 年 9 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- **另外三个 Azure 区域中的可用性**

  公共预览版的 Azure Database for MySQL - 灵活服务器现已在以下 Azure 区域推出：

  - 英国西部
  - 加拿大东部
  - 日本西部

- **Bug 修复**

  在以下区域中修复了同区域高可用性创建：

  - 印度中部
  - 东亚
  - 韩国中部
  - 南非北部
  - 瑞士北部

## <a name="august-2021"></a>2021 年 8 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- **使用相同区域高可用性的单一区域内的高可用性**

  通过该服务，客户现在可以在启用高可用性时为其备用服务器灵活地选择首选可用性区域。 借助此功能，客户可以将备用服务器放置在与主服务器相同的区域，从而减少主服务器和备用服务器之间的复制延迟。 如果应用程序服务器和数据库服务器位于同一 Azure 区域内，还可以降低它们之间的延迟。 [了解详细信息](/azure/mysql/flexible-server/concepts-high-availability#same-zone-high-availability)。

- **使用区域冗余高可用性选择备用区域**

  现在，客户可通过该服务选择备用服务器区域位置。 使用此功能，客户可以将备用服务器放置在他们选择的区域中。 将备用数据库服务器和备用应用程序共置在同一区域可减少延迟，并让客户能够更好地为灾难恢复情况和“区域故障”场景做好准备。 [了解详细信息](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection)。

- **专用 DNS 区域集成**

  [Azure 专用 DNS](../../dns/private-dns-privatednszone.md) 为虚拟网络提供可靠且安全的 DNS 服务（负责将服务名称转换为 IP 地址）。 Azure 专用 DNS 可管理并解析虚拟网络中的域名，使你无需配置自定义 DNS 解决方案。 通过此功能，可以将虚拟网络上运行的应用程序连接到在本地或全球对等虚拟网络上运行的灵活服务器。 Azure Database for MySQL - 现在可通过灵活服务器与 Azure 专用 DNS 区域集成，无缝解析当前 VNet 或专用 DNS 区域链接到的任何对等 VNet 中的专用 DNS。 通过此集成，如果后端灵活服务器的 IP 地址在故障转移或任何其他事件期间发生更改，则将自动更新集成的专用 DNS 区域，以确保应用程序连接在服务器联机后自动恢复。 [了解详细信息](./concepts-networking-vnet.md)。

- **指定虚拟网络中服务器的时间点还原**

  该服务的时间点还原体验现在支持客户配置网络设置，用户可在执行还原操作时在专用和公共网络选项之间切换。 通过此功能，客户可以灵活地将要恢复的服务器注入到指定的虚拟网络中，从而保护其连接终结点。 [了解详细信息](./how-to-restore-server-portal.md)。

- **可用性区域中服务器的时间点还原**

  该服务的时间点恢复体验现在支持客户配置可用性区域，将数据库服务器和备用应用程序共置在同一区域中可减少延迟，并使客户能够更好地为灾难恢复情况和“区域故障”情况做好准备。 [了解详细信息](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection)。

- validate_password 和 caching_sha2_password 插件在个人预览版中可用。

  灵活服务器现在支持在个人预览版中启用 validate_password 和 caching_sha2_password 插件。 请发送电子邮件到 AskAzureDBforMySQL@service.microsoft.com 与我们联系

- **另外四个 Azure 区域中的可用性**

  公共预览版的 Azure Database for MySQL - 灵活服务器现已在以下 Azure 区域推出，[了解详细信息](overview.md#azure-regions)：

  - Australia Southeast
  - 南非北部
  - 东亚（香港）
  - 印度中部

- **已知问题**

  - 在区域冗余高可用性服务器故障转移之后，如果使用 SSL 和 ssl_mode VERIFY_IDENTITY，客户端将无法连接到服务器。 可以通过将 ssl_mode 用作 VERIFY_CA 来缓解此问题。
  - 无法在以下区域创建相同区域高可用性服务器：印度中部、东亚、韩国中部、南非北部、瑞士北部。
  - 在极少数情况下和高可用性故障转移后，主服务器将处于 read_only 模式。 在“服务器参数”边栏选项卡中将“read_only”更新为“OFF”可解决此问题。
  - 在“计算+存储”边栏选项卡中成功缩放计算后，IOPS 将重置为 SKU 默认值。 客户可以在计算部署和随后的 IOPS 重置后，在“计算+存储”边栏选项卡中将 IOPS 重新缩放为所需值（先前设置），从而解决此问题。

## <a name="july-2021"></a>2021 年 7 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- **从单一服务器到灵活服务器的在线迁移**

  现在，客户可以使用数据传入复制将 Azure Database for MySQL – 单一服务器的实例迁移到灵活服务器，同时将其应用程序的停机时间降到最低。 有关详细的分步说明，请参阅[以最少停机时间将 Azure Database for MySQL – 单一服务器迁移到灵活服务器](../howto-migrate-single-flexible-minimum-downtime.md)。

- **美国西部和德国中西部的可用性**

  公共预览版的 Azure Database for MySQL - 灵活服务器现已在美国西部和德国中西部 Azure 区域推出。

## <a name="june-2021"></a>2021 年 6 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- 改进了较小存储服务器的性能

    从 2021 年 6 月 21 开始，所有新建服务器允许的最小预配存储大小已从 5 GB 增大至 20 GB。 此外，免费可用的 IOPS 已从 100 增大至 300。 下表汇总了这些变化：

    | **Current** | 从 2021 年 6 月 21 日开始 |
    |:----------|:----------|
    | 允许的最小存储大小：5 GB | 允许的最小存储大小：20 GB |
    | 可用 IOPS：Max(100, 3 * [以 GB 为单位预配的存储量]) | 可用 IOPS：(300 + 3 * [以 GB 为单位预配的存储量]) |

- 12 个月免费产品/服务

  截至 2021 年 6 月 15 日，[Azure 免费帐户](https://azure.microsoft.com/free/)为客户提供长达 12 个月的对 Azure Database for MySQL – 灵活服务器的免费访问，每月使用时间为 750 小时，存储空间为 32 GB。 客户可以利用此产品/服务来开发和部署使用 Azure Database for MySQL - 灵活服务器的应用程序。 [了解详细信息](./how-to-deploy-on-azure-free-account.md)。

- **存储自动增长**

  存储自动增长可防止服务器耗尽存储空间并变为只读。 如果启用了存储自动增长，存储会在不影响工作负荷的情况下自动增长。 从 2021 年 6 月 21 开始，所有新建的服务器默认将启用存储自动增长。 [了解详细信息](concepts-compute-storage.md#storage-auto-grow)。

- 数据传入复制

    灵活服务器现在支持[数据传入复制](concepts-data-in-replication.md)。 使用此功能可将本地运行的 MySQL 服务器、虚拟机、Azure Database for MySQL 单一服务器或 Azure 外部的数据库服务中的数据同步和迁移到 Azure Database for MySQL - 灵活服务器。 详细了解[如何配置数据传入复制](how-to-data-in-replication.md)。

- 在 Azure CLI 中提供 GitHub 操作支持

  灵活服务器 CLI 现在允许客户使用 GitHub 操作来自动完成部署更新的工作流。 借助此功能，可以使用 MySQL GitHub 操作工作流来设置和部署数据库更新。 这些 CLI 命令有助于设置存储库来实现持续部署，以简化开发工作。 [了解详细信息](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true)。

- 区域冗余的 HA 强制故障转移修补程序

  此版本包含针对强制故障转移相关已知问题的修补程序，用于确保每次故障转移后，能够持久保存服务器参数和其他 IOPS 更改。

- **已知问题**

  - 如果尝试在预配的存储小于 20 GB 的现有服务器上执行计算纵向扩展或纵向缩减操作，则不会成功完成。 通过将预配的存储纵向扩展为 20 GB 并重试计算缩放操作来解决此问题。

## <a name="may-2021"></a>2021 年 5 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- **扩展区域可用性（法国中部、巴西南部和瑞士北部）**

    公共预览版的 Azure Database for MySQL - 灵活服务器现已在法国中部、巴西南部和瑞士北部区域推出。 [了解详细信息](overview.md#azure-regions)。

- 可以禁用 SSL/TLS 1.2 强制实施

   此版本提供增强的灵活性，允许自定义 SSL 和最低 TLS 版本的强制实施。 有关详细信息，请参阅[使用加密连接连接到 Azure Database for MySQL - 灵活服务器](how-to-connect-tls-ssl.md)。

- **区域冗余高可用性已在英国南部和日本东部区域推出**

   Azure Database for MySQL - 灵活服务器现在在新增的以下两个区域提供区域冗余高可用性：英国南部和日本东部。 [了解详细信息](overview.md#azure-regions)。

- **已知问题**

  - 其他 IOPS 更改不会在启用了区域冗余高可用性的服务器中生效。 客户可以通过禁用高可用性、缩放 IOPS 和重新启用区域冗余高可用性来绕过此问题。
  - 强制执行故障转移后，门户中不会准确反映备用可用性区域。 （无变通方法）
  - 强制执行故障转移后，服务器参数更改不会在启用了区域冗余高可用性的服务器中生效。 （无变通方法）

## <a name="april-2021"></a>2021 年 4 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- **已发布以下功能：强制故障转移到具有区域冗余高可用性的备用服务器**

  现在，客户可以手动强制执行故障转移以测试其应用程序场景的功能，这可以帮助他们为发生任何中断做好准备。 [了解详细信息](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/forced-failover-for-azure-database-for-mysql-flexible-server/ba-p/2280671)。

- 已发布适用于灵活服务器的 PowerShell 模块

  开发人员现在可以使用 PowerShell 来预配、管理、操作和支持 MySQL 灵活服务器及相关资源。 [了解详细信息](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-the-mysql-flexible-server-powershell-module/ba-p/2203383)。

- 使用 Azure CLI 连接、测试和执行查询

  Azure Database for MySQL 灵活服务器现在提供改进的开发人员体验，支持客户使用 Azure CLI 以及“az mysql flexible-server connect”和“az mysql flexible-server execute”命令连接和执行对其服务器的查询。 [了解详细信息](connect-azure-cli.md#view-all-the-arguments)。

- 修复了使用专用访问在虚拟网络中创建服务器时发生预配失败的问题

  修复了在虚拟网络中创建服务器时导致的所有预配失败问题。 在此版本中，用户每次都可以成功使用专用访问创建灵活服务器。  

## <a name="march-2021"></a>2021 年 3 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- 已发布 MySQL 8.0.21

  MySQL 8.0.21 现已在所有主要 [Azure 区域](overview.md#azure-regions)中的灵活服务器上推出。 客户可以使用 Azure 门户、Azure CLI 或 Azure 资源管理器模板来预配 MySQL 8.0.21 版本。 [了解详细信息](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)。

- **已发布在服务器创建期间放置可用性区域的支持**

  现在，客户可以在创建服务器时指定其首选的可用性区域。 通过此功能，客户可以将 Azure VM、虚拟机规模集或 AKS 上托管的应用程序和数据库并置到相同的可用性区域，以最大限度地减少数据库延迟并提高性能。 [了解详细信息](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)。

- 修复了使用专用访问在虚拟网络中运行灵活服务器时出现的性能问题

  在此版本之前，灵活服务器在采用虚拟网络配置的情况下运行时，其性能会明显下降。 此版本修复了此问题，用户将会发现虚拟网络中灵活服务器的性能已得到提升。

- **已知问题**

  - SSL\TLS 1.2 是强制实施的，且无法禁用。 （无解决方法）
  - 在 VNet 中预配的服务器会发生间歇性的预配失败。 解决方法是重试服务器预配，直到成功为止。

## <a name="february-2021"></a>2021 年 2 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- 已发布“更多 IOPS”功能

  Azure Database for MySQL - 灵活服务器支持预配更多 [IOPS](concepts-compute-storage.md#iops)，无论已预配的存储量如何。 客户随时可以使用此功能根据其工作负载要求来增加或减少 IOPS 数量。

- **已知问题**

  在启用专用访问虚拟网络隔离的情况下，Azure Database for MySQL - 灵活服务器的性能会下降（无解决方法）。

## <a name="january-2021"></a>2021 年 1 月

此版本的 Azure Database for MySQL - 灵活服务器包含以下更新。

- MySQL - 灵活服务器最多有 10 个只读副本

  灵活服务器现在支持将一个 Azure Database for MySQL 服务器（“源”）中的数据异步复制到同一区域中的最多 10 个 Azure Database for MySQL 服务器（“副本”）。 此功能使得读取密集型工作负载能够根据用户的偏好横向扩展，并在副本服务器之间实现均衡。 [了解详细信息](concepts-read-replicas.md)。

## <a name="contacts"></a>联系人

如有 Azure Database for MySQL 用法方面的问题或者要提出相关建议，请根据情况通过以下方式联系我们：

- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请发送电子邮件至 AskAzureDBforMySQL@service.microsoft.com。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/server/)的详细信息。
- 浏览 Azure Database for MySQL - 灵活服务器的[公共文档](index.yml)。
- 查看有关[排查常见迁移错误](../howto-troubleshoot-common-errors.md)的详细信息。