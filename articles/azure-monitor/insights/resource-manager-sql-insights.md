---
title: 用于 SQL 见解的资源管理器模板示例
description: 用于部署和配置 SQL 见解的 Azure 资源管理器模板示例。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730185"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>用于 SQL 见解的资源管理器模板示例
本文包括 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)示例，用于启用 SQL 见解以监视在 Azure 中运行的 SQL。  有关支持的 SQL 产品/服务和版本的详细信息，请参阅 [SQL 见解文档](sql-insights-overview.md)。 每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>创建 SQL 见解监视配置文件
下面的示例创建一个 SQL 见解监视配置文件，该配置文件包含要收集的 SQL 监视数据、数据收集频率，并指定数据将发送到的工作区。


### <a name="template-file"></a>模板文件

查看 [git hub 上的模板文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)。

### <a name="parameter-file"></a>参数文件

查看 [git hub 上的参数文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)。


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>将监视 VM 添加到 SQL 见解监视配置文件
创建监视配置文件后，需要分配 Azure 虚拟机，这些虚拟机将配置为从你在该 VM 的配置中指定的 SQL 资源远程收集数据。  有关更多详细信息，请参阅 SQL 见解启用文档。

下面的示例将监视 VM 配置为从指定的 SQL 资源收集数据。


### <a name="template-file"></a>模板文件

查看 [git hub 上的模板文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)。

### <a name="parameter-file"></a>参数文件

查看 [git hub 上的参数文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)。


## <a name="create-an-alert-rule-for-sql-insights"></a>为 SQL 见解创建警报规则
下面的示例创建一个警报规则，该规则将涵盖指定监视配置文件范围内的 SQL 资源。  此警报规则将显示在警报 UI 上下文面板的 SQL 见解 UI 中。  

参数文件包含我们在 SQL 见解中提供的某个警报模板中的值，你可以对其进行修改，以针对我们为 SQL 收集的其他数据发出警报。  该模板未指定警报规则的操作组。


#### <a name="template-file"></a>模板文件

查看 [git hub 上的模板文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)。

### <a name="parameter-file"></a>参数文件

查看 [git hub 上的参数文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)。





## <a name="next-steps"></a>后续步骤

* [获取 Azure Monitor 的其他示例模板](../resource-manager-samples.md)。
* [详细了解 SQL 见解](sql-insights-overview.md)。
