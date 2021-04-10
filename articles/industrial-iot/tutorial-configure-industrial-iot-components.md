---
title: 配置 Azure 工业 IoT 组件
description: 在本教程中，将了解如何更改配置的默认值。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787196"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>教程：配置 Azure 工业 IoT 组件

部署脚本会自动将所有组件配置为使用默认值进行处理。 但是，可以更改默认值的设置以满足您的要求。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 自定义组件的配置


下面是组件的一些更相关的自定义设置：
* IoT 中心
    * 网络→公共访问：配置 Internet 访问权限，例如 IP 筛选器
    * 网络→专用终结点连接：创建无法通过 Internet 访问，但可由其他 Azure 服务或本地设备在内部使用的终结点（例如，通过 VPN 连接）
    * IoT Edge：管理连接到 OPC UA 服务器的边缘设备的配置 
* Cosmos DB
    * 全局复制数据：配置数据冗余
    * 防火墙和虚拟网络：配置 Internet 和 VNET 访问权限以及 IP 筛选器
    * 专用终结点连接：创建无法通过 Internet 访问的终结点 
* Key Vault
    * 机密：管理平台设置
    * 访问策略：管理哪些应用程序和用户可以访问 Key Vault 中的数据，以及哪些操作（例如，读取、写入、列出、删除）允许在网络、防火墙、VNET 和专用终结点上执行
* Azure Active Directory (AAD)→应用注册
    * <APP_NAME>-web →身份验证：管理回复 URI，这是在身份验证成功后可用作登陆页面的 URI 列表。 在某些情况下，部署脚本可能无法自动配置此设置，例如缺乏 AAD 管理员权限。 在更改 Web 应用程序的主机名时，可能需要添加或修改 URI，例如，localhost 用于调试的端口号
* 应用服务
    * 配置：管理控制服务或 UI 的环境变量
* 虚拟机
    * 网络：配置支持的网络和防火墙规则
    * 串行控制台： SSH 访问以获取见解或进行调试，从部署脚本的输出获取凭据或重置密码
* IoT 中心→ IoT Edge
    * 管理可以访问中心的 IoT Edge 设备的标识，配置要安装的模块及其使用的配置，例如，为 OPC 发布服务器编码参数
* IoT 中心 → IoT Edge → \<DEVICE> → 设置模块 → OpcPublisher（仅限独立 OPC 发布服务器操作）


## <a name="configuration-options"></a>配置选项

|配置选项（速记/全名）    |    说明   |
|----------------------------------------------|------------------|
pf/publishfile |用于配置要发布的节点的文件名。 如果指定此选项，则会将 OPC 发布服务器置于独立模式。
lf/logfile |要使用的日志文件的文件名。
ll/loglevel |要使用的日志级别（允许：严重、错误、警告、信息、调试、详细）。
me/messageencoding |传出消息允许的值的消息传递编码：Json、Uadp
mm/messagingmode |传出消息允许的值的消息传递模式：PubSub、Samples
fm/fullfeaturedmessage |消息的全功能模式（填写所有字段）。 默认值为“true”，要实现旧的兼容性，请使用“false”
aa/autoaccept |发布服务器信任其连接到的所有服务器
bs/batchsize |要进行批处理缓存的 OPC UA 数据更改消息数。
si/iothubsendinterval |触发器批处理间隔（秒）。
ms/iothubmessagesize |(IoT D2C) 消息的最大大小。
om/maxoutgressmessages |(IoT D2C) 消息流出量缓冲区的最大大小。
di/diagnosticsinterval |按指定的时间间隔（以秒为单位）显示发布服务器诊断信息（需要日志级别信息）。 -1 禁用远程诊断日志和诊断输出
lt/logflugtimespan |应刷新日志文件的时间跨度（以秒为单位）。
ih/iothubprotocol |用于与中心通信的协议。 允许的值：AmqpOverTcp、AmqpOverWebsocket、MqttOverTcp、MqttOverWebsocket、Amqp、Mqtt、Tcp、Websocket、Any
hb/heartbeatinterval |发布服务器使用此值作为不带检测信号间隔设置的节点的检测信号间隔设置的默认值（秒）。
ot/operationtimeout |发布服务器 OPC UA 客户端的操作超时（毫秒）。
ol/opcmaxstringlen |字符串 opc 可传输/接收的最大长度。
oi/opcsamplinginterval |请求服务器到示例值的默认值（以毫秒为单位）
op/opcpublishinginterval |针对 OPC UA 服务器的订阅的发布间隔设置的默认值（以毫秒为单位）。
ct/createsessiontimeout |发布服务器在其连接的终结点上将保持连接消息发送到 OPC 服务器的时间间隔（以秒为单位）。
kt/keepalivethresholt |指定服务器在断开会话连接之前可能会错过的保持连接数据包的数量。
tm/trustmyself |发布服务器证书自动放入受信任的存储中。
at/appcertstoretype |自己的应用程序证书存储类型（允许：Directory、X509Store）。


## <a name="next-steps"></a>后续步骤
现在，您已了解如何更改配置的默认值，您可以 

> [!div class="nextstepaction"]
> [将 IIoT 数据拉取到 ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [使用时序见解可视化和分析数据](tutorial-visualize-data-time-series-insights.md)
