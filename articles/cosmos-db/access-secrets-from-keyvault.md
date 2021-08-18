---
title: 使用 Key Vault 存储和访问 Azure Cosmos DB 密钥
description: 使用 Azure Key Vault 来存储和访问 Azure Cosmos DB 连接字符串、密钥、终结点。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 6d09fb5f728ec430991f487b8b4acba937f7262c
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355782"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>使用 Azure Key Vault 保护 Azure Cosmos 密钥 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> 访问 Azure Cosmos DB 密钥的建议解决方案是使用[系统分配的托管标识](managed-identity-based-authentication.md)。 如果服务无法利用托管标识，则使用[基于证书的解决方案](certificate-based-authentication.md)。 如果托管标识解决方案和基于证书的解决方案都不能满足你的需求，请使用下面的密钥保管库解决方案。

对应用程序使用 Azure Cosmos DB 时，可以使用应用配置文件中的终结点和密钥来访问数据库、集合、文档。  但是，将密钥和 URL 直接放在应用程序代码中并不安全，因为它们将以明文格式向所有用户提供。 应确保通过一种安全机制提供终结点和密钥。 这正是 Azure Key Vault 的作用所在，它能够安全地存储和管理应用程序机密。

需要执行以下步骤才能在 Key Vault 中存储以及从中读取 Azure Cosmos DB 访问密钥：

* 创建密钥保管库  
* 将 Azure Cosmos DB 访问密钥添加到 Key Vault  
* 创建 Azure Web 应用程序  
* 注册应用程序，并授予读取 Key Vault 的权限  


## <a name="create-a-key-vault"></a>创建密钥保管库

1. 登录到 [Azure 门户](https://portal.azure.com/)。  
2. 选择“创建资源”>“安全性”>“Key Vault”。   
3. 在“创建密钥保管库”部分，提供以下信息：   
   * **名称：** 提供 Key Vault 的唯一名称。  
   * **订阅：** 选择要使用的订阅。  
   * 在“资源组”  下选择“新建”  ，然后输入资源组名称。  
   * 在“位置”下拉菜单中选择一个位置。  
   * 将其他选项保留默认值。  
4. 提供上述信息后，选择“创建”  。  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>将 Azure Cosmos DB 访问密钥添加到 Key Vault。
1. 导航到在上一步骤中创建的 Key Vault，打开“机密”选项卡。   
2. 选择“+生成/导入”。  

   * 为“上传选项”选择“手动”。  
   * 提供机密的 **名称**。
   * 在“值”字段中提供 Cosmos DB 帐户的连接字符串。  然后选择“创建”  。

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="创建机密":::

4. 创建机密后，将其打开并复制采用以下格式的 **机密标识符。 下一部分要用到此标识符。 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>创建 Azure Web 应用程序

1. 创建 Azure Web 应用程序，或者从 [GitHub 存储库](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo)下载应用。 它是一个简单的 MVC 应用程序。  

2. 解压缩已下载的应用程序，并打开 **HomeController.cs** 文件。 更新以下行中的机密 ID：

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **保存** 文件并 **生成** 解决方案。  
4. 接下来，将应用程序部署到 Azure。 右键单击项目并选择“发布”。  创建新的应用服务配置文件（可将应用命名为 WebAppKeyVault1），然后选择“发布”。    

5. 部署应用程序后， 在 Azure 门户中导航到已部署的 Web 应用程序，并启用此应用程序的“托管服务标识”。   

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="托管服务标识":::

如果现在就运行此应用程序，将会看到以下错误，因为尚未在 Key Vault 中向此应用程序授予任何权限。

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="已部署的无访问权限的应用":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>注册应用程序，并授予读取 Key Vault 的权限

在本部分，我们将应用程序注册到 Azure Active Directory，并向应用程序授予读取 Key Vault 的权限。 

1. 导航到 Azure 门户，打开在上一部分创建的 **Key Vault**。  

2. 打开“访问策略”，选择“+新增”，找到已部署的 Web 应用，选择权限，然后选择“确定”。     

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="添加访问策略":::

现在，如果运行此应用程序，即可从 Key Vault 读取机密。

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="使用机密部署的应用":::
 
同样，可以添加一个用户来访问 Key Vault。 需要通过选择“访问策略”  将自己添加到 Key Vault，然后授予从 Visual studio 运行此应用程序所需的全部权限。 从桌面运行此应用程序时，它会采用你的标识。

## <a name="next-steps"></a>后续步骤

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](how-to-configure-firewall.md)一文。
* 若要配置虚拟网络服务终结点，请参阅[使用 VNet 服务终结点保护访问](how-to-configure-vnet-service-endpoint.md)一文。
