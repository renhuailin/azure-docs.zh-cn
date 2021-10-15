---
title: 添加证书以在工作流中保护 B2B 消息
description: 将证书添加到集成帐户，以使用 Azure 逻辑应用和 Enterprise Integration Pack 保护工作流中的 B2B 消息。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: b68bc9f1f77c6ad5b60cd5b2ea9c4b644acb4c5b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361044"
---
# <a name="add-certificates-to-integration-accounts-for-securing-messages-in-workflows-with-azure-logic-apps"></a>将证书添加到集成帐户，以使用 Azure 逻辑应用在工作流中保护消息

当需要在逻辑应用企业对企业 (B2B) 工作流中交换机密消息时，可以使用证书提高此通信的安全性。 证书是一种数字文档，可帮助以下列方式保护通信：

* 检查参与者在电子通信中的标识。

* 对消息内容进行加密。

* 对消息进行数字签名。

可以在工作流中使用以下证书类型：

* [公用证书](https://en.wikipedia.org/wiki/Public_key_certificate)，必须从公用 Internet [证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 购买。 这些证书不需要任何密钥。

* 专用证书或 [*自签名证书*](https://en.wikipedia.org/wiki/Self-signed_certificate)，由你自己创建并颁发。 但是，这些证书需要私钥。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用（消耗）”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，必须[将集成帐户链接到逻辑应用资源](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

    若要创建和添加要在“逻辑应用（消耗）”工作流中使用的证书，尚不需要逻辑应用资源。 但是，当你准备好在工作流中使用这些证书时，逻辑应用资源需有一个用于存储这些证书的链接集成帐户。

  * 如果使用的是[“逻辑应用（标准）”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户无需一个指向逻辑应用资源的链接，但仍需存储其他项目，例如合作伙伴、协议和证书，以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

    > [!NOTE]
    > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

* 对于专用证书，必须满足以下先决条件：

  * 在 [Azure Key Vault](../key-vault/general/overview.md) 中添加私钥并提供密钥名称。 有关详细信息，请参阅[将私钥添加到 Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)。

  * 授权 Azure 逻辑应用服务对密钥保管库执行操作。 若要向逻辑应用服务主体授予访问权限，请使用 PowerShell 命令 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)，例如：

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * 向密钥保管库中[添加对应的公用证书](#add-public-certificate)。 此证书显示在[协议的“发送”和“接收”设置中，用于对消息进行签名和加密](logic-apps-enterprise-integration-agreements.md)。  例如，查看 [Azure 逻辑应用中 AS2 消息设置的参考](logic-apps-enterprise-integration-as2-message-settings.md)。

* 在你的集成账户中，至少添加两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)以及一份[各合作伙伴之间达成的协议](logic-apps-enterprise-integration-agreements.md)。 协议都需要有主持人合作伙伴和来宾合作伙伴。 此外，协议要求两个合作伙伴都使用相同或兼容的业务标识限定符，此标识符适用于 AS2、X12、EDIFACT 或 RosettaNet 协议。

* （可选）要使用证书的逻辑应用资源和工作流。 工作流需要任意能够启动逻辑应用工作流的触发器。 如果之前尚未创建逻辑应用工作流，请查看[快速入门：创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>添加公用证书

要在工作流中使用公用证书，必须先将该证书添加到集成帐户。

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加证书的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“证书”。

1. 在“证书”窗格中，选择“添加”。 

1. 在“添加证书”窗格中，提供以下有关证书的信息：

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*证书名称*> | 你的证书的名称，在本例中为 `publicCert` |
   | **证书类型** | 是 | **Public** | 你的证书的类型 |
   | **证书** | 是 | <*证书文件名*> | 要浏览要添加的证书文件，请选择“证书”框旁边的文件夹图标。 |
   |||||

   ![该屏幕截图显示了 Azure 门户和集成帐户（已选中了“添加”）以及“添加证书”窗格（包含公用证书详细信息）。](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. 完成后，请选择“确定”。

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![该屏幕截图显示了 Azure 门户和集成帐户（包含“证书”列表中的公用证书）。](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>添加专用证书

要在工作流中使用专用证书，必须先将该证书添加到集成帐户。 请确保还满足[专用证书的先决条件](#prerequisites)。

1. 在 [Azure 门户](https://portal.azure.com)搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 在“集成帐户”下，选择要添加证书的集成帐户。

1. 在集成帐户菜单的“设置”下，选择“证书”。

1. 在“证书”窗格中，选择“添加”。 

1. 在“添加证书”窗格中，提供以下有关证书的信息：

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*证书名称*> | 你的证书的名称，在本例中为 `privateCert` |
   | **证书类型** | 是 | **专用** | 你的证书的类型 |
   | **证书** | 是 | <*证书文件名*> | 要浏览要添加的证书文件，请选择“证书”框旁边的文件夹图标。 在包含私钥的密钥保管库中，你添加的文件是公用证书。 |
   | **资源组** | 是 | <*集成帐户资源组*> | 你的集成帐户的资源组，在本例中为 `Integration-Account-RG` |
   | **密钥保管库** | 是 | <*密钥保管库名称*> | 你的密钥保管库名称 |
   | **密钥名称** | 是 | <*key-name*> | 你的密钥名称 |
   |||||

   ![该屏幕截图显示了 Azure 门户和集成帐户（已选中了“添加”）以及“添加证书”窗格（包含专用证书详细信息）。](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. 完成后，请选择“确定”。

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![该屏幕截图显示了 Azure 门户和集成帐户（包含“证书”列表中的专用证书）。](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>后续步骤

* [交换 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [交换 X12 消息](logic-apps-enterprise-integration-x12.md)
* [交换 RosettaNet 消息](logic-apps-enterprise-integration-rosettanet.md)
