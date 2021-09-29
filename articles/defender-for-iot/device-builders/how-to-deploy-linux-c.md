---
title: 安装和部署 Linux C 代理
description: 了解如何在 Linux 上安装和部署 Defender for IoT 基于 C 的安全代理
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 46677a971165c65440310e21933f586cc685b771
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584861"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>部署适用于 Linux 的 Defender for IoT 基于 C 的安全代理

本指南介绍如何在 Linux 上安装和部署 Defender for IoT 基于 C 的安全代理。

- 安装
- 验证部署
- 卸载代理
- 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，在希望在其上进行安装的计算机上，需要拥有本地管理员权限 (sudo)。

1. 为设备[创建 Defender-IoT-micro-agent](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要安装和部署安全代理，请使用以下工作流：

1. 将 [b](https://aka.ms/iot-security-github-c) 中的最新代理版本下载到计算机。

1. 提取包的内容，并导航到 /src/installation 文件夹。

1. 运行以下命令，将运行权限添加到 InstallSecurityAgent 脚本：

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 然后运行：

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   有关身份验证参数的详细信息，请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)。

此脚本可执行以下功能：

1. 安装必备组件。

1. 添加服务用户（在禁用交互式登录的情况下）。

1. 安装用作 **守护程序** 的代理 - 假设设备使用 **systemd** 进行服务管理。

1. 使用提供的身份验证参数配置代理。

如需更多帮助，请结合 -help 参数运行该脚本：

`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请结合–-uninstall 参数运行该脚本：

`./InstallSecurityAgent.sh -–uninstall`

## <a name="troubleshooting"></a>疑难解答

运行以下命令来检查部署状态：

`systemctl status ASCIoTAgent.service`

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解 Defender for IoT [什么是基于代理的设备生成器解决方案](architecture-agent-based.md)
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读 [Azure Defender for IoT 代理常见问题](resources-agent-frequently-asked-questions.md)
- 了解[安全警报](concept-security-alerts.md)
