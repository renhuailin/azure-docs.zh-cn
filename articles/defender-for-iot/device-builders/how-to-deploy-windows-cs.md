---
title: 在 Windows 设备上安装 C# 代理
description: 了解如何在 32 位或 64 位 Windows 设备上安装 Defender for IoT 代理。
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 1a9bab1f9e24fba6a2b04b6d0bb5d9f2560dcc1d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643966"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>为 Windows 部署 Defender for IoT 基于 C# 的安全代理

本指南介绍如何在 Windows 上安装 Defender for IoT 基于 C# 的安全代理。

本指南介绍如何：

- 安装
- 验证部署
- 卸载代理
- 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 要安装它的计算机上的本地管理员权限。

1. 为设备[创建 Defender-IoT-micro-agent](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要安装安全代理，请使用以下工作流：

1. 在设备上安装 Defender for IoT Windows C# 代理。 从 Defender for IoT [GitHub 存储库](https://github.com/Azure/Azure-IoT-Security-Agent-CS)将最新版本下载到计算机。

1. 提取包的内容，并导航到 /Install 文件夹。

1. 以管理员身份打开 Windows PowerShell。
1. 运行以下命令，将运行权限添加到 InstallSecurityAgent 脚本：

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    然后，运行以下命令：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    例如：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    有关身份验证参数的详细信息，请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)。

此脚本执行以下操作：

* 安装必备组件。
* 添加服务用户（在禁用交互式登录的情况下）。
* 将代理作为 **系统服务** 安装。
* 使用提供的身份验证参数配置代理。

如需额外帮助，请在 PowerShell 中使用 Get-Help 命令。

Get-Help 示例：```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>验证部署状态

运行以下命令来检查代理部署状态：

`sc.exe query "ASC IoT Agent"`

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请执行以下操作：

1. 运行以下 PowerShell 脚本，将 **-mode** 参数设置为 **Uninstall**。

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>疑难解答

如果代理无法启动，请启用日志记录（日志记录默认设置为“禁用”）以获取更多信息。

若要启用日志记录，请执行以下操作：

1. 使用标准文件编辑器打开配置文件 (General.config) 进行编辑。

1. 编辑以下值：

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 建议在故障排除操作完成后关闭日志记录功能。 让日志记录保持启用状态会增加日志文件大小和数据使用量。

1. 运行以下 PowerShell 或命令行，以便重启代理：

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   或

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. 查看日志文件，了解有关故障的详细信息。 日志文件将出现在运行脚本的工作目录中。 

   日志文件位置：`.\IoTAgentLog.log`

## <a name="next-steps"></a>后续步骤

* 阅读 Defender for IoT 服务[概述](overview.md)
* 详细了解 Defender for IoT [什么是基于代理的设备生成器解决方案](architecture-agent-based.md)
* 启用该[服务](quickstart-onboard-iot-hub.md)
* 阅读 [Azure Defender for IoT 代理常见问题](resources-agent-frequently-asked-questions.md)
* 了解[警报](concept-security-alerts.md)
