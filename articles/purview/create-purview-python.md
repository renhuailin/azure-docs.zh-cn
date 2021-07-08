---
title: 快速入门：使用 Python 创建 Purview 帐户
description: 使用 Python 创建 Azure Purview 帐户。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: ec1f8890d9626ca1ed96f538bd464da7c30caf17
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983781"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>快速入门：使用 Python 创建 Purview 帐户

本快速入门介绍如何使用 Python 创建 Purview 帐户。 

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 你的帐户必须有权在订阅中创建资源

* 如果 Azure Policy 阻止所有应用程序创建存储帐户和 EventHub 命名空间，则需要使用标记设置策略例外，可以在创建 Purview 帐户的过程中输入此信息  。 主要原因是，对于创建的每个 Purview 帐户，它需要创建一个托管资源组，并在此资源组中创建存储帐户和 EventHub 命名空间。 有关详细信息，请参阅[创建目录门户](create-catalog-portal.md)


## <a name="install-the-python-package"></a>安装 Python 包

1. 使用管理员特权打开一个终端或命令提示符。 
2. 首先，安装 Azure 管理资源的 Python 包：

    ```python
    pip install azure-mgmt-resource
    ```
3. 若要为 Purview 安装 Python 包，请运行以下命令：

    ```python
    pip install azure-mgmt-purview
    ```

    [适用于 Purview 的 Python SDK](https://github.com/Azure/azure-sdk-for-python) 支持 Python 2.7、3.3、3.4、3.5、3.6 和 3.7。

4. 要为 Azure 标识身份验证安装 Python 包，请运行以下命令：

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > 在某些常见依赖关系上，“azure-identity”包可能与“azure-cli”冲突。 如果遇到任何身份验证问题，请删除“azure-cli”及其依赖关系，或使用未安装“azure-cli”包的初始状态计算机，以确保“azure-identity”包正常工作。
    
## <a name="create-a-purview-client"></a>创建 Purview 客户端

1. 创建 purview.py 文件。 添加以下语句来添加对命名空间的引用。

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. 将以下代码添加到“Main”方法，此方法可创建 PurviewManagementClient 类的实例。 你可以使用此对象创建 purview 帐户、删除 purview 帐户、检查名称可用性和其他资源提供程序操作。
 
    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>创建 Purview 帐户

1. 将以下代码添加到“Main”方法，此方法可创建 purview 帐户。 如果资源组已存在，请注释掉第一个 `create_or_update` 语句。

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
      ```

2. 现在添加以下语句，以便在运行程序时调用 **main** 方法：

   ```python
   # Start the main method
   main()
   ```

## <a name="full-script"></a>完整脚本

下面是完整的 Python 代码：

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>运行代码

生成并启动应用程序，然后验证管道执行。

控制台会输出数据工厂、链接服务、数据集、管道和管道运行的创建进度。 请等到出现包含数据读取/写入大小的复制活动运行详细信息。 然后，使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具检查 blob 是否已根据变量中的指定从“inputBlobPath”复制到“outputBlobPath”。

下面是示例输出：

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>验证输出

转到 Azure 门户的“Purview 帐户”页，并验证使用上述代码创建的帐户。 

## <a name="delete-purview-account"></a>删除 Purview 帐户

若要删除 Purview 帐户，请在计划中添加以下代码：

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>后续步骤

本教程中的代码创建和删除 Purview 帐户。 你现可下载 python SDK，并了解你可以对 Purview 帐户执行的其他资源提供程序操作。

请继续阅读下一篇文章，了解如何允许用户访问 Azure Purview 帐户。 

> [!div class="nextstepaction"]
> [将用户添加到 Azure Purview 帐户](catalog-permissions.md)
