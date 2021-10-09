---
title: 将 Qradar 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Qradar 与 Azure Defender for IoT 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: ec55a652b59f7d45e01ef22e62bd046473ef8559
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839049"
---
# <a name="tutorial-integrate-qradar-with-azure-defender-for-iot"></a>教程：将 Qradar 与 Azure Defender for IoT 集成

本教程帮助你了解如何将 Qradar 与 Azure Defender for IoT 集成，以及如何使用 Qradar。

Defender for IoT 提供唯一具有获得专利的 ICS 感知威胁分析和机器学习的 ICS 和 IoT 网络安全平台。

Defender for IoT 已将其持续的 ICS 威胁监视平台与 IBM QRadar 集成。

集成的部分好处如下：

- 可将 Azure Defender for IoT 警报转发到 IBM QRadar，以实现统一的 IT 和 OT 安全监视以及治理。

- 能够大致了解 IT 和 OT 环境。 允许你检测和响应经常跨越 IT 和 OT 边界的多阶段攻击。

- 与现有 SOC 工作流集成。

在本教程中，你将了解：

> [!div class="checklist"]
> * 为 QRadar 配置 Syslog 侦听器
> * 部署 Defender for IoT 平台 QID
> * 设置 QRadar 转发规则
> * 在管理控制台中将通知映射到 QRadar
> * 向警报添加自定义字段

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

本教程没有任何必备条件。

## <a name="configure-syslog-listener-for-qradar"></a>为 QRadar 配置 Syslog 侦听器

若要配置 Syslog 侦听器以使用 QRadar，请执行以下操作：

1. 登录 QRadar。

1. 从左侧窗格中，选择“管理” > “数据源” 。

1. 在“数据源”窗口中，选择“日志源”。

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="从可用选项中选择日志源的屏幕截图。":::](media/tutorial-qradar/log.png#lightbox)

1. 在“模态”窗口中，选择“添加” 。

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="选择“Syslog”后模态窗口随即打开的屏幕截图。":::](media/tutorial-qradar/modal.png#lightbox)

1. 在“添加日志源”对话框中，设置以下参数：

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="通过填写相应字段来添加日志源的屏幕截图。":::

   - **日志源名称**：`<Sensor name>`

   - **日志源说明**：`<Sensor name>`

   - **日志源类型**：`Universal LEEF`

   - **协议配置**：`Syslog`

   - **日志源标识符**：`<Sensor name>`

   > [!NOTE]
   > 日志源标识符名称不得包含空格。 建议使用下划线替换每个空格字符。

1. 选择“保存”。

1. 选择“部署更改”。

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="“部署更改”视图的屏幕截图":::

## <a name="deploy-defender-for-iot-platform-qid"></a>部署 Defender for IoT 平台 QID

QID 是 QRadar 中的事件标识符。 所有 Defenders for IoT 平台报表均标记在同一事件（传感器警报）下。

若要部署 Defender for IoT 平台 QID，请执行以下操作：

1. 登录到 QRadar 控制台。

1. 创建名为 `xsense_qids` 的文件。

1. 在文件中，使用以下命令：`,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`。

1. 执行：`sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`。 此时将显示已成功部署 QID 的消息。

## <a name="setup-qradar-forwarding-rules"></a>设置 QRadar 转发规则

若要使集成正常工作，需要在 Defender for IoT 设备中设置 Qradar 转发规则。

**在 Defender for IoT 设备中定义 QRadar 通知**：

1. 在侧边栏菜单中，选择“转发”。

1. 选择“创建转发规则”。

1. 将“操作”设置为“QRadar”。

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="“创建转发规则”窗口的屏幕截图。":::

1. 配置 QRadar IP 地址和时区。

1. 选择“提交”。

## <a name="map-notifications-to-qradar"></a>将通知映射到 QRadar

然后必须在本地管理控制台上映射规则。

若要将通知映射到 QRadar，请执行以下操作：

1. 登录到管理控制台。

1. 从左侧窗格中选择“转发”。

1. 在 Qradar GUI 中的 QRadar 下，选择“日志活动”。

1. 选择“添加筛选器”并设置以下参数：
   - 参数：`Log Sources [Indexed]`
   - 运算符：`Equals`
   - 日志源组：`Other`
   - 日志源：`<Xsense Name>`

1. 双击来自传感器的未知报表。

1. 选择“映射事件”。

1. 在“模态日志源事件”页面，按如下所示进行选择：
   - “高级类别”-“可疑活动 + 低级类别”-“未知可疑事件 + 日志”
   - “源类型”-“任何”

1. 选择“搜索”。

1. 从结果中，选择显示有名称 XSense 的行，然后选择“确定”。

从现在开始，所有传感器报表均标记为传感器警报。

## <a name="add-custom-fields-to-the-alerts"></a>向警报添加自定义字段

**向警报添加自定义字段**：

1. 选择“提取属性”。

1. 选择“基于正则表达式”。

1. 配置以下字段：
   - 新建属性：_从下表中选择_
      - 传感器警报说明
      - 传感器警报 ID
      - 传感器警报分数
      - 传感器警报标题
      - 传感器目标名称
      - 传感器直接重定向
      - 传感器发送方 IP
      - 传感器发送方名称
      - 传感器警报引擎
      - 传感器源设备名称
   - 选中“优化分析”
   - 字段类型：`AlphaNumeric`
   - 选中“已启用”
   - 日志源类型：`Universal LEAF`
   - 日志源：`<Sensor Name>`
   - 事件名称（应已设置为传感器警报）
   - 捕获组：1
   - 正则表达式：
      - 传感器警报说明正则表达式：`msg=(.*)(?=\t)`
      - 传感器警报 ID 正则表达式：`alertId=(.*)(?=\t)`
      - 传感器警报分数正则表达式：`Detected score=(.*)(?=\t)`
      - 传感器警报标题正则表达式：`title=(.*)(?=\t)`
      - 传感器目标名称正则表达式：`dstName=(.*)(?=\t)`
      - 传感器直接重定向正则表达式：`rta=(.*)(?=\t)`
      - 传感器发送方 IP 正则表达式：`reporter=(.*)(?=\t)`
      - 传感器发送方名称正则表达式：`senderName=(.*)(?=\t)`
      - 传感器警报引擎正则表达式：`engine =(.*)(?=\t)`
      - 传感器源设备名称正则表达式：`src`

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 QRadar 集成。 继续了解如何[将 ServiceNow 与 Azure Defender for IoT 集成](tutorial-servicenow.md)。

> [!div class="nextstepaction"]
> [将 ServiceNow 与 Azure Defender for IoT 集成](tutorial-servicenow.md)
