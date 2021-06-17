---
title: 空间分析容器的遥测和日志记录
titleSuffix: Azure Cognitive Services
description: 空间分析为每个容器提供通用的配置框架见解、日志记录和安全设置。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: 08afa72507bb5689dbd1a003cb776958d6e63f1d
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746440"
---
# <a name="telemetry-and-troubleshooting"></a>遥测和故障排除

空间分析包括一组功能，可用于监视系统的运行状况，并帮助解决诊断问题。

## <a name="enable-visualizations"></a>启用可视化

若要为视频帧中的 AI 见解事件启用可视化，需要在台式机上使用 `.debug` 版本的[空间分析操作](spatial-analysis-operations.md)。 在 Azure Stack Edge 设备上无法进行可视化。 有四个可用的调试操作。

如果你的设备不是 Azure Stack Edge 设备，请编辑[台式机](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)的部署清单文件，以使用正确的 `DISPLAY` 环境变量值。 此变量需要与主计算机上的 `$DISPLAY` 变量匹配。 更新部署清单后，重新部署容器。

部署完成后，可能需要将 `.Xauthority` 文件从主计算机复制到容器，然后重启容器。 在以下示例中，`peopleanalytics` 是主计算机上的容器名称。

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>收集系统运行状况遥测数据

Telegraf 是可与空间分析配合使用的开源映像，它在 Microsoft 容器注册表中提供。 它采用以下输入并将其发送到 Azure Monitor。 可以使用所需的自定义输入和输出生成 telegraf 模块。 空间分析中的 telegraf 模块配置是部署清单的一部分（在上面链接）。 此模块是可选的，如果不需要它，可将其从清单中删除。 

输入： 
1. 空间分析指标
2. 磁盘指标
3. CPU 指标
4. Docker 指标
5. GPU 指标

输出：
1. Azure Monitor

提供的空间分析 telegraf 模块会将空间分析容器发出的所有遥测数据发布到 Azure Monitor。 有关将 Azure Monitor 添加到订阅的信息，请参阅 [Azure Monitor](../../azure-monitor/overview.md)。

设置 Azure Monitor 后，需要创建凭据，使模块能够发送遥测数据。 可以使用 Azure 门户或以下 Azure CLI 命令创建新的服务主体。

> [!NOTE] 
> 此命令要求你在订阅中拥有“所有者”特权。 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

在 [Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或 [带有 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的部署清单中找到 *telegraf* 模块，将以下值替换为在上一步骤中获取的服务主体信息，然后重新部署。

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

部署 telegraf 模块后，可以通过 Azure Monitor 服务或者通过在 Azure 门户上的 IoT 中心内选择“监视”来访问报告的指标。

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor 遥测报告":::

### <a name="system-health-events"></a>系统运行状况事件

| 事件名称                  | 描述    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | 当用户将空间分析模块状态从“正在运行”更改为“已停止”时发送。   |
| archon_error                | 当容器中的任何进程崩溃时发送。 这是一个关键错误。      |
| InputRate                   | 图处理视频输入的速率。 每隔 5 分钟报告一次。              |
| OutputRate                  | 图输出 AI 见解的速率。 每隔 5 分钟报告一次。                |
| archon_allGraphsStarted     | 所有图均已完成启动后发送。                                           |
| archon_configchange         | 更改了图配置时发送。                                              |
| archon_graphCreationFailed  | 当具有报告的 `graphId` 的图无法启动时发送。                           |
| archon_graphCreationSuccess | 当具有报告的 `graphId` 的图成功启动时发送。                      |
| archon_graphCleanup         | 当具有报告的 `graphId` 的图完成清理并退出时发送。                      |
| archon_graphHeartbeat       | 每分钟为每个技能图发送的检测信号。                                   |
| archon_apiKeyAuthFail       | 当计算机视觉资源密钥出于以下原因，已超过 24 小时无法对容器进行身份验证时发送：超出配额、无效、脱机。 |
| VideoIngesterHeartbeat      | 每小时发送一次，指示视频是从视频源流式传输的，并提供该小时内发生的错误数。 针对每个图报告。 |
| VideoIngesterState          | 报告视频流式传输的“已停止”或“已启动”状态。  针对每个图报告。              |

##  <a name="troubleshooting-an-iot-edge-device"></a>排查 IoT Edge 设备问题

可以使用 `iotedge` 命令行工具来检查正在运行的模块的状态和日志。 例如：
* `iotedge list`：报告正在运行的模块的列表。 
  可以使用 `iotedge logs edgeAgent` 进一步检查错误。 如果 `iotedge` 停滞，你可以尝试使用 `iotedge restart edgeAgent` 将其重启
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` 可重启特定的模块 

## <a name="collect-log-files-with-the-diagnostics-container"></a>使用诊断容器收集日志文件

空间分析生成 Docker 调试日志，你可以使用这些日志来诊断运行时问题，或将其包含在支持票证中。 可供下载的空间分析诊断模块已在 Microsoft 容器注册表中提供。 在 [Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或[带有 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的清单部署文件中找到该诊断模块。

在“env”节中添加以下配置：

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

若要优化上传到远程终结点（例如 Azure Blob 存储）的日志，我们建议保持较小的文件大小。 请参阅以下示例来了解建议的 Docker 日志配置。

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>配置日志级别

使用日志级别配置可以控制生成的日志的详细程度。 支持的日志级别为：`none`、`verbose`、`info`、`warning` 和 `error`。 节点和平台的默认日志详细级别为 `info`。 

可以通过将 `ARCHON_LOG_LEVEL` 环境变量设置为允许的值之一，来全局修改日志级别。
还可以通过 IoT Edge 模块孪生文档为所有已部署的技能全局设置日志级别，或者按如下所示通过设置 `platformLogLevel` 和 `nodesLogLevel` 的值来为每个特定技能设置日志级别。

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>收集日志

> [!NOTE]
> `diagnostics` 模块不影响日志记录内容，它只是帮助收集、筛选和上传现有的日志。
> 必须具有 Docker API 1.40 或更高版本才能使用此模块。

[Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或[带有 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的示例部署清单文件包含一个名为 `diagnostics` 的模块，该模块可收集和上传日志。 此模块默认已禁用，当你需要访问日志时，应通过 IoT Edge 模块配置启用此模块。 

`diagnostics` 收集是按需进行的，可通过 IoT Edge 直接方法对其进行控制；此收集模块可将日志发送到 Azure Blob 存储。

### <a name="configure-diagnostics-upload-targets"></a>配置诊断上传目标

在 IoT Edge 门户中选择你的设备，然后选择“诊断”模块。 在 [Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或 [带有 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的示例部署清单文件中，找到用于诊断的名为 `env` 的 **Environment Variables** 节，并在其中添加以下信息：

**配置上传到 Azure Blob 存储**

1. 创建你自己的 Azure Blob 存储帐户（如果尚未这样做）。
2. 从 Azure 门户获取你的存储帐户的 **连接字符串**。 此连接字符串位于“访问密钥”中。
3. 空间分析日志会自动上传到名为“rtcvlogs”的 Blob 存储容器，其文件名格式如下：`{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log`。

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>上传空间分析日志

在 `getRTCVLogs` 模块中使用 `diagnostics` IoT Edge 方法按需上传日志。 


1. 转到 IoT 中心门户页，选择“Edge 设备”，然后选择你的设备和诊断模块。 
2. 转到模块的详细信息页，单击“直接方法”选项卡。
3. 在“方法名称”中键入 `getRTCVLogs`，在“有效负载”中键入 JSON 格式的字符串。 可以输入 `{}`，表示空有效负载。 
4. 设置连接和方法超时，然后单击“调用方法”。
5. 选择你的目标容器，并使用“日志记录语法”部分中所述的参数生成有效负载 JSON 字符串。 单击“调用方法”以执行请求。

>[!NOTE]
> 结合空有效负载调用 `getRTCVLogs` 方法会返回设备上部署的所有容器的列表。 方法名称区分大小写。 如果提供了错误的方法名称，则你会收到 501 错误。

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="调用 getRTCVLogs 方法":::
![getRTCVLogs 直接方法页](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>日志记录语法

下表列出了查询日志时可以使用的参数。

| 关键字 | 说明 | 默认值 |
|--|--|--|
| StartTime | 所需的日志开始时间 (UTC)，以毫秒为单位。 | `-1`，容器运行时的开始时间。 将 `[-1.-1]` 用作时间范围时，API 将返回过去一小时的日志。|
| EndTime | 所需的日志结束时间 (UTC)，以毫秒为单位。 | `-1`，表示当前时间。 使用 `[-1.-1]` 时间范围时，API 将返回过去一小时的日志。 |
| ContainerId | 用于提取日志的目标容器。| 没有容器 ID 时为 `null`。 API 将连同 ID 一起返回所有可用的容器信息。|
| DoPost | 执行上传操作。 如果此参数设置为 `false`，则会执行请求的操作并返回上传大小，但不执行上传。 如果设置为 `true`，则会启动所选日志的异步上传 | `false`，表示不上传|
| 限制 | 指示要在每个批中上传多少行日志 | `1000`，使用此参数可以调整发布速度。 |
| 筛选器 | 筛选要上传的日志 | `null`，可以根据空间分析日志结构将筛选器指定为键值对：`[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]`。 例如：`{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

下表列出了查询响应中的特性。

| 关键字 | 说明|
|--|--|
|DoPost| *true* 或 *false*。 指示日志是否已上传。 选择不上传日志时，API 将以***同步** _方式返回信息。 选择上传日志时，如果请求有效，则 API 将返回 200，并开始以_*_异步_**方式上传日志。|
|TimeFilter| 应用于日志的时间筛选器。|
|ValueFilters| 应用于日志的关键字筛选器。 |
|时间戳| 方法执行开始时间。 |
|ContainerId| 目标容器 ID。 |
|FetchCounter| 日志行总数。 |
|FetchSizeInByte| 日志数据总量，以字节为单位。 |
|MatchCounter| 有效的日志行数。 |
|MatchSizeInByte| 有效的日志数据量，以字节为单位。 |
|FilterCount| 应用筛选器后的日志行总数。 |
|FilterSizeInByte| 应用筛选器后的日志数据总量，以字节为单位。 |
|FetchLogsDurationInMiliSec| 提取操作持续时间。 |
|PaseLogsDurationInMiliSec| 筛选操作持续时间。 |
|PostLogsDurationInMiliSec| 发布操作持续时间。 |

#### <a name="example-request"></a>示例请求 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>示例响应 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

检查提取日志的行、时间和大小，如果这些设置看上去正确，请将 ***DoPost*** 替换为 `true`，以便将具有相同筛选器的日志推送到目标。 

排查问题时，可以从 Azure Blob 存储导出日志。 

## <a name="troubleshooting-the-azure-stack-edge-device"></a>排查 Azure Stack Edge 设备问题

以下部分旨在帮助你调试和验证 Azure Stack Edge 设备的状态。

### <a name="access-the-kubernetes-api-endpoint"></a>访问 Kubernetes API 终结点。 

1. 在设备的本地 UI 中，转到“设备”页。 
2. 在“设备终结点”下，复制 Kubernetes API 服务终结点。 此终结点是采用以下格式的字符串：`https://compute..[device-IP-address]`。
3. 保存该终结点字符串。 稍后在配置 `kubectl` 以访问 Kubernetes 群集时，你将使用此字符串。

### <a name="connect-to-powershell-interface"></a>连接到 PowerShell 界面

从 Windows 客户端远程连接。 创建 Kubernetes 群集后，可以通过此群集管理应用程序。 你将需要连接到设备的 PowerShell 界面。 远程连接到设备的过程根据客户端操作系统的不同而异。 以下步骤适用于运行 PowerShell 的 Windows 客户端。

> [!TIP]
> * 在开始之前，请确保 Windows 客户端运行的是 Windows PowerShell 5.0 或更高版本。
> * 也可以[在 Linux 上使用](/powershell/scripting/install/installing-powershell-core-on-linux) PowerShell。

1. 以管理员身份运行 Windows PowerShell 会话。 
    1. 确保 Windows 远程管理服务正在客户端上运行。 在命令提示符下键入 `winrm quickconfig`。

2. 为设备 IP 地址分配一个变量。 例如，`$ip = "<device-ip-address>"`。

3. 使用以下命令将设备的 IP 地址添加到客户端的受信任主机列表。 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. 在设备上启动 Windows PowerShell 会话。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. 根据提示提供密码。 使用登录到本地 Web 界面时所用的同一密码。 默认的本地 Web 界面密码为 `Password1`。 

### <a name="access-the-kubernetes-cluster"></a>访问 Kubernetes 群集

创建 Kubernetes 群集后，可以使用 `kubectl` 命令行工具访问该群集。

1. 创建新命名空间。 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. 创建一个用户并获取一个配置文件。 此命令将输出 Kubernetes 群集的配置信息。 复制此信息并将其保存到名为“config”的文件中。保存该文件时请不要指定文件扩展名。
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. 将 *config* 文件添加到本地计算机上你的用户配置文件中的 *.kube* 文件夹。    

4. 将命名空间与创建的用户相关联。

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. 使用以下命令在你的 Windows 客户端上安装 `kubectl`：
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. 将 DNS 条目添加到系统上的 hosts 文件中。 
    1. 以管理员身份运行记事本，并打开位于 `C:\windows\system32\drivers\etc\hosts` 处的 *hosts* 文件。 
    2. 在 hosts 文件中创建一个条目，并在其中包含你从本地 UI 的“设备”页获取的设备 IP 地址和 DNS 域。 应使用的终结点类似于：`https://compute.asedevice.microsoftdatabox.com/10.100.10.10`。

7. 验证是否可以连接到 Kubernetes Pod。

    ```powershell
    kubectl get pods -n "iotedge"
    ```

若要获取容器日志，请运行以下命令：

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>有用的命令

|命令  |说明  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | 生成 Kubernetes 配置文件。 使用该命令时，请将信息复制到名为“config”的文件中。保存该文件时请不要指定文件扩展名。        |
| `Get-HcsApplianceInfo` | 返回有关你的设备的信息。 |
| `Enable-HcsSupportAccess` | 生成访问凭据以启动支持会话。 |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>如何提交空间分析的支持票证 

如果你需要我们提供更多的支持来帮助你解决使用空间分析容器时遇到的问题，请按照以下步骤填写并提交支持票证。 我们的团队将向你提供更多的指导。 

### <a name="fill-out-the-basics"></a>填写基本信息 
在[新建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)页上创建新的支持票证。 按照提示填写以下参数：

![支持基本信息](./media/support-ticket-page-1-final.png)

1. 将“问题类型”设置为 `Technical`。
2. 选择要用于部署空间分析容器的订阅。
3. 选择 `My services`，并选择 `Cognitive Services` 作为服务。
4. 选择要用于部署空间分析容器的资源。
5. 简要但又具体地说明你所遇到的问题。 
6. 选择 `Spatial Analysis` 作为问题类型。
7. 从下拉列表中选择适当的子类型。
8. 选择“下一步: 解决方法”转到下一页。

### <a name="recommended-solutions"></a>建议的解决方案
在下一阶段，将为你选择的问题类型提供建议的解决方法。 这些解决方法可解决最常见的问题，如果它们不能有效地解决你遇到的问题，请选择“下一步: 详细信息”转到下一步。

### <a name="details"></a>详细信息
在此页上，添加有关你所遇到的问题的一些附加详细信息。 请务必包含尽可能多的细节，这有助于我们的工程师更好地缩小问题的排查范围。 请包含你的首选联系方法和问题严重性，以便我们正确地与你取得联系；然后选择“下一步: 查看 + 创建”转到下一步。 

### <a name="review-and-create"></a>查看 + 创建 
查看支持请求的详细信息，确保所有内容准确，并有效地陈述了问题。 准备就绪后，选择“创建”将票证发送给我们的团队！ 在我们收到你的票证后，会向你发送一封确认电子邮件，然后我们的团队将尽快提供帮助。 你可以在 Azure 门户中查看票证的状态。

## <a name="next-steps"></a>后续步骤

* [部署人员计数 Web 应用程序](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [相机放置指南](spatial-analysis-camera-placement.md)
* [区域和线条放置指南](spatial-analysis-zone-line-placement.md)
