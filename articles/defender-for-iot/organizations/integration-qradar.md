---
title: QRadar 集成
description: 配置 Defender for IoT 解决方案与 QRadar 的集成。
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341413"
---
# <a name="about-the-qradar-integration"></a>关于 QRadar 集成

Defender for IoT 提供了由蓝队专家生成的唯一 ICS 和 IoT 网络安全平台，此平台在保护关键国家基础结构方面过往表现不俗，也是唯一已获专利的 ICS 感知威胁分析和机器学习的平台。

Defender for IoT 已将其持续的 ICS 威胁监视平台与 IBM QRadar 集成。 

集成的部分好处如下：

- 可将 Azure Defender for IoT 警报转发到 IBM QRadar，以实现统一的 IT 和 OT 安全监视以及治理。

- 可获取 IT 和 OT 环境的鸟瞰视图。 这使你可以检测和响应经常跨越 IT 和 OT 边界的多阶段攻击。

- 与现有 SOC 工作流集成。

## <a name="configuring-syslog-listener-for-qradar"></a>为 QRadar 配置 Syslog 侦听器

配置 Syslog 侦听器以使用 QRadar：

1. 登录 QRadar。

1. 从左侧窗格中，选择“管理” > “数据源” 。

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="从可用选项中选择日志源。":::](media/integration-qradar/log.png#lightbox)

1. 在“数据源”窗口中，选择“日志源”。

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="选择“Syslog”后，模态窗口随即打开。":::](media/integration-qradar/modal.png#lightbox)

1. 在“模态”窗口中，选择“添加” 。

   :::image type="content" source="media/integration-qradar/source.png" alt-text="通过填写相应的字段来添加日志源。":::

1. 在“添加日志源”对话框中，设置以下参数：

   - **日志源名称**：`<XSense Name>`
   
   - **日志源说明**：`<XSense Name>`
   
   - **日志源类型**：`Universal LEEF`

   - **协议配置**：`Syslog`
   
   - **日志源标识符**：`<XSenseName>`
   
   > [!NOTE]
   > 日志源标识符名称不得包含空格。 建议使用下划线替换每个空格字符。

1. 选择“保存”。

1. 选择“部署更改”。

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="“部署更改”视图的屏幕截图":::

## <a name="deploying-defender-for-iot-platform-qid"></a>部署 Defender for IoT 平台 QID

QID 是 QRadar 中的事件标识符。 所有 Defenders for IoT 平台报表均标记在同一事件（XSense 警报）下。

**部署 Xsense QID**：

1. 登录到 QRadar 控制台。

1. 创建名为 `xsense_qids` 的文件。

1. 在文件中，使用以下命令：`,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`。

1. 执行：`sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`。 此时将显示已成功部署 QID 的消息。

## <a name="setting-up-qradar-forwarding-rules"></a>设置 QRadar 转发规则

在 Defender for IoT 设备中，配置 Qradar 转发规则。 在本地管理控制台上映射规则。

**在 Defender for IoT 设备中定义 QRadar 通知**：

1. 在侧边栏菜单中，选择“转发”。

   :::image type="content" source="media/integration-qradar/create.png" alt-text="创建转发规则":::

1. 将“操作”设置为“QRadar”。

1. 配置 QRadar IP 地址和时区。

1. 选择“提交”。

**将通知映射到 Central Manager 中的 QRadar**：

1. 在侧边栏菜单中，选择“转发”。

1. 在 Qradar GUI 中的 QRadar 下，选择“日志活动”。

1. 选择“添加筛选器”并设置以下参数：
   - 参数：`Log Sources [Indexed]`
   - 运算符：`Equals`
   - 日志源组：`Other`
   - 日志源：`<Xsense Name>`

1. 双击来自 XSense 的未知报表。

1. 选择“映射事件”。

1. 在“模态日志源事件”页面，按如下所示进行选择：
   - “高级类别”-“可疑活动 + 低级类别”-“未知可疑事件 + 日志”
   - “源类型”-“任何”

1. 选择“搜索”。

1. 从结果中，选择显示有名称 XSense 的行，然后选择“确定”。

从现在开始，所有 XSense 报表均标记为 XSense 警报。

## <a name="adding-custom-fields-to-alerts"></a>向警报添加自定义字段

**向警报添加自定义字段**：

1. 选择“提取属性”。

1. 选择“基于正则表达式”。

1. 配置以下字段：
   - 新建属性：_从下表中选择_
      - Xsense 警报说明
      - Xsense 警报 ID
      - Xsense 警报评分
      - Xsense 警报标题
      - Xsense 目标名称
      - Xsense 直接重定向
      - Xsense 发送方 IP
      - Xsense 发送方名称
      - Xsense 警报引擎
      - Xsense 源设备名称
   - 选中“优化分析”
   - 字段类型：`AlphaNumeric`
   - 选中“已启用”
   - 日志源类型：`Universal LEAF`
   - 日志源：`<Xsense Name>`
   - 事件名称（应已设置为 XSense 警报）
   - 捕获组：1
   - 正则表达式：
      - Xsense 警报说明正则表达式：`msg=(.*)(?=\t)`
      - Xsense 警报 ID 正则表达式：`alertId=(.*)(?=\t)`
      - Xsense 警报源正则表达式：`Detected score=(.*)(?=\t)`
      - Xsense 警报标题正则表达式：`title=(.*)(?=\t)`
      - Xsense 目标名称正则表达式：`dstName=(.*)(?=\t)`
      - Xsense 直接重定向正则表达式：`rta=(.*)(?=\t)`
      - Xsense 发送方 IP 正则表达式：`reporter=(.*)(?=\t)`
      - Xsense 发送方名称正则表达式：`senderName=(.*)(?=\t)`
      - Xsense 警报引擎正则表达式：`engine =(.*)(?=\t)`
      - Xsense 源设备名称正则表达式：`src`

## <a name="defining-defender-for-iot-appliance-name"></a>定义 Defender for IoT 设备名称

随时都可更改平台的名称。

生成站点并在本地管理控制台中将设备分配到各区域时，应为每个设备分配一个有意义的名称。 例如，“Motorcycles PL Unit 2”表示此设备正在保护摩托车生产线中的单元 #2。 

务必为设备选择一个有意义的名称，因为设备的名称会传递到日志。 查看日志时，每个警报都附加有一个传感器。 你将能够根据名称确定与每个警报相关的传感器。

**更改设备名称**：

1. 在侧边栏菜单中，选择当前设备名称。 此时将显示“编辑管理控制台配置”对话框。

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="更改控制台的名称。":::

1. 在“名称”字段中输入一个名称，然后选择“保存”。

## <a name="next-steps"></a>后续步骤

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
