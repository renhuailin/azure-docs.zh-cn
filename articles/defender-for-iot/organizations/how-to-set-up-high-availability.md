---
title: 设置高可用性
description: 通过安装本地管理控制台高可用性设备，提高你的 Defender for IoT 部署的复原能力。 高可用性部署确保你的托管传感器持续向活动的本地管理控制台进行报告。
ms.date: 07/11/2021
ms.topic: how-to
ms.openlocfilehash: d5ea894188c4801f9ca29379347bb0a737a8ed8f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471764"
---
# <a name="about-high-availability"></a>关于高可用性

通过安装本地管理控制台高可用性设备，提高你的 Defender for IoT 部署的复原能力。 高可用性部署确保你的托管传感器持续向活动的本地管理控制台进行报告。

此部署的实现需要一个本地管理控制台对，其中包括一个主要设备和一个辅助设备。

## <a name="about-primary-and-secondary-communication"></a>关于主要通信和辅助通信

当主要和辅助本地管理控制台成对出现时：

- 将应用一个本地管理控制台 SLL 证书，以在主要设备与辅助设备之间创建安全连接。 SLL 可能是默认安装的自签名证书，也可能是客户安装的证书。

    验证为 `ON` 时，设备应该能够与证书定义的 CRL 服务器建立连接。

- 主要本地管理控制台数据会自动备份到辅助本地管理控制台，每 10 分钟一次。 本地管理控制台配置和设备数据会进行备份。 PCAP 文件和日志不包含在备份中。 你可以手动备份和还原 PCAP 和日志。

- 管理控制台主要设备上的设置（例如系统设置）将复制到辅助设备。 如果主要设备上更新了这些设置，则辅助设备上也会更新这些设置。

- 在辅助设备的许可证过期之前，你应将辅助设备定义为主要设备，以便更新许可证。

## <a name="about-failover-and-failback"></a>关于故障转移和故障回复

如果传感器无法连接到主要本地管理控制台，则它会自动连接到辅助本地管理控制台。 如果不到一半的传感器与辅助本地管理控制台进行通信，则主要和辅助本地管理控制台将同时为你的系统提供支持。 当超过一半的传感器与辅助本地管理控制台进行通信时，辅助本地管理控制台将进行接管。 从主要本地管理控制台故障转移到辅助本地管理控制台大约需要三分钟时间。 发生故障转移时，主要本地管理控制台会冻结。 发生这种情况时，可以使用相同的登录凭据登录到辅助本地管理控制台。

在故障转移期间，传感器会继续尝试与主要设备进行通信。 当超过一半的托管传感器成功与主要本地管理控制台进行通信时，将还原主要本地管理控制台。 主要本地管理控制台被还原时，会在辅助控制台上显示以下消息。

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="主要本地管理控制台被还原时在辅助控制台上显示的消息的屏幕截图。":::

重定向后，重新登录到主要设备。

## <a name="high-availability-setup-overview"></a>高可用性设置概述

安装和配置过程在四个主要阶段执行：

1. 安装本地管理控制台主要设备。 

1. 配置本地管理控制台主要设备。 例如，计划的备份设置、VLAN 设置。 有关详细信息，请参阅本地管理控制台用户指南。 配对后，会自动将所有设置应用于辅助设备。

1. 安装本地管理控制台辅助设备。 有关详细信息，请参阅[关于 Defender for IoT 安装](how-to-install-software.md)。

1. 将主要和辅助本地管理控制台设备配对，如[此文](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)所述。 主要本地管理控制台必须至少管理两个传感器才能执行设置。

## <a name="high-availability-requirements"></a>高可用性要求

验证是否满足以下高可用性要求：

- 证书要求

- 软件和硬件要求

- 网络访问要求

### <a name="software-and-hardware-requirements"></a>软件和硬件要求

- 主要和辅助本地管理控制台设备必须运行相同的硬件型号和软件版本。

- 只有 Defender for IoT 用户能够使用 CLI 工具设置高可用性系统。

### <a name="network-access-requirements"></a>网络访问要求

请验证组织安全策略是否允许你访问主要和辅助本地管理控制台上的以下服务。 这些服务还允许在传感器与辅助本地管理控制台之间建立连接：

|端口|服务|说明|
|----|-------|-----------|
|443 或 TCP|HTTPS|授予对本地管理控制台（Web 控制台）的访问权限。|
|22 或 TCP|SSH|在主要与辅助本地管理控制台设备之间同步数据|
|123 或 UDP|NTP| 本地管理控制台的 NTP 时间同步。请验证是否为主动和被动设备定义了相同的时区。|

## <a name="create-the-primary-and-secondary-pair"></a>创建主要和辅助设备对

在开始此过程之前，请验证主要和辅助本地管理控制台设备是否都已启动。

### <a name="on-the-primary"></a>在主要设备上

1. 登录到管理控制台。

1. 选择侧边菜单中的“系统设置”。

1. 复制“连接字符串”。

    :::image type="content" source="../media/how-to-set-up-high-availability/connection-string.png" alt-text="复制连接字符串以用于以下命令。":::

1. 在主要设备上运行以下命令：

    ```bash
    sudo cyberx-management-trusted-hosts-add -ip <Secondary IP> -token <connection string>
    ```

    >[!NOTE]
    > 在本文档中，主体本地管理控制台称为主要设备，代理称为辅助设备。

1. 在 ```<Secondary ip>``` 字段中输入辅助设备的 IP 地址，并选择 Enter。 然后验证 IP 地址，并将 SSL 证书下载到主要设备。 输入 IP 地址还会将传感器关联到辅助设备。

1. 在主要设备上运行以下命令，以验证是否正确安装了证书：

    ```bash
    sudo cyberx-management-trusted-hosts-apply
    ```

1. 在主要设备上运行以下命令。 请勿通过 sudo 运行。

    ```bash
    cyberx-management-deploy-ssh-key <Secondary IP>
    ```

   这样就可以在主要与辅助设备之间建立连接，以便在它们之间进行备份和还原。

1. 输入辅助设备的 IP 地址，并选择 Enter。

### <a name="on-the-secondary"></a>在辅助设备上

1. 以 Defender for IoT 用户身份登录到 CLI。

1. 在辅助设备上运行以下命令。 请勿通过 sudo 运行：

    ```bash
    cyberx-management-deploy-ssh-key <Primary ip>
    ```

    这样就可以在主要与辅助设备之间建立连接，以便在它们之间进行备份和还原。

1. 输入主要设备的 IP 地址，并按 Enter。

### <a name="track-high-availability-activity"></a>跟踪高可用性活动

可以导出核心应用程序日志并将其发送到 Defender for IoT 支持团队，以处理任何高可用性问题。  

访问核心日志：

1. 从“系统设置”窗口中选择“导出”。

## <a name="update-the-on-premises-management-console-with-high-availability"></a>更新本地管理控制台的高可用性

按照以下顺序执行高可用性更新。 请确保在完成一个步骤后再开始一个新步骤。

更新高可用性：

1. 更新主要本地管理控制台。

1. 更新辅助本地管理控制台。

1. 更新传感器。

## <a name="see-also"></a>另请参阅

[激活和设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)
