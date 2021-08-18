---
title: 选择并部署安全代理
description: 了解如何在 IoT 设备上选择并部署 Defender for IoT 安全代理。
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: c71c92ffa79c844f3529265320b46eadd0c158cf
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014514"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 设备上选择并部署安全代理

Defender for IoT 提供用于安全代理的参考体系结构，这些安全代理监视 IoT 设备并从 IoT 设备收集数据。
若要了解详细信息，请参阅[安全代理参考体系结构](security-agent-architecture.md)。

代理是作为开源项目开发的，以两种风格提供： <br> [C](https://aka.ms/iot-security-github-c) 和 [C#](https://aka.ms/iot-security-github-cs)。

在本文中，学习如何：
- 比较安全代理风格
- 发现支持的代理平台
- 为解决方案选择正确的代理风格

## <a name="understand-security-agent-options"></a>了解安全代理选项

每一种 Defender for IoT 安全代理风格都提供相同的一组功能，并且支持类似的配置选项。

基于 C 的安全代理占用内存较少，非常适用于可用资源较少的设备。

|     | 基于 C 的安全代理 | 基于 C# 的安全代理 |
| --- | ----------- | --------- |
| **开源** | 在 [GitHub](https://aka.ms/iot-security-github-c) 中按 [MIT 许可](https://en.wikipedia.org/wiki/MIT_License)条款提供 | 在 [GitHub](https://aka.ms/iot-security-github-cs) 中按 [MIT 许可](https://en.wikipedia.org/wiki/MIT_License)条款提供 |
| **开发语言**    | C | C# |
| 是否有受支持的 Windows 平台？ | 否 | 是 |
| Windows 先决条件 | --- | [WMI](/windows/desktop/wmisdk/) |
| 是否有受支持的 Linux 平台？ | 是，x64 和 x86 | 是，仅 x64 |
| **Linux 先决条件** | libunwind8、libcurl3、uuid-runtime、auditd、audispd-plugins | libunwind8、libcurl3、uuid-runtime、auditd、audispd-plugins、sudo、netstat、iptables |
| 磁盘占用情况 | 10.5 MB | 90 MB |
| 内存占用情况（平均值） | 5.5 MB | 33 MB |
| 向 IoT 中心进行[身份验证](concept-security-agent-authentication-methods.md) | 是 | 是 |
| 安全数据[收集](how-to-agent-configuration.md#supported-security-events) | 是 | 是 |
| **事件聚合** | 是 | 是 |
| 通过 [Defender-IoT-micro-agent 孪生体](concept-security-module.md)进行远程配置 | 是 | 是 |

## <a name="security-agent-installation-guidelines"></a>安全代理安装准则

对于 **Windows**：必须从管理员 PowerShell 窗口中执行 Install SecurityAgent.ps1 脚本。

对于 **Linux**：必须以超级用户身份运行 InstallSecurityAgent.sh。 建议为该安装命令添加“sudo”前缀。

## <a name="choose-an-agent-flavor"></a>选择代理风格

请回答以下有关 IoT 设备的问题，以选择正确的代理：

- 你使用的是 Windows Server 还是 Windows IoT Core ？

    [部署适用于 Windows 的基于 C# 的安全代理](how-to-deploy-windows-cs.md)。

- 你是否在使用具有 x86 体系结构的 Linux 分发版？

    [部署适用于 Linux 的基于 C 的安全代理](how-to-deploy-linux-c.md)。

- 你是否在使用具有 x64 体系结构的 Linux 分发版？

    两种代理风格均可使用。 <br>
    [部署适用于 Linux 的基于 C 的安全代理](how-to-deploy-linux-c.md)并且/或者[部署适用于 Linux 的基于 C# 的安全代理](how-to-deploy-linux-cs.md)。

两种代理风格都提供相同的一组功能，并且支持类似的配置选项。
有关详细信息，请参阅[安全代理比较](how-to-deploy-agent.md#understand-security-agent-options)。

## <a name="supported-platforms"></a>受支持的平台

以下列表包含当前支持的所有平台。

|Defender for IoT 代理 |操作系统 |体系结构 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    X64|
|C|Ubuntu 18.04 |    x64、ARMv7|
|C|Debian 9 |    x64、x86|
|C#|Ubuntu 16.04     |X64|
|C#|Ubuntu 18.04    |x64、ARMv7|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版 17763    |X64|
|

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请继续阅读有关代理配置的操作指南。
> [!div class="nextstepaction"]
> [代理配置操作指南](./how-to-agent-configuration.md)