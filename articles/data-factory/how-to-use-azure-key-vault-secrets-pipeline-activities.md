---
title: 在管道活动中使用 Azure Key Vault 机密
description: 了解如何从 Azure Key Vault 提取存储的凭据，并在数据工厂管道运行期间使用这些凭据。
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b94ceb3bc190f57e3e5190e89e1018fc8a6e77f6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831144"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管道活动中使用 Azure Key Vault 机密

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

可以在 Azure Key Vault 中存储凭据或机密值，并在管道执行过程中使用它们来传递到活动。

## <a name="prerequisites"></a>先决条件

此功能依赖于数据工厂托管标识。  从[数据工厂的托管标识](./data-factory-service-identity.md)了解其工作原理，并确保数据工厂具有一个关联的托管标识。

## <a name="steps"></a>步骤

1. 打开数据工厂的属性，然后复制“托管标识应用程序 ID”值。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="托管标识值":::

2. 打开密钥保管库访问策略，并添加托管标识权限以获取和列出机密。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="屏幕截图显示&quot;&quot;&quot;&quot;“访问策略”页，其中突出显示了“添加访问策略”操作。":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="Key Vault 访问策略":::

    单击“添加”，再单击“保存”。

3. 导航到 Key Vault 机密并复制机密标识符。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="机密标识符":::

    记下要在数据工厂管道运行期间获取的机密 URI。

4. 在数据工厂管道中，添加新的 Web 活动，并按如下所示对其进行配置。  

    |属性  |值  |
    |---------|---------|
    |安全输出     |正确         |
    |URL     |[Your secret URI value]?api-version=7.0         |
    |方法     |GET         |
    |身份验证     |MSI         |
    |资源        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="Web 活动":::

    > [!IMPORTANT]
    > 必须将“？api version=7.0” 添加到机密 URI 的末尾。  

    > [!CAUTION]
    > 将“安全输出”选项设置为 true，以防止机密值以纯文本格式记录。  使用此值的任何其他活动都应将其“安全输入”选项设置为 true。

5. 若要使用另一个活动中的值，请使用以下代码表达式“@activity('Web1').output.value”。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="代码表达式":::

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Azure Key Vault 存储数据存储和计算的凭据，请参阅 [在 Azure Key Vault 中存储凭据](./store-credentials-in-key-vault.md)