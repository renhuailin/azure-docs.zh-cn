---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 76d04b8d55b25c3fd5c96f34ad38088472c504b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740654"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本文详细说明如何将 IoT Edge 设备配置为充当透明网关，供其他设备用来与 IoT 中心通信。 本文使用术语 *IoT Edge 网关* 来指代配置为透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>在 IoT Edge 版本 1.1 及更低版本中，IoT Edge 设备不能作为 IoT Edge 网关的下游。
>
>下游设备不能使用文件上传。

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>下游设备不能使用文件上传。

::: moniker-end

成功设置透明网关连接需要完成三个常规步骤。 本文介绍其中的第一个步骤：

1. **将网关设备配置为服务器，以便下游设备能够安全地连接到该设备。设置网关以接收来自下游设备的消息，并将其路由到适当的目标。**
2. 为下游设备创建设备标识，以便它可以通过 IoT 中心进行身份验证。 配置下游设备，使其通过网关设备发送消息。 有关这些步骤，请参阅[向 Azure IoT 中心验证下游设备](how-to-authenticate-downstream-device.md)。
3. 将下游设备连接到网关设备并开始发送消息。 有关这些步骤，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

充当网关的设备需要安全地连接到下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止设备连接到潜在的恶意网关。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
下游设备可以是包含通过 [Azure IoT 中心](../iot-hub/index.yml)云服务创建的标识的任何应用程序或平台。 这些应用程序通常使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 但是，IoT Edge 设备不能位于 IoT Edge 网关的下游。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
下游设备可以是包含通过 [Azure IoT 中心](../iot-hub/index.yml)云服务创建的标识的任何应用程序或平台。 这些应用程序通常使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。
:::moniker-end
<!-- end 1.2 -->

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假设使用相同的证书设置来启用 IoT 中心的 [X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)，其中涉及与特定 IoT 中心（IoT 中心根 CA）关联的 X.509 CA 证书，以及通过此 CA 签名的一系列证书和 IoT Edge 设备的 CA。

>[!NOTE]
>在这些文章中使用的术语“根 CA 证书”是指 PKI 证书链最顶层的颁发机构公共证书，而不一定是联合证书颁发机构的证书根。 在许多情况下，它实际上是中间 CA 公共证书。

以下步骤将演示创建证书并将它们安装在网关上的正确位置的过程。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

安装了 IoT Edge 的 Linux 或 Windows 设备。

如果设备均未就绪，可在 Azure 虚拟机中创建一个。 按照[将第一个 IoT Edge 模块部署到虚拟 Linux 设备](quickstart-linux.md)中的步骤，创建一个 IoT 中心、创建一个虚拟机并配置 IoT Edge 运行时。

## <a name="set-up-the-device-ca-certificate"></a>安装设备 CA 证书

所有 IoT Edge 网关上都需要安装设备 CA 证书。 IoT Edge 安全守护程序使用 IoT Edge 设备 CA 证书为工作负载 CA 证书签名，而工作负载 CA 证书又为 IoT Edge 中心的服务器证书签名。 在连接启动期间，网关将其服务器证书提供给下游设备。 下游设备将进行检查，确保服务器证书是汇总到根 CA 证书的证书链的一部分。 此过程允许下游设备确认网关是否来自受信任的源。 有关详细信息，请参阅[了解 Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

根 CA 证书和设备 CA 证书（带有私钥）需要在 IoT Edge 网关设备上提供，并在 IoT Edge 配置文件中进行配置。 请记住，在这种情况下，“根 CA 证书”表示此 IoT Edge 方案的最顶层证书颁发机构。 网关设备 CA 证书和下游设备证书需要汇总到同一根 CA 证书。

>[!TIP]
>在 IoT Edge 设备上安装根 CA 证书和设备 CA 证书的过程也在[管理 IoT Edge 设备上的证书](how-to-manage-device-certificates.md)中进行了更详细的介绍。

准备好以下文件：

* 根 CA 证书
* 设备 CA 证书
* 设备 CA 私钥

对于生产方案，你应该通过自己的证书颁发机构生成这些文件。 对于开发和测试方案，可以使用演示证书。

如果没有自己的证书颁发机构，并且想要使用演示证书，请按照[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)中的说明来创建文件。 在该页上，需要执行以下步骤：

   1. 首先在设备上设置生成证书的脚本。
   2. 创建根 CA 证书。 在完成这些说明后，你将有一个根 CA 证书文件：
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. 创建 IoT Edge 设备 CA 证书。 完成这些说明后，你将有一个设备 CA 证书及其私钥：
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` 和
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

如果在另一计算机上创建了这些证书，请将它们复制到 IoT Edge 设备，然后继续后续步骤。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 在 IoT Edge 设备上，打开安全守护程序配置文件。

   * Windows： `C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

1. 找到文件的“证书设置”部分。 取消评论以“certificates:”开头的四行，并将你的三个文件的文件 URI 作为以下属性的值来提供：
   * **device_ca_cert**：设备 CA 证书
   * **device_ca_pk**：设备 CA 私钥
   * **trusted_ca_certs**：根 CA 证书

   确保“certificates:”行前面没有空格，并且其它行缩进了两个空格。

1. 保存并关闭该文件。

1. 重启 IoT Edge。
   * Windows： `Restart-Service iotedge`
   * Linux：`sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 在 IoT Edge 设备上，打开配置文件：`/etc/aziot/config.toml`

   >[!TIP]
   >如果设备上尚不存在配置文件，则使用 `/etc/aziot/config.toml.edge.template` 作为模板来创建一个。

1. 查找 `trust_bundle_cert` 参数。 取消注释此行，并为设备上的根 CA 证书文件提供文件 URI。

1. 找到文件的 `[edge_ca]` 节。 取消注释此部分中的三行，并将证书和密钥文件的文件 URI 作为以下属性的值提供：
   * cert：设备 CA 证书
   * pk：设备 CA 私钥

1. 保存并关闭该文件。

1. 单击“应用”以应用更改。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="deploy-edgehub-and-route-messages"></a>部署 edgeHub 和路由消息

下游设备将遥测和消息发送到网关设备，其中 IoT Edge 中心模块负责将信息路由到其他模块或 IoT 中心。 若要为此功能准备网关设备，请确保：

* IoT Edge 中心模块已部署到设备。

  首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块，即 IoT Edge 代理。 为设备创建第一个部署后，第二个系统模块（IoT Edge 中心）也将启动。 如果 edgeHub 模块未在设备上运行，请为设备创建一个部署。

* IoT Edge 中心模块设置了路由，用于处理来自下游设备的传入消息。

  网关设备必须有一个适当的路由来处理来自下游设备的消息，否则这些消息将不会被处理。 可以将消息发送到网关设备上的模块，也可以直接发送到 IoT 中心。

若要部署 IoT Edge 中心模块并为其配置路由以处理来自下游设备的传入消息，请执行以下步骤：

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备。

3. 选择“设置模块”  。

4. 在“模块”页上，可以添加任何要部署到网关设备的模块。 就本文而言，我们侧重于配置和部署 edgeHub 模块，无需在此页面上显式设置。

5. 在完成时选择“下一步:路由”。

6. 在“路由”页上，请确保存在用于处理来自下游设备的消息的路由。 例如：

   * 将所有消息（无论是来自模块还是来自下游设备）发送到 IoT 中心的路由：
       * **名称**：`allMessagesToHub`
       * **值**：`FROM /messages/* INTO $upstream`

   * 将来自所有下游设备的全部消息发送到 IoT 中心的路由：
      * **名称**：`allDownstreamToHub`
      * **值**：`FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      该路由之所以有效，是因为与来自 IoT Edge 模块的消息不同，来自下游设备的消息没有与之关联的模块 ID。 使用路由的 WHERE 子句，我们可以筛选出具有该系统属性的所有消息。

      有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。

7. 创建一个或多个路由后，选择“查看 + 创建”。

8. 在“查看 + 创建”页面上，选择“创建”。 

## <a name="open-ports-on-gateway-device"></a>在网关设备上打开端口

标准 IoT Edge 设备不需要任何入站连接便可工作，因为与 IoT 中心之间的所有通信都是通过出站连接执行的。 网关设备则不同，因为它们需要从其下游设备接收消息。 如果下游设备与网关设备之间有防火墙，则也需要能够通过防火墙进行通信。

要使网关方案能够正常工作，必须为来自下游设备的入站流量打开 IoT Edge 中心的至少一个受支持协议。 受支持的协议包括 MQTT、AMQP、HTTPS、基于 WebSocket 的 MQTT 和基于 Websocket 的 AMQP。

| 端口 | 协议 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="next-steps"></a>后续步骤

现在，你已将一个 IoT Edge 设备设置为透明网关，需要将下游设备配置为信任该网关并向其发送消息。 继续[在 Azure IoT 中心内对下游设备进行身份验证](how-to-authenticate-downstream-device.md)，以进行设置透明网关方案的后续步骤。