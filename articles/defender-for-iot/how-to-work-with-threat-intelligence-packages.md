---
title: 更新威胁情报数据
description: 威胁情报数据包随每个新的 Defender for IoT 版本提供（或者如果在版本之间需要的话提供）。
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784044"
---
# <a name="threat-intelligence-research-and-packages"></a>威胁情报研究和包

Microsoft 的安全团队执行专有的 ICS 威胁情报和漏洞研究。 这些团队包括 MSTIC（Microsoft 威胁情报中心），DART（Microsoft 检测和响应团队），DCU（反数字犯罪部门），以及 Section 52（IoT/OT/ICS 领域专家，这些专家跟踪特定于 ICS 的零日漏洞、反向工程恶意软件、市场活动和对手）。

团队提供对 Microsoft 的以下各项进行安全检测、分析和响应：

- 云基础结构和服务。
- 传统产品和设备。
- 内部公司资源。

安全团队获得以下权益：

- 防范已知和相关的威胁。
- 有助于会审和设置优先级的见解。
- 在威胁受到影响之前，对其完整上下文的了解。
- 更相关、准确且可操作的数据。

此情报添加了上下文信息以丰富 Microsoft 平台分析并支持公司的托管服务以进行事件响应和违规调查。 威胁情报包包含签名（包括恶意软件签名）、CVE 和其他安全内容。

用户可以从 Azure Defender for IoT 门户的“更新”页下载包。

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="通过 Azure Defender for IoT 门户下载更新。":::

## <a name="update-threat-intelligence-data"></a>更新威胁情报数据

威胁情报数据包随每个新的 Defender for IoT 版本更新提供（或者如果在版本之间需要的话提供）。

可以手动将从 Defender for IoT 门户下载的包上传到各个传感器。 如果本地管理控制台管理传感器，则可以将威胁情报包下载到管理控制台，并将它们同时推送到多个传感器。

如果要更新单个传感器上的包：

1. 转到 Azure Defender for IoT“更新”页。

2. 下载并保存“威胁情报”包。

3. 登录传感器控制台。

4. 在侧边菜单中，选择“系统设置”。

5. 选择“威胁情报数据”，然后选择“更新”。

6. 上传新包。

如果要同时更新多个传感器上的包：

1. 转到 Azure Defender for IoT“更新”页。

2. 下载并保存“威胁情报”包。

3. 登录到管理控制台。

4. 在侧边菜单中，选择“系统设置”。

5. 在“传感器引擎配置”部分中，选择应接收更新包的传感器。  

6. 在“选择威胁情报数据”部分中，选择加号 ( **+** )。

7. 上传该包。

## <a name="see-also"></a>另请参阅

[更新版本](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
