---
title: 设置高可用性
description: 通过安装本地管理控制台高可用性设备，提高 IoT 用于 IoT 部署的复原能力。 高可用性部署确保托管传感器持续向活动的本地管理控制台报告。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 6540b5f93bebfe39253a88dc495a3613568f8926
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838141"
---
# <a name="about-high-availability"></a>关于高可用性

通过安装本地管理控制台高可用性设备，提高 IoT 用于 IoT 部署的复原能力。 高可用性部署确保托管传感器持续向活动的本地管理控制台报告。

此部署是使用本地管理控制台对（包含主要和辅助设备）实现的。

## <a name="about-primary-and-secondary-communication"></a>关于主要和次要通信

当主要和辅助本地管理控制台成对出现时：

- 应用本地管理控制台 SLL 证书，以在主要和辅助设备之间创建安全连接。 SLL 可能是默认安装的自签名证书或客户安装的证书。

- 主要本地管理控制台数据每10分钟自动备份到辅助本地管理控制台。 备份本地管理控制台配置和设备数据。 PCAP 文件和日志不包含在备份中。 你可以手动备份和还原 PCAPs 和日志。

- 管理控制台上的主要设置在辅助副本上重复;例如，"系统设置"。 如果在主副本上更新这些设置，则它们也会在辅助数据库上更新。

- 在辅助副本的许可证过期之前，你应将其定义为主，以便更新许可证。

## <a name="about-failover-and-failback"></a>关于故障转移和故障回复

如果传感器无法连接到主本地管理控制台，它会自动连接到辅助数据库。 如果两个以上的传感器都与辅助数据库通信，则主节点和辅助副本将同时支持您的系统。 当超过一半的传感器与其通信时，辅助副本将接管。 从主数据库故障转移到辅助数据库大约需要3分钟。 发生故障转移时，主本地管理控制台将会冻结。 发生这种情况时，可以使用相同的登录凭据登录到辅助数据库。

在故障转移期间，传感器将继续尝试与主要设备进行通信。 当超过一半的托管传感器成功与主传感器通信时，将还原主传感器。 恢复主控制台时，会在辅助控制台上显示以下消息。

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="恢复主控制台时在辅助控制台上显示的消息的屏幕截图。":::

重定向后，重新登录到主要设备。

## <a name="high-availability-setup-overview"></a>高可用性设置概述

安装和配置过程分为四个主要阶段：

1. 安装本地管理控制台主要设备。 

2. 配置本地管理控制台主要设备。 例如，计划的备份设置、VLAN 设置。 有关详细信息，请参阅本地管理控制台用户指南。 配对后，会自动将所有设置应用到辅助设备。

3. 安装本地管理控制台辅助设备。 有关详细信息，请参阅 [关于用于 IoT 安装的 Defender](how-to-install-software.md)。

4. 如 [此处](/create-the-primary-and-secondary-pair.md)所述，将主要和辅助本地管理控制台设备配对。 主要本地管理控制台必须至少管理两个传感器，才能执行安装程序。

## <a name="high-availability-requirements"></a>高可用性要求

验证是否满足以下高可用性要求：

- 证书要求

- 软件和硬件要求

- 网络访问要求

### <a name="software-and-hardware-requirements"></a>软件和硬件要求

- 主要和辅助本地管理控制台设备都必须运行相同的硬件型号和软件版本。

- 使用 CLI 工具，只能通过 Defender 为 IoT 用户设置高可用性系统。

### <a name="network-access-requirements"></a>网络访问要求

你需要验证你的组织安全策略是否允许你访问主要和辅助本地管理控制台上的以下服务。 这些服务还允许传感器和辅助本地管理控制台之间的连接：

|端口|服务|说明|
|----|-------|-----------|
|**443或 TCP**|HTTPS|授予对本地管理控制台 web 控制台的访问权限。|
|**22或 TCP**|SSH|在主要和辅助本地管理控制台设备之间同步数据|
|**123或 UDP**|NTP| 本地管理控制台的 NTP 时间同步。验证主动和被动设备是否已定义为具有相同的时区。|

## <a name="create-the-primary-and-secondary-pair"></a>创建主对和辅助对

在开始此过程之前，请验证主要和辅助本地管理控制台设备是否已启动。  

### <a name="on-the-primary"></a>在主

1. 以 IoT 用户身份登录到 CLI。

2. 在主副本上运行以下命令：

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>在本文档中，主要用户管理控制台称为 "主"，代理称为 "辅助"。

3. 在字段中输入辅助设备的 IP 地址 ```<Secondary ip>``` ，然后选择 "enter"。 然后验证 IP 地址，并将 SSL 证书下载到主证书。 输入 IP 地址也会将传感器关联到辅助设备。

4. 在主副本上运行以下命令，以验证是否正确安装了证书：

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. 在主副本上运行以下命令。 **请勿在 sudo 中运行。**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

这允许在主要和辅助设备之间建立连接，以便在它们之间进行备份和还原。

6. 输入辅助数据库的 IP 地址，然后选择 "Enter"。

### <a name="on-the-secondary"></a>在辅助数据库上

1. 以 IoT 用户身份登录到 CLI。

2. 在辅助副本上运行以下命令。 **不运行 sudo**：

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

这允许在主要和辅助设备之间建立连接，以便在它们之间进行备份和还原。

3. 输入主副本的 IP 地址，然后按 Enter。

### <a name="track-high-availability-activity"></a>跟踪高可用性活动

核心应用程序日志可以导出到用于 IoT 的 Defender 支持团队，以处理任何高可用性问题。  

访问核心日志：

1. 从 "**系统设置**" 窗口中选择 "**导出**"。

## <a name="update-the-on-premises-management-console-with-high-availability"></a>更新具有高可用性的本地管理控制台

按照以下顺序执行高可用性更新。 在开始新步骤之前，请确保每个步骤均已完成。

若要更新高可用性，请执行以下操作：

1. 更新主本地管理控制台。

2. 更新辅助本地管理控制台。

3. 更新传感器。

## <a name="see-also"></a>另请参阅

[激活并设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)