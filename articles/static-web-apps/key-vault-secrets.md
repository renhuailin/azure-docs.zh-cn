---
title: 保护 Azure Key Vault 中的身份验证机密
description: 使用托管标识保护 Azure Key Vault 中的身份验证机密。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: 3f373122d6d68e6435cfe7083115c91718e05a09
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737471"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>保护 Azure Key Vault 中的身份验证机密

配置自定义身份验证提供程序时，可能需要将连接机密存储在 Azure Key Vault 中。 本文演示了如何使用托管标识来授予 Azure Static Web Apps 访问 Key Vault 的权限以获取自定义身份验证机密。

安全机密要求准备好以下项。

- 在 Static Web Apps 实例中创建系统分配的标识。
- 将访问 Key Vault 机密的权限授予标识。
- 从 Static Web Apps 应用程序设置引用 Key Vault 机密。

本文演示如何在生产中为[自带函数应用程序](./functions-bring-your-own.md)设置每一项。

Key Vault 集成不适用于：

- [静态 Web 应用的过渡版本](./review-publish-pull-requests.md)。 Key Vault 集成仅在生产环境中受支持。
- [使用托管函数的静态 Web 应用](./apis.md)。 

## <a name="prerequisites"></a>先决条件

- 使用[自带函数](./functions-bring-your-own.md)的现有 Azure Static Web Apps 站点。
- 现有带机密值的 Key Vault 资源。

## <a name="create-identity"></a>创建标识

1. 在 Azure 门户中打开 Static Web Apps 站点。

1. 在“设置”菜单下，选择“标识”。

1. 选择“系统分配”选项卡。

1. 在“状态”标签下，选择“开”按钮。

1. 选择“保存”按钮。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="添加系统分配的标识":::

1. 出现确认对话框时，选择“是”按钮。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="确认标识分配。":::

现在可以添加访问策略，以允许静态 web 应用读取 Key Vault 机密。

## <a name="add-a-key-vault-access-policy"></a>添加 Key Vault 访问策略

1. 在 Azure 门户中打开 Key Vault 资源。

1. 在“设置”菜单下，选择“访问策略”。

1. 选择链接，“添加访问策略”。

1. 从“机密权限”下拉列表中，选择“获取”。

1. 下一步至“选择主体”标签，选择“未选择任何项”链接。

1. 在搜索框中，搜索 Static Web Apps 应用程序名称。

1. 选择与应用程序名称匹配的列表项。

1. 选择“选择”按钮  。

1. 选择“添加”按钮。

1. 选择“保存”按钮。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="保存 Key Vault 访问策略":::

访问策略现已保存到 Key Vault。 接下来，将静态 web 应用关联到 Key Vault 资源时，访问要使用的机密 URI。

1. 在“设置”菜单下，选择“机密”。

1. 从列表中选择所需机密。

1. 从列表中选择所需机密版本。

1. 选择“机密标识符”文本框末尾“复制按钮”，将机密 URI 值复制到剪贴板。

1. 将此值粘贴到文本编辑器中以供以后使用。

## <a name="add-application-setting"></a>添加应用程序设置

1. 在 Azure 门户中打开 Static Web Apps 站点。

1. 在“设置”菜单下，选择“配置”。

1. 在“应用程序设置”部分下，选择“添加”按钮。

1. 在“名称”字段的文本框中输入名称。

1. 在“值”字段的文本框中确定机密值。

    机密值是几个不同值的组合。 以下模板演示了如何生成最终字符串。

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    使用以下步骤来生成完整的机密值。

1. 复制上面的模板，并将其粘贴到文本编辑器中。

1. 将 `<YOUR-KEY-VAULT-SECRET-URI>` 替换为前面先设置的 Key Vault URI 值。

1. 复制新的完整字符串值。

1. 将该值粘贴到“值”字段的文本框中。

1. 选择“确定”按钮。

1. 选择“应用程序设置”工具栏顶部的“保存”按钮。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="保存应用程序设置":::

现在，当自定义身份验证配置引用新创建的应用程序设置时，将使用静态 web 应用的标识从 Azure Key Vault 提取值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [自定义身份验证](./authentication-custom.md)
