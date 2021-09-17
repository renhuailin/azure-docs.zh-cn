---
title: 将 Azure 应用配置与 Python 应用结合使用的快速入门 | Microsoft Docs
description: 在本快速入门中，使用 Azure 应用程序配置创建 Python 应用，集中存储和管理与代码分离的应用程序设置。
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 36b840cb851ef544d0956ff0dec969ce251855d4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748757"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>快速入门：使用 Azure 应用程序配置创建 Python 应用

在本快速入门中，你将使用[适用于 Python 的应用程序配置客户端库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)通过 Azure 应用程序配置来集中存储和管理应用程序设置。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- Python 2.7、3.6 或更高版本 - 有关在 Windows 上设置 Python 的信息，请参阅 [Windows 上的 Python 文档]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 选择“配置资源管理器” > “创建” > “键-值”来添加以下键值对    ：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 |

    暂时将“标签”和“内容类型”保留为空   。

8. 选择“应用”。

## <a name="setting-up-the-python-app"></a>设置 Python 应用

1. 在本教程中，你将为名为 app-configuration-quickstart 的项目创建一个新目录。

    ```console
    mkdir app-configuration-quickstart
    ```

1. 切换到新创建的 app-configuration-quickstart 目录。

    ```console
    cd app-configuration-quickstart
    ```

1. 使用 `pip install` 命令安装 Azure 应用程序配置客户端库。

    ```console
    pip install azure-appconfiguration
    ```

1. 在 app-configuration-quickstart 目录中，创建名为 app-configuration-quickstart.py 的新文件，并添加以下代码 ：

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> 本快速入门中的代码片段将帮助你开始使用适用于 Python 的应用程序配置客户端库。 对于应用程序，你还应考虑根据需要处理异常。 若要详细了解异常处理，请参阅 [Python SDK 文档](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)。

## <a name="configure-your-app-configuration-connection-string"></a>配置应用程序配置连接字符串

1. 设置名为“AZURE_APP_CONFIG_CONNECTION_STRING”的环境变量，并将其设置为应用程序配置存储区的访问密钥。 在命令行中运行以下命令：

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. 重启命令提示符以使更改生效。 输出环境变量的值以验证其设置是否正确。

## <a name="code-samples"></a>代码示例

本部分中的示例代码片段演示如何通过适用于 Python 的应用程序配置客户端库执行常见操作。 将这些代码片段添加到之前创建的 app-configuration-quickstart.py 文件中的 `try` 块。

> [!NOTE]
> 应用程序配置客户端库将键-值对象称为 `ConfigurationSetting`。 因此在本文中，应用程序配置存储区中的“键-值”将被称为为“配置设置” 。

* [连接到应用程序配置存储区](#connect-to-an-app-configuration-store)
* [获取配置设置](#get-a-configuration-setting)
* [添加配置设置](#add-a-configuration-setting)
* [获取配置设置列表](#get-a-list-of-configuration-settings)
* [锁定配置设置](#lock-a-configuration-setting)
* [解锁配置设置](#unlock-a-configuration-setting)
* [更新配置设置](#update-a-configuration-setting)
* [删除配置设置](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区

以下代码片段使用环境变量中存储的连接字符串创建 AzureAppConfigurationClient 实例。

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>获取配置设置

以下代码片段按 `key` 名称检索配置设置。

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>添加配置设置

以下代码片段使用 `key` 和 `value` 字段创建 `ConfigurationSetting` 对象，并调用 `add_configuration_setting` 方法。 如果尝试添加存储区中已存在的配置设置，此方法将引发异常。 如果要避免此异常，可以改为使用 [set_configuration_setting](#update-a-configuration-setting) 方法。

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>获取配置设置列表

以下代码片段将检索配置设置列表。 可提供 `key_filter` 和 `label_filter` 参数，分别基于 `key` 和 `label` 筛选键-值。 有关筛选的详细信息，请参阅如何[查询配置设置](./concept-key-value.md#query-key-values)。

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>锁定配置设置

应用程序配置中键-值的锁定状态由 `ConfigurationSetting` 对象的 `read_only` 属性表示。 如果 `read_only` 为 `True`，则该设置为锁定状态。 可使用 `read_only=True` 参数调用 `set_read_only` 方法来锁定配置设置。

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>解锁配置设置

如果 `ConfigurationSetting` 的 `read_only` 属性为 `False`，则该设置已解锁。 可使用 `read_only=False` 参数调用 `set_read_only` 方法来解锁配置设置。

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>更新配置设置

`set_configuration_setting` 方法可用于更新现有设置或创建新的设置。 以下代码片段可更改现有配置设置的值。

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>删除配置设置

以下代码片段按 `key` 名称删除配置设置。

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>运行应用

在本快速入门中，你创建了一个 Python 应用，它使用 Azure 应用程序配置客户端库来检索通过 Azure 门户创建的配置设置、添加新设置、检索现有设置列表、锁定和解锁设置、更新设置和最终删除设置。

此时，app-configuration-quickstart.py 文件应具有以下代码：

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

在控制台窗口中，导航到包含 app-configuration-quickstart.py 文件的目录，然后执行以下 python 命令来运行应用：

```console
python app-configuration-quickstart.py
```

应该会看到以下输出：

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>清理资源


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了新的应用配置存储区，并了解了如何从 Python 应用访问键/值。

有关其他代码示例，请访问：

> [!div class="nextstepaction"]
> [Azure 应用程序配置客户端库示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
