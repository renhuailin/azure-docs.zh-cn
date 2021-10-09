---
title: 快速入门：使用 Python 创建 Purview 帐户
description: 使用 Python 创建 Azure Purview 帐户。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 7a6d13da2ee3138e6dfc5eca4a5b75f454f90457
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212470"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>快速入门：使用 Python 创建 Purview 帐户

在本快速入门中，使用 Python 以编程方式创建一个 Purview 帐户。 可以使用 [Purview 的 Python 参考](/python/api/azure-mgmt-purview/)，但本文将指导你完成使用 Python 创建帐户所需的所有步骤。

Azure Purview 是一种数据管理服务，可帮助你管理和治理数据环境。 通过跨本地源、多云源和服务型软件 (SaaS) 源连接到数据，Purview 创建了最新的信息地图。 它对敏感数据进行标识和分类，并提供端到端的沿袭。 数据使用者能够发现整个组织的数据，而数据管理员能够审核数据、保护数据和确保对你的数据的正确使用。

有关 Purview 的详细信息，[请参阅概述页](overview.md)。 有关在组织中部署 Purview 的详细信息，[请参阅部署最佳做法](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

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
    > 在某些常见依赖关系上，“azure-identity”包可能与“azure-cli”冲突。 如果遇到任何身份验证问题，请删除“azure-cli”及其依赖关系，或使用未安装“azure-cli”包的初始状态计算机。

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

2. 将以下代码添加到“Main”方法，此方法可创建 PurviewManagementClient 类的实例。 使用此对象创建 Purview 帐户、删除 Purview 帐户、检查名称可用性，以及执行其他资源提供程序操作。

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

生成并启动应用程序。 控制台将打印 Purview 帐户创建的进度。 等待它完成。
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

若要删除 Purview 帐户，请向程序添加以下代码，然后运行：

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>后续步骤

本教程中的代码创建和删除 Purview 帐户。 你现可下载 python SDK，并了解你可以对 Purview 帐户执行的其他资源提供程序操作。

请按照接下来的这些文章学习如何导航 Purview Studio、创建集合以及授予对 Purview 的访问权限。

* [如何使用 Purview Studio](use-purview-studio.md)
* [创建集合](quickstart-create-collection.md)
* [将用户添加到 Azure Purview 帐户](catalog-permissions.md)