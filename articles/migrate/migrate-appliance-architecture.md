---
title: Azure Migrate 设备体系结构
description: 概述在服务器发现、评估和迁移过程中使用的 Azure Migrate 设备。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 78b62c91148fc7a2c202cbf6792c1de442c142cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742026"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 设备体系结构

本文介绍 Azure Migrate 设备体系结构和过程。 Azure Migrate 设备是一种部署在本地的轻型设备，用于发现要迁移到 Azure 的服务器。

## <a name="deployment-scenarios"></a>部署方案

Azure Migrate 设备用于以下应用场景。

**方案** | **工具** | **用于**
--- | --- | ---
发现和评估 VMware 环境中运行的服务器 | Azure Migrate：发现和评估 | 发现在你的 VMware 环境中运行的服务器<br/><br/> 发现已安装的软件清单、ASP.NET Web 应用、SQL Server 实例和数据库，并执行无代理依赖项分析。<br/><br/> 收集服务器配置和性能元数据以进行评估。
在 VMware 环境中运行的服务器的无代理迁移 | Azure Migrate：服务器迁移 | 发现在你的 VMware 环境中运行的服务器。<br/><br/> 复制服务器，不在其上安装任何代理。
发现和评估在 Hyper-V 环境中运行的服务器 | Azure Migrate：发现和评估 | 发现在你的 Hyper-V 环境中运行的服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。
发现和评估本地的物理服务器或虚拟化服务器 |  Azure Migrate：发现和评估 |  发现本地的物理服务器或虚拟化服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。

## <a name="deployment-methods"></a>部署方法

可以使用几种方法来部署设备：

- 可以使用适用于在 VMware 或 Hyper-V 环境中运行的服务器的模板（[用于 VMware 的 OVA 模板](how-to-set-up-appliance-vmware.md)或[用于 Hyper-V 的 VHD](how-to-set-up-appliance-hyper-v.md)）来部署设备。
- 如果你不想使用模板，则可以使用 [PowerShell 安装程序脚本](deploy-appliance-script.md)为 VMware 或 Hyper-V 环境部署该设备。
- 在 Azure 政府版中，应该使用 PowerShell 安装程序脚本来部署该设备。 请参考[此处](deploy-appliance-script-government.md)的部署步骤。
- 对于本地或任何其他云中的物理服务器或虚拟化服务器，请始终使用 PowerShell 安装程序脚本来部署设备。请参考[此处](how-to-set-up-appliance-physical.md)的部署步骤。
- 下表提供了下载链接。

## <a name="appliance-services"></a>设备服务

设备包括以下服务：

- 设备配置管理器：这是一个 Web 应用程序，可以为其配置源详细信息以启动服务器的发现和评估。
- 发现代理：此代理收集可用于创建本地评估的服务器配置元数据。
- 评估代理：此代理收集可用于创建基于性能的评估的服务器性能元数据。
- 自动更新服务：此服务使在设备上运行的所有代理保持最新。 它每 24 小时自动运行一次。
- DRA 代理：协调服务器复制，并协调复制的服务器与 Azure 之间的通信。 只有在使用无代理迁移将服务器复制到 Azure 时，才会使用它。
- 网关：将复制的数据发送到 Azure。 只有在使用无代理迁移将服务器复制到 Azure 时，才会使用它。
- SQL 发现和评估代理：将 SQL Server 实例和数据库的配置和性能元数据发送到 Azure。
- **Web 应用发现和评估代理**：将 Web 应用配置数据发送到 Azure。

> [!Note]
> 最后 4 项服务仅适用于对 VMware 环境中运行的服务器进行发现和评估的设备。

## <a name="discovery-and-collection-process"></a>发现和收集过程

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="设备体系结构":::

设备使用以下过程与发现源进行通信。

**处理** | VMware 设备 | Hyper-V 设备 | **物理设备**
---|---|---|---
**启动发现** | 默认情况下，设备通过 TCP 端口 443 与 vCenter Server 进行通信。 如果 vCenter Server 侦听其他端口，则可以在设备配置管理器中配置该端口。 | 设备通过 WinRM 端口 5985 (HTTP) 与 Hyper-V 主机进行通信。 | 设备通过 WinRM 端口 5985 (HTTP) 与 Windows 服务器进行通信，通过端口 22 (TCP) 与 Linux 服务器进行通信。
收集配置和性能元数据 | 设备通过在端口 443（默认端口）上或 vCenter Server 侦听的任何其他端口上进行连接，使用 vSphere API 收集在 vCenter Server 上运行的服务器的元数据。 | 设备通过在端口 5985 上与 Hyper-V 主机进行通用信息模型 (CIM) 会话，收集这类主机上运行的服务器的元数据。| 设备通过在端口 5985 上与 Windows 服务器进行通用信息模型 (CIM) 会话，从这类服务器收集元数据，通过在端口 22 上使用 SSH 连接从 Linux 服务器收集元数据。
发送发现数据 | 设备将收集的数据发送到“Azure Migrate: 发现和评估”以及“Azure Migrate: 通过 SSL 端口 443 迁移服务器”。<br/><br/>  设备可以通过 Internet 或通过 ExpressRoute 专用对等互连或 Microsoft 对等互连线路连接到 Azure。 | 设备通过 SSL 端口 443 将收集的数据发送到“Azure Migrate: 发现和评估”。<br/><br/> 设备可以通过 Internet 或通过 ExpressRoute 专用对等互连或 Microsoft 对等互连线路连接到 Azure。 | 设备通过 SSL 端口 443 将收集的数据发送到“Azure Migrate: 发现和评估”。<br/><br/> 设备可以通过 Internet 或通过 ExpressRoute 专用对等互连或 Microsoft 对等互连线路连接到 Azure。 
数据收集频率 | 配置元数据每 30 分钟收集并发送一次。 <br/><br/> 性能元数据每 20 秒收集一次，并进行聚合，每 10 分钟向 Azure 发送一个数据点。 <br/><br/> 软件清单数据每 12 小时向 Azure 发送一次。 <br/><br/> 无代理依赖项数据每 5 分钟收集一次，并聚合在设备上，每 6 小时向 Azure 发送一次。 <br/><br/> SQL Server 配置数据每 24 小时更新一次，性能数据每 30 秒捕获一次。 <br/><br/> Web 应用配置数据每 24 小时更新一次。 不会为 Web 应用捕获性能数据。| 配置元数据每 30 分钟收集并发送一次。 <br/><br/> 性能元数据每 30 秒收集一次，并进行聚合，每 10 分钟向 Azure 发送一个数据点。|  配置元数据每 30 分钟收集并发送一次。 <br/><br/> 性能元数据每 5 分钟收集一次，并进行聚合，每 10 分钟向 Azure 发送一个数据点。
**评估和迁移** | 你可以使用“Azure Migrate: 发现和评估”工具基于设备收集的元数据创建评估。<br/><br/>此外，你还可以使用“Azure Migrate: 服务器迁移”工具开始迁移在 VMware 环境中运行的服务器，以便协调无代理服务器复制。| 你可以使用“Azure Migrate: 发现和评估”工具基于设备收集的元数据创建评估。 | 你可以使用“Azure Migrate: 发现和评估”工具基于设备收集的元数据创建评估。

## <a name="next-steps"></a>后续步骤

[查看](migrate-appliance.md)设备支持矩阵。