---
title: 管理 Service Fabric 群集升级
description: 管理 Service Fabric 群集运行时的更新时间和方式
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 129bdae4dc131013bd7c13377b61575141c27ccd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714574"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>管理 Service Fabric 群集升级

Azure Service Fabric 群集是你拥有的但部分由 Microsoft 管理的资源。 下面介绍如何管理 Microsoft 更新 Azure Service Fabric 群集的时间和方式。

有关群集升级概念和过程的更多背景信息，请参阅[升级和更新 Azure Service Fabric 群集](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>设置升级模式

可以将群集设置为在 Microsoft 发布自动 Service Fabric 升级时接收这些升级，也可以通过设置群集的升级模式从当前支持的版本列表中手动进行选择。 此操作可通过 Azure 门户中的“结构升级模式”控件或群集部署模板中的 `upgradeMode` 设置来完成。

### <a name="azure-portal"></a>Azure 门户

使用 Azure 门户，你将在创建新的 Service Fabric 群集时选择是自动升级还是手动升级。

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="在 Azure 门户中使用“高级”选项创建新群集时可选择自动升级或手动升级":::

还可以通过现有群集资源的“结构升级”部分在自动升级和手动升级之间切换。

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="在 Azure 门户中群集资源的“结构升级”部分选择自动升级或手动升级":::

### <a name="manual-upgrades-with-azure-portal"></a>使用 Azure 门户手动升级

选择手动升级选项时，启动升级所需执行的全部操作就是从可用版本下拉列表中进行选择，然后选择“保存”。 此时会立即开始进行群集升级。

在升级期间，将遵守[群集运行状况策略](#custom-policies-for-manual-upgrades)（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。 如果不符合现行的群集运行状况策略，则将回滚升级。

修复造成回滚的问题后，将需要按照与之前完全相同的步骤重新启动升级。

### <a name="resource-manager-template"></a>资源管理器模板

若要使用资源管理器模板更改群集升级模式，需要为 Microsoft.ServiceFabric/clusters 资源定义的 `upgradeMode` 属性指定“自动”或“手动”  。 如果选择手动升级，还需将 `clusterCodeVersion` 设置为当前[支持的结构版本](#check-for-supported-cluster-versions)。

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="屏幕截图显示一个模板，该模板以纯文本方式缩进来反映结构。突出显示了“clusterCodeVersion”和“upgradeMode”属性。":::

成功部署模板后，将应用对群集升级模式所做的更改。 如果群集处于手动模式，将自动开始进行群集升级。

在升级期间，将遵守[群集运行状况策略](#custom-policies-for-manual-upgrades)（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。 如果不符合现行的群集运行状况策略，则回滚升级。

修复造成回滚的问题后，将需要按照与之前完全相同的步骤重新启动升级。

## <a name="wave-deployment-for-automatic-upgrades"></a>自动升级的波形部署

使用自动升级模式，可以选择对群集进行波形部署。 使用波形部署，可以创建一个用于按顺序升级测试、阶段和生产群集的管道，并通过内置的“烘焙时间”进行分隔，以便在更新生产群集之前验证即将发布的 Service Fabric 版本。

### <a name="enable-wave-deployment"></a>启用波形部署

> [!NOTE]
> 波形部署需要对 Microsoft.ServiceFabric/clusters 资源使用 `2020-12-01-preview`（或更高版本的）API 版本。

若要启用波形部署以进行自动升级，需首先确定要将群集分配到的波形：

* 波形 0 (`Wave0`)：发布新的 Service Fabric 版本后，立即更新群集。 用于测试/开发群集。
* 波形 1 (`Wave1`)：在发布新版本一周（七天）后更新群集。 用于预生产/暂存群集。
* 波形 2 (`Wave2`)：在发布新版本两周（14 天）后更新群集。 用于生产群集。

然后，使用上面列出的某个波形值直接将 `upgradeWave` 属性添加到群集资源模板。 确保群集资源 API 版本为 `2020-12-01-preview` 或更高版本。

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

部署更新后的模板后，将在为下一升级时段和之后的时段指定的波形中注册群集。

如果群集升级失败，可以借助进一步帮助的链接[注册电子邮件通知](#register-for-notifications)。

### <a name="register-for-notifications"></a>注册通知

可以在群集升级失败时注册通知。 你指定的电子邮件地址将收到一封电子邮件，其中包含有关升级失败的详细信息以及指向进一步帮助的链接。

> [!NOTE]
> 无需注册波形部署即可接收升级失败通知。

若要注册通知，需要向群集资源模板添加一个 `notifications` 部分，并指定一个或多个用于接收通知的电子邮件地址（接收方）：

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

部署更新后的模板后，将注册升级失败通知。

## <a name="custom-policies-for-manual-upgrades"></a>手动升级的自定义策略

可为手动群集升级指定自定义运行状况策略。 每次选择新的运行时版本时，都会应用这些策略，从而触发系统启动群集升级。 如果未重写这些策略，则会使用默认设置。

可以通过为“升级策略”选择“自定义”选项，指定自定义运行状况策略或查看 Azure 门户中群集资源的“结构升级”部分下的当前设置。

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="在 Azure 门户中的群集资源的“结构升级”部分中选择“自定义”升级策略选项，以便在升级期间设置自定义运行状况策略":::

## <a name="check-for-supported-cluster-versions"></a>查看支持的群集版本

可以参考 [Service Fabric 版本](service-fabric-versions.md)，了解有关支持的版本和操作系统的更多详细信息。

还可以使用 [Azure REST API](/rest/api/azure/) 列出可用于指定位置和订阅的所有可用的 Service Fabric 运行时版本 ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list))。

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


## <a name="check-for-supported-upgrade-path"></a>查看支持的升级路径

可以参考 [Service Fabric 版本](service-fabric-versions.md)文档，了解支持的升级路径和相关版本信息。 

利用支持的目标版本信息，可以使用以下 PowerShell 步骤来验证支持的升级路径。

1) 登录 Azure
   ```PowerShell
   Login-AzAccount
   ```

2) 选择订阅
   ```PowerShell
   Set-AzContext -SubscriptionId <your-subscription>
   ```

3) 调用 API
   ```PowerShell
   $params = @{ "TargetVersion" = "<target version>"}
   Invoke-AzResourceAction -ResourceId -ResourceId <cluster resource id> -Parameters $params -Action listUpgradableVersions -Force
   ```

   示例： 
   ```PowerShell
   $params = @{ "TargetVersion" = "8.1.335.9590"}
   Invoke-AzResourceAction -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabric/clusters/myCluster -Parameters $params -Action listUpgradableVersions -Force

   Output
   supportedPath
   -------------
   {8.1.329.9590, 8.1.335.9590}
   ```


## <a name="next-steps"></a>后续步骤

* [管理 Service Fabric 升级](service-fabric-cluster-upgrade-version-azure.md)
* 自定义 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)
* [扩展和缩减群集](service-fabric-cluster-scale-in-out.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
