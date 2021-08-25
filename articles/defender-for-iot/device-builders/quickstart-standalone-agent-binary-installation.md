---
title: 快速入门：安装 Defender for IoT 微代理（预览版）
description: 通过本快速入门，了解如何安装 Defender 微代理并对其进行身份验证。
ms.date: 06/27/2021
ms.topic: quickstart
ms.openlocfilehash: e77ebaf3ab99fc88d3d0138edf3c815b63507e6c
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195546"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>快速入门：安装 Defender for IoT 微代理（预览版）

本文介绍了如何安装和验证 Defender 微代理。

## <a name="prerequisites"></a>先决条件

在安装 Defender for IoT 模块之前，必须在 IoT 中心创建一个模块标识。 有关如何创建模块标识的详细信息，请参阅[创建 Defender IoT 微代理模块孪生（预览）](quickstart-create-micro-agent-module-twin.md)。

## <a name="install-the-package"></a>安装包

添加相应的 Microsoft 包存储库：

1. 下载与设备操作系统匹配的存储库配置。  

    - 对于 Ubuntu 18.04

        ```bash
        curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
        ```

    - 对于 Ubuntu 20.04

        ```bash
            curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > ./microsoft-prod.list
        ```

    - 对于 Debian 9（AMD64 和 ARM64）

        ```bash
        curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
        ```

1. 将存储库配置复制到 `sources.list.d` 目录。

    ```bash
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```

1. 更新通过以下命令添加的存储库中的包列表：

    ```bash
    sudo apt-get update
    ```

若要在 Debian 和基于 Ubuntu 的 Linux 发行版上安装 Defender 微代理包，请使用以下命令：

```bash
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>微代理身份验证方法 

可使用下面两种选项对 Defender for IoT 微代理进行身份验证： 

- 模块标识连接字符串。 

- 证书。

### <a name="authenticate-using-a-module-identity-connection-string"></a>使用模块标识连接字符串进行身份验证

在开始执行这些步骤之前，请确保符合本文所述的[先决条件](#prerequisites)并创建模块标识。 

#### <a name="get-the-module-identity-connection-string"></a>获取模块标识连接字符串

若要从 IoT 中心获取模块标识连接字符串： 

1. 导航到“IoT 中心”并选择你的中心。

1. 在左侧菜单中的“资源管理器”部分下，选择“IoT 设备”。 

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="在左侧菜单中选择“IoT 设备”。":::

1. 在“设备 ID”列表中选择一个设备以查看“设备详细信息”页。

1. 选择“模块标识” ****  选项卡。

1. 在与设备关联的模块标识的列表中选择“DefenderIotMicroAgent” ****  模块。

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="选择“模块标识”选项卡。":::

1. 在“模块标识详细信息”页中，选择“复制”按钮以复制“连接字符串(主密钥)”。

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="选择“复制”按钮以复制“连接字符串(主密钥)”。":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>配置使用模块标识连接字符串进行身份验证

若要将代理配置为使用模块标识连接字符串进行身份验证：

1. 输入以下命令，将包含 utf-8 编码的连接字符串的 `connection_string.txt` 文件放到 Defender 代理目录 `/var/defender_iot_micro_agent` 路径中：

    ```bash
    sudo bash -c 'echo "<connection string>" > /var/defender_iot_micro_agent/connection_string.txt'
    ```

    `connection_string.txt` 应位于以下路径位置：`/var/defender_iot_micro_agent/connection_string.txt`。

1. 使用以下命令重启服务：  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>使用证书进行身份验证

若要使用证书进行身份验证：

1. 按照[这些说明](../../iot-hub/tutorial-x509-scripts.md)获取证书。

1. 将证书的 PEM 编码的公共部分和私钥放在 Defender 代理目录的文件 `certificate_public.pem` 和 `certificate_private.pem` 中。 

1. 将相应的连接字符串放到 `connection_string.txt` 文件中。 连接字符串应如下所示： 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    此字符串提醒 Defender 需要提供证书进行身份验证。 

1. 使用以下命令重启服务：  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>验证你的安装

若要验证你的安装：

1. 使用以下命令确保微代理正在正常运行：  

    ```bash
    systemctl status defender-iot-micro-agent.service
    ```

1. 通过确认服务状态为 `active` 来确保服务稳定，并确保进程运行时间很合适

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="检查确保服务稳定且处于活动状态。":::
 
## <a name="testing-the-system-end-to-end"></a>端到端测试系统 

可在设备上创建触发器文件来端到端测试系统。 触发器文件将在代理中引发基线扫描，检测文件是否违反基线。 

使用以下命令在文件系统上创建一个文件：

```bash
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```

中心内将显示一条基线验证失败建议，其中 `CceId` 为 CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0： 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="中心内显示的基线验证失败建议。" lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

建议最多可在中心显示一小时的时间。 

## <a name="micro-agent-versioning"></a>微代理版本控制 

若要安装特定版本的 Defender IoT 微代理，请运行以下命令： 

```bash
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：创建 Defender IoT 微代理模块孪生（预览版）](quickstart-create-micro-agent-module-twin.md)