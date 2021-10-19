---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: 66f2ca97ce20dc0aeb6b1fcecf691861b645284b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706711"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 对于反恶意软件和漏洞评估等解决方案，安全中心可以在虚拟机上预配代理。 对于防火墙设备，安全中心可以处理所需的许多网络配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一的运行状况监视和管理**：客户可以使用集成式运行状况事件，一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

目前，集成安全解决方案包括由 [Qualys](https://www.qualys.com/public-cloud/#azure) 和 [Rapid7](https://www.rapid7.com/products/insightvm/) 提供的漏洞评估以及 [Azure 应用程序网关上的 Microsoft Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)。

> [!NOTE]
> 安全中心不会在合作伙伴虚拟设备上安装 Log Analytics 代理，因为大多数安全供应商都禁止在其设备上运行外部代理。

若要详细了解 Qualys 中漏洞扫描工具的集成，包括 Azure Defender 客户可使用的内置扫描程序，请参阅[适用于 Azure 和混合计算机的 Azure Defender 集成 Qualys 漏洞扫描程序](deploy-vulnerability-assessment-vm.md)。

安全中心还为以下内容提供漏洞分析：

* SQL 数据库 - 请参阅[在漏洞评估仪表板中浏览漏洞评估报告](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Azure 容器注册表映像 - 请参阅[使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 还可以连接其他安全数据源，包括在本地或其他云中运行的计算机。

:::image type="content" source="./media/security-center-partner-integration/security-solutions-page.png" alt-text="合作伙伴解决方案集成。" lightbox="./media/security-center-partner-integration/security-solutions-page.png":::

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 在 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)中，打开“安全中心”。

1. 从安全中心的菜单中，选择“安全解决方案”。

从“安全解决方案”页面中，可以查看集成式 Azure 安全解决方案的运行状况，并运行基本管理任务。

### <a name="connected-solutions"></a>已连接解决方案

“已连接解决方案”部分包括当前连接到安全中心的所有安全解决方案。 它还显示每个解决方案的运行状况。  

![已连接的解决方案。](./media/security-center-partner-integration/connected-solutions.png)

合作伙伴解决方案的状态可能为：

* **正常**（绿色）- 没有运行状况问题。
* **不正常**（红色）- 存在需要立即关注的运行状况问题。
* **停止报告**（橙色）- 解决方案已停止报告其运行状况。
* **未报告**（灰色）- 解决方案尚未报告任何内容，并且未提供任何运行状况数据。 如果解决方案是最近连接的且仍在部署，则其状态可能是“未报告”。

> [!NOTE]
> 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 如果没有运行状况数据可用且在过去 14 天内没有收到警报，则安全中心会指出解决方案不正常或未报告。
>
>

选择“查看”以获取其他信息和选项，例如：

   - **解决方案控制台** - 打开此解决方案的管理体验。
   - **链接 VM** - 打开“链接应用程序”页面。 此处，可将资源连接到合作伙伴解决方案。
   - **删除解决方案**
   - **配置**

   ![合作伙伴解决方案详细信息。](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>已发现解决方案

安全中心会自动发现在 Azure 中运行但未连接到安全中心的安全解决方案，并在“发现的解决方案”部分中显示这些解决方案。 这些解决方案包括 Azure 解决方案（例如 [Azure AD 标识保护](../active-directory/identity-protection/overview-identity-protection.md)）和合作伙伴解决方案。

> [!NOTE]
> 在订阅级别为发现的解决方案功能启用 Azure Defender。 在[快速入门：启用 Azure Defender](enable-azure-defender.md) 中了解详细信息。

在解决方案下选择“连接”，以便与安全中心集成，并获得安全警报通知。

### <a name="add-data-sources"></a>添加数据源

“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。

![数据源。](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 若要了解如何设置与 Azure Sentinel 或任何其他 SIEM 的集成，请参阅[连续导出安全中心数据](continuous-export.md)。