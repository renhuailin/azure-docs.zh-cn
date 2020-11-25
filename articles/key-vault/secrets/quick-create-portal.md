---
title: Azure 快速入门 - 使用 Azure 门户在 Key Vault 中设置和检索机密 | Microsoft Docs
description: 快速入门介绍如何使用 Azure 门户在 Azure Key Vault 中设置和检索机密
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 212e5fb62043c2ffe2b8876249a6aad1d224411d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685845"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密

Azure Key Vault 是一项云服务，它为机密提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 在本快速入门中，你将创建一个 Key Vault 并使用它来存储机密。 

有关详细信息，请参阅 
- [Key Vault 概述](../general/overview.md)
- [机密概述](about-secrets.md)。

## <a name="prerequisites"></a>先决条件

若要访问 Azure Key Vault，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

对机密的所有访问都通过 Azure Key Vault 进行。 对于本快速入门，请使用 [Azure 门户](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 创建密钥保管库。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

若要将机密添加到保管库，请执行以下步骤：

1. 在 Azure 门户中，导航到新的密钥保管库
1. 在 Key Vault 设置页中，选择“机密”。
1. 单击“生成/导入”。
1. 在“创建机密”屏幕上，选择以下值：
    - **上传选项**：手动。
    - **名称**：键入机密的名称。 机密名称在 Key Vault 中必须是唯一的。 该名称必须是 1-127 个字符的字符串，以字母开头且仅包含 0-9、a-z、A-Z 和 -。 有关命名的详细信息，请参阅 [Key Vault 对象、标识符和版本控制](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning)
    - **值**：键入机密的值。 Key Vault API 接受机密值并将其作为字符串返回。 
    - 让其他值保留默认设置。 单击“创建”。

收到机密已成功创建的消息后，即可单击列表中的该机密， 

有关机密属性的详细信息，请参阅[关于 Azure Key Vault 机密](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets)

## <a name="retrieve-a-secret-from-key-vault"></a>从 Key Vault 检索机密

如果单击当前版本，则可看到在上一步指定的值。

![机密属性](../media/quick-create-portal/current-version-hidden.png)

单击右侧窗格中的“显示机密值”按钮后，可看到隐藏的值。 

![显示的机密值](../media/quick-create-portal/current-version-shown.png)

你还可以使用 [Azure CLI]() 或 [Azure PowerShell]() 来检索之前创建的机密。

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除” 。

> [!NOTE]
> 请务必注意，删除机密、密钥、证书或密钥保管库后，它将在时长为 7 到 90 个日历日的可配置期间内保持可恢复状态。 如果未指定配置，默认恢复期将会被设置为 90 天。 这样，用户就有充足的时间来注意到意外的机密删除并做出响应。 有关删除和恢复密钥保管库和密钥保管库对象的详细信息，请参阅 [Azure Key Vault 软删除概述](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)

## <a name="next-steps"></a>后续步骤

在本快速入门中，创建了 Key Vault 并在其中存储了一个机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读[保护对 Key Vault 的访问](../general/secure-your-key-vault.md)
- 参阅[将 Key Vault 与应用服务 Web 应用结合使用](../general/tutorial-net-create-vault-azure-web-app.md)
- 参阅[将 Key Vault 与部署到 VM 的应用程序结合使用](../general/tutorial-net-virtual-machine.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
