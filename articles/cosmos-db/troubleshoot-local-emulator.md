---
title: 排查使用 Azure Cosmos DB 模拟器时出现的问题
description: 了解如何排查使用 Azure Cosmos DB 模拟器时出现的服务不可用、证书、加密和版本控制问题。
ms.service: cosmos-db
ms.topic: troubleshooting
ms.author: esarroyo
author: StefArroyo
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e255388432c51562b254bf86c52090a50cb8e4a5
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358896"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>排查使用 Azure Cosmos DB 模拟器时出现的问题
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 本文中的提示有助于解决在安装或使用 Azure Cosmos DB 模拟器时遇到的问题。 

如果安装了新版本的模拟器并遇到错误，请务必重置数据。 重置数据的方法如下：在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，然后单击“重置数据...”。 如果仍无法消除错误，可卸载该模拟器和所有旧版模拟器（如有），删除“C:\Program files\Azure Cosmos DB Emulatorr”目录，并卸载模拟器。 有关说明，请参阅[卸载本地模拟器](local-emulator.md#uninstall)。 如果重置数据没有用，也可导航到 `%LOCALAPPDATA%\CosmosDBEmulator` 位置并删除该文件夹。

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>排查损坏的 Windows 性能计数器

* 如果 Azure Cosmos DB 模拟器崩溃，请从 `%LOCALAPPDATA%\CrashDumps` 文件夹收集转储文件，对其进行压缩，然后从 [Azure 门户](https://portal.azure.com)打开支持工单。

* 如果 `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` 中出现崩溃，这可能表示性能计数器处于损坏状态。 通常，从管理员命令提示符处运行以下命令即可解决此问题：

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>解决连接问题

* 如果遇到连接问题，请[收集跟踪文件](#trace-files)，对其进行压缩，然后在 [Azure 门户](https://portal.azure.com)中开具支持票证。

* 如果收到“服务不可用”消息，模拟器可能无法初始化网络堆栈。 请查看是否安装了 Pulse 安全客户端或 Juniper 网络客户端，因为其网络筛选器驱动程序可能会导致该问题。 卸载第三方网络筛选器驱动程序通常可修复此问题。 或者，使用 /DisableRIO 启动模拟器，这会将模拟器网络通信切换到常规 Winsock。 

* 如果遇到“禁止”-“消息”：“正在使用传输协议或密码中禁止的加密方法发出请求。请检查帐户 SSL/TLS 允许的最低协议设置…”连接问题，这可能是 OS 中发生全局更改（例如，预览体验计划预览版 20170）或启用 TLS 1.3 作为默认值的浏览器设置导致的。 使用 SDK 对 Cosmos 模拟器执行请求时，可能会发生类似错误，例如“Microsoft.Azure.Documents.DocumentClientException:正在使用传输协议或密码中禁止的加密方法发出请求。请检查帐户 SSL/TLS 允许的最低协议设置”。 此时这是预期情况，因为 Cosmos 模拟器仅接受并使用 TLS 1.2 协议。 建议的解决方法是将设置和默认值更改为 TLS 1.2；例如，在 IIS 管理器中，导航到“站点”->“默认网站”，找到端口 8081 的“网站绑定”并进行编辑，以禁用 TLS 1.3。 可以通过“设置”选项对 Web 浏览器执行类似操作。

* 在模拟器运行时，如果计算机进入了睡眠模式或运行了任何 OS 更新，则你可能会看到“服务当前不可用”消息。 请右键单击 Windows 通知托盘中显示的图标，再选择“重置数据”来重置模拟器的数据。

## <a name="collect-trace-files"></a><a id="trace-files"></a>收集跟踪文件

若要收集调试跟踪，请在管理命令提示符下运行以下命令：

1. 导航到安装了模拟器的路径：

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. 关闭模拟器，监视系统任务栏，确保该程序已关闭。 该过程可能需要一分钟时间。 你也可在 Azure Cosmos DB 模拟器用户界面中选择“退出”。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 使用以下命令开始日志记录：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. 启动模拟器

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 再现问题。 如果数据资源管理器无法运行，只需等待几秒钟，待浏览器打开以捕获错误。

1. 使用以下命令停止日志记录：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. 导航到 `%ProgramFiles%\Azure Cosmos DB Emulator` 路径，查找 docdbemulator_000001.etl 文件。

1. 在 [Azure 门户](https://portal.azure.com)中开具支持票证，并提供 .etl 文件以及再现步骤。

## <a name="next-steps"></a>后续步骤

本文介绍了如何调试本地模拟器的问题。 你现在可以继续学习下面的文章：

* [导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书](local-emulator-export-ssl-certificates.md)
* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
