---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567305"
---
#### <a name="portal"></a>[Portal](#tab/portal)

使用 [Azure Monitor 指标资源管理器](../articles/azure-monitor/essentials/metrics-getting-started.md)可以图形格式查看消耗计划函数应用的成本相关数据。 

1. 在 [Azure 门户]中，导航到你的函数应用。

1. 在左侧面板中，向下滚动至“监视”，然后选择“指标” 。  

1. 在“指标”中，为“聚合”选择“函数执行计数”和“求和”。 这会在图表中将所选时间段内的执行计数之和相加。

    ![定义要添加到图表中的函数应用指标](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. 选择“添加指标”并重复步骤 2-4，将“函数执行单位”添加到图表中。 

生成的图表包含所选时间范围内（在本例中为 2 小时）两个执行指标的总和。

![函数执行计数和执行单位图表](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

由于执行单位数远远大于执行计数，因此图表只显示执行单位。

此图显示两小时时间段内总共消耗了 11.1 亿个 `Function Execution Units`（以“MB 毫秒”度量）。 若要转换为 GB 秒，请除以 1024000。 在此示例中，函数应用消耗了 `1110000000 / 1024000 = 1083.98` GB 秒。 可以将此值乘以 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)上执行时间的当前价格，从而得出这两个小时的成本（假设已用完了所有免费授予的执行时间）。 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[Azure CLI](/cli/azure/) 提供了用于检索指标的命令。 你可以从本地命令环境中使用 CLI，也可以直接从门户中使用 [Azure Cloud Shell](../articles/cloud-shell/overview.md)。 例如，以下 [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) 命令返回以前使用的同一时间段内的每小时数据。

请务必将 `<AZURE_SUBSCRIPTON_ID>` 替换为运行该命令的 Azure 订阅 ID。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

此命令返回类似于以下示例的 JSON 有效负载：

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
此特定响应显示，从 `2019-09-11T21:46` 到 `2019-09-11T23:18`，应用消耗了 1110000000 MB 毫秒（1083.98 GB 秒）。

---

[Azure 门户]: https://portal.azure.com