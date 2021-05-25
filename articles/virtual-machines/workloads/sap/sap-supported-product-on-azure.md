---
title: Azure 上的 SAP：Azure 支持的 SAP 软件
description: 介绍已支持在 Azure 上部署的 SAP 软件
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673543"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支持的 SAP 软件
本文介绍如何确定 Azure 部署支持哪些 SAP 软件，以及需要哪些操作系统版本或 DBMS 版本。

评估当前 SAP 软件是否受支持以及 Azure 中的 SAP 软件支持哪些 OS 和 DBMS 版本时，需要访问：

- SAP 支持说明
- SAP 产品可用性对照表



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作负载的一般限制
可用于 SAP 工作负载的 Azure IaaS 服务仅限于 x86-64 或 x64 硬件。 没有任何基于 Sparc 或 Power 的 CPU 产品/服务适用于 SAP 工作负载。 如果客户在 IBM 大型机或 AS400 等硬件体系结构专有的操作系统上运行应用程序，或者在使用了 HP-UX、Solaris 或 AIX 操作系统的环境中运行应用程序，那么他们需将其 SAP 应用程序（包括 DBMS）更改为下列操作系统之一：

- 适用于 x86-64 平台的 64 位 Windows Server
- 适用于 x86-64 平台的 64 位 SUSE Linux
- 适用于 x86-64 平台的 64 位 Red Hat Linux
- 适用于 x86-64 平台的 64 位 Oracle Linux

不支持将任何其他 OS 版本或 Linux 发行版与 SAP 软件结合使用。 本文档稍后将介绍有关特定版本和用例的确切详细信息。


## <a name="you-start-here"></a>从此处开始
请参考 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 作为起点。 从头到尾浏览此 SAP 说明时，你会看到其中从多个方面说明了支持的软件和 VM

第一部分概括性地列出了 Azure VM 中的 SAP 软件支持的操作系统版本最低要求。 如果你达不到这些最低要求并运行这些操作系统的早期版本，需要将 OS 版本升级到所述的最低版本甚至更高版本。 一般情况下，Azure 确实支持其中某些操作系统的早期版本。 但是，版本限制或最低版本是根据执行的测试和资格认证列出的，而且今后不会放宽。 


> [!NOTE]
>某些特定的 VM 类型、HANA 大型实例或 SAP 工作负载需要更高的 OS 版本。 在整个文档中会提到这种情况。 SAP 说明或其他 SAP 出版物中清晰地介绍了这样的情况。

接下来的部分列出了受支持的版本所支持的一般 SAP 平台，更重要的是，列出了支持的 SAP 内核。 其中列出了受支持且需要最低内核版本的 NetWeaver/ABAP 或 Java 堆栈。 较新的 ABAP 堆栈在 Azure 上受支持，但不需要最低内核版本，因为最初在开发较新堆栈时就已经实施了为 Azure 做出的更改

需要检查：

- 所述的最低版本是否涵盖了你正在运行的 SAP 应用程序。 如果未涵盖，则需要定义新的目标版本，并在 SAP 产品可用性对照表中检查新的目标版本支持哪些操作系统内部版本和 DBMS 组合。 这样，就可以选择正确的操作系统版本和 DBMS 版本
- 在迁移到 Azure 的过程中是否需要更新 SAP 内核
- 是否需要更新 SAP 支持包。 尤其是需要迁移到较新 DBMS 版本时可能要用到的基本支持包


下一部分将更详细地说明适用于 Windows 和 Linux 的 Azure 上的 SAP 支持的其他 SAP 产品和 DBMS 版本。 

> [!NOTE]
> 不同 DBMS 的最低版本是精心选择的，不一定始终反映不同 DBMS 供应商在一般情况下在 Azure 上支持的整个 DBMS 版本范围。 在定义这些最低版本时，我们已经考虑到了与 SAP 工作负载相关的许多因素。 不需要花费精力对早期的 DBMS 版本进行测试和资格认证。 

> [!NOTE]
> 列出的最低版本代表了操作系统和数据库的早期版本。 我们强烈建议使用最新的操作系统版本和数据库版本。 在很多情况下，较新的操作系统和数据库版本考虑到了在公有云中运行的用例，并且改编了代码，以优化其在公有云（更具体地说，是 Azure）中的运行

## <a name="oracle-dbms-support"></a>Oracle DBMS 支持
[SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619) 中专门列出了 Azure 支持的操作系统、Oracle DBMS 版本和 Oracle 功能。 该说明中的重点内容归纳如下：

- 已通过 NetWeaver 认证并且受 Azure VM 支持的最低 Oracle 版本为 Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- 作为来宾操作系统，只有 Windows 和 Oracle Linux 符合资格。 该说明中列出了 OS 的确切版本以及相关的最低 DBMS 版本
- Oracle Linux 支持还扩展到了 Oracle DBMS 客户端。 这意味着，所有 SAP 组件（例如 ABAP 或 Java 堆栈的对话实例）也需要在 Oracle Linux 上运行。 只有不需要连接到 Oracle DBMS 的此类 SAP 系统中的 SAP 组件才能运行其他 Linux 操作系统
- 不支持 Oracle RAC 
- 在某些情况下支持 Oracle ASM。 该说明中已列出详细信息
- 只有在 Windows 来宾 OS 中运行的应用程序服务器才支持非 Unicode SAP 系统。 DBMS 的来宾操作系统可以是 Oracle Linux 或 Windows。 SAP 产品可用性对照表 (PAM) 中非常清楚地解释了实施此项限制的原因。 对于 Oracle Linux，SAP 从未发布过非 Unicode SAP 内核

了解目标 Azure 基础结构支持的 DBMS 版本后，需要在 SAP 产品可用性对照表中检查你打算运行的 SAP 产品版本是否支持所需的 OS 版本和 DBMS。 

## <a name="oracle-linux"></a>Oracle Linux
与 Oracle Linux 相关的最常见问题是，SAP 是否也支持 Red Hat 内核，该内核是 Oracle Linux 必不可少的组成部分。 有关详细信息，请参阅 [SAP 支持说明 #1565179](https://launchpad.support.sap.com/#/notes/1565179)。

## <a name="other-database-than-sap-hana"></a>除 SAP HANA 以外的其他数据库
[SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中提到了非 HANA 数据库对 SAP 工作负载的支持。


## <a name="sap-hana-support"></a>SAP HANA 支持
在 Azure 中，有两个服务可用于运行 HANA 数据库：

- Azure 虚拟机
- [HANA 大型实例](./hana-overview-architecture.md)

要运行 SAP HANA，SAP 不仅需要运行 NetWeaver 或其他 SAP 应用程序和 DBMS，而且在基础结构方面还要满足更多且更严格的条件。 因此，只有少量的 Azure VM 符合运行 SAP HANA DBMS 的条件。 可以在所谓的 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中找到 SAP HANA 支持的 Azure 基础结构列表。 

> [!NOTE]
> 以字母“S”开头的单元是 [HANA 大型实例](./hana-overview-architecture.md)单元。 

> [!NOTE]
> SAP 不实施与 SAP HANA 主要版本相关的认证。 与共识相反，[HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中的“认证方案”列并未给出有关已认证的 HANA 主要或次要版本的表述 。 需要假设只要特定单元的已认证操作系统版本也受 HANA 1.0 版本的支持，则列出的所有单元就均可用于 HANA 1.0 和 HANA 2.0。 

使用 SAP HANA 时，适用的最低 OS 版本可能与一般的 NetWeaver 用例不同。 需要单独检查每个单元支持的操作系统，因为这些操作系统可能不同。 单击每个单元即可进行检查。 将显示更多详细信息。 列出的详细信息之一是此特定单元支持的不同操作系统。

> [!NOTE]
> 与 Azure VM 相比，Azure HANA 大型实例单元支持的操作系统限制更严格。 另一方面，Azure VM 可以强制要求比最新版本更高的操作系统版本。 要求更改 Linux 内核的某些较大型 VM 单元尤其如此

了解 Azure 基础结构支持的 OS 后，需要在 [SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581) 中检查用作目标的 Azure 单元所支持的确切 SAP HANA 版本和补丁级别。 

> [!IMPORTANT]
> 检查支持的确切 SAP HANA 版本和补丁级别这一步骤非常重要。 在很多情况下，支持哪个特定的 OS 版本取决于 SAP HANA 可执行文件的特定补丁级别。

了解可以在目标 Azure 基础结构上运行的特定 HANA 版本后，需要查看 SAP 产品可用性对照表，以确定支持你筛选出的 HANA 版本的 SAP 产品版本是否存在限制


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>已认证的 Azure VM 和 HANA 大型实例单元及业务事务吞吐量
除了评估 Azure 基础结构单元支持的操作系统版本、DBMS 版本和相关的 SAP 支持软件版本以外，还需要认定这些单元的业务事务吞吐量（SAP 使用单位“SAPS”表示）是否符合条件。 所有的 SAP 大小调整都依赖于 SAPS 计算。 评估现有的 SAP 系统通常可以在基础结构提供商的帮助下计算出单元的 SAPS。 这一点适用于 DBMS 层以及应用程序层。 在创建了新功能的其他用例中，在 SAP 中进行大小调整可以找出应用程序层和 DBMS 层所需的 SAPS 数。 作为基础结构提供商，Microsoft 有义务提供已通过 NetWeaver 和/或 HANA 认证的不同单元的 SAP 吞吐量特征。

对于 Azure VM，这些 SAPS 吞吐量数字已在 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中阐述。 对于 Azure HANA 大型实例单元，SAPS 吞吐量数字已在 [SAP 支持说明 #2316233](https://launchpad.support.sap.com/#/notes/2316233) 中阐述

在 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中，以下备注适用：

- 对于 M 系列的 Azure VM 和 Mv2 系列的 Azure VM，适用的最低 OS 版本与其他 Azure VM 类型不同。 对更高 OS 版本的要求是根据不同操作系统供应商必须在其操作系统版本中提供的更改给出的，提供这些更改的目的是使其操作系统能够在特定的 Azure VM 类型上运行，或者优化这些 VM 类型上的 SAP 工作负载的性能和吞吐量
- 有两个表格指定了不同的 VM 类型。 第二个表格指定了仅支持 Azure 标准存储的 Azure VM 类型的 SAPS 吞吐量。 不支持在该说明的第二个表格中指定的单元上进行 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 支持的其他 SAP 产品
一般情况下的假设是，在诸如 Azure 这样的超大规模状态的云中，大多数 SAP 软件应该都可正常运行，而不会出现功能性的问题。 尽管如此，SAP 仍然会明确表示不同 SAP 产品对不同超大规模云提供商的支持，这一点与私有云可视化的表述相反。 因此，不同的 SAP 支持说明都指出了不同 SAP 产品对 Azure 的支持。 

对于 Business Objects BI 平台，[SAP 支持说明 #2145537](https://launchpad.support.sap.com/#/notes/2145537) 提供了 Azure 支持的 SAP Business Objects 产品列表。 对于有可能未列出的或者不支持的，以及版本比所列最低版本更高的组件或者软件版本和 OS 版本的组合，如果你有任何疑问，需要针对你要请求支持的组件提出 SAP 支持请求。

对于 Business Objects Data Services，[SAP 支持说明 #22288344](https://launchpad.support.sap.com/#/notes/2288344) 解释了 Azure 上运行的 SAP Data Services 的最低支持。 

> [!NOTE]
> 如 SAP 支持说明中所述，需要查看 SAP PAM 以确定 Azure 支持的正确支持包级别

[SAP 支持说明 #2464722](https://launchpad.support.sap.com/#/notes/2464722) 中详细说明了 Azure Kubernetes 服务 (AKS) 中的 SAP Datahub/Vora 支持

[SAP 支持说明 #2451795](https://launchpad.support.sap.com/#/notes/2451795) 中说明了对 SAP BPC 10.1 SP08 的支持

[Hybris 文档](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)中详细说明了 Azure 对 SAP Hybris Commerce Platform 的支持。 SAP Hybris Commerce Platform 支持的 DBMS 列表包括：

- Windows 操作系统平台上的 SQL Server 和 Oracle。 适用的最低版本与 SAP NetWeaver 相同。 有关详细信息，请参阅 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Red Hat 和 SUSE Linux 上的 SAP HANA。 如[本文档前面](#sap-hana-support)所述，必须使用 SAP HANA 认证的 VM 类型。 SAP (Hybris) Commerce Platform 被视为 OLTP 工作负载
- 在 SAP (Hybris) Commerce Platform 1811 和更高版本中支持的 SQL Azure DB




## <a name="next-steps"></a>后续步骤
参阅 [SAP NetWeaver 的 Azure 虚拟机规划和实施](./planning-guide.md)中的后续步骤
