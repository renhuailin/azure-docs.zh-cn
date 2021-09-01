---
title: 安装和部署 Linux C# 代理
description: 了解如何在 Linux 上安装和部署 Defender for IoT 基于 C# 的安全代理
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 8dd6119d4f1a6e8a738d34521234d2691ba8c883
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014510"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>部署 Linux 版 Defender for IoT 基于 C# 的安全代理

本指南介绍如何在 Linux 上安装和部署 Defender for IoT 基于 C# 的安全代理。

本指南介绍如何：

- 安装
- 验证部署
- 卸载代理
- 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，需要在安装计算机上拥有本地管理员权限。

1. 为设备[创建 Defender-IoT-micro-agent](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要部署安全代理，请执行以下步骤：

1. 将 [GitHub](https://aka.ms/iot-security-github-cs) 中的最新代理版本下载到计算机。

1. 提取包的内容，并导航到 _/Install_ 文件夹。

1. 运行 `chmod +x InstallSecurityAgent.sh`，将运行权限添加到 **InstallSecurityAgent 脚本**

1. 接下来，使用根权限运行以下命令：

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   有关身份验证参数的详细信息，请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)。

此脚本可执行以下操作：

- 安装必备组件。

- 添加服务用户（在禁用交互式登录的情况下）。

- 安装用作守护程序的代理 - 假定设备将 systemd 用于经典部署模型。

- 配置 sudoers，允许代理以 root 身份执行某些任务。

- 使用提供的身份验证参数配置代理。

如需更多帮助，请结合 -help 参数运行该脚本：`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请结合 -u 参数运行该脚本：`./InstallSecurityAgent.sh -u`。

> [!NOTE]
> 卸载时，不会删除在安装过程中安装的任何缺失的必备组件。

## <a name="troubleshooting"></a>疑难解答

1. 运行以下命令来检查部署状态：

    `systemctl status ASCIoTAgent.service`

1. 启用日志记录。
   如果代理无法启动，请启用日志记录以获取更多信息。

   通过以下方式启用日志记录：

   1. 在任意 Linux 编辑器中打开要编辑的配置文件：

        `vi /var/ASCIoTAgent/General.config`

   1. 编辑以下值：

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       **logFilePath** 值是可以配置的。

       > [!NOTE]
       > 建议在故障排除操作完成后关闭日志记录功能。 让日志记录保持启用状态会增加日志文件大小和数据使用量。

   1. 通过运行以下命令重启代理：

       `systemctl restart ASCIoTAgent.service`

   1. 查看日志文件，了解有关故障的详细信息。

       日志文件位置为：`/var/ASCIoTAgent/IotAgentLog.log`

       根据你在步骤 2 中为 **logFilePath** 选择的名称，更改文件位置路径。

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解 Defender for IoT [什么是基于代理的设备生成器解决方案](architecture-agent-based.md)
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读 [Azure Defender for IoT 代理常见问题](resources-agent-frequently-asked-questions.md)
- 了解[警报](concept-security-alerts.md)
