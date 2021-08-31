---
title: 排查安全代理启动问题 (Linux)
description: 排查使用面向 Linux 的 Azure Defender for IoT 安全代理时出现的问题。
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 203a6112f2926bb714022e1fd26b282dedaa8104
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014669"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全代理故障排除指南 (Linux)

本文介绍如何解决安全代理启动过程中的潜在问题。

安装后，Azure Defender for IoT 代理会立即自动启动。 代理启动过程包括读取本地配置、连接到 Azure IoT 中心以及检索远程孪生配置。 这些步骤中的任何一个步骤失败都有可能导致安全代理失败。

本故障排除指南介绍如何：

- 验证安全代理是否正在运行
- 获取安全代理错误
- 了解并修正安全代理错误

## <a name="validate-if-the-security-agent-is-running"></a>验证安全代理是否正在运行

1. 若要验证安全代理是否正在运行，请在安装代理后等待几分钟，然后运行以下命令。
     <br>

    **C 代理**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C# 代理**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. 如果该命令返回空行，则表示安全代理未能成功启动。

## <a name="force-stop-the-security-agent"></a>强制停止安全代理

如果安全代理无法启动，请使用以下命令停止代理，然后继续参考以下错误表：

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>获取安全代理错误

1. 通过运行以下命令来检索安全代理错误：

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. get security agent error 命令将检索 Defender for IoT 代理创建的所有日志。 通过下表了解错误并采取正确的修正措施。

> [!Note]
> 错误日志按时间顺序显示。 请确保记下每个错误的时间戳，以帮助进行修正。

## <a name="restart-the-agent"></a>重新启动代理

1. 找到并修复安全代理错误之后，尝试通过运行以下命令来重启代理。

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 如果代理在启动过程中仍然失败，请重复前面的停止和检索错误过程。

## <a name="understand-security-agent-errors"></a>了解安全代理错误

大多数安全代理错误均按以下格式显示：

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 错误代码 | 错误子代码 | 错误详细信息 | 修正 C | 修正 C# |
|--|--|--|--|--|
| 本地配置 | 缺少配置 | 本地配置文件中缺少某个配置。 错误消息应说明缺少哪个键。 | 将缺少的键添加到 /var/LocalConfiguration.json 文件中。有关详细信息，请参阅 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)。 | 将缺少的键添加到 General.config 文件中。有关详细信息，请参阅 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)。 |
| 本地配置 | 无法分析配置 | 无法分析某个配置值。 错误消息应说明无法分析哪个键。 由于某个配置值不是预期的类型或者超出了范围，因此无法分析该值。 | 在 /var/LocalConfiguration.json 文件中修复该键的值，使其与 LocalConfiguration 架构匹配。有关详细信息，请参阅 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)。 | 在 General.config 文件中修复该键的值，使其与架构匹配。有关详细信息，请参阅 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)。 |
| 本地配置 | 文件格式 | 未能分析配置文件。 | 配置文件已损坏，请下载并重新安装代理。 | - |
| 远程配置 | 超时 | 代理无法在超时期限内提取 azureiotsecurity 模块孪生。 | 请确保身份验证配置正确无误，然后重试。 | 代理无法在超时期限内提取 azureiotsecurity 模块孪生。 请确保身份验证配置正确无误，然后重试。 |
| 身份验证 | 文件不存在 | 给定路径中的文件不存在。 | 确保文件存在于给定路径中，或转到 LocalConfiguration.json 文件并更改 FilePath 配置。 | 确保文件存在于给定路径中，或转到 Authentication.config 文件并更改 filePath 配置。 |
| 身份验证 | 文件权限 | 代理没有足够的权限打开文件。 | 授予 asciotagent 用户对给定路径中文件的读取权限。 | 确保文件可供访问。 |
| 身份验证 | 文件格式 | 给定文件的格式不正确。 | 确保文件采用正确格式。 支持的文件类型为 .pfx 和 .pem。 | 确保文件是有效的证书文件。 |
| 身份验证 | 未授权 | 代理无法使用给定凭据向 IoT 中心进行身份验证。 | 验证 LocalConfiguration 文件中的身份验证配置，仔细检查身份验证配置并确保所有详细信息正确无误，然后验证文件中的机密是否与经过身份验证的标识匹配。 | 验证 Authentication.config 中的身份验证配置，仔细检查身份验证配置并确保所有详细信息正确无误，然后验证文件中的机密是否与经过身份验证的标识匹配。 |
| 身份验证 | 未找到 | 找到了设备/模块。 | 验证身份验证配置 - 确保主机名正确无误，设备存在于 IoT 中心并具有 azureiotsecurity 孪生模块。 | 验证身份验证配置 - 确保主机名正确无误，设备存在于 IoT 中心并具有 azureiotsecurity 孪生模块。 |
| 身份验证 | 缺少配置 | Authentication.config 文件中缺少某个配置。 错误消息应说明缺少哪个键。 | 将缺少的键添加到 LocalConfiguration.json 文件中。 | 将缺少的键添加到 Authentication.config 文件中。有关详细信息，请参阅 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)。 |
| 身份验证 | 无法分析配置 | 无法分析某个配置值。 错误消息应说明无法分析哪个键。 由于某个配置值不是预期的类型或者超出了范围，因此无法分析该值。 | 在 LocalConfiguration.json 文件中修复该键的值。 | 在 Authentication.config 文件中修复该键的值，使其与架构匹配。有关详细信息，请参阅 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)。|

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解[适用于设备生成器的基于 Defender for IoT 代理的解决方案](architecture-agent-based.md)
- 启用 Defender for IoT [服务](quickstart-onboard-iot-hub.md)
- 阅读 Defender for IoT 服务 [Defender for IoT 常见问题解答](resources-agent-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
