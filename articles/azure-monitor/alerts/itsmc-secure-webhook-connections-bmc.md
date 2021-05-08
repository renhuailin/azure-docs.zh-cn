---
title: IT 服务管理连接器 - Azure Monitor 中的安全导出 - 使用 BMC 进行配置
description: 本文介绍如何基于 Azure Monitor 中的安全导出连接 ITSM 产品/服务与 BMC。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041699"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>将 BMC Helix 连接到 Azure Monitor

以下部分提供有关如何连接 BMC Helix 产品和 Azure 中的安全导出的详细信息。

## <a name="prerequisites"></a>先决条件

确保符合以下先决条件：

* 已注册 Azure AD。
* 具有受支持的 BMC Helix 多云服务管理版本（版本 19.08 或更高版本）。

## <a name="configure-the-bmc-helix-connection"></a>配置 BMC Helix 连接

1. 在 BMC Helix 环境中使用以下过程获取安全导出的 URI：

   1. 登录 Integration Studio。
   1. 搜索“从 Azure 警报创建事件”流。
   1. 复制 Webhook URL。
   
   ![Integration Studio 中的 Webhook URL 的屏幕截图。](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. 请按照以下说明操作，具体视版本而异：
   * [为版本 20.02 启用与 Azure Monitor 的预生成集成](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * [为版本 19.11 启用与 Azure Monitor 的预生成集成](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. 在 BMC Helix 中配置连接时，请进入集成 BMC 实例，并按照以下说明进行操作：

   1. 选择“目录”。
   2. 选择“Azure 警报”。
   3. 选择“连接器”。
   4. 选择“配置”。
   5. 选择“添加新的连接”配置。
   6. 填写配置部分中的信息：
      - **名称**：自行填写。
      - **授权类型**：无
      - **说明**：自行创建。
      - **站点**：云
      - **实例数**：2（默认值）。
      - **检查**：默认选中便于使用。
      - Azure 租户 ID 和 Azure 应用程序 ID 取自之前定义的应用程序。

![显示 BMC 配置的屏幕截图。](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
