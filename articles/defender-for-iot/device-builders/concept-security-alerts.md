---
title: 内置和自定义警报列表
description: 了解使用 Defender for IoT 中心的功能和服务的安全警报及建议的修正措施。
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014536"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender for IoT 中心安全警报

Defender for IoT 使用高级分析和威胁智能来持续分析 IoT 解决方案，在存在恶意活动时发出警报。
此外，你可以根据对预期设备行为的了解来创建自定义警报。
该警报用作潜在危害指标，应进行调查和修正。

本文提供了可在 IoT 中心上触发的内置警报的列表。
除内置警报外，借助 Defender for IoT 还可以根据预期 IoT 中心和/或设备行为定义自定义警报。
有关详细信息，请参阅[可自定义的警报](concept-customizable-security-alerts.md)。

## <a name="built-in-alerts-for-iot-hub"></a>IoT 中心的内置警报

| 严重性 | 名称 | 说明 | 建议的补救措施 |
|--|--|--|--|
| 中等严重性 |  |  |  |
| 将新证书添加到 IoT 中心 | 中 | 名为 \'%{DescCertificateName}\' 的证书已添加到 IoT 中心 \'%{DescIoTHubName}\'。 如果此操作是由未授权方执行的，则可能表明存在恶意活动。 | 1.请确保证书由授权方添加。 <br> 2.如果该证书不是由授权方添加的，请删除该证书并将警报上报给组织安全团队。 |
| 从 IoT 中心删除证书 | 中 | 名为 \'%{DescCertificateName}\' 的证书已从 IoT 中心 \'%{DescIoTHubName}\' 中删除。 如果此操作是由未授权方执行的，则可能表明存在恶意活动。 | 1.请确保证书已由授权方删除。 <br> 2.如果该证书不是由授权方删除的，请添加回该证书并将警报上报给组织安全团队。 |
| 检测到将证书添加到 IoT 中心的尝试失败 | 中 | 尝试将证书 \'%{DescCertificateName}\' 添加到 IoT 中心 \'%{DescIoTHubName}\' 失败。 如果此操作是由未授权方执行的，则可能表明存在恶意活动。 | 请确保仅向授权方授予更改证书的权限。 |
| 检测到从 IoT 中心删除证书的尝试失败 | 中 | 尝试从 IoT 中心 \'%{DescIoTHubName}\' 删除证书 \'%{DescCertificateName}\' 失败。 如果此操作是由未授权方执行的，则可能表明存在恶意活动。 | 请确保仅向授权方授予更改证书的权限。 |
| x.509 设备证书指纹不匹配 | 中 | x.509 设备证书指纹与配置不匹配。 | 查看设备上的警报。 无需进一步操作。 |
| x.509 证书已过期 | 中 | X.509 设备证书已过期。 | 这可能是证书过期的合法设备，也可能是一次模拟合法设备的尝试。 如果合法设备当前通信正确，这可能是一次模拟尝试。 |
| 低严重性 |  |  |  |
| 检测到尝试添加或编辑 IoT 中心的诊断设置 | 低 | 检测到尝试添加或编辑 IoT 中心的诊断设置。 通过诊断设置，可以在发生安全事件或网络遭到入侵时重新创建活动线索，以用于调查目的。 如果此操作不是由授权方执行的，则可能表明存在恶意活动。 | 1.请确保证书已由授权方删除。<br> 2.如果该证书不是由授权方删除的，请添加回该证书并将警报上报给信息安全团队。 |
| 检测到尝试从 IoT 中心删除诊断设置 | 低 | %{DescAttemptStatusMessage}\' 尝试添加或编辑 IoT 中心 \'%{DescIoTHubName}\' 的诊断设置 \'%{DescDiagnosticSettingName}\'。 通过诊断设置，可以在发生安全事件或网络遭到入侵时重新创建活动线索，以用于调查目的。 如果此操作不是由授权方执行的，则可能表明存在恶意活动。 | 请确保仅向授权方授予更改诊断设置的权限。 |
| SAS 令牌已过期 | 低 | 设备使用的 SAS 令牌已过期 | 可能是令牌过期的合法设备，也可能是一次模拟合法设备的尝试。 如果合法设备当前通信正确，这可能是一次模拟尝试。 |
| SAS 令牌签名无效 | 低 | 设备使用的 SAS 令牌具有无效的签名。 签名与主密钥或辅助密钥不匹配。 | 查看设备上的警报。 无需进一步操作。 |

## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
