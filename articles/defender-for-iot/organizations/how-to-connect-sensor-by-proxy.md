---
title: 将传感器与代理连接
description: 了解如何配置 Azure Defender for IoT 以在无直接 Internet 访问的情况下通过代理与传感器通信。
ms.topic: how-to
ms.date: 07/04/2021
ms.openlocfilehash: f16ec5c45d78237e256dcd5936ac0612d175022b
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297879"
---
# <a name="connect-azure-defender-for-iot-sensors-without-direct-internet-access-by-using-a-proxy"></a>在无直接 Internet 访问的情况下通过使用代理连接 Azure Defender for IoT 传感器 

本文介绍如何配置 Azure Defender for IoT 以在无直接 Internet 访问的情况下通过代理与传感器通信。 使用具有 HTTP 隧道的转发代理连接传感器，并使用 HTTP CONNECT 命令建立连接。 此处提供的说明使用的是开源 Squid 代理，可以使用支持 CONNECT 的任何其他代理。 

代理使用加密 SSL 隧道将数据从传感器传输到服务。 代理不会检查、分析或缓存任何数据。 

以下示意图显示了数据从 OT 段的 Azure Defender to IoT 传感器传输到云，然后经由 IT 网络中的代理传输到工业外围网络。

:::image type="content" source="media/how-to-connect-sensor-by-proxy/cloud-access.png" alt-text="通过云将传感器连接到代理。":::

## <a name="set-up-your-system"></a>设置系统

在本方案中，我们将在 Ubuntu 18 服务器上安装并配置最新版本的 [Squid](http://www.squid-cache.org/)。

> [!Note]
> Azure Defender for IoT 不支持 Squid 或任何其他代理服务。

**若要在 Ubuntu 18 服务器上安装 Squid 代理**：

1. 登录到指定的代理 Ubuntu 计算机。

1. 启动终端窗口。
 
1. 使用以下命令将软件更新到最新版本。

    ```bash
    sudo apt-get update 
    ```

1. 使用以下命令安装 Squid 包。

    ```bash
    sudo apt-get install squid 
    ```

1. 找到位于 `/etc/squid/squid.conf` 和 `/etc/squid/conf.d/` 的 Squid 配置文件。

1. 使用以下命令备份原始文件。

    ```bash
    sudo cp -v /etc/squid/squid.conf{,.factory}'/etc/squid/squid.conf' -> '/etc/squid/squid.conf.factory sudo nano /etc/squid/squid.conf
    ```

1. 在文本编辑器中打开 `/etc/squid/squid.conf`。

1. 搜索 `# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS`。

1. 将 `acl sensor1 src <sensor-ip>` 和 `http_access allow sensor1` 添加到文件中。

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/add-lines.png" alt-text="将以下两行代码添加到文本中，并保存该文件。":::

1. （可选）添加更多传感器，方法是为每个传感器添加额外的行。

1. 使用以下命令在启动时启用 Squid 服务。

    ```bash
    sudo systemctl enable squid 
    ```

## <a name="set-up-a-sensor-to-use-squid"></a>将传感器设置为使用 Squid

**将传感器设置为使用 Squid**：

1. 登录传感器。

1. 导航到“系统设置” > “网络”。

1. 选择“启用代理”。

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/enable-proxy.png" alt-text="从“传感器网络配置”窗口中选择“启用代理”。":::

1. 输入代理地址。

1. 输入端口。 默认端口为 `3128`。

1. （可选）输入代理用户和密码。

1. 选择“保存”。

## <a name="see-also"></a>另请参阅

[管理订阅](how-to-manage-subscriptions.md)。
