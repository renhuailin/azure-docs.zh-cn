---
title: 常见问题解答 - Azure Monitor for SAP Solutions | Microsoft Docs
description: 本文提供有关 Azure Monitor for SAP Solutions 的常见问题解答 (FAQ)。
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377118"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor for SAP Solutions 常见问题解答（预览版）
## <a name="frequently-asked-questions"></a>常见问题

本文提供有关 Azure Monitor for SAP Solutions 的常见问题解答 (FAQ)。  

 - 是否必须为 Azure Monitor for SAP Solutions 付费？  
Azure Monitor for SAP Solutions 不收取许可费。  
但是，客户需负责承担受管理资源组组件的费用。  

 - 此服务的公共预览版将在哪些区域推出？  
此服务的公共预览版将在美国东部 2、美国西部 2、美国东部和西欧区域推出。  

 - 我是否需要提供权限才能在我的订阅中部署受管理资源组？  
不需要提供显式权限。  

 - 应将收集器 VM 放在何处？  
部署 Azure Monitor for SAP Solutions 资源时，我们建议客户选择用于监视其 SAP HANA 服务器的同一个 VNET 来监视该资源。 因此，建议将收集器 VM 放到 SAP HANA 服务器所在的同一个 VNET 中。 如果客户使用非 HANA 数据库，则收集器 VM 将位于非 HANA 数据库所在的同一个 VNET 中。  

 - 支持哪些 HANA 版本？  
HANA 1.0 SPS 12（修订版 120 或更高版本）和 HANA 2.0 SPS03 或更高版本  

 - 支持哪些 HANA 部署配置？  
支持以下配置：
   - 单节点（纵向扩展）和多节点（横向扩展）  
   - 单个数据库容器 (HANA 1.0 SPS 12) 和多个数据库容器（HANA 1.0 SPS 12 或 HANA 2.0）  
   - 自动主机故障转移 (n+1) 和 HSR  

 - 支持哪些 SQL Server 版本？  
SQL Server 2012 SP4 或更高版本。  

 - 支持哪些 SQL Server 配置？  
支持以下配置：
   - 虚拟机中的默认或命名独立实例  
   - 群集实例或采用 AlwaysOn 配置的实例（使用群集资源的虚拟名称或使用 AlwaysOn 侦听器名称时）。 目前不会收集特定于群集或 AlwaysOn 的指标    
   - 目前不支持 Azure SQL 数据库 (PAAS)  

 - 如果意外删除了受管理资源组，会发生什么情况？  
受管理资源组默认处于锁定状态。 因此，客户意外删除受管理资源组的可能性微乎其微。  
如果客户删除了受管理资源组，Azure Monitor for SAP Solutions 将停止工作。 客户必须部署新的 Azure Monitor for SAP Solutions 资源，然后重新开始。  

 - 我的 Azure 订阅中需要哪些角色才能部署 Azure Monitor for SAP Solutions 资源？  
“参与者”角色。  

 - 此产品的 SLA 是什么？  
服务级别协议不涉及预览版。 请阅读 Azure Monitor for SAP Solutions 市场映像附带的完整许可条款。  

 - 是否可以通过此解决方案监视我的整个环境？  
当前可以在公共预览版中监视 HANA 数据库、底层基础结构、高可用性群集、Microsoft SQL Server、SAP Netweaver 可用性和 SAP 应用程序实例可用性指标。  

 - 此服务是否取代了 SAP Solution Manager？  
否。 客户仍可使用 SAP Solution Manager 进行业务流程监视。  

 - 与 SAP HANA Cockpit/Studio 等传统解决方案相比，此服务的价值是什么？  
Azure Monitor for SAP Solutions 并不特定于 HANA 数据库。 Azure Monitor for SAP Solutions 还支持 AnyDB。  

- 支持哪些 SAP NetWeaver 版本？  
SAP NetWeaver 7.0 或更高版本。  

- 支持哪些 SAP NetWeaver 配置？  
支持 ABAP、Java 和双堆栈 SAP NetWeaver 应用程序服务器配置。

- 为什么需要取消保护 SAP NetWeaver 应用程序监视的方法？  
在 >= 7.3 的 SAP 版本中，默认情况下会保护大多数 Web 服务方法。 要通过调用这些方法获取可用性和性能指标，需要取消保护以下方法：GetQueueStatistic、ABAPGetWPTable、GetProcessList、EnqGetStatistic 和 GetSystemInstancelist。

- 取消保护 SAPCONTROL Web 方法是否有任何风险？  
一般来说，取消保护 SAPCONTROL Web 方法不会带来[任何](https://launchpad.support.sap.com/#/notes/1439348)安全风险，但若客户想通过 sapstartsrv 的服务器端口 (5XX13/5XX14) 限制/禁止访问未受保护的 Web 方法，可以通过在 SAP 访问控制列表 (ACL) 中添加筛选器来完成此操作。[OSS 备注](https://service.sap.com/sap/support/notes/1495075)中描述了实现此目的所需的配置。 

- 执行系统配置以设置 SAP NetWeaver 提供程序后，是否需要重启 SAP 实例？  
是的，通过 SAP 配置更改而出现未受保护的方法后，将需要重启相应的 SAP 系统以确保更新配置更改。  

## <a name="next-steps"></a>后续步骤

- 创建第一个 Azure Monitor for SAP Solutions 资源。
