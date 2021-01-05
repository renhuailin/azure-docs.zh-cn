---
title: 设置 SNMP MIB 监视
description: 可以使用 SNMP 执行传感器运行状况监视。 传感器响应从授权监视服务器发送的 SNMP 查询。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e0ae029323d5b64288c5e61ea28a494c1106a53f
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838188"
---
# <a name="set-up-snmp-mib-monitoring"></a>设置 SNMP MIB 监视

您可以使用简单网络管理协议 (SNMP) 来执行传感器运行状况监视。 传感器响应从授权监视服务器发送的 SNMP 查询。 SNMP 监视器会定期轮询传感器 Oid， (多达50倍于第二次) 。

SNMP 支持的版本为 SNMP v2 或 SNMP v3。 SNMP 将 UDP 用作其使用端口 161 (SNMP) 的传输协议。

在开始配置 SNMP 监视之前，需要在防火墙中打开端口 UDP 161。

## <a name="oids"></a>Oid

| 管理控制台和传感器 | OID | 格式 | 说明 |
|--|--|--|--|
| 设备名称 | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | 本地管理控制台的设备名称 |
| Vendor | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft 支持部门 (support.microsoft.com)  |
| 平台 | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | 传感器或本地管理控制台 |
| 序列号 | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | 许可证使用的字符串 |
| 软件版本 | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Xsense 完全版本字符串和管理的完整版本字符串 |
| CPU 使用率 | 1.3.6.1.4.1.9.9.53313.3.1 | 的 GAUGE32 | 指示0到100 |
| CPU 温度 | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | 基于 Linux 输入的摄氏指示值为零到100 |
| 内存使用率 | 1.3.6.1.4.1.9.9.53313.3.3 | 的 GAUGE32 | 指示0到100 |
| 磁盘使用情况 | 1.3.6.1.4.1.9.9.53313.3.4 | 的 GAUGE32 | 指示0到100 |
| 服务状态 | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | 如果四个关键组件中有一项关闭，则联机或脱机 |
| 带宽 | 超出2.4 的范围 |  | Xsense 中每个监视器接口上收到的带宽 |

   - 非现有密钥会根据 [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)以 NULL、HTTP 200 响应。
    
   - 与硬件相关的 Mib (CPU 使用情况、CPU 温度、内存使用率、磁盘使用情况) 应在所有体系结构和物理传感器上测试。 虚拟机上的 CPU 温度应不适用。

您可以下载包含传感器接收的所有 SNMP 查询的日志，包括来自数据包的连接数据和原始数据。

定义 SNMP v2 运行状况监视：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **活动发现** " 窗格中，选择 " **SNMP MIB 监视**" :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="编辑 SNMP 窗口。":::

3. 在 " **允许的主机** " 部分中，选择 " **添加主机** "，并输入执行系统运行状况监视的服务器的 IP 地址。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="输入允许的主机的 IP 地址。":::

4. 在 " **身份验证** " 部分的 " **SNMP v2 团体字符串** " 框中，输入字符串。 SNMP 团体字符串最多可包含32个字符，并包含字母数字字符的任意组合 (大写字母、小写字母和数字) 。 不允许空格。

5. 选择“保存”。

定义 SNMP v3 运行状况监视：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **活动发现** " 窗格中，选择 " **SNMP MIB 监视**" :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="编辑 SNMP 窗口。":::

3. 在 " **允许的主机** " 部分中，选择 " **添加主机** "，并输入执行系统运行状况监视的服务器的 IP 地址。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="输入允许的主机的 IP 地址。":::

4. 在 " **身份验证** " 部分中，设置以下参数：

    | 参数 | 说明 |
    |--|--|
    | **用户名** | SNMP 用户名最多可以包含32个字符，并包含字母数字字符的任意组合 (大写字母、小写字母和数字) 。 不允许空格。 <br /> <br />必须在系统和 SNMP 服务器上配置 SNMP v3 身份验证的用户名。 |
    | **密码** | 输入区分大小写的身份验证密码。 身份验证密码可以包含8到12个字符，并包含字母数字字符的任意组合 (大写字母、小写字母和数字) 。 <br /> <br/>必须在系统和 SNMP 服务器上配置 SNMP v3 身份验证的用户名。 |
    | **身份验证类型** | 选择 MD5 或 SHA。 |
    | **加密** | 选择 "DES 或 AES"。 |
    | **密钥** | 此密钥必须包含8个字符，并包含字母数字字符的任意组合 (大写字母、小写字母和数字) 。 |

5. 选择“保存”。

## <a name="see-also"></a>另请参阅

[导出疑难解答日志](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
