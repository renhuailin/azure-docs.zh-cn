---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967110"
---
1. 使用 `az group create` 命令以创建资源组：

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    如果愿意，你可以将“eastus”更改为离你更近的位置。

1. 使用 `az keyvault create` 创建密钥保管库：

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    将 `<your-unique-keyvault-name>` 替换为在整个 Azure 中均唯一的名称。 通常使用个人或公司名称以及其他数字和标识符。 

1. 创建用于向代码提供 Key Vault 名称的环境变量：

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```