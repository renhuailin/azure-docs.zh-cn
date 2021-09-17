---
title: Azure Sentinel SAP 解决方案的详细 SAP 要求 | Microsoft Docs
description: 了解 Azure Sentinel SAP 解决方案对 SAP 系统的详细要求。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 06/09/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 63ca9c2a76b5daaf576992f715a7ec112ac98b25
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681522"
---
# <a name="azure-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Azure Sentinel SAP 解决方案的详细 SAP 要求（公共预览版）

[部署 Azure Sentinel SAP 解决方案的默认过程](sap-deploy-solution.md)包括所需的 SAP 更改请求和 SAP 注释，并且提供了具有所有必需权限的内置角色。

本文详细列出了所需的 SAP 更改请求、注释和权限。

如果你是管理员，或者你要[手动部署 SAP 解决方案](sap-solution-deploy-alternate.md)，请使用本文作为参考。 本文适用于高级 SAP 用户。


> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前处于预览版阶段。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

> [!NOTE]
> 如果使用安全 SNC 连接部署 SAP 数据连接器，则会列出其他要求。 有关详细信息，请参阅[通过 SNC 部署 Azure Sentinel SAP 数据连接器](sap-solution-deploy-snc.md)。
>
## <a name="recommended-virtual-machine-sizing"></a>建议的虚拟机大小

下表描述了根据预期用途不同为虚拟机建议的大小：

|使用情况  |建议的大小  |
|---------|---------|
|最小规格，例如用于实验室环境     |   Standard_B2s VM      |
|标准连接器（默认）     |   DS2_v2 VM，具有： <br>- 2 个核心<br>- 8 GB 内存      |
|多个连接器     |Standard_B4ms VM，具有： <br>- 4 个核心<br>- 16 GB 内存         |
|     |         |

## <a name="required-sap-log-change-requests"></a>所需的 SAP 日志更改请求

根据 SAP Basis 版本的不同，SAP 解决方案需要以下 SAP 日志更改请求：

- SAP Basis 版本 7.50 及更高版本，请安装 NPLK900144
- 如需较低版本，请安装 NPLK900146
- 若要创建具有所需授权的 SAP 角色，则对于任何受支持的 SAP Basis 版本，都请安装 NPLK900140。 有关详细信息，请参阅[配置 SAP 系统](sap-deploy-solution.md#configure-your-sap-system)和[所需 ABAP 授权](#required-abap-authorizations)。

> [!NOTE]
> 所需 SAP 日志更改请求会公开连接器所需的自定义 RFC FM，并且不会更改任何标准的或自定义的对象。
>

## <a name="required-sap-notes"></a>必需的 SAP 注释

如果 SAP Basis 版本为 7.50 或更低，请安装以下 SAP 注释：

|SAP Basis 版本  |所需注释 |
|---------|---------|
|- 750 SP01 到 SP12<br>- 751 SP01 到 SP06<br>- 752 SP01 到 SP03     |  2641084：安全审核日志数据的标准化读取访问       |
|- 700 到 702<br>- 710 到 711、730、731、740 和 750     | 2173545：CD：CHANGEDOCUMENT_READ_ALL        |
|- 700 到 702<br>- 710 到 711、730、731 和 740<br>- 750 到 752     | 2502336：CD（更改文档）：RSSCD100 - 仅从存档读取，而不从数据库中读取        |
|     |         |

从 [SAP 支持快速启动板站点](https://support.sap.com/en/index.html)访问 SAP 注释。

## <a name="required-abap-authorizations"></a>所需 ABAP 授权

下表列出了后端 SAP 用户将 Azure Sentinel 连接到 SAP 日志所需的 ABAP 授权。 有关详细信息，请参阅[配置 IDE](sap-deploy-solution.md#configure-your-sap-system)。

所需的授权按日志类型列出。 你只需要为计划引入到 Azure Sentinel 的日志类型列出的授权。

> [!TIP]
> 若要创建具有所有必需授权的角色，请在 SAP 系统上部署 SAP 更改请求 [NPLK900114](#required-sap-log-change-requests)。 此更改请求会创建 /MSFTSEN/SENTINEL_CONNECTOR 角色，并将该角色分配给连接到 Azure Sentinel 的 ABAP 用户。
>

| 授权对象 | 字段 | Value |
| -------------------- | ----- | ----- |
| 所有 RFC 日志 | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| ABAP 应用程序日志  | | |
| S_APPL_LOG | ACTVT | 显示 |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| ABAP 更改文档日志 | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| ABAP CR 日志 | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | 显示 |
| S_TRANSPRT | TTYPE | * |
| ABAP DB 表数据日志 | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | 显示 |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + 日志记录所需的任何对象 |
| S_TABU_NAM | ACTVT | 显示 |
| S_TABU_NAM | TABLE | + 日志记录所需的任何对象 |
| S_TABU_NAM | TABLE | DBTABLOG |
| ABAP 作业日志 | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| ABAP 作业日志，ABAP 应用程序日志 | | |
| S_XMI_PRD | INTERFACE | XBP |
| ABAP 安全审核日志 - XAL | | |
| 所有 RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | 显示 |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | INTERFACE | XAL |
| ABAP 安全审核日志 - XAL、ABAP 作业日志、ABAP 应用程序日志 | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Azure Sentinel |
| ABAP 安全审核日志 - SAL | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| ABAP 后台日志，ABAP 后台输出日志 | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| ABAP 工作流日志 | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| | |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [为 SAP 部署 Azure Sentinel 解决方案](sap-deploy-solution.md)
- [通过 SNC 部署 Azure Sentinel SAP 数据连接器](sap-solution-deploy-snc.md)
- [专家配置选项、本地部署和 SAPControl 日志源](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 解决方案日志参考](sap-solution-log-reference.md)
- [Azure Sentinel SAP 解决方案：可用的安全内容](sap-solution-security-content.md)
- [Azure Sentinel SAP 解决方案部署故障排除](sap-deploy-troubleshoot.md)
