---
title: 快速入门：使用 Python 创建管理组
description: 在本快速入门中，你将使用 Python 创建一个管理组，来将资源整理到资源层次结构中。
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: ec4b66033093d6fa56e2bb5bce0f9d0fedf0a7e5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324947"
---
# <a name="quickstart-create-a-management-group-with-python"></a>快速入门：使用 Python 创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组将成为根管理组的子级或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)，并将为创建者分配“所有者”角色。 管理组服务允许此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>添加 Resource Graph 库

若要使用 Python 来管理管理组，必须添加库。 此库适用于可使用 Python 的任何情况，包括 [Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 请确保安装最新的 Python（至少为 3.8）。 如果尚未安装，请在 [Python.org](https://www.python.org/downloads/) 下载。

1. 请确保安装最新的 Azure CLI（至少为 2.5.1）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 在下面的示例中需要使用 Azure CLI，这样 Python 才能使用基于 CLI 的身份验证。 有关其他选项的信息，请参阅[使用适用于 Python 的 Azure 管理库进行身份验证](/azure/developer/python/azure-sdk-authenticate)。

1. 通过 Azure CLI 进行身份验证。

   ```azurecli
   az login
   ```

1. 在你选择的 Python 环境中，请安装管理组所需的库：

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 如果已为所有用户安装了 Python，则必须从提升的控制台运行这些命令。

1. 验证是否已安装这些库。 `azure-mgmt-managementgroups` 应为 0.2.0 版或更高版本，`azure-mgmt-resource` 应为 9.0.0 版或更高版本，`azure-cli-core` 应为 2.5.0 版或更高版本  。

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>创建管理组

1. 创建 Python 脚本并将以下源保存为 `mgCreate.py`：

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI

   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))

       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}

       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)

       # Show results
       print(mg)

   createmanagementgroup("MyNewMG")
   ```

1. 使用 `az login` 向 Azure CLI 进行身份验证。

1. 在终端中输入以下命令：

   ```bash
   py mgCreate.py
   ```

创建管理组会以 `LROPoller` 对象的形式向控制台输入内容。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Python 环境中删除已安装的库，可使用以下命令执行此操作：

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

要详细了解管理组以及如何管理资源层次结构，请继续执行以下操作：

> [!div class="nextstepaction"]
> [使用管理组管理资源](./manage.md)
