---
title: 为工作流定义贸易合作伙伴
description: 在使用 Enterprise Integration Pack 的 Azure 逻辑应用中向用于工作流的集成帐户添加贸易合作伙伴。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549828"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>在 Azure 逻辑应用中向用于工作流的集成帐户添加贸易合作伙伴

若要在企业到企业 (B2B) 企业集成工作流中表示组织和其他方，请在[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)中创建“贸易合作伙伴”，以表示业务关系中的每一个参与者。 合作伙伴是参与 B2B 交易并相互交换消息的业务实体。

> [!IMPORTANT]
> 在定义这些合作伙伴之前，请与合作伙伴沟通，了解如何识别和验证相互发送的消息。 若要参与协议并相互交换消息，集成帐户中的合作伙伴必须使用相同或兼容的“业务限定符”。 在认可这些详细信息后，就可以在集成帐户中创建合作伙伴了。

本文介绍如何创建和管理合作伙伴，之后你可以使用这些合作伙伴创建贸易合作伙伴协议，以定义合作伙伴之间交换消息所遵循的特定行业标准协议。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（例如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户需要一个[指向逻辑应用资源的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户无需一个指向逻辑应用资源的链接，但仍需存储其他项目，例如合作伙伴、协议和证书，以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 集成帐户仍必须满足其他要求，例如，使用与逻辑应用资源相同的 Azure 订阅并与该资源位于同一位置。

  > [!NOTE]
  > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

<a name="add-partner"></a>

## <a name="add-a-partner"></a>添加合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“合作伙伴”。

1. 在“合作伙伴”窗格中，选择“添加”。 

1. 在“添加合作伙伴”窗格中，提供以下有关合作伙伴的信息：

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 合作伙伴的名称 |
   | **Qualifier** | 是 | 身份验证正文，用于向组织提供唯一业务标识，例如 **D-U-N-S (Dun & Bradstreet)** 。 <p>合作伙伴可以选择使用双方约定的业务标识。 对于这些方案，请为 EDIFACT 选择“双方约定”，或为 X12 选择“双方约定(X12)”。   <p>对于 RosettaNet，请仅选择“DUNS”  ，这是标准。 <p>重要说明：为使集成帐户中的合作伙伴参与协议并相互交换消息，这些合作伙伴必须使用相同或兼容的限定符。 |
   | **值** | 是 | 一个值，用于标识逻辑应用接收的文档。 <p>对于使用 RosettaNet 的合作伙伴，此值必须是对应于 DUNS 号的九位数字。 通过创建 RosettaNet 合作伙伴并[编辑其定义](#edit-partner)，可以为其提供更多信息，例如其分类和联系信息。 |
   ||||

1. 完成后，请选择“确定”。

   此时“合作伙伴”列表中将显示合作伙伴。

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 在集成帐户菜单的“设置”下，选择“合作伙伴”。

1. 在“合作伙伴”窗格中，选择合作伙伴并选择“编辑”，然后进行更改。 

   对于使用 RosettaNet 的合作伙伴，可以在“RosettaNet 合作伙伴属性”下提供下表中所述的详细信息：

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **合作伙伴分类** | 否 | 合作伙伴的组织类型 |
   | **供应链代码** | 否 | 合作伙伴的供应链代码，例如“信息技术”或“电子组件” |
   | **联系人姓名** | 否 | 合作伙伴的联系人姓名 |
   | **电子邮件** | 否 | 合作伙伴的电子邮件地址 |
   | **Fax** | 否 | 合作伙伴的传真号码 |
   | **电话** | 否 | 合作伙伴的电话号码 |
   ||||

1. 完成后，请选择“确定”。

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 在集成帐户菜单的“设置”下，选择“合作伙伴”。

1. 在“合作伙伴”窗格中，选择要删除的合作伙伴，然后选择“删除” 。

1. 选择“是”以确认要删除该合作伙伴。

## <a name="next-steps"></a>后续步骤

* [添加合作伙伴之间的协议](logic-apps-enterprise-integration-agreements.md)