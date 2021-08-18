---
title: 使用 Defender for IoT CLI 命令
description: 本文介绍适用于传感器和本地管理控制台的 Defender for IoT CLI 命令。
ms.date: 05/20/2021
ms.topic: article
ms.openlocfilehash: bac3e84d77436e9dfc500baa9f729ba847c49bd4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014586"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>使用 Defender for IoT CLI 命令

本文介绍适用于传感器和本地管理控制台的 CLI 命令。 以下用户可访问这些命令：

- 管理员
- CyberX 
- 支持

若要开始在 CLI 中工作，请使用终端进行连接。 例如，终端名称 `Putty` 和 `Support` 用户。 

## <a name="create-local-alert-exclusion-rules"></a>创建本地警报排除规则

可以通过在 CLI 中输入以下命令来创建本地警报排除规则：

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

以下属性可用于警报排除规则：

| 属性 | 说明 |
|--|--|
| [-h] | 打印命令的帮助信息。 |
| -n NAME | 所创建的规则的名称。 |
| [-ts TIMES] | 规则处于活动状态的时间跨度。 这应指定为：<br />`xx:yy-xx:yy`<br />可以通过在各个时间段之间使用逗号来定义多个时间段。 例如：`xx:yy-xx:yy, xx:yy-xx:yy`。 |
| [-dir DIRECTION] | 应用规则的方向。 这应指定为：<br />`both | src | dst` |
| [-dev DEVICES] | 规则要排除的设备的 IP 地址和地址类型，指定为：<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | 规则将排除的警报的名称：<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>追加本地警报排除规则

可以通过在 CLI 中输入以下命令来追加本地警报排除规则：

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

此处使用的属性与“创建本地警报排除规则”部分中所述的属性相同。 用法区别在于，此处的属性应用于现有规则。

## <a name="show-local-alert-exclusion-rules"></a>显示本地警报排除规则

输入以下命令以显示排除规则的现有列表：

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>删除本地警报排除规则

可以通过输入以下命令来删除现有警报排除规则：

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

以下属性可用于警报排除规则：

| 属性 | 说明|
| --------- | ---------------------------------- |
| -n NAME | 要删除的规则的名称。 |

## <a name="sync-time-from-the-ntp-server"></a>从 NTP 服务器同步时间

可以启用或禁用从指定 NTP 服务器进行的时间同步。

### <a name="enable-ntp-sync"></a>启用 NTP 同步

输入以下命令以定期从指定 NTP 服务器检索时间：

```azurecli-interactive
ntp enable IP
```

可以在命令中定义的属性是 NTP 服务器的 IP 地址。

### <a name="disable-ntp-sync"></a>禁用 NTP 同步

输入以下命令以禁用与指定 NTP 服务器的时间同步：

```azurecli-interactive
ntp disable IP
```

可以在命令中定义的属性是 NTP 服务器的 IP 地址。

## <a name="network-configuration"></a>网络配置

下表说明了可用于为 Azure Defender for IoT 配置网络选项的命令：

|名称|Command|说明|
|-----------|-------|-----------|
|Ping|`ping IP`| 对 Defender for IoT 平台外部的地址执行 Ping 操作。|
|Blink|`network blink`| 通过使接口指示灯闪烁来找到连接。 |
|重新配置网络 |`network edit-settings`| 实现网络配置参数更改。 |
|显示网络设置 |`network list`|显示网络适配器参数。 |
|验证网络配置文件 |`network validate` |显示输出网络设置。 <br /> <br />例如： <br /> <br />当前网络设置： <br /> 接口：eth0 <br /> ip：10.100.100.1 <br />子网：255.255.255.0 <br />默认网关：10.100.100.254 <br />dns：10.100.100.254 <br />监视接口：eth1|
|导入证书 |`certificate import FILE` |导入 HTTPS 证书。 需要指定完整路径，该路径指向 \*.crt 文件。 |
|显示日期 |`date` |以 GMT 格式返回主机上的当前日期。 |

## <a name="network-capture-filter-configuration"></a>网络捕获筛选器配置

`network capture-filter` 命令使管理员可以消除无需分析的网络流量。 可以使用包含列表或排除列表筛选流量。

```azurecli-interactive
network capture-filter
```

输入该命令后，系统会使用以下问题进行提示：

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

选择 `Y` 可打开 nano 文件，在其中可以根据以下语法添加设备、通道、端口和子集：

| 属性 | 说明 |
|--|--|
| 1.1.1.1 | 包含此设备的所有流量。 |
| 1.1.1.1,2.2.2.2 | 包含此通道的所有流量。 |
| 1.1.1,2.2.2 | 包含此子网的所有流量。 |

通过放置行来分隔参数。

包含设备、通道或子网时，传感器会处理该参数的所有有效流量，包括通常不进行处理的端口和流量。

随后系统会询问以下问题：

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

选择 `Y` 可打开 nano 文件，在其中可以根据以下语法添加设备、通道、端口和子集：

| 属性 | 说明 |
|--|--|
| 1.1.1.1 | 排除此设备的所有流量。 |
| 1.1.1.1,2.2.2.2 | 排除此通道的所有流量，这意味着两个设备之间的所有流量。 |
| 1.1.1.1,2.2.2.2,443 | 按端口排除此通道的所有流量。 |
| 1.1.1 | 排除此子网的所有流量。 |
| 1.1.1,2.2.2 | 排除子网之间的所有流量。 |

通过放置行来分隔参数。

排除设备、通道或子网时，传感器会排除该参数的所有有效流量。

### <a name="ports"></a>端口

对所有流量包含或排除 UDP 和 TCP 端口。

>`502`：单个端口

>`502,443`：两个端口

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>组件

系统会询问以下问题：

>`In which component do you wish to apply this capture filter?`

选项包括： `all`、`dissector`、`collector`、`statistics-collector`、`rpc-parser` 或 `smb-parser`。

在大多数用例中，选择 `all`。

### <a name="custom-base-capture-filter"></a>自定义基本捕获筛选器

基本捕获筛选器是组件的基线。 例如，该筛选器会确定对组件可用的端口。

对于以下所有选项，请选择 `Y`。 设置更改后，所有筛选器都会添加到基线中。 如果进行更改，则更改会覆盖现有基线。

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

如果选择排除子网（如 1.1.1）：

- `internal` 仅排除该子网。

- `all-connected` 会排除该子网以及进出该子网的所有流量。

建议选择 `internal`。

> [!NOTE]
> 选择会用于工具中的所有筛选器，不依赖于会话。 换句话说，无法选择将 `internal` 用于某些筛选器，而选择将 `all-connected` 用于其他筛选器。

### <a name="comments"></a>注释

可以在 ```/var/cyberx/properties/cybershark.properties``` 中查看筛选器：

- statistics-collector： ```/var/cyberx/properties/net.stats.collector.properties``` 中的 `bpf_filter property`

- dissector： ```/var/cyberx/properties/cybershark.properties``` 中的 `override.capture_filter` 属性

- rpc-parser： ```/var/cyberx/properties/rpc-parser.properties``` 中的 `override.capture_filter` 属性

- smb-parser： ```/var/cyberx/properties/smb-parser.properties``` 中的 `override.capture_filter` 属性

- collector： ```/var/cyberx/properties/collector.properties``` 中的 `general.bpf_filter` 属性

可以通过为 cyberx 用户输入以下代码来还原默认配置：

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>定义客户端和服务器主机

如果 Defender for IoT 未自动检测客户端和服务器主机，则输入以下命令来设置客户端和服务器主机：

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

可以在 `directions` 命令中使用以下属性：

| 属性 | 说明 |
|--|--|
| [-h] | 打印命令的帮助信息。 |
| [--identifier IDENTIFIER] | 服务器标识符。 |
| [--port PORT] | 服务器端口。 |
| [--remove] | 从列表中删除客户端或服务器主机。 |
| [--add] | 向列表中添加客户端或服务器主机。 |
| [--tcp] | 与此主机通信时使用 TCP。 |
| [--udp] | 与此主机通信时使用 UDP。 |

## <a name="system-actions"></a>系统操作
下表说明可用于在 Defender for IoT 中执行各种系统操作的命令：

|名称|代码|说明|
|----|----|-----------|
|显示日期|`date`|以 GMT 格式返回主机上的当前日期。|
|重启主机|`system reboot`|重启主机设备。|
|关闭主机|`system shutdown`|关闭主机。|
|备份系统|`system backup`|启动即时备份（未计划备份）。|
|从备份还原系统|`system restore`|从最新备份还原。|
|列出备份文件|`system backup-list`|列出可用备份文件。|
|显示所有 Defender for IoT 平台服务的状态|`system sanity`|通过列出所有 Defender for IoT 平台服务的当前状态来检查系统的性能。|
|显示软件版本|`system version`|显示系统上当前正在运行的软件的版本。|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>将 SSL 和 TLS 证书部署到设备

在 CLI 中输入以下命令以导入 SSL 和 TLS 企业证书：

```azurecli-interactive
cyberx-xsense-certificate-import
```
若要使用该工具，需要将证书文件上传到设备。 可以通过 WinSCP 或 Wget 等工具执行此操作。 

此命令支持以下输入标志：

| 标志 | 描述 |
|--|--|
| -H | 显示命令行帮助语法。 |
| --crt | 指向证书文件的路径（.crt 扩展名）。 |
| --key | \*.key 文件。 密钥长度应至少为 2,048 位。 |
| --chain | 指向证书链文件的路径（可选）。 |
| --pass | 用于加密证书的密码（可选）。 |
| --passphrase-set | 默认值为 False（未使用） 。 <br />设置为 True 可使用上一个证书提供的先前密码（可选）。 | 

使用工具时：

- 验证证书文件在设备上是否可读。 

- 向 IT 确认包含 DNS 服务器的设备域（如证书中所示）和相应的 IP 地址。 
    
## <a name="see-also"></a>另请参阅

[Defender for IoT API 传感器和管理控制台 API](references-work-with-defender-for-iot-apis.md)
