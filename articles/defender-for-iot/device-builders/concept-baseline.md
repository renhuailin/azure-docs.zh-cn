---
title: 基线和自定义检查
description: 了解 Azure Defender for IoT 基线的概念。
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014554"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender for IoT 基线和自定义检查

本文介绍 Defender for IoT 基线，并汇总基线自定义检查的所有相关属性。

## <a name="baseline"></a>Baseline

基线会为每个设备建立标准行为，并使建立异常行为或偏离预期规范的行为变得更加简单。

## <a name="baseline-custom-checks"></a>基线自定义检查

基线自定义检查使用设备的模块标识孪生建立每个设备基线的自定义检查列表。

## <a name="setting-baseline-properties"></a>设置基线属性

1. 在 IoT 中心找到并选择要更改的设备。

1. 单击设备，然后单击 azureiotsecurity 模块。

1. 单击“模块标识孪生”。

1. 将“基线自定义检查”文件上传到设备。

1. 将基线属性添加到 Defender-IoT-micro-agent，并单击“保存”。

### <a name="baseline-custom-check-file-example"></a>基线自定义检查文件示例

配置基线自定义检查：

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>基线自定义检查属性

| 名称| 状态 | 有效值| 默认值| 说明 |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|必需：true |有效值：**布尔值** |默认值：False |发送高优先级消息前的最大时间间隔。|
|baselineCustomChecksFilePath |必需：true|有效值：字符串、NULL  |默认值：NULL |基线 xml 配置的完整路径|
|baselineCustomChecksFileHash |必需：true|有效值：字符串、NULL  |默认值：NULL |xml 配置文件的 `sha256sum`。 请使用 [sha256sum 引用](https://linux.die.net/man/1/sha256sum)获取其他信息。 |

若要查看其他基线示例，请参阅[自定义基线示例 -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) 和[自定义基线示例 -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)。

## <a name="next-steps"></a>后续步骤

- 访问[原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并浏览[安全建议](concept-recommendations.md)
- 了解并浏览[安全警报](concept-security-alerts.md)
