---
title: 安装 Defender for IoT 微代理（预览）
titleSuffix: Azure Defender for IoT
description: 了解如何安装和验证 Defender 微代理。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8d7e6dffcc40ba1e34a4a84ecccccc1f8b181393
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703042"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>安装 Defender for IoT 微代理（预览）

本文介绍了如何安装和验证 Defender 微代理。

## <a name="prerequisites"></a>先决条件

安装 Defender for IoT 模块之前，必须在 IoT 中心创建一个模块标识。 若要详细了解如何创建模块标识，请查看[创建 azureiotsecurity 模块孪生](quickstart-create-security-twin.md)

## <a name="install-the-package"></a>安装包

按照[这些说明](/windows-server/administration/linux-package-repository-for-microsoft-software)安装和配置 Microsoft 包存储库。 

对于 Debian 9，相关说明不包含需要添加的存储库，请使用以下命令添加该存储库： 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

若要在 Debian 和基于 Ubuntu 的 Linux 发行版上安装 Defender 微代理包，请使用以下命令：

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>微代理身份验证方法 

可使用下面两种选项对 Defender for IoT 微代理进行身份验证： 

- 连接字符串。 

- 证书。

### <a name="authenticate-using-a-connection-string"></a>使用连接字符串进行身份验证

若要使用连接字符串进行身份验证：

1. 输入以下命令，将包含 utf-8 编码的连接字符串的 `connection_string.txt` 文件放到 Defender 代理目录 `/var/defender_iot_micro_agent` 路径中：

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    `connection_string.txt` 现应在路径位置 `/var/defender_iot_micro_agent/connection_string.txt` 中。

1. 使用以下命令重启服务：  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>使用证书进行身份验证

若要使用证书进行身份验证：

1. 按照[这些说明](../iot-hub/iot-hub-security-x509-get-started.md)获取证书。

1. 将证书的 PEM 编码的公共部分和私钥放在 Defender 代理目录的文件 `certificate_public.pem` 和 `certificate_private.pem` 中。 

1. 将相应的连接字符串放到 `connection_string.txt` 文件中。 连接字符串应如下所示： 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    此字符串提醒 Defender 需要提供证书进行身份验证。 

1. 使用以下命令重启服务：  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>验证你的安装

若要验证你的安装：

1. 使用以下命令确保微代理正在正常运行：  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. 通过确认服务状态为 `active` 来确保服务稳定，并确保进程运行时间很合适

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="检查确保服务稳定且处于活动状态。":::
 
## <a name="testing-the-system-end-to-end"></a>端到端测试系统 

可在设备上创建触发器文件来端到端测试系统。 触发器文件将在代理中引发基线扫描，检测文件是否违反基线。 

使用以下命令在文件系统上创建一个文件：

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
中心内将显示一条基线验证失败建议，其中 `CceId` 为 CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0： 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="中心内显示的基线验证失败建议。" lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

建议最多可在中心显示一小时的时间。 

## <a name="micro-agent-versioning"></a>微代理版本控制 

若要安装特定版本的 Defender IoT 微代理，请运行以下命令： 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>后续步骤

[通过源代码构建 Defender 微代理](quickstart-building-the-defender-micro-agent-from-source.md)