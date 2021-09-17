---
title: 用于在 Azure 虚拟机上部署 Oracle 应用的体系结构 | Microsoft Docs
description: 用于在 Microsoft Azure 虚拟机上使用 Azure 或 Oracle Cloud Infrastructure (OCI) 中的数据库部署 Oracle 应用（包括 E-Business Suite、JD Edwards EnterpriseOne 和 PeopleSoft）的应用程序体系结构。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: ac9c699addf996178b1d6aabdf010267bda75964
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690066"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>用于在 Azure 上部署 Oracle 应用程序的体系结构

**适用于：** :heavy_check_mark: Linux VM 

Microsoft 和 Oracle 竭诚合作，让客户能够在云中部署 Oracle E-Business Suite、JD Edwards EnterpriseOne 和 PeopleSoft 等 Oracle 应用程序。 通过引入 Microsoft Azure 与 Oracle Cloud Infrastructure (OCI) 之间的预览[专用网络互连](configure-azure-oci-networking.md)，现在可以在 Azure 上使用 Azure 或 OCI 中的后端数据库部署 Oracle 应用程序。 Oracle 应用程序还可以与 Azure Active Directory 集成，从而允许你设置单一登录，以便用户可以使用其 Azure Active Directory (Azure AD) 凭据登录到 Oracle 应用程序。

OCI 为 Oracle 应用程序提供了多种 Oracle 数据库选项，包括 DBaaS、Exadata Cloud Service、Oracle RAC 和基础结构即服务 (IaaS)。 目前，Oracle 应用程序不支持将自治数据库作为后端。

可通过[多种选项](oracle-overview.md)在 Azure 中部署 Oracle 应用程序，包括以高度可用且安全的方式。 如果选择完全在 Azure 上运行 Oracle 应用程序，Azure 还提供可部署的 [Oracle 数据库 VM 映像](oracle-vm-solutions.md)。

以下部分概述了 Microsoft 和 Oracle 提出的体系结构建议，以将 Oracle E-Business Suite、JD Edwards EnterpriseOne 和 PeopleSoft 部署到跨云配置中或完全部署到 Azure 中。 Microsoft 和 Oracle 已经测试了这些应用程序，并确认其性能符合 Oracle 为这些应用程序设定的标准。

## <a name="architecture-considerations"></a>体系结构注意事项

Oracle 应用程序由多个服务组成，这些服务可以托管在 Azure 中的相同或多个虚拟机上，也可以托管在 OCI 中。 

你可以使用专用或公共终结点来设置应用程序实例。 Microsoft 和 Oracle 建议在单独的子网中使用公共 IP 地址设置堡垒主机 VM，以管理应用程序。 然后，仅将专用 IP 地址分配给其他虚拟机，包括数据库层。 

在跨云体系结构中设置应用程序时，需要进行规划，以确保 Azure 虚拟网络中的 IP 地址空间不与 OCI 虚拟云网络中的专用 IP 地址空间重叠。

为了提高安全性，请在子网级别设置网络安全组，以确保仅允许特定端口和 IP 地址上的流量通过。 例如，中间层的虚拟机只能从虚拟网络内部接收流量。 外部流量不应直接到达中间层虚拟机。

为实现高可用性，你可以在相同的可用性集或不同的可用性区域中设置不同服务器的冗余实例。 可用性区域可让你实现 99.99% 的正常运行时间 SLA，而可用性集可让你在区域内实现 99.95% 的正常运行时间 SLA。 本文中显示的示例体系结构跨两个可用性区域进行部署。

使用跨云互连部署应用程序时，可以继续使用现有的 ExpressRoute 线路将 Azure 环境连接到本地网络。 但是，与 OCI 互连时，需要使用单独的 ExpressRoute 线路，而不能使用连接到本地网络的 ExpressRoute 线路。

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) 是一个应用程序套件，其中包括供应链管理 (SCM) 和客户关系管理 (CRM)。 若要利用 OCI 的托管数据库项目组合，可以使用 Microsoft Azure 和 OCI 之间的跨云互连来部署 EBS。 在此配置中，呈现层和应用层在 Azure 中运行，数据库层则在 OCI 中运行，如以下体系结构图（图 1）所示。

![E-Business Suite 跨云体系结构](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*图 1：E-Business Suite 跨云体系结构* 

在此体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用层在 Azure 中设置，数据库则在 OCI 中设置。 建议将每个组件部署到其自己的子网中，并添加网络安全组，以仅允许来自特定端口上特定子网的流量通过。

你也可以将此体系结构调整为完全部署在 Azure 上，此模式在一个区域的两个可用性区域中使用 Oracle Data Guard 配置高度可用的 Oracle 数据库。 下图（图 2）是此体系结构模式的示例：

![E-Business Suite 仅 Azure 体系结构](media/oracle-oci-applications/ebs-arch-azure.png)

*图 2：E-Business Suite 仅 Azure 体系结构*

以下部分概要介绍了各种组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>应用（中间）层

应用层隔离在其自己的子网中。 为了实现容错和简化补丁管理，该层设置了多个虚拟机。 这些 VM 可由 Azure NetApp 文件和超级 SSD 提供的共享存储提供支持。 使用此配置，可以更轻松地部署补丁而不会造成停机。 应在应用层的虚拟机前面放置一个公共负载均衡器，这样，即使该层中的一台虚拟机由于故障而脱机，对 EBS 应用层的请求也能得到处理。

### <a name="load-balancer"></a>负载均衡器

使用 Azure 负载均衡器可以在工作负载的多个实例之间分配流量，以确保高可用性。 在本例中，我们设置了公共负载均衡器，因为用户可以通过 Web 访问 EBS 应用程序。 负载均衡器将负载分配给中间层的两台虚拟机。 为了提高安全性，仅允许来自以下用户的流量通过：使用站点到站点 VPN 或 ExpressRoute 和网络安全组从公司网络访问系统的用户。

### <a name="database-tier"></a>数据库层

此层托管 Oracle 数据库，并隔离在其自己的子网中。 建议添加网络安全组，以仅允许 Oracle 特定数据库端口 1521 上从应用层到数据库层的流量通过。

Microsoft 和 Oracle 建议采用高可用性设置。 通过使用 Oracle Data Guard 在两个可用性区域中设置两个 Oracle 数据库，或通过在 OCI 中使用 Oracle Database Exadata Cloud Service，可以实现 Azure 中的高可用性。 使用 Oracle Database Exadata Cloud Service 时，数据库部署在两个子网中。 你还可以使用 Oracle Data Guard 在 OCI 的两个可用性域的 VM 中设置 Oracle 数据库。


### <a name="identity-tier"></a>标识层

标识层包含 EBS Asserter VM。 使用 EBS Asserter 可以同步来自 Oracle Identity Cloud Service (IDCS) 和 Azure AD 的标识。 之所以需要 EBS Asserter，是因为 EBS 不支持 SAML 2.0 或 OpenID Connect 之类的单一登录协议。 EBS Asserter 使用 OpenID 连接令牌（由 IDCS 生成），对其进行验证，然后为 EBS 中的用户创建会话。 

虽然此体系结构显示了 IDCS 集成，但也可以使用 Oracle Internet Directory 或 Oracle Unified Directory 通过 Oracle Access Manager 来启用 Azure AD 统一访问和单一登录。 有关详细信息，请参阅 [Deploying Oracle EBS with IDCS Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf)（使用 IDCS 集成部署 Oracle EBS）或 [Deploying Oracle EBS with OAM Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)（使用 OAM 集成部署 Oracle EBS）白皮书。

为实现高可用性性，建议跨多个可用性区域部署 EBS Asserter 的冗余服务器，并在其前面放置负载均衡器。

设置完基础结构之后，可以按照 Oracle 提供的安装指南来安装 E-Business Suite。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle 的 JD Edwards EnterpriseOne 是一个集成的应用程序套件，其中包含全面的企业资源计划软件。 它是一个多层应用程序，可以使用 Oracle 或 SQL Server 数据库后端进行设置。 本部分介绍有关使用 OCI 或 Azure 中的 Oracle 数据库后端部署 JD Edwards EnterpriseOne 的详细信息。

在下面推荐的体系结构（图 3）中，管理层、呈现层和中间层部署到 Azure 的虚拟网络中。 数据库部署在 OCI 的虚拟云网络中。

与 E-Business Suite 一样，你可以设置一个可选的堡垒层，以实现安全管理。 将堡垒 VM 主机用作跳转服务器，以访问应用程序和数据库实例。

![JD Edwards EnterpriseOne 跨云体系结构](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*图 3：JD Edwards EnterpriseOne 跨云体系结构*

在此体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用层在 Azure 中设置，数据库则在 OCI 中设置。 建议将每个组件部署到其自己的子网中，并添加网络安全组，以仅允许来自特定端口上特定子网的流量通过。

你也可以将此体系结构调整为完全部署在 Azure 上，此模式在一个区域的两个可用性区域中使用 Oracle Data Guard 配置高度可用的 Oracle 数据库。 下图（图 4）是此体系结构模式的示例：

![JD Edwards EnterpriseOne 仅 Azure 体系结构](media/oracle-oci-applications/jdedwards-arch-azure.png)

*图 4：JD Edwards EnterpriseOne 仅 Azure 体系结构*

以下部分概要介绍了各种组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>管理层

顾名思义，此层用于管理任务。 你可以为管理层划分一个单独的子网。 此层中的服务和服务器主要用于应用程序的安装和管理。 因此，部署这些服务器的单个实例就足够了。 无需部署冗余实例来实现应用程序的高可用性。

此层的组件如下所示：
    
 - **预配服务器** - 该服务器用于应用程序不同组件的端到端部署。 它通过端口 22 与其他层中的实例（包括数据库层中的实例）通信。 它托管 JD Edwards EnterpriseOne 的服务器管理器控制台。
 - **部署服务器** - 该服务器主要用于安装 JD Edwards EnterpriseOne。 在安装过程中，该服务器充当所需文件和安装包的中央存储库。 软件从该服务器分发或部署到其他服务器和客户端。
 - **开发客户端** - 此服务器包含在 Web 浏览器以及本机应用程序中运行的组件。

### <a name="presentation-tier"></a>呈现层

此层包含各种组件，例如应用程序接口服务 (AIS)、应用程序开发框架 (ADF) 和 Java 应用程序服务器 (JAS)。 此层的服务器与中间层的服务器通信。 其前面放置一个负载均衡器，该负载均衡器根据接收流量的端口号和 URL 将流量路由到必要的服务器。 建议为每种服务器类型部署多个实例，以实现高可用性。

此层的组件如下所示：
    
- **应用程序接口服务 (AIS)** - AIS 服务器提供 JD Edwards EnterpriseOne 移动企业应用程序和 JD Edwards EnterpriseOne 之间的通信接口。
- **Java 应用程序服务器 (JAS)** - JAS 接收来自负载均衡器的请求，并将其传递给中间层以执行复杂的任务。 JAS 能够执行简单的业务逻辑。
- **BI 发布服务器 (BIP)** - 此服务器根据 JD Edwards EnterpriseOne 应用程序收集的数据来呈现报告。 你可以根据不同的模板来设计和控制报告呈现数据的方式。
- **业务服务服务器 (BSS)** - BSS 支持与其他 Oracle 应用程序进行信息交换和互操作。
- **实时事件服务器 (RTE)** - 通过 RTE 服务器，你可以向外部系统设置有关 JDE EnterpriseOne 系统中发生的事务的通知。 它使用订阅者模型，允许第三方系统订阅事件。 若要对两个 RTE 服务器的请求进行负载均衡，请确保服务器位于群集中。
- **应用程序开发框架 (ADF) 服务器** - ADF 服务器用于运行使用 Oracle ADF 开发的 JD Edwards EnterpriseOne 应用程序。 它部署在具有 ADF 运行时的 Oracle WebLogic 服务器上。

### <a name="middle-tier"></a>中间层

中间层包含逻辑服务器和批处理服务器。 在本例中，这两个服务器安装在同一虚拟机上。 但是，在生产场景中，建议将逻辑服务器和批处理服务器部署到不同的服务器上。 为了提高可用性，可在中间层跨两个可用性区域部署多个服务器。 应创建一个 Azure 负载均衡器，将服务器放置在其后端池中，以确保这两个服务器都处于活动状态并且正在处理请求。

中间层的服务器仅接收来自呈现层服务器和公共负载均衡器的请求。 网络安全组规则必须设置为拒绝来自呈现层子网和负载均衡器之外的任何地址的流量。 为便于管理，还可以设置一个 NSG 规则，以允许端口 22 上来自堡垒主机的流量通过。 你可以使用公共负载均衡器对中间层 VM 之间的请求进行负载均衡。

以下两个组件位于中间层：

- **逻辑服务器** - 包含业务逻辑或业务函数。
- **批处理服务器** - 用于批处理

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 的 PeopleSoft 应用程序套件包含用于人力资源和财务管理的软件。 该应用程序套件分为多层，其应用程序包括人力资源管理系统 (HRMS)、客户关系管理 (CRM)、财务和供应链管理 (FSCM) 以及企业绩效管理 (EPM)。

建议将该软件套件的每个层部署在其自己的子网中。 需要使用 Oracle 数据库或 Microsoft SQL Server 作为应用程序的后端数据库。 本部分介绍有关使用 Oracle 数据库后端部署 PeopleSoft 的详细信息。

下面是用于在跨云体系结构中部署 PeopleSoft 应用程序套件的规范体系结构（图 5）。

![PeopleSoft 跨云体系结构](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*图 5：PeopleSoft 跨云体系结构*

在此示例体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用层在 Azure 中设置，数据库则在 OCI 中设置。 建议将每个组件部署到其自己的子网中，并添加网络安全组，以仅允许来自特定端口上特定子网的流量通过。

你也可以将此体系结构调整为完全部署在 Azure 上，此模式在一个区域的两个可用性区域中使用 Oracle Data Guard 配置高度可用的 Oracle 数据库。 下图（图 6）是此体系结构模式的示例：

![PeopleSoft 仅 Azure 体系结构](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*图 6：PeopleSoft 仅 Azure 体系结构*

以下部分概要介绍了各种组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>应用层

应用层包含 PeopleSoft 应用程序服务器、PeopleSoft Web 服务器、弹性搜索和 PeopleSoft Process Scheduler 的实例。 Azure 负载均衡器已设置为接受来自用户的请求，这些请求路由到应用层中的相应服务器。

为实现高可用性，请考虑在应用层跨不同可用性区域设置每个服务器的冗余实例。 可以为 Azure 负载均衡器设置多个后端池，以将每个请求定向到正确的服务器。

### <a name="peopletools-client"></a>PeopleTools Client

PeopleTools Client 用于执行管理活动，例如开发、迁移和升级。 由于 PeopleTools Client 不是实现应用程序高可用性所必需的，因此不需要部署 PeopleTools Client 的冗余服务器。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>后续步骤

使用 [Terraform 脚本](https://github.com/microsoft/azure-oracle)在 Azure 中设置 Oracle 应用，并与 OCI 建立跨云连接。

有关 OCI 的详细信息和白皮书，请参阅 [Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
