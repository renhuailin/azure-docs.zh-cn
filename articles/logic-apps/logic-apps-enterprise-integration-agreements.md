---
title: 在工作流中定义合作伙伴之间的协议
description: 在使用 Enterprise Integration Pack 的 Azure 逻辑应用中向用于工作流的集成帐户添加合作伙伴之间的协议。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 7b634da15248513ee782967eb1c86092e940ec9b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361290"
---
# <a name="add-agreements-between-partners-in-integration-accounts-for-workflows-in-azure-logic-apps"></a>在 Azure 逻辑应用中向用于工作流的集成帐户添加合作伙伴之间的协议

将合作伙伴添加到集成帐户后，通过在集成帐户中定义[协议](logic-apps-enterprise-integration-agreements.md)来指定合作伙伴如何交换消息。 协议通过定义用于交换消息的特定行业标准协议并提供以下共享权益，帮助组织彼此无缝通信：

* 使组织能够以熟知的格式交换信息。

* 在进行企业对企业 (B2B) 交易时提高效率。

* 轻松创建、管理和使用协议，以构建企业集成解决方案。

协议需要一个主机合作伙伴（始终是你的组织）和一个来宾合作伙伴（始终是与你的组织交换消息的组织）。  托管方可以是其他公司，甚至是自己的组织中的部门。 使用此协议，可以从主机合作伙伴的角度指定如何处理入站和出站消息。

本文展示了如何创建和管理协议，然后你可以使用该协议通过 AS2、X12、EDIFACT 或 RosettaNet 操作与其他合作伙伴交换 B2B 消息。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户需要一个[指向逻辑应用资源的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户无需一个指向逻辑应用资源的链接，但仍需存储其他项目，例如合作伙伴、协议和证书，以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

  > [!NOTE]
  > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

* 集成帐户中至少有两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)。 协议都需要有主机合作伙伴和来宾合作伙伴。 此外，协议要求两个合作伙伴都使用相同或兼容的业务标识限定符，此标识符适用于 AS2、X12、EDIFACT 或 RosettaNet 协议。

* （可选）要使用协议交换消息的逻辑应用资源和工作流。 工作流需要任意能够启动逻辑应用工作流的触发器。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。

## <a name="add-an-agreement"></a>添加协议

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“协议”。

1. 在“协议”窗格中，选择“添加”。

1. 在“添加”窗格中，提供以下有关协议的信息：

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*agreement-name*> | 协议的名称 |
   | **协议类型** | 是 | AS2、X12、EDIFACT 或 RosettaNet    | 协议 (agreement) 的协议 (protocol) 类型。 创建协议 (agreement) 文件时，该文件中的内容必须与协议 (agreement) 类型匹配。 |
   | **主机合作伙伴** | 是 | <*host-partner-name*> | 主机合作伙伴代表你的组织 |
   | **主机标识** | 是 | <*host-partner-identifier*> | 主机合作伙伴的标识符 |
   | **来宾合作伙伴** | 是 | <*guest-partner-name*> | 来宾合作伙伴表示与你的组织通信的组织 |
   | **来宾标识** | 是 | <*guest-partner-identifier*> | 来宾合作伙伴的标识符 |
   | **接收设置** | 多种多样 | 多种多样 | 这些属性指定主机合作伙伴如何从协议中的来宾合作伙伴处接收入站消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   | **发送设置** | 多种多样 | 多种多样 | 这些属性指定主机合作伙伴如何向协议中的来宾合作伙伴发送出站消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet PIP 引用** | 多种多样 | 多种多样 | 此窗格指定有关要使用 RosettaNet 消息的一个或多个合作伙伴接口进程 (PIP) 的信息。 有关详细信息，请查看[交换 RosettaNet 消息](logic-apps-enterprise-integration-rosettanet.md)。 |
   |||||

   > [!IMPORTANT]
   > 协议的解决方案取决于是否匹配在合作伙伴和入站消息中定义的以下项：
   >
   > * 发送方的限定符和标识符
   > * 接收方的限定符和标识符
   >
   > 如果这些值因合作伙伴而更改，请确保同时更新协议。

1. 完成后，请选择“确定”。

   协议现在出现在“协议”列表中。

## <a name="edit-an-agreement"></a>编辑协议

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“协议”。

1. 在“协议”窗格中，选择协议，选择“编辑”，然后进行更改。 

1. 完成后，请选择“确定”。

## <a name="delete-an-agreement"></a>删除协议

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“协议”。

1. 在“协议”窗格中，选择要删除的协议，然后选择“删除” 。

1. 要确认删除协议，请选择“是”。

## <a name="next-steps"></a>后续步骤

* [交换 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [交换 X12 消息](logic-apps-enterprise-integration-x12.md)
* [交换 RosettaNet 消息](logic-apps-enterprise-integration-rosettanet.md)
