---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956228"
---
在[生成和部署 IoT Edge 部署清单](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)步骤中，在 Visual Studio Code 中，展开 AZURE IOT 中心（在左下部分）下的“lva-sample-device”节点 。 应会看到已部署以下模块：

* 实时视频分析模块，名为 `lvaEdge`
* `rtspsim` 模块，可模拟 RTSP 服务器，充当实时视频源的源

  ![模块](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 上述步骤假设你使用的是设置脚本创建的虚拟机。 如果使用的是自己的边缘设备，请转到你的边缘设备并使用管理员权限运行以下命令，以拉取并存储该快速入门所使用的示例视频文件：  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
