---
title: 在 GitHub Actions 中使用密钥保管库对 Azure Spring Cloud 进行身份验证
description: 如何在 GitHub Actions 中结合使用密钥保管库与 Azure Spring Cloud 的 CI/CD 工作流
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: ca57e3a659d92faa3fd38c233c68067bfe0fcbd2
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015342"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>在 GitHub Actions 中使用密钥保管库对 Azure Spring Cloud 进行身份验证

本文适用于：✔️ Java ✔️ C#

密钥保管库是存储密钥的安全位置。 企业用户需要在其控制的作用域内存储 CI/CD 环境的凭据。 用于在密钥保管库中获取凭据的密钥应限制到资源作用域。  它只能访问密钥保管库作用域，不能访问整个 Azure 作用域。 它类似于只能打开保险柜的钥匙，而不是可以打开建筑物内所有门的万能钥匙。 这是一种使用一个密钥获取另一个密钥的方法，适用于 CICD 工作流。

## <a name="generate-credential"></a>生成凭据

若要生成用于访问密钥保管库的密钥，请在本地计算机上执行以下命令：

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```

`--scopes` 参数指定的作用域限制了对资源的密钥访问。  它只能访问保险柜。

结果如下：

```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```

然后，按[设置 GitHub 存储库并进行 Azure 身份验证](./how-to-github-actions.md#set-up-github-repository-and-authenticate)中所述，将结果保存到 GitHub 机密。

## <a name="add-access-policies-for-the-credential"></a>添加凭据的访问策略

在上文中创建的凭据只能获取密钥保管库的一般信息，不能获取其存储的内容。  若要获取密钥保管库中存储的机密，需设置凭据的访问策略。

转到 Microsoft Azure 门户中的“密钥保管库”仪表板，选择“访问控制”菜单，然后打开“角色分配”选项卡。为“类型”选择“应用”，并为“作用域”选择`This resource`。  应看到在上一步中创建的凭据：

![设置访问策略](./media/github-actions/key-vault1.png)

复制凭据名称，例如 `azure-cli-2020-01-19-04-39-02`。 打开“访问策略”菜单，然后选择“添加访问策略”链接。  为“模板”选择 `Secret Management`，然后选择“主体” 。 将凭据名称粘贴到“主体”/“选择”输入框 ：

![Select](./media/github-actions/key-vault2.png)

选择“添加访问策略”对话框中的“添加”按钮，然后选择“保存”。

## <a name="generate-full-scope-azure-credential"></a>生成全作用域 Azure 凭据

这是打开建筑物内所有门的万能钥匙。 该过程类似于上一步，但这里我们要更改作用域以生成主密钥：

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同样，结果如下：

```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```

复制整个 JSON 字符串。  返回到“密钥保管库”仪表板。 打开“机密”菜单，然后选择“生成/导入”按钮。 输入机密名称，如 `AZURE-CREDENTIALS-FOR-SPRING`。 将 JSON 凭据字符串粘贴到“值”输入框。 你可能会注意到，值输入框是单行文本字段，而不是多行文本区域。  可以在其中粘贴完整的 JSON 字符串。

![完整作用域凭据](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>合并 GitHub Actions 中的凭据

设置 CICD 管道执行时使用的凭据：

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>后续步骤

* [Spring Cloud GitHub Actions](./how-to-github-actions.md)
