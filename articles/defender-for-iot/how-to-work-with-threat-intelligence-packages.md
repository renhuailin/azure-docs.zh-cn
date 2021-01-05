---
title: 更新威胁智能数据
description: 每个新的 Defender for IoT 版本都提供威胁情报包，或者在发布之间需要。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838534"
---
# <a name="threat-intelligence-research-and-packages"></a>威胁情报研究和包

Microsoft 中的安全团队执行专有的 ICS 威胁情报和漏洞研究。 这些团队包括 MSTIC (Microsoft 威胁情报中心) ，DART (Microsoft 检测和响应团队) ，DCU (数字犯罪部门) ，以及 52 (IoT/OT/ICS 域专家，这些专家跟踪特定于 ICS 的零天、反向工程恶意软件、市场活动和攻击者) 。

团队提供对 Microsoft 的安全检测、分析和响应：

- 云基础结构和服务。
- 传统的产品和设备。
- 内部公司资源。

安全团队获得以下好处：

- 防范已知和相关的威胁。
- 有助于诊断和确定优先级的见解。
- 在威胁受到影响之前，对其进行了全面的了解。
- 更相关、准确且可操作的数据。

此智能添加了上下文信息以丰富 Microsoft 平台分析，并支持公司的托管服务进行事件响应和违规调查。 威胁智能包包含签名 (包括恶意软件签名) 、标识符和其他安全内容。

你可以从 Azure Defender for IoT 门户的 " **更新** " 页下载包。

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="通过 Azure Defender for IoT 门户下载更新。":::

## <a name="update-threat-intelligence-data"></a>更新威胁智能数据

每个新 Defender 用于 IoT 版本更新，或者在发布之间需要时，会提供威胁智能包。

可以手动将从 Defender for IoT 门户下载的包上传到各个传感器。 如果本地管理控制台管理你的传感器，你可以将威胁情报包下载到管理控制台，并同时将它们推送到多个传感器。

更新单个传感器上的包：

1. 请参阅 Azure Defender for IoT **更新** 页。

2. 下载并保存 **威胁情报** 包。

3. 登录到传感器控制台。

4. 在侧边菜单中，选择 " **系统设置**"。

5. 选择 " **威胁智能数据**"，然后选择 " **更新**"。

6. 上传新包。

同时更新多个传感器上的包：

1. 请参阅 Azure Defender for IoT **更新** 页。

2. 下载并保存 **威胁情报** 包。

3. 登录到管理控制台。

4. 在侧边菜单中，选择 " **系统设置**"。

5. 在 " **传感器引擎配置** " 部分中，选择应接收更新包的传感器。  

6. 在 " **选择威胁智能数据** " 部分中，选择加号 (**+**) 。

7. 上传该包。

## <a name="see-also"></a>另请参阅

[更新版本](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
