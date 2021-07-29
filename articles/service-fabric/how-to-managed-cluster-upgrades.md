---
title: 升级 Azure Service Fabric 托管群集
description: 了解用于更新 Azure Service Fabric 托管群集的选项
ms.topic: how-to
ms.date: 05/10/2021
ms.openlocfilehash: 478b39a6222906c793d826ab69edeeaddbb096bf
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961000"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>管理 Service Fabric 托管群集升级

Azure Service Fabric 群集是你拥有的但部分由 Microsoft 管理的资源。 下面介绍如何管理 Microsoft 更新 Azure Service Fabric 托管群集的时间和方式。

## <a name="set-upgrade-mode"></a>设置升级模式

默认情况下，Azure Service Fabric 托管群集设置为接收自动 Service Fabric 升级，因为它们由 Microsoft 使用[波形部署策略](#wave-deployment-for-automatic-upgrades)发布。 或者，可以设置手动模式升级，以便从当前支持的版本列表中选择。 你可通过 Microsoft Azure 门户中的 *结构升级* 空间或群集部署模板中的 `ClusterUpgradeMode` 设置来完成。

## <a name="wave-deployment-for-automatic-upgrades"></a>自动升级的波形部署

使用波形部署，可以创建一个用于按顺序升级测试、阶段和生产群集的管道，并通过内置的“烘焙时间”进行分隔，以便在更新生产群集之前验证即将发布的 Service Fabric 版本。

>注意 默认情况下，群集将设置为波形 0。

要为自动升级启用波形部署，首先请确定要为群集分配的波形：

* 波形 0 (`Wave0`)：发布新的 Service Fabric 版本后，立即更新群集。
* **波形 1** (`Wave1`)：当波形 0 可用于生产群集后，群集更新。 发生在波形 0 发布至少 7 天后
* **波形 2** `Wave2` ()：最后更新群集以允许进一步生产群集。 发生在波形 0 发布至少 14 天后

## <a name="set-the-wave-for-your-cluster"></a>为群集设置波形

通过 Microsoft Azure 门户中的 *结构升级* 控件或群集部署模板中的 `ClusterUpgradeMode` 设置，您可将群集设置为可用波形之一。

### <a name="azure-portal"></a>Azure 门户

使用 Microsoft Azure 门户，你可以在新建 Service Fabric 群集时选择自动升级或手动升级。

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="在 Microsoft Azure 门户中使用“高级”选项创建新群集时可选择不同的可用波形":::

还可以通过现有群集资源的 **结构升级** 部分在可用自动波形间切换。

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="在 Microsoft Azure 门户中群集资源的“结构升级”部分选择不同的自动波形":::

### <a name="resource-manager-template"></a>资源管理器模板

若要使用资源管理器模板更改群集升级模式，需要为 Microsoft.ServiceFabric/clusters 资源定义的 `ClusterUpgradeMode` 属性指定“自动”或“手动”  。 如果选择手动升级，还需将 `clusterCodeVersion` 设置为当前[支持的结构版本](#query-for-supported-cluster-versions)。

#### <a name="manual-upgrade"></a>手动升级

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Manual",
        "ClusterCodeVersion": "7.2.457.9590"
        }
}
```

成功部署模板后，将应用对群集升级模式所做的更改。 如果群集处于手动模式，将自动开始进行群集升级。

在升级期间，将遵守[群集运行状况策略](./service-fabric-health-introduction.md#health-policies)（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。 如果不符合现行的群集运行状况策略，则回滚升级。

修复造成回滚的问题后，将需要按照与之前完全相同的步骤重新启动升级。

#### <a name="automatic-upgrade-with-wave-deployment"></a>使用波形部署进行自动升级

要配置自动升级和波形部署，只需将添加/验证 `ClusterUpgradeMode` 设置为 `Automatic`，并且使用上面在资源管理器模板中列出的波形值之一定义 `upgradeWave` 属性。

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        }  
}
```

部署更新后的模板后，将在为下一升级时段和之后的时段指定的波形中注册群集。

## <a name="custom-policies-for-manual-upgrades"></a>手动升级的自定义策略

可为手动群集升级指定自定义运行状况策略。 每次选择新的运行时版本时，都会应用这些策略，从而触发系统启动群集升级。 如果未重写这些策略，则会使用默认设置。

可以通过为“升级策略”选择“自定义”选项，指定自定义运行状况策略或查看 Azure 门户中群集资源的“结构升级”部分下的当前设置。

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="在 Azure 门户中的群集资源的“结构升级”部分中选择“自定义”升级策略选项，以便在升级期间设置自定义运行状况策略":::

## <a name="query-for-supported-cluster-versions"></a>查询支持的群集版本

可以使用 [Azure REST API](/rest/api/azure/) 列出可用于指定位置和订阅的所有可用的 Service Fabric 运行时版本 ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list))。

还可以参考 [Service Fabric 版本](service-fabric-versions.md)，了解有关支持的版本和操作系统的更多详细信息。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

输出中的 `supportExpiryUtc` 报告给定版本的到期时间。 最新版本没有有效日期，但有一个值 9999-12-31T23:59:59.9999999，这表示尚未设置其到期日期。

## <a name="next-steps"></a>后续步骤

* [自定义 Service Fabric 托管群集配置选项](how-to-managed-cluster-configuration.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[Upgrade-Wave-Settings]: ./media/service-fabric-cluster-upgrade/manage-upgrade-wave-settings.png
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG