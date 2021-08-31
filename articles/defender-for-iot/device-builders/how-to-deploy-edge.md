---
title: 部署 IoT Edge 安全模块
description: 了解如何在 IoT Edge 上部署 Defender for IoT 安全代理。
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 45f7351b47554ce3eb6906d5b6011de945182484
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014513"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT Edge 设备上部署安全模块

Defender for IoT 模块为 IoT Edge 设备提供了一个全面的安全解决方案。
此安全模块收集、聚合和分析来自你的操作系统和容器系统的原始安全数据，从而生成可操作的安全建议和警报。
若要了解详细信息，请参阅[适用于 IoT Edge 的安全模块](security-edge-architecture.md)。

本文介绍如何在 IoT Edge 设备上部署安全模块。

## <a name="deploy-security-module"></a>部署安全模块

使用以下步骤部署适用于 IoT Edge 的 Defender for IoT 安全模块。

### <a name="prerequisites"></a>先决条件

1. 在你的 IoT 中心，确保你的设备是[注册新设备](../../iot-edge/how-to-register-device.md#register-a-new-device)。

1. Defender for IoT Edge 模块需要在 IoT Edge 设备上安装 [AuditD 框架](https://linux.die.net/man/8/auditd)。

    - 通过在 IoT Edge 设备上运行以下命令来安装框架：

    `sudo apt-get install auditd audispd-plugins`

    - 通过运行以下命令来验证 AuditD 是否已激活：

    `sudo systemctl status auditd`<br>
    - 预期响应为：`active (running)`

### <a name="deployment-using-azure-portal"></a>使用 Azure 门户进行部署

1. 从 Azure 门户打开“市场”。

1. 选择“物联网”，然后搜索“适用于 IoT 的 Azure 安全中心”并选择它。

   :::image type="content" source="media/howto/edge-onboarding.png" alt-text="选择 Defender for IoT":::

1. 选择“创建”以配置部署。

1. 选择 IoT 中心的 Azure 订阅，然后选择“IoT 中心” 。<br>选择“部署到设备”以针对单个设备进行部署，或选择“大规模部署”以针对多个设备进行部署，然后选择“创建”。 有关大规模部署的详细信息，请参阅[如何部署](../../iot-edge/how-to-deploy-at-scale.md)。

    >[!Note]
    >如果选择了“大规模部署”，请添加设备名称和详细信息，然后继续在以下说明中的“添加模块”选项卡中进行操作 。

完成每个步骤即可完成针对 Defender for IoT 的 IoT Edge 部署。

#### <a name="step-1-modules"></a>步骤 1：模块

1. 选择 AzureSecurityCenterforIoT 模块。
1. 在“模块设置”选项卡上，将“名称”更改为“azureiotsecurity”  。
1. 在“环境变量”选项卡上，根据需要添加一个变量（例如，可以添加“调试级别”并将其设置为以下值之一：“致命”、“错误”、“警告”或“信息”）。
1. 在“容器创建选项”选项卡上，添加以下配置：

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. 在“模块孪生设置”选项卡上，添加以下配置：

   模块孪生属性：
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   模块孪生属性内容： 

   ```json
     {

     }
   ```
    
   有关如何配置代理的详细信息，请参阅[配置安全代理](./how-to-agent-configuration.md)。

1. 选择“更新”。

#### <a name="step-2-runtime-settings"></a>步骤 2：运行时设置

1. 选择“运行时设置”。
2. 在“Edge 中心”下，将“映像”更改为“mcr.microsoft.com/azureiotedge-hub:1.0.8.3”  。

    >[!Note]
    > 目前，支持版本 1.0.8.3 或更早版本。

3. 验证“创建选项”是否已设置为以下配置：

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. 选择“保存”。

5. 选择“下一步”  。

#### <a name="step-3-specify-routes"></a>步骤 3：指定路由

1. 在“指定路由”选项卡上，确保有一个路由（显式或隐式），该路由会根据以下示例将消息从 azureiotsecurity 模块转发到 $upstream  。 仅当路由已准备就绪时，才选择“下一步”。

   示例路由：

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. 选择“下一步”  。

#### <a name="step-4-review-deployment"></a>步骤 4：评审部署

- 在“查看部署”选项卡上，查看部署信息，然后选择“创建”以完成部署 。

## <a name="diagnostic-steps"></a>诊断步骤

如果遇到问题，容器日志是了解 IoT Edge 安全模块设备的状态的最佳方式。 可以使用本部分中的命令和工具来收集信息。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>验证是否已安装所需的容器并且容器是否按预期运行

1. 在 IoT Edge 设备上运行以下命令：

    `sudo docker ps`

1. 验证以下容器是否正在运行：

   | 名称 | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   如果不足所需的最小容器数，请检查 IoT Edge 部署清单是否与推荐的设置一致。 有关详细信息，请参阅[部署 IoT Edge 模块](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>检查模块日志中是否存在错误

1. 在 IoT Edge 设备上运行以下命令：

   `sudo docker logs azureiotsecurity`

1. 对于更详细的日志，请将以下环境变量添加到 azureiotsecurity 模块部署：`logLevel=Debug`。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请继续阅读有关模块配置的操作指南。
> [!div class="nextstepaction"]
> [模块配置操作指南](./how-to-agent-configuration.md)