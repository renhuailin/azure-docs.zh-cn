---
title: IT 服务管理连接器-通过 BMC 在 Azure Monitor 配置中保护导出
description: 本文介绍如何在 Azure Monitor 中通过 BMC 将 ITSM 产品/服务连接到安全导出。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041699"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>将 BMC Helix 连接到 Azure Monitor

以下部分提供有关如何在 Azure 中连接 BMC Helix 产品和安全导出的详细信息。

## <a name="prerequisites"></a>先决条件

确保满足以下先决条件：

* 已注册 Azure AD。
* 你具有受支持的 BMC Helix 版本19.08 或更高版本 (版本或更高版本) 。

## <a name="configure-the-bmc-helix-connection"></a>配置 BMC Helix 连接

1. 在 BMC Helix 环境中使用以下过程获取用于安全导出的 URI：

   1. 登录到 Integration Studio。
   1. **从 "Azure 警报** 流" 中搜索 "创建事件"。
   1. 复制 webhook URL。
   
   ![Integration Studio 中 webhook U R L 的屏幕截图。](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. 按照以下版本的说明操作：
   * 正在[为版本20.02 启用预置与 Azure Monitor 的集成](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * 正在[为版本19.11 启用预置与 Azure Monitor 的集成](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. 在 BMC Helix 中的连接配置过程中，请进入你的集成 BMC 实例，并按照以下说明进行操作：

   1. 选择 " **目录**"。
   2. 选择 " **Azure 警报**"。
   3. 选择 " **连接器**"。
   4. 选择 " **配置**"。
   5. 选择 " **添加新的连接** 配置"。
   6. 填写配置部分中的信息：
      - **名称**：构成自己的。
      - **授权类型**： **无**
      - **说明**：自行创建。
      - **站点**： **云**
      - **实例数**： **2**（默认值）。
      - **选中**：默认情况下选择此选项可以启用使用。
      - Azure 租户 ID 和 Azure 应用程序 ID 取自前面定义的应用程序。

![显示 BMC 配置的屏幕截图。](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
