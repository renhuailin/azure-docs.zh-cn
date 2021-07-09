---
title: 部署 Microsoft OPC 发布服务器
description: 在本教程中，您将了解如何在独立模式下部署 OPC 发布服务器。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 80d4ef5fc474ff40807e01b6ecb9014db1ae8f51
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677866"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>教程：部署 OPC 发布服务器

OPC 发布服务器是一种完全受支持的 Microsoft 产品，在开放的环境中开发，可弥补工业资产与 Microsoft Azure 云之间的差距。 它通过连接到启用了 OPC UA 的资产或工业连接软件来实现此目的，并以各种格式将遥测数据发布到 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)，包括 IEC62541 OPC UA PubSub 标准格式（从2.6 版开始）。

它作为模块在 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 上运行，或者作为容器在纯 Docker 上运行。 由于它利用 [.NET 跨平台运行时](/dotnet/core/introduction)，因此也会在 Linux 和 Windows 10 上本地运行。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 部署 OPC 发布服务器
> * 以容器的形式运行 OPC 发布服务器的最新发布版本
> * 在 Azure 门户中指定容器创建选项

如果没有 Azure 订阅，请创建一个免费的试用帐户

## <a name="prerequisites"></a>必备条件

- 必须创建 IoT 中心
- 必须创建 IoT Edge 设备
- 必须注册 IoT Edge 设备

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>从 Azure 市场部署 OPC 发布服务器

1. 选择要使用的 Azure 订阅。 如果没有 Azure 订阅可用，则必须创建一个。
2. 选择 OPC 发布服务器应该向其发送数据的 IoT 中心。 如果无 IoT 中心可用，则必须创建一个。
3. 选择 OPC 发布服务器应该在其上运行的 IoT Edge 设备（或输入要创建的新 IoT Edge 设备的名称）。
4. 单击“创建”。 所选 IoT Edge 设备的“在设备上设置模块”页将打开。
5. 单击“OPCPublisher”打开 OPC 发布服务器的“更新 IoT Edge 模块”页，然后选择“容器创建选项”。
6. 基于您对 OPC 发布服务器的使用情况指定其他容器创建选项，请参阅下一节。

[此处](https://mcr.microsoft.com/v2/iotedge/opc-publisher/tags/list)列出了 Docker OPC 发布服务器支持的所有 Docker 映像。 对于非 OPC UA 启用的资产，我们已与领先的行业连接提供商合作，帮助他们将其 OPC UA 适配器软件移植到 Azure IoT Edge。 Azure [市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1)中提供了这些适配器。

## <a name="specifying-container-create-options-in-the-azure-portal"></a>在 Azure 门户中指定容器创建选项
通过 Azure 门户部署 OPC 发布服务器时，可以在 OPC 发布服务器的“更新 IoT Edge 模块”页中指定容器创建选项。 这些创建选项必须采用 JSON 格式。 可以通过 Cmd 键指定 OPC 发布服务器命令行参数，例如：
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

OPC 发布服务器的一组典型 IoT Edge 模块容器创建选项为：
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

指定这些选项后，OPC 发布服务器将读取配置文件 `./pn.json`。 在启动时，OPC 发布服务器的工作目录设置为 `/appdata`，因此，OPC 发布服务器将读取其 Docker 容器内的文件 `/appdata/pn.json`。 OPC 发布服务器的日志文件将写入 `/appdata` ，并且还将在此目录中创建用于 OPC UA 证书的目录 `CertificateStores`。 若要使这些文件在 IoT Edge 主机文件系统中可用，容器配置需要绑定装载卷。 `/iiotedge:/appdata` 绑定会将目录 `/appdata` 映射到主机目录 `/iiotedge`（如果不存在，IoT Edge 运行时会创建一个）。
**如果没有此绑定装载卷，则在重启容器时，所有 OPC 发布服务器配置文件都将丢失。**

使用其主机名而没有在网络上配置 DNS 服务器的 OPC UA 服务器，可以通过将 `ExtraHosts` 条目添加到 `HostConfig` 部分来实现和它的连接：

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>后续步骤 
现在，您已部署 OPC 发布服务器 Edge 模块，下一步是对它进行配置：

> [!div class="nextstepaction"]
> [配置 OPC 发布服务器](tutorial-publisher-configure-opc-publisher.md)