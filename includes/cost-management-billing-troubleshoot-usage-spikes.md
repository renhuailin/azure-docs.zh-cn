---
title: include 文件
description: include 文件
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "121777781"
---
## <a name="troubleshoot-usage-spikes"></a>对用量激增进行故障排除

本部分介绍 Azure 使用情况文件中为何出现用量激增、如何防止用量激增、监视资源以及何时联系 Azure 支持人员。 本部分面向签署了企业协议 (EA) 或 Microsoft 客户协议的客户。 这些客户必须具有 EA 管理员或计费管理员角色。 有关权限的详细信息，请参阅[下载或查看 Azure 账单发票和每日使用数据](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)。

Azure 用量激增或特定服务或资源产生意外费用通常是因事件或偶然误用导致的。

在这两种情况下，你都需要在联系支持人员之前缩小受影响的服务和资源的范围，以便选择正确的支持区域。

需要了解的是，Microsoft 不太可能能够确定用量增加以及相关费用增加的根本原因。 因此，客户可以在 Azure 门户中下载自己的详细使用数据。

由于安全问题和客户隐私，Microsoft 不会监视部署的 Azure 资源，例如虚拟机、网络或数据传输。 但 Microsoft 会尝试告知如何监视 Azure 使用情况。 归根结底，你有责任监视自己的使用情况。

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>使用情况文件中激增的表现形式

应用上述部分所述的筛选器后，可以查找异常。 例如，可以对带宽计量类别的激增进行故障排除。

将“产品”和“实例 ID”（Microsoft 客户协议的资源 ID）放在数据透视表工具的“行”部分 。 然后，在“值”中添加“成本”，在“筛选器”中添加“订阅 ID”，在“列”中添加“日期”  。 然后，筛选为仅显示某个订阅 ID 的数据。 例如 `111111111111-1111-1111-111111111111`。

下图显示了带宽（数据传输）激增的表现形式。

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="显示用量激增的 Excel 的屏幕截图。" lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

激增对应于特定资源。 在这种情况下，Excel 文件中的第 7 行显示存储帐户 storageaccountnameazurefile1 的成本值。 2020 年 10 月 1 日，成本值接近零 (0) 美元（2.23043E-06，等于 0.000002230431449）。 2020 年 10 月 2 日和 2020 年 10 月 3 日出现大幅激增，当时的成本分别为 10,000 美元和 28,000 美元。 成本于 2020 年 10 月 4 日 (9.29E-07) 恢复正常。

在本示例中，你确定了产生大量带宽费用的资源、产生费用的日期，以及特定产品（区域间数据传输 - 数据发送 - 欧洲）。 确定激增是否因大量数据传输导致。 使用上述部分中的信息来验证受影响的资源。

如果确定在上述日期没有从资源传输数据，请联系 Azure 技术团队。 该团队可帮助确定是否存在导致出现该问题的 bug 或事件。 在本示例中，受影响的资源是存储帐户。 因此，请联系 Azure 存储技术团队。 同样，如果激增影响了虚拟机，请联系 Azure 虚拟机技术团队，确定是否存在正在进行的事件对虚拟机服务造成了影响。

如果存在正在进行的事件，Azure 技术团队将与 Azure 计费团队协同审查退款请求。

### <a name="tools-to-monitor-azure-usage"></a>用于监视 Azure 使用情况的工具

始终可以使用 Azure 成本管理来管理成本并创建预算。 有关详细信息，请参阅：

- [使用 Azure 预算管理成本](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [教程：创建和管理 Azure 预算](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

对于存储用量，建议使用存储分析工具。 借助该工具，可使用每事务日志记录。 日志很详细，但你可以自行进行全面的跟踪和调试。 有关详细信息，请参阅：

- [存储分析](../articles/storage/common/storage-analytics.md)
- [存储分析日志格式](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [启用和管理 Azure 存储分析日志（经典）](../articles/storage/common/manage-storage-analytics-logs.md)

对于与网络相关的使用，可以使用网络捕获工具，例如[网络监视器](https://www.microsoft.com/download/details.aspx?id=4865)或 Fiddler。

对于与具有 Windows 操作系统映像的虚拟机相关的问题，可以使用 [Windows 事件日志](/windows/win32/wes/windows-event-log)。

对于平台即服务 (PaaS) 部署，请在应用程序中[启用 Azure 诊断](../articles/cloud-services/cloud-services-dotnet-diagnostics.md)。

对于基础结构即服务 (IaaS) 部署，请启用 [Windows Communication Foundation 跟踪](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing)。

启用 [IIS 8.5 的增强日志记录](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85)。

[在 Azure 应用服务中启用 Web 应用的诊断日志记录](../articles/app-service/troubleshoot-diagnostic-logs.md)。

若要详细了解你的情况并获取建议，请联系 Microsoft 客户成功客户经理，请求云解决方案架构师提供帮助。