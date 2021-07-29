---
title: 使用修补业务流程应用程序
description: 使用修补业务流程应用程序在非 Azure 托管 Service Fabric 群集上自动进行操作系统修补。
ms.topic: how-to
ms.date: 2/01/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3d51cfeaa13bd2556e1d32ffc39232b514f8a7b4
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663680"
---
# <a name="use-patch-orchestration-application"></a>使用修补业务流程应用程序

> [!IMPORTANT]
> 从 2019 年 4 月 30 日起，修补业务流程应用程序版本 1.2.* 不再受支持。 请务必升级到最新版本。

修补业务流程应用程序 (POA) 是围绕 Azure Service Fabric 修复管理器服务的包装器，可为非 Azure 托管群集启用基于配置的 OS 修补计划。 非 Azure 托管群集不需要 POA，但需要按更新域计划修补程序安装，以便在不停机的情况下修补 Service Fabric 群集主机。

POA 是一个 Service Fabric 应用程序，可在 Service Fabric 群集中自动修补操作系统，而无需停机。

POA 提供以下功能：

- 自动完成操作系统更新安装。 自动下载并安装操作系统更新。 可根据需要重启群集节点，且无需让群集停机。

- 群集感知修补和运行状况集成。 POA 在应用更新时，会监视群集节点的运行状况。 群集节点的更新方式为一次一个节点，或一次一个更新域。 如果群集的运行状况由于修补进程而恶化，则会停止修补以防止问题加重。

## <a name="internal-details-of-poa"></a>POA 的内部详细信息

POA 由以下子组件构成：

- **协调器服务**：此有状态服务负责：
    - 协调整个群集上的 Windows 更新作业。
    - 存储已完成的 Windows 更新操作的结果。

- **节点代理服务**：此无状态服务在所有 Service Fabric 群集节点上运行。 此服务负责：
    - 启动节点代理 NTService。
    - 监视节点代理 NTService。

- **节点代理 NTService**：此 Windows NT 服务以更高级别的特权 (SYSTEM) 运行。 相比之下，节点代理服务和协调器服务以较低级别的特权 (NETWORK SERVICE) 运行。 该服务负责在所有群集节点上执行以下 Windows 更新作业：
    - 在节点上禁用自动 Windows 更新。
    - 根据用户提供的策略下载并安装 Windows 更新。
    - 安装 Windows 更新后重启计算机。
    - 将 Windows 更新的结果上传到协调器服务。
    - 在某个操作用完所有重试次数仍失败后报告运行状况。

> [!NOTE]
> POA 使用 Service Fabric 的修复管理器服务来禁用/启用节点和执行运行状况检查。 POA 创建的修复任务跟踪每个节点的 Windows 更新进度。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> 所需的最低 .NET Framework 版本为 4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>启用修复管理器服务（如果尚未运行）

POA 要求在群集上启用修复管理器服务。

#### <a name="azure-clusters"></a>Azure 群集

银级持久层中的 Azure 群集默认启用修复管理器服务。 金级持久层中的 Azure 群集可能启用或不启用修复管理器服务，具体取决于这些群集的创建时间。 铜级持久层中的 Azure 群集默认不启用修复管理器服务。 如果已启用该服务，可以看到它在 Service Fabric Explorer 的系统服务部分运行。

##### <a name="the-azure-portal"></a>Azure 门户
在设置群集时，可以从 Azure 门户启用修复管理器。 配置群集时，选择“附加功能”下的“包括修复管理器”选项。 

![从 Azure 门户启用修复管理器的插图](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure 资源管理器部署模型
另外，也可以使用 [Azure 资源管理器部署模型](./service-fabric-cluster-creation-via-arm.md)在新的或现有 Service Fabric 群集上启用修复管理器服务。 获取要部署的群集的模板。 可以使用示例模板，或者创建自定义 Azure 资源管理器部署模型模板。 

若要使用 [Azure 资源管理器部署模型模板](./service-fabric-cluster-creation-via-arm.md)启用修复管理器服务，请执行以下操作：

1. 检查并确保 *Microsoft.ServiceFabric/clusters* 资源的 `apiVersion` 设置为 *2017-07-01-preview*。 否则，需要将 `apiVersion` 更新为 *2017-07-01-preview* 或更高的值：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 通过在 `fabricSettings` 节后面添加以下 `addonFeatures` 节来启用修复管理器服务：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 通过这些更改更新群集模板后，应用更改并等待更新完成。 现在可以看到修复管理器服务在群集中运行。 它在 Service Fabric Explorer 中的系统服务部分中名为 *fabric:/System/RepairManagerService*。 

### <a name="standalone-on-premises-clusters"></a>独立的本地群集

若要在新的或现有的 Service Fabric 群集上启用修复管理器服务，可以使用[独立 Windows 群集的配置设置](./service-fabric-cluster-manifest.md)。

启用修复管理器服务：

1. 检查并确保 [常规群集配置](./service-fabric-cluster-manifest.md#general-cluster-configurations)中的 `apiVersion` 设置为 *04-2017* 或更高的值，如下所示：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 通过在 `fabricSettings` 节后面添加以下 `addonFeatures` 节来启用修复管理器服务，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 通过这些更改更新群集清单后，使用已更新的群集清单[创建新群集](./service-fabric-cluster-creation-for-windows-server.md)或[升级群集配置](./service-fabric-cluster-upgrade-windows-server.md)。 

   群集使用已更新的群集清单运行后，就可以看到修复管理器服务在群集中运行。 其名为 *fabric:/System/RepairManagerService*，位于 Service Fabric Explorer 中的系统服务部分中。

### <a name="configure-windows-updates-for-all-nodes"></a>为所有节点配置 Windows 更新

自动 Windows 更新可能导致失去可用性，因为多个群集节点可能同时重启。 POA 默认会尝试在每个群集节点上禁用自动 Windows 更新。 但是，如果设置由管理员或组策略管理，建议将 Windows 更新策略显式设置为“下载之前发出通知”。

## <a name="download-the-application-package"></a>下载应用程序包

若要下载应用程序包，请转到 GitHub 上的[修补业务流程应用程序版本页](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-poa-behavior"></a>配置 POA 的行为

可根据需求配置 POA 的行为。 在创建或更新应用程序时，通过传入应用程序参数来替代默认值。 可以通过在 cmdlet `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` 中指定 `ApplicationParameter` 来提供应用程序参数。

| 参数        | 类型                          | 详细信息 |
|:-|-|-|
|MaxResultsToCache    |Long                              | 应缓存的 Windows 更新结果的最大数目。 <br><br>在假定以下情况时，默认值为 3000： <br> &nbsp;&nbsp;- 节点数为 20。 <br> &nbsp;&nbsp;- 节点上每月发生的更新次数为 5。 <br> &nbsp;&nbsp;- 每个操作的结果数可为 10。 <br> &nbsp;&nbsp;- 应存储过去三个月的结果。 |
|TaskApprovalPolicy   |枚举 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 所指示的策略将由协调器服务用于跨 Service Fabric 群集节点安装 Windows 更新。<br><br>允许的值为： <br>*NodeWise*：每次在一个节点上安装 Windows 更新。 <br> *UpgradeDomainWise*：每次在一个更新域上安装 Windows 更新。 （在最大程度情况下，属于更新域的所有节点都可进行 Windows 更新。）<br><br> 若要帮助确定哪种策略最适合你的群集，请参阅[常见问题解答](#frequently-asked-questions)部分。
|LogsDiskQuotaInMB   |Long  <br> （默认值：*1024*）               | 可在节点本地持久保存的修补业务流程应用日志的最大大小，以 MB 为单位。
| WUQuery               | string<br>（默认值：*IsInstalled=0*）                | 用于获取 Windows 更新的查询。 有关详细信息，请参阅 [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)。
| InstallWindowsOSOnlyUpdates | *布尔值* <br> （默认值：false）                 | 使用此标志来控制应当下载并安装哪些更新。 允许以下值 <br>true - 仅安装 Windows 操作系统更新。<br>false - 在计算机上安装所有可用的更新。          |
| WUOperationTimeOutInMinutes | int <br>（默认值：*90*）                   | 指示任何 Windows 更新操作（搜索、下载或安装）的超时。 在指定的超时内未完成的操作将被中止。       |
| WURescheduleCount     | int <br> （默认值：*5*）                  | 在操作持续失败的情况下，服务重新计划 Windows 更新的最大次数。          |
| WURescheduleTimeInMinutes | int <br>（默认值：*30*） | 在持续失败的情况下，服务重新计划 Windows 更新的间隔。 |
| WUFrequency           | 逗号分隔的字符串（默认值：*Weekly, Wednesday, 7:00:00*）     | 安装 Windows 更新的频率。 其格式和可能的值包括： <br>- Monthly, DD, HH:MM:SS（示例：“Monthly, 5, 12:22:32”）。 字段 DD（日）允许的值为 1 到 28 中的数字和“last”。 <br>- Weekly, Day, HH:MM:SS（示例：“Weekly, Tuesday, 12:22:32”）  <br>- Daily, HH:MM:SS（示例：“Daily, 12:22:32”）  <br>- MonthlyByWeekAndDay, Week, Day, HH:MM:SS（示例：“MonthlyByWeekAndDay, 2, Friday, 21:00:00”表示每月第二周星期五晚上 9:00 UTC） <br>- “None”表示不应执行 Windows 更新。  <br><br> 时间为 UTC 时间。|
| AcceptWindowsUpdateEula | 布尔 <br>（默认值：*true*） | 设置此标志即表示该应用程序将代表计算机所有者接受 Windows 更新的最终用户许可协议。              |

> [!TIP]
> 若要立即进行 Windows 更新，请依据应用程序部署时间设置 `WUFrequency`。 例如，假设你有一个 5 节点测试群集，并计划在大约 UTC 下午 5:00 部署应用。 如果假定应用程序升级或部署最多需要 30 分钟，请将 WUFrequency 设置为 *Daily, 17:30:00*。

## <a name="deploy-poa"></a>部署 POA

1. 若要准备群集，请完成所有先决条件步骤。
1. 像部署任何其他 Service Fabric 应用一样部署 POA。 若要使用 PowerShell 进行部署，请参阅[使用 PowerShell 部署和删除应用程序](./service-fabric-deploy-remove-applications.md)。
1. 若要在部署时配置应用程序，请将 `ApplicationParameter` 传递至 `New-ServiceFabricApplication` cmdlet。 为方便起见，我们随应用程序一同提供了脚本 Deploy.ps1。 使用脚本：

    - 使用 `Connect-ServiceFabricCluster` 连接到 Service Fabric 群集。
    - 结合相应的 `ApplicationParameter` 值执行 PowerShell 脚本 Deploy.ps1。

> [!NOTE]
> 让脚本和应用程序文件夹 *PatchOrchestrationApplication* 始终位于同一目录中。

## <a name="upgrade-poa"></a>升级 POA

若要使用 PowerShell 升级 POA 版本，请按照[使用 PowerShell 进行 Service Fabric 应用程序升级](./service-fabric-application-upgrade-tutorial-powershell.md)中的说明操作。

## <a name="remove-poa"></a>删除 POA

若要删除应用程序，请按照[使用 PowerShell 部署和删除应用程序](./service-fabric-deploy-remove-applications.md)中的说明操作。

为方便起见，我们随应用程序一同提供了 Undeploy.ps1 脚本。 使用脚本：

  - 使用 ```Connect-ServiceFabricCluster``` 连接到 Service Fabric 群集。
  - 执行 PowerShell 脚本 Undeploy.ps1。

> [!NOTE]
> 让脚本和应用程序文件夹 *PatchOrchestrationApplication* 始终位于同一目录中。

## <a name="view-the-windows-update-results"></a>查看 Windows 更新结果

POA 公开 REST API 以向用户显示历史结果。 下面是 JSON 结果的示例：

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

下表描述了 JSON 字段：

字段 | 值 | 详细信息
-- | -- | --
OperationResult | 0 - 已成功<br> 1 - 已成功但有错误<br> 2 - 已失败<br> 3 - 已中止<br> 4 - 已中止，超时 | 指示整个操作（通常涉及安装一个或多个更新）的结果。
ResultCode | 与 OperationResult 相同 | 此字段指示单个更新的安装操作的结果。
OperationType | 1 - 安装<br> 0 - 搜索并下载| 默认情况下，“安装”是结果中显示的唯一 OperationType。
WindowsUpdateQuery | 默认值为 "IsInstalled=0" | 用来搜索更新的 Windows 更新查询。 有关详细信息，请参阅 [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)。
RebootRequired | true - 需要重新启动<br> true - 不需要重新启动 | 指示是否需要重新启动才能完成安装更新。
OperationStartTime | DateTime | 指示启动操作（下载/安装）的时间。
OperationTime | DateTime | 指示完成操作（下载/安装）的时间。
HResult | 0 - 成功<br> 其他 - 失败| 指示 Windows 更新失败并出现 updateID“7392acaf-6a85-427c-8a8d-058c25beb0d6”的原因。

如果尚未计划更新，则生成的 JSON 为空。

请登录到群集以查询 Windows 更新结果。 找出协调器服务的主要地址的副本 IP 地址，并在浏览器中打开以下 URL： http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults。

协调器服务的 REST 终结点有一个动态端口。 若要查看确切的 URL，请参考 Service Fabric Explorer。 例如，可在 *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* 处获取结果。

![REST 终结点的插图](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

如果在群集上启用了反向代理，则也可从群集外部访问该 URL。

需要访问的终结点是 *http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*。

若要在群集上启用反向代理，请按照 [Azure Service Fabric 中的反向代理](./service-fabric-reverseproxy.md)中的说明操作。 

> 
> [!WARNING]
> 配置反向代理后，公开 HTTP 终结点的群集中的所有微服务都可从群集外部进行访问。

## <a name="diagnostics-and-health-events"></a>诊断和运行状况事件

本部分介绍如何通过 Service Fabric 群集上的 POA 调试或诊断修补更新问题。

> [!NOTE]
> 若要获取下面列出的多项自我诊断改进功能，请安装 POA 1.4.0 或更高版本。

节点代理 NTService 创建[修复任务](/dotnet/api/system.fabric.repair.repairtask)用于在节点上安装更新。 然后，协调器服务根据任务审批策略准备每个任务。 准备好的任务最终由修复管理器审批，如果群集处于不正常状态，修复管理器不会批准任何任务。 

让我们逐步了解如何在节点上继续更新：

1. 在每个节点上运行的 NodeAgentNTService 按计划的时间查找可用的 Windows 更新。 如果有可用的更新，它会将更新下载到节点上。

1. 下载更新后，节点代理 NTService 将为节点创建名为“POS___\<unique_id>”的相应修复任务。 可以使用 [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) cmdlet 或节点详细信息部分所述的 SFX 查看这些修复任务。 创建修复任务后，它会立即转到 [*Claimed* 状态](/dotnet/api/system.fabric.repair.repairtaskstate)。

1. 协调器服务定期查找处于 *Claimed* 状态的修复任务，然后根据 TaskApprovalPolicy 将这些任务更新到 *Preparing* 状态。 如果 TaskApprovalPolicy 配置为 NodeWise，仅当没有任何其他修复任务当前处于 *Preparing*、*Approved*、*Executing* 或 *Restoring* 状态时，才会准备对应于节点的修复任务。 

   同理，如果 TaskApprovalPolicy 配置为 UpgradeWise，只有属于同一个更新域的节点才具有处于上述状态的任务。 在修复任务转到 *Preparing* 状态后，相应的 Service Fabric 节点将会 [禁用](/powershell/module/servicefabric/disable-servicefabricnode)，其意图设置为 *Restart*。

   POA 1.4.0 和更高版本使用 CoordinatorService 上的 ClusterPatchingStatus 属性发布事件，以显示正在修补的节点。 更新将在 _poanode_0 上安装，如下图所示：

    [![群集修补状态的插图](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 禁用该节点后，修复任务将转到 *Executing* 状态。 
   
   > [!NOTE]
   > 停滞在 *Disabled* 状态的节点可能会导致阻止新的修复任务，因此会停止群集上的修补操作。

1. 修复任务进入 *Executing* 状态时，将开始在该节点上安装修补程序。 安装修补程序后，节点不一定重启，具体取决于安装的修补程序。 接下来，修复任务将转到 *Restoring* 状态，这会重新启用节点。 然后，修复任务将标记为 completed。

   在 POA 1.4.0 和更高版本中，可以使用“WUOperationStatus-\<NodeName>”属性查看 NodeAgentService 上的运行状况事件，以便查找更新的状态。 下图中的突出显示部分显示了节点 *poanode_0* 和 *poanode_2* 上的 Windows 更新状态：

   [![屏幕截图展示了 Windows 更新操作状态控制台窗口，其中突出显示了“poanode_0”。](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![屏幕截图展示了 Windows 更新操作状态控制台窗口，其中突出显示了“poanode_1”。](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   也可以使用 PowerShell 获取详细信息。 为此，请连接到群集并使用 [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) 提取修复任务的状态。 
   
   在以下示例中，“POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15”任务处于 *DownloadComplete* 状态。 这表示更新已下载到 *poanode_2* 节点，任务转为 *Executing* 状态后，将尝试安装这些更新。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果仍会出现更多问题，请登录到虚拟机 (VM)，并使用 Windows 事件日志来了解相关信息。 上述修复任务只能在以下执行程序子状态下存在：

      ExecutorSubState | 说明
    -- | -- 
      None=1 |  表示节点上没有正在进行的操作。 状态可能正在过渡。
      DownloadCompleted=2 | 表示下载操作已完成，状态为成功、部分失败或失败。
      InstallationApproved=3 | 表示下载操作已提前完成，修复管理器已批准安装。
      InstallationInProgress=4 | 对应于修复任务的执行状态。
      InstallationCompleted=5 | 表示安装已完成，状态为成功、部分成功或失败。
      RestartRequested=6 | 表示修补程序安装已完成，节点上有一个挂起的重启操作。
      RestartNotNeeded=7 |  表示完成修补程序安装后不需要重启。
      RestartCompleted=8 | 表示重启已成功完成。
      OperationCompleted=9 | Windows 更新操作已成功完成。
      OperationAborted=10 | 表示 Windows 更新操作已中止。

1. 在 POA 1.4.0 和更高版本中，当节点更新尝试完成后，将在 NodeAgentService 上发布一个包含属性“WUOperationStatus-[NodeName]”的事件，以通知下一次要在何时开始尝试下载并安装 Windows 更新。 下图显示了此信息：

     [![屏幕截图展示了包含 NodeAgentService 的 Windows 更新操作状态控制台窗口。](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>诊断日志

修补业务流程应用程序日志是作为 Service Fabric 运行时日志的一部分进行收集的。

可以使用所选的诊断工具或管道来捕获日志。 POA 使用以下固定的提供程序 ID 通过[事件源](/dotnet/api/system.diagnostics.tracing.eventsource)记录事件：

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>运行状况报告

对于以下情况，POA 还会针对节点代理服务或协调器服务发布运行状况报告：

* 节点代理 NTService 关闭

   如果某个节点上的节点代理 NTService 关闭，将会针对节点代理服务生成警告级别的运行状况报告。

* 未启用修复管理器服务

   如果在群集上找不到修复管理器服务，将会针对协调器服务生成警告级别的运行状况报告。

## <a name="frequently-asked-questions"></a>常见问题

**问：当 POA 正在运行时，群集为何处于错误状态？**

答：在安装过程中，POA 将会禁用或重启节点，这可能会暂时导致群集处于不正常状态。

根据应用程序的策略，执行修补操作期间可以让一个节点关闭，或者可以让整个更新域完全关闭。

在 Windows 更新安装结束时，节点会在重启后重新启用。

在以下示例中，由于两个节点关闭且违反了 MaxPercentageUnhealthyNodes 策略，群集暂时进入了错误状态。 这是暂时性错误，在修补操作开始后即可恢复。

![不正常群集的图像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果问题持续出现，请参阅“故障排除”部分。

**问：如果 POA 处于警告状态，该怎么办？**

答：检查针对应用程序发布的运行状况报告是否指示了根本原因。 通常，警告中会包含问题的详细信息。 如果该问题是暂时性的，则应用程序预期会自动恢复。

**问：如果群集运行不正常，而我需要进行紧急的操作系统更新，该怎么办？**

答：如果群集不正常，POA 不会安装更新。 请尝试将群集恢复正常状态，以取消阻止 POA 工作流。

**问：对于我的群集，应将 TaskApprovalPolicy 设置为“NodeWise”还是“UpgradeDomainWise”？**

答：“UpgradeDomainWise”设置通过同时修补属于某个更新域的所有节点，来加快总体群集修复速度。 在此过程中，属于整个更新域的节点将不可用（处于 [*Disabled* 状态](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)）。

相比之下，“NodeWise”设置每次只修补一个节点，这意味着，总体群集修补可能需要更长时间。 但是，在修补过程中最多只有一个节点不可用（处于 *Disabled* 状态）。

如果群集在修补周期内可以容忍在 N-1 个更新域上运行（其中 N 是群集上更新域的总数），则你可以将策略设置为“UpgradeDomainWise”。 否则，请将其设置为“NodeWise”。

**问：修补一个节点需要多长时间？**

答：修补一个节点可能需要几分钟（例如 [Windows Defender 定义更新](https://www.microsoft.com/en-us/wdsi/definitions)）到几小时（例如 [Windows 累积更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)）。 修补一个节点所需的时间主要取决于： 
 - 更新的大小。
 - 必须在修补窗口中应用的更新数。
 - 安装更新、重新启动节点（如果需要）以及完成重新启动后安装步骤所需的时间。
 - VM 或计算机的性能以及网络状况。

**问：修补整个群集需要多长时间？**

答：修补整个群集所需的时间取决于：

- 修补一个节点所需的时间。

- 协调器服务的策略。 如果使用默认策略“NodeWise”，则每次只会修补一个节点，这种方法比使用“UpgradeDomainWise”要慢。 

   例如：如果修补一个节点需要大约 1 小时，那么，修补包含 5 个更新域（每个更新域包含 4 个节点）的 20 节点（节点类型相同）群集需要：
    - 使用“NodeWise”时：大约 20 小时。
    - 使用“UpgradeDomainWise”时：大约 5 小时。

- 群集负载。 每个修补操作都需要将客户工作负荷重新分配到群集中的其他可用节点。 正在进行修补的节点在此期间将处于 [*Disabling* 状态](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling)。 如果群集正在运行接近峰值负载，则禁用过程将需要更长时间。 因此，在这种重压条件下，整个修补过程可能会看起来很慢。

- 修补期间的群集运行状况错误。 [群集运行状况](./service-fabric-health-introduction.md)出现任何[降级](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error)都会中断修补过程。 此问题会增加修补整个群集所需的总时间。

**问：为什么某些更新会出现在通过 REST API 获得的 Windows 更新结果中，而不是在计算机的 Windows 更新历史记录下？**

答：某些产品更新仅在其自身的更新或修补程序历史记录中出现。 例如，Windows Defender 更新不一定会显示在 Windows Server 2016 上的 Windows 更新历史记录中。

**问：是否可以使用 POA 修补开发群集（单节点群集）？**

答：POA 不可用于修补单节点群集。 此限制是设计使然，因为 [Service Fabric 系统服务](./service-fabric-technical-overview.md#system-services)或其他客户应用会造成停机。 因此，修复管理器永远不会批准修补修复作业。

**问：如何在 Linux 上修补群集节点？**

答：若要了解 Linux 上的协调更新，请参阅 [Azure 虚拟机规模集自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。

**问：为何更新周期需要花费这么长时间？**

答：查询结果 JSON，进入所有节点的更新周期，然后可以尝试使用 OperationStartTime 和 OperationTime (OperationCompletionTime) 来了解在每个节点上安装更新所花费的时间。 

如果在某个较长时间段内未进行更新，原因可能是群集处于错误状态，因此，修复管理器无法批准任何 POA 修复任务。 如果任一节点上的更新安装花费了较长时间，原因可能是该节点有一段时间未曾更新过。 可能有大量的更新正在等待安装，因此导致了延迟。 

此外，可能该节点停滞在 *Disabling* 状态，因此其修补被阻止。 这种情况往往是禁用节点导致仲裁或数据丢失造成的。

**问：POA 修补节点时为何必须禁用该节点？**

答：POA 使用 *Restart* 意图禁用节点，这会停止或重新分配该节点上运行的所有 Service Fabric 服务。 POA 这样做的目的是确保应用程序最终不会混用新的和旧的 DLL，因此，我们不建议在未禁用节点的情况下对其进行修补。

**问：可以使用 POA 更新的最大节点数是多少？**

答：POA 使用 Service Fabric 修复管理器为要更新的节点创建修复任务。 但是，同时存在的修复任务不能超过 250 个。 目前，POA 会同时为每个节点创建修复任务，因此 POA 在一个群集中最多只能更新 250 个节点。 

## <a name="disclaimers"></a>免责声明

- POA 代表用户接受 Windows 更新的最终用户许可协议。 可以选择在应用程序的配置中关闭该设置。

- POA 收集遥测数据以跟踪使用情况和性能。 应用程序遥测遵循 Service Fabric 运行时的遥测设置（默认设置）。

## <a name="troubleshooting"></a>故障排除

本部分提供修补节点时出现的问题的可能故障排除解决方法。

### <a name="a-node-is-not-coming-back-to-up-state"></a>节点无法恢复启动状态

* 节点可能会出于以下原因停滞在 *Disabling* 状态：

  - 安全检查已挂起。 若要纠正此情况，请确保有足够多的节点处于正常状态。

* 节点可能会出于以下原因停滞在 *Disabled* 状态：

  - 已手动禁用节点。
  - 某个正在进行的 Azure 基础结构作业导致禁用了节点。
  - POA 已暂时禁用节点以对其进行修补。

* 节点可能会出于以下原因停滞在关闭状态：

  - 手动将节点置于关闭状态。
  - 节点正在重启（可能由 POA 触发）。
  - VM 或计算机出现故障，或出现网络连接问题。

### <a name="updates-were-skipped-on-some-nodes"></a>在某些节点上跳过了更新

POA 根据重新计划策略尝试安装 Windows 更新。 服务根据应用程序策略尝试恢复节点并跳过更新。

在这种情况下，将针对节点代理服务生成警告级别的运行状况报告。 Windows 更新结果也包含可能的失败原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>安装更新时群集运行状况转为错误状态

Windows 更新发生故障时，会使特定节点或更新域上的应用程序或群集的运行状况恶化。 POA 会终止任何后续的 Windows 更新操作，直到群集再次正常运行。

管理员必须介入，并判断为何 Windows 更新会导致应用程序或群集运行不正常。

## <a name="poa-release-notes"></a>POA 发行说明

>[!NOTE]
> 对于 POA 版本 1.4.0 及更高版本，可以在 GitHub 的[修补业务流程应用程序版本页](https://github.com/microsoft/Service-Fabric-POA/releases/)上找到发行说明和版本。

### <a name="version-110"></a>版本 1.1.0
- 公开发布的版本

### <a name="version-111"></a>版本 1.1.1
- 修复了 NodeAgentService 的 SetupEntryPoint 中的 bug，可阻止安装 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 系统重启工作流中的 Bug 修复。
- 由于修复任务准备过程中的运行状况检查，RM 任务创建过程中的 Bug 修复未能按预期方式进行。
- 将 Windows 服务 POANodeSvc 的启动模式从自动更改为延时自动。

### <a name="version-121"></a>版本 1.2.1

- 群集缩减工作流中的 Bug 修复。 引入了针对不存在节点中 POA 修复任务的垃圾回收逻辑。

### <a name="version-122"></a>版本 1.2.2

- 其他 Bug 修复。
- 二进制文件现已签名。
- 为应用程序添加了 sfpkg 链接。

### <a name="version-130"></a>版本 1.3.0

- 将 InstallWindowsOSOnlyUpdates 设置为 false 现在会安装所有可用的更新。
- 更改了禁用自动更新的逻辑。 这修复了在 Server 2016 及更高版本上不会禁用自动更新的 bug。
- 针对高级用例，对 POA 的微服务的放置约束进行了参数化。

### <a name="version-131"></a>版本 1.3.1
- 修复了因为禁用自动更新失败而导致 POA 1.3.0 无法在 Windows Server 2012 R2 或更早版本上运行的回归。 
- 修复了 InstallWindowsOSOnlyUpdates 配置总是被选为 True 的 bug。
- 将 InstallWindowsOSOnlyUpdates 的默认值更改为 False。

### <a name="version-132"></a>版本 1.3.2
- 修复了一个问题，如果存在其名称属于当前节点名称子集的节点，此问题会影响节点上的修补生命周期。 对于此类节点，可能出现修补缺失或重启操作挂起的情况。
