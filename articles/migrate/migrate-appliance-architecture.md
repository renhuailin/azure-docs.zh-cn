---
title: Azure Migrate 设备体系结构
description: 概述了服务器评估和迁移期间使用的 Azure Migrate 设备。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: d695758849fd4f7e6f595820221f6b8606fe7cf1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096184"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 设备体系结构

本文介绍 Azure Migrate 设备体系结构和过程。 Azure Migrate 设备是一种轻型设备，部署在本地，用于发现要迁移到 Azure 的 Vm 和物理服务器。

## <a name="deployment-scenarios"></a>部署方案

Azure Migrate 设备用于以下应用场景。

**方案** | **工具** | **用于** 
--- | --- | ---
**发现和评估 VMware 环境中运行的服务器** | Azure Migrate：服务器评估 | 发现 VMware 环境中运行的服务器<br/><br/> 执行发现已安装的应用程序、无代理依赖项分析和发现 SQL Server 实例和数据库。<br/><br/> 收集服务器配置和性能元数据以进行评估。
**在 VMware 环境中运行的服务器的无代理迁移** | Azure Migrate：服务器迁移 | 发现在 VMware 环境中运行的服务器。<br/><br/> 复制服务器，无需在其上安装任何代理。
**发现和评估在 Hyper-v 环境中运行的服务器** | Azure Migrate：服务器评估 | 发现在 Hyper-v 环境中运行的服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。
**发现和评估本地物理或虚拟化服务器** |  Azure Migrate：服务器评估 |  发现本地物理或虚拟化服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。

## <a name="deployment-methods"></a>部署方法

可以使用几种方法来部署设备：

- 可以使用在 VMware 或 Hyper-v 环境中运行的服务器的模板来部署该设备 (适用于 [vmware 的 .ova 模板](how-to-set-up-appliance-vmware.md) 或 [Hyper-v) 的 VHD](how-to-set-up-appliance-hyper-v.md) 。
- 如果你不想使用模板，则可以使用 [PowerShell 安装程序脚本](deploy-appliance-script.md)为 VMware 或 hyper-v 环境部署该设备。
- 在 Azure 政府版中，应使用 PowerShell 安装程序脚本部署设备。 请参阅 [此处](deploy-appliance-script-government.md)的部署步骤。
- 对于本地或任何其他云上的物理服务器或虚拟服务器，你始终使用 PowerShell 安装程序脚本来部署该设备。请参阅 [此处](how-to-set-up-appliance-physical.md)的部署步骤。
- 下表提供了下载链接。

## <a name="appliance-services"></a>设备服务

设备具有以下服务：

- **设备配置管理器**：这是一个 web 应用程序，可配置为使用源详细信息来启动服务器的发现和评估。 
- **发现代理**：代理收集可用于创建作为本地评估的服务器配置元数据。
- **评估代理**：代理收集可用于创建基于性能的评估的服务器性能元数据。
- **自动更新服务**：服务使在设备上运行的所有代理保持最新。 它每24小时自动运行一次。
- **DRA 代理**：协调服务器复制，并协调复制服务器和 Azure 之间的通信。 仅当使用无代理迁移将服务器复制到 Azure 时使用。
- 网关：将复制的数据发送到 Azure。 仅当使用无代理迁移将服务器复制到 Azure 时使用。
- **SQL 发现和评估代理**：将 SQL Server 实例和数据库的配置和性能元数据发送到 Azure。

> [!Note]
> 最后3个服务仅在用于发现和评估 VMware 环境中运行的服务器的设备中可用。<br/> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。


## <a name="discovery-and-collection-process"></a>发现和收集过程

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="设备体系结构":::

设备使用以下过程与发现源通信。

**处理** | **VMware 设备** | **Hyper-v 设备** | **物理设备**
---|---|---|---
**开始发现** | 默认情况下，设备会与 TCP 端口443上的 vCenter 服务器进行通信。 如果 vCenter 服务器侦听其他端口，则可以在 "设备配置管理器" 中对其进行配置。 | 设备与 WinRM 端口5985上的 Hyper-v 主机通信 (HTTP) 。 | 设备通过 WinRM 端口5985上的 Windows 服务器 (HTTP) 通过端口 22 (TCP) 与 Linux 服务器进行通信。
**收集配置和性能元数据** | 设备通过连接端口 443 (默认端口) 或 vCenter Server 侦听的任何其他端口，收集 vCenter Server 上运行的服务器的元数据。 | 设备使用与端口5985上的主机通用信息模型 (CIM) 会话，收集 Hyper-v 主机上运行的服务器的元数据。| 设备使用与端口5985上的服务器通用信息模型 (CIM) 会话，以及在端口22上使用 SSH 连接从 Linux 服务器收集来自 Windows 服务器的元数据。
**发送发现数据** | 设备会将收集的数据发送到 Azure Migrate：服务器评估和 Azure Migrate：通过 SSL 端口443迁移服务器。<br/><br/> 设备可以通过 internet 或 ExpressRoute 连接到 Azure (需要 Microsoft 对等互连) 。 | 设备将收集的数据发送到 Azure Migrate：通过 SSL 端口443的服务器评估。<br/><br/> 设备可以通过 internet 或 ExpressRoute 连接到 Azure (需要 Microsoft 对等互连) 。| 设备将收集的数据发送到 Azure Migrate：通过 SSL 端口443的服务器评估。<br/><br/> 设备可以通过 internet 或 ExpressRoute 连接到 Azure (需要 Microsoft 对等互连) 。
**数据收集频率** | 每30分钟收集一次配置元数据并发送。 <br/><br/> 每隔20秒收集一次性能元数据，并将其聚合起来，每10分钟发送一次数据点到 Azure。 <br/><br/> 软件清单数据每12小时发送到 Azure 一次。 <br/><br/> 无代理依赖项数据每5分钟收集一次，并聚合在设备上，每隔6小时发送到 Azure。 <br/><br/> SQL Server 配置数据每24小时更新一次，每隔30秒捕获一次性能数据。| 每30分钟收集一次配置元数据并发送。 <br/><br/> 每隔30秒收集一次性能元数据，并将其聚合起来，每10分钟发送一次数据点到 Azure。|  每30分钟收集一次配置元数据并发送。 <br/><br/> 性能元数据每5分钟收集一次，并聚合为每10分钟发送一次数据点到 Azure。
**评估和迁移** | 你可以使用 Azure Migrate： Server 评估工具从设备收集的元数据中创建评估。<br/><br/>此外，还可以使用 Azure Migrate： Server 迁移工具开始迁移在 VMware 环境中运行的服务器，以便协调无代理服务器复制。| 你可以使用 Azure Migrate： Server 评估工具从设备收集的元数据中创建评估。 | 你可以使用 Azure Migrate： Server 评估工具从设备收集的元数据中创建评估。

## <a name="next-steps"></a>后续步骤

[查看](migrate-appliance.md) 设备支持矩阵。