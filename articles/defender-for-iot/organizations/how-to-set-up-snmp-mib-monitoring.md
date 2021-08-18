---
title: 设置 SNMP MIB 监视
description: 可以使用 SNMP 执行传感器运行状况监视。 传感器响应从授权监视服务器发送的 SNMP 查询。
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 14803fd2f9c088fb4454f97ff1524e8d651ccd05
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015532"
---
# <a name="set-up-snmp-mib-monitoring"></a>设置 SNMP MIB 监视

可以使用简单网络管理协议 (SNMP) 来执行传感器运行状况监视。 传感器响应从授权监视服务器发送的 SNMP 查询。 SNMP 监视器会定期轮询传感器 OID（一秒多达 50 次）。

SNMP 支持的版本为 SNMP v2 或 SNMP v3。 SNMP 将 UDP 用作使用端口 161 的传输协议 (SNMP)。

在开始配置 SNMP 监视之前，需要在防火墙中打开端口 UDP 161。

## <a name="oids"></a>OID

| 管理控制台和传感器 | OID | 格式 | 说明 |
|--|--|--|--|
| 设备名称 | 1.3.6.1.2.1.1.5.0 | STRING | 本地管理控制台的设备名称 |
| Vendor | 1.3.6.1.2.1.1.4.0 | STRING | Microsoft 支持部门 (support.microsoft.com) |
| 平台 | 1.3.6.1.2.1.1.1.0 | STRING | 传感器或本地管理控制台 |
| 序列号 | 1.3.6.1.4.1.53313.1 |STRING | 许可证使用的字符串 |
| 软件版本 | 1.3.6.1.4.1.53313.2 | STRING | Xsense 完整版字符串和管理完整版字符串 |
| CPU 使用率 | 1.3.6.1.4.1.53313.3.1 | GAUGE32 | 指示 0 到 100 |
| CPU 温度 | 1.3.6.1.4.1.53313.3.2 | STRING | 基于 Linux 输入指示 0 到 100 摄氏度。 将从没有实际物理温度传感器的任何计算机（例如 VM）返回“找不到传感器”|
| 内存使用率 | 1.3.6.1.4.1.53313.3.3 | GAUGE32 | 指示 0 到 100 |
| 磁盘使用情况 | 1.3.6.1.4.1.53313.3.4 | GAUGE32 | 指示 0 到 100 |
| 服务状态 | 1.3.6.1.4.1.53313.5  |STRING | 如果四个关键组件之一关闭，则联机或脱机 |
| 服务状态 | 1.3.6.1.4.1.53313.5  |STRING | 如果四个关键组件之一关闭，则联机或脱机 |
| 本地/云连接 | 1.3.6.1.4.1.53313.6   |STRING | 指示传感器是连接到 Defender for IoT 门户还是仅在本地托管 |
| 许可证状态 | 1.3.6.1.4.1.53313.5  |STRING | 指示激活文件是否已过期 |

   - 根据 [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)，非现有密钥的响应为 NULL，HTTP 200。
    
   - 应在所有体系结构和物理传感器上测试与硬件相关的 MIB（CPU 使用率、CPU 温度、内存使用率、磁盘使用率）。 虚拟机上的 CPU 温度应不适用。

你可以下载包含传感器接收的所有 SNMP 查询的日志，包括来自数据包的连接数据和原始数据。

定义 SNMP v2 运行状况监视：

1. 在侧边菜单中选择“系统设置”。

2. 在“活动发现”窗格中，选择“SNMP MIB 监视” :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="编辑 SNMP 窗口。":::

3. 在“允许的主机”部分中，选择“添加主机”，并输入执行系统运行状况监视的服务器的 IP 地址 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="输入允许的主机的 IP 地址。":::

4. 在“身份验证”部分的“SNMP v2 社区字符串”框中，输入字符串 。 SNMP 社区字符串最多可包含 32 个字符，并包含字母数字字符的任意组合（大写字母、小写字母和数字）。 不允许空格。

5. 选择“保存”。

定义 SNMP v3 运行状况监视：

1. 在侧边菜单中选择“系统设置”。

2. 在“活动发现”窗格上，选择“SNMP MIB 监视” :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="编辑 SNMP 窗口。":::

3. 在“允许的主机”部分中，选择“添加主机”，并输入执行系统运行状况监视的服务器的 IP 地址 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="输入允许的主机的 IP 地址。":::

4. 在“身份验证”部分，设置以下参数：

    | 参数 | 说明 |
    |--|--|
    | **用户名** | SNMP 用户名最多可包含 32 个字符，并包含字母数字字符的任意组合（大写字母、小写字母和数字）。 不允许空格。 <br /> <br />必须在系统和 SNMP 服务器上配置 SNMP v3 身份验证的用户名。 |
    | **密码** | 输入身份验证密码（区分大小写）。 身份验证密码可包含 8-12 个字符，并包含字母数字字符的任意组合（大写字母、小写字母和数字）。 <br /> <br/>必须在系统和 SNMP 服务器上配置 SNMP v3 身份验证的用户名。 |
    | **身份验证类型** | 选择“MD5”或“SHA”。 |
    | **加密** | 选择“DES”或“AES”。 |
    | **密钥** | 密钥必须刚好包含 8 个字符，并且包含字母数字字符的任意组合（大写字母、小写字母和数字）。 |

5. 选择“保存”。

## <a name="see-also"></a>另请参阅

[导出故障排除日志](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
