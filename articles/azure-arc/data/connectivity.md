---
title: 连接模式和要求
description: 介绍从你的环境到 Azure 的已启用 Azure Arc 的数据服务连接选项
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: 0cd1d38bf2a96e8530c9647a6b09ad55893e569a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748741"
---
# <a name="connectivity-modes-and-requirements"></a>连接模式和要求


## <a name="connectivity-modes"></a>连接模式

从已启用 Azure Arc 的数据服务环境到 Azure 的连接程度有多种选项。 根据业务策略、政府法规或与 Azure 的网络连接的可用性，你的要求会不同，你可以从以下连接模式中进行选择。

已启用 Azure Arc 的数据服务向你提供在两种不同连接模式下连接到 Azure 的选项： 

- 直接连接 
- 间接连接

利用连接模式，你可以灵活地选择发送到 Azure 的数据量，以及用户与 Arc 数据控制器的交互方式。 根据所选的连接模式，已启用 Azure Arc 的数据服务的某些功能可能可用也可能不可用。

重要的是，如果已启用 Azure Arc 的数据服务直接连接到 Azure，则用户可以使用 [Azure 资源管理器 API](/rest/api/resources/)、Azure CLI 和 Azure 门户来操作 Azure Arc 数据服务。 直接连接模式的体验非常类似于在 Azure 门户中使用任何其他 Azure 服务进行预配/取消预配、缩放、配置等的体验。  如果已启用 Azure Arc 的数据服务间接连接到 Azure，则 Azure 门户为只读视图。 你可以查看已部署的 SQL 托管实例和 Postgres 超大规模实例的清单，以及有关它们的详细信息，但无法在 Azure 门户中对其执行操作。  在间接连接模式下，必须使用 Azure Data Studio、相应的 CLI 或 Kubernetes 原生工具（如 kubectl）在本地执行所有操作。

此外，Azure Active Directory 和 Azure 基于角色的访问控制只能在直接连接模式下使用，因为这种方式依赖于与 Azure 之间的连续直接连接来提供此功能。

某些 Azure 附加服务仅在可直接访问时才可用，例如 Azure Defender 安全服务、容器见解和到 Blob 存储的 Azure 备份。

||**间接连接**|**直接连接**|**从未连接**|
|---|---|---|---|
|**说明**|间接连接模式在环境中本地提供大多数管理服务，而不直接连接到 Azure。  仅出于清单和计费目的，必须将最小数量的数据发送到 Auzre。 此数据每月至少导出一次到文件并上传到 Azure。  不需要直接或连续连接到 Azure。  某些需要连接到 Azure 的功能和服务将无法使用。|直接连接模式提供了可与 Azure 建立直接连接时的所有可用服务。 连接始终从你的环境启动到 Azure，并使用标准端口和协议（如 HTTPS/443）。|不能以任何方式将数据发送到 Azure 或从 Azure 发送数据。|
|**当前可用性**| 可用 |以预览版提供。|目前尚不支持。|
|**典型用例**|本地数据中心，因业务或法规符合性策略或出于担心外部攻击或数据泄露，其不允许数据中心的数据区域内外的连接。  典型示例：金融机构、医疗保健业、政府。 <br/><br/>边缘站点通常未连接到 Internet 的边缘站点位置。  典型示例：石油/天然气或军事领域应用程序。  <br/><br/>有长时间中断的间接性连接的边缘站点位置。  典型示例：体育场、大型游轮。 | 使用公有云的组织。  典型示例： Azure、AWS 或 Google Cloud。<br/><br/>通常存在和允许 Internet 连接的边缘站点位置。  典型示例：零售商店、制造业。<br/><br/>具有更多宽容策略的公司数据中心，可用于到数据中心的数据区域的连接，或从数据中心的数据区域连接到 Internet。  典型示例：非管控企业、中小型企业|真正的“气隙”环境，其中在任何情况下都没有来自数据环境的数据。 典型示例：最高机密政府设施。|
|**如何将数据发送到 Azure**|如何向 Azure 发送计费和清单数据有三个选项：<br><br> 1) 通过连接到安全数据区域和 Azure 的自动化过程，将数据导出数据区域。<br><br>2) 通过数据区域中的自动化过程将数据从数据区域导出，自动将其复制到安全级别较低的区域，然后该区域中的自动过程将数据上传到 Azure。<br><br>3) 数据由安全区域内的用户手动导出，手动将其带出安全区域，并手动上传到 Azure。 <br><br>前两个选项是一个自动化的连续过程，可以安排频繁运行，因此将数据传输到 Azure 的延迟最小，但仅限于与 Azure 的可用连接。|数据自动并持续发送到 Azure。|数据永远不会发送到 Azure。|

## <a name="feature-availability-by-connectivity-mode"></a>按连接模式的功能可用性

|**功能**|**间接连接**|**直接连接**|
|---|---|---|
|**自动高可用性**|支持|支持|
|**自助式预配**|支持<br/>可通过 Azure Data Studio、相应的 CLI 或 Kubernetes 原生工具（helm、kubectl、oc 等），或使用已启用 Azure Arc 的 Kubernetes GitOps 预配来完成创建。|支持<br/>除了间接连接模式创建选项以外，还可以通过 Azure 门户、Azure 资源管理器 API、Azure CLI 或 ARM 模板进行创建。 **直接连接模式的待定可用性**
|**弹性的可伸缩性**|支持|支持<br/>**直接连接模式的待定可用性**|
|**Billing**|支持<br/>计费数据会定期导出并发送到 Azure。|支持<br/>计费数据自动并持续发送到 Azure 并准实时反映。 **直接连接模式的待定可用性**|
|**库存管理**|支持<br/>清单数据定期导出并发送到 Azure。<br/><br/>使用客户端工具（如 Azure Data Studio、Azure Data CLI 或 `kubectl`）在本地查看和管理清单。|支持<br/>清单数据自动并持续发送到 Azure 并准实时反映。 因此，你可以直接从 Azure 门户管理清单。|
|**自动升级和修补**|支持<br/>数据控制器必须能够直接访问 Microsoft Container Registry (MCR)，或需要从 MCR 拉取容器映像，并将其推送到数据控制器有权访问的本地专用容器注册表。|支持<br/>**直接连接模式的待定可用性**|
|**自动备份和还原**|支持<br/>自动本地备份和还原。|支持<br/>除了自动本地备份和还原外，你可以选择将备份发送到 Azure 备份以进行长期、异地保留。 **直接连接模式的待定可用性**|
|**Monitoring**|支持<br/>使用 Grafana 和 Kibana 仪表板进行本地监视。|支持<br/>除了本地监视仪表板，你可以选择将监视数据和日志发送到 Azure Monitor 以在一个位置进行多个站点的大规模监视。 **直接连接模式的待定可用性**|
|**身份验证**|使用本地用户名/密码进行数据控制器和仪表板身份验证。 使用 SQL 和 Postgres 登录名或 Active Directory（AD 当前不受支持，不久将发行预览版）连接到数据库实例。  使用 K8s 身份验证提供程序对 Kubernetes API 进行身份验证。|除了间接连接模式的身份验证方法，你可以选择使用 Azure Active Directory。 **直接连接模式的待定可用性**|
|**基于角色的访问控制 (RBAC)**|在 Kubernetes API 上使用 Kubernetes RBAC。 对数据库实例使用 SQL 和 Postgres RBAC。|可使用 Azure Active Directory 和 Azure RBAC。|
|**Azure Defender**|不支持|规划未来|

## <a name="connectivity-requirements"></a>连接要求

**某些功能需要连接到 Azure。**

**与 Azure 的所有通信始终从你的环境中启动。** 即使对于操作（由 Azure 门户中的用户启动）来说也是如此。  在这种情况下，实际上有一项任务，即在 Azure 中排队。  你的环境中的代理启动与 Azure 的通信，以查看队列中有哪些任务，运行任务，并将状态/完成/失败报告回 Azure。

|**数据类型**|**方向**|**必需/可选**|**额外成本**|**需要的模式**|**备注**|
|---|---|---|---|---|---|
|**容器映像**|Microsoft Container Registry -> 客户|必选|否|间接或直接|容器映像是分发软件的方法。  在可通过 Internet 连接到 Microsoft Container Registry (MCR) 的环境中，可以直接从 MCR 拉取容器映像。  如果部署环境没有直接连接，你可以从 MCR 中拉取映像，并将其推送到部署环境中的专用容器注册表。  在创建时，你可以将创建过程配置为从专用容器注册表而不是从 MCR 中拉取。 这也将适用于自动更新。|
|**资源清单**|客户环境 -> Azure|必选|否|间接或直接|数据控制器或数据库实例（PostgreSQL 和 SQL）的清单将保留在 Azure 中以便计费，还用于在一个位置创建所有数据控制器和数据库实例的清单，如果你有多个使用 Azure Arc 数据服务的环境，将特别有用。  对实例进行预配、取消预配、横向扩展/缩减、纵向扩展/缩减时，清单会在 Azure 中更新。|
|**计费遥测数据**|客户环境 -> Azure|必选|否|间接或直接|必须将数据库实例的使用情况发送到 Azure 以便计费。 |
|**监视数据和日志**|客户环境 -> Azure|可选|可能取决于数据量（请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)）|间接或直接|你可能想要将本地收集的监视数据和日志发送到 Azure Monitor，以便将跨多个环境的数据聚合到一个位置，还可以使用 Azure Monitor 服务（如警报）、使用 Azure 机器学习中的数据等。|
|**Azure 基于角色的访问控制 (Azure RBAC)**|客户环境 -> Azure -> 客户环境|可选|否|仅直接|如果要使用 Azure RBAC，则必须始终与 Azure 建立连接。  如果你不想使用 Azure RBAC，则可以使用本地 Kubernetes RBAC。|
|**Azure Active Directory (AAD)（将来版本）**|客户环境 -> Azure -> 客户环境|可选|或许需要付费，但你可能已为 Azure AD 付费|仅直接|如果要使用 Azure AD 进行身份验证，则必须始终与 Azure 建立连接。 如果不想使用 Azure AD 进行身份验证，则可以使用 Active Directory 联合身份验证服务 (ADFS) 而不是 Active Directory。 **直接连接模式的待定可用性**|
|**备份和还原**|客户环境 -> 客户环境|必选|否|直接或间接|备份和还原服务可以配置为指向本地存储类。 **直接连接模式的待定可用性**|
|**Azure 备份 - 长期保留（将来版本）**| 客户环境 -> Azure | 可选| Azure 存储是必需的 | 仅直接 |你可能想要将在本地执行的备份发送到 Azure 备份，以便进行长期、异地备份保留，并将其恢复到本地环境以进行还原。 **直接连接模式的待定可用性**|
|**Azure Defender 安全服务（将来版本）**|客户环境 -> Azure -> 客户环境|可选|是|仅直接|**直接连接模式的待定可用性**|
|**Azure 门户中的预配和配置更改**|客户环境 -> Azure -> 客户环境|可选|否|仅直接|通过使用 Azure Data Studio 或相应的 CLI 在本地完成预配和配置更改。  在直接连接模式下，你还可以从 Azure 门户进行预配和配置更改。|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>有关 Internet 地址、端口、加密和代理服务器支持的详细信息

目前只有间接连接模式已正式发布。 在此模式下，Internet 上可用的服务仅需要三个连接。 这些连接包括：

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure 资源管理器 API](#azure-resource-manager-apis)
- [Azure Monitor API](#azure-monitor-apis)

使用 SSL/TLS 并使用官方签名和可验证的证书对所有与 Azure 的 HTTPS 连接和 Microsoft Container Registry 进行加密。

以下部分提供了有关这些连接的详细信息。 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Microsoft Container Registry 托管已启用 Azure Arc 的数据服务容器映像。  可以从 MCR 拉取这些映像，并将其推送到专用容器注册表，也可将数据控制器部署过程配置为从该专用容器注册表拉取容器映像。

#### <a name="connection-source"></a>连接源

每个 Kubernetes 节点上的 Kubernetes kubelet 都会拉取容器映像。

#### <a name="connection-target"></a>连接目标

`mcr.microsoft.com`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication

无

### <a name="azure-resource-manager-apis"></a>Azure 资源管理器 API
Azure Data Studio 和 Azure CLI 连接到 Azure 资源管理器 API，以在 Azure 中传输和检索数据，从而获取某些功能。

#### <a name="connection-source"></a>连接源

运行 Azure Data Studio 或 Azure CLI 以连接到 Azure 的计算机。

#### <a name="connection-target"></a>连接目标

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure Monitor API

Azure Data Studio 和 Azure CLI 连接到 Azure 资源管理器 API，以在 Azure 中传输和检索数据，从而获取某些功能。

#### <a name="connection-source"></a>连接源

运行 Azure CLI 以将监视指标或日志上传到 Azure Monitor 的计算机。

#### <a name="connection-target"></a>连接目标

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication 

Azure Active Directory

> [!NOTE]
> 目前，所有发送到用于运行 `az arcdata dc export` 命令的数据控制器和发送到 Grafana 和 Kibana 仪表板的浏览器 HTTPS/443 连接均使用自签名证书进行 SSL 加密。  未来将推出一项功能，让你能够提供自己的证书来加密这些 SSL 连接。

从 Azure Data Studio 到 Kubernetes API 服务器的连接使用你已建立的 Kubernetes 身份验证和加密。  使用 Azure Data Studio 或 CLI 的每个用户都必须拥有到 Kubernetes API 的已经过身份验证的连接，才能执行与已启用 Azure Arc 的数据服务相关的许多操作。

