---
title: 配置 Windows 终结点监视
description: 使用 Windows 终结点监视和 WMI 来扩充设备上解析的数据。
ms.date: 05/03/2021
ms.topic: how-to
ms.openlocfilehash: 4d701cfda88a2c257b001a52a19853a937661714
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015921"
---
# <a name="configure-windows-endpoint-monitoring-wmi"></a>配置 Windows 终结点监视 (WMI)

利用 Windows 终结点监视功能，可以配置 Azure Defender for IoT，以有选择地探测 Windows 系统。 这样可以为用户提供更明确更准确的设备信息，例如服务包级别。

用户可配置探测特定范围和主机，并可将此操作配置为仅按所需频次执行。 为实现选择性探测，可使用 Windows Management Instrumentation (WMI)，这是用于管理 Windows 系统的 Microsoft 标准脚本语言。

> [!NOTE]
> - 一次只能运行一个扫描。
> - 可以具有域或本地管理员权限的用户身份获取最佳结果。
> - 在开始 WMI 配置之前，请先配置防火墙规则，以使用 UDP 端口 135 和 1024 以上的所有 TCP 端口开启从传感器到已扫描子网的传出流量。

在探测完成时，使用导出日志的选项可提供包含所有探测尝试的日志文件。 该日志包含已探测的所有 IP 地址。 对于每个 IP 地址，该日志都会显示成功和失败信息。 其中还包含错误代码，此代码是一个根据异常派生的自由字符串。 系统中只保留最后一个扫描日志。

可以执行计划扫描或手动扫描。 在扫描完成时，可在 CSV 文件中查看结果。

**必备条件**

配置防火墙规则，以便通过使用 UDP 端口 135 和 1024 以上的所有 TCP 端口打开从传感器到扫描的子网的传出流量。

## <a name="perform-an-automatic-scan"></a>执行自动扫描

本部分介绍如何执行自动扫描

执行自动扫描：

1. 在侧边菜单中选择“系统设置”。

2. 选择“Windows 终结点监视”:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示选择“Windows 终结点监视”的屏幕截图。":::

3. 在“扫描计划”窗格上，配置选项，如下所示：

      - 按固定间隔（以小时为单位）：根据以小时为单位的间隔设置扫描计划。

      - 按特定时间：根据特定时间设置扫描计划，并选择“保存扫描”。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="显示“保存扫描”按钮的屏幕截图。":::

4. 若要定义扫描范围，请选择“设置扫描范围”。

5. 设置 IP 地址范围并添加用户和密码。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="显示添加用户和密码的屏幕截图。":::

6. 若要从扫描中排除某个 IP 范围，请选择该范围旁边的“禁用”。

7. 若要删除某个范围，请选择该范围旁边的 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false":::。

8. 选择“保存”。 “编辑扫描范围配置”对话框会关闭，范围数会显示在“扫描范围”窗格中 。

## <a name="perform-a-manual-scan"></a>执行手动扫描

**若要执行手动扫描，请执行以下操作：**

1. 在侧边菜单中选择“系统设置”。

2. 选择“Windows 终结点监视”:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示“Windows 终结点监视”设置屏幕的屏幕截图。":::

3. 在“操作”窗格中，选择“开始扫描”。 “操作”窗格中会显示一个状态栏，其中会显示扫描进程的进度。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="显示“开始扫描”按钮的屏幕截图。":::

## <a name="view-scan-results"></a>查看扫描结果

**若要查看扫描结果，请执行以下操作：**

1. 在扫描完成时，在“操作”窗格中选择“查看扫描结果” 。 包含扫描结果的 CSV 文件会下载到计算机。