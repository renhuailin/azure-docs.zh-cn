---
title: 媒体服务 Python 基本编码快速入门
description: 本快速入门演示如何通过 Python 和 Azure 媒体服务进行基本编码。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 7/2/2021
ms.author: inhenkel
ms.openlocfilehash: a0534bc562d20f96cc1c12a8b4dbe0adfc8f9282
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284418"
---
# <a name="media-services-basic-encoding-with-python"></a>使用 Python 的媒体服务基本编码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>简介

本快速入门演示如何通过 Python 和 Azure 媒体服务进行基本编码。 它使用 2020-05-01 媒体服务 v3 API。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 创建一个资源组以用于此快速入门。
- 创建媒体服务 v3 帐户。
- 获取存储帐户密钥。
- 创建服务主体和密钥。

## <a name="get-the-sample"></a>获取示例

创建一个分支，并克隆位于 [Python 示例存储库](https://github.com/Azure-Samples/media-services-v3-python)中的示例。 在本快速入门中，我们将使用 BasicEncoding 示例。

## <a name="create-the-env-file"></a>创建 .env 文件

从你的帐户获取值以创建 .env 文件。 就是这样，保存时不带名称，只带扩展名。  使用 sample.env 作为模板，然后将 .env 文件保存到本地克隆中的 BasicEncoder 文件夹 。

## <a name="use-python-virtual-environments"></a>使用 Python 虚拟环境
对于示例，我们建议始终使用以下步骤创建并激活一个 Python 虚拟环境：

1. 在 VSCode 或其他编辑器中打开示例文件夹
2. 创建虚拟环境

    ``` bash
      # py -3 uses the global python interpreter. You can also use python -m venv .venv.
      py -3 -m venv .venv
    ```

   此命令运行 Python venv 模块，并在名为 .venv 的文件夹中创建虚拟环境。

3. 激活虚拟环境：

    ``` bash
      .venv\scripts\activate
    ```

  虚拟环境是项目内的一个文件夹，用于隔离特定 Python 解释器的副本。 激活该环境（Visual Studio Code 自动执行该操作）后，运行 pip install 只会将某个库安装到该环境中。 然后，在运行 Python 代码时，它将在环境的确切上下文中运行，且每个库都有特定的版本。 运行 pip freeze 时，将获取这些库的确切列表。 （在许多示例中，你将为所需的库创建 requirements.txt 文件，然后运行 pip install -r requirements.txt。 将代码部署到 Azure 时，通常需要一个 requirements 文件。）

## <a name="set-up"></a>设置

[为 Azure 设置并配置本地 Python 开发环境](/azure/developer/python/configure-local-development-environment)

安装适用于 Python 的 azure-identity 库。 Azure Active Directory 身份验证需要此模块。 请参阅[适用于 Python 的 Azure 标识客户端库](/python/api/overview/azure/identity-readme#environment-variables)中的详细信息

  ``` bash
  pip install azure-identity
  ```

安装适用于 [Azure 媒体服务](/python/api/overview/azure/media-services)的 Python SDK

在以下链接中可以找到媒体服务 Python SDK 的 Pypi 页以及最新版本详细信息：[azure-mgmt-media](https://pypi.org/project/azure-mgmt-media/)

  ``` bash
  pip install azure-mgmt-media
  ```

安装[适用于 Python 的 Azure 存储 SDK](https://pypi.org/project/azure-storage-blob/)

  ``` bash
  pip install azure-storage-blob
  ```

可以选择性地使用 samples 文件夹中的“requirements.txt”文件，为给定的示例安装所有必备组件

  ``` bash
  pip install -r requirements.txt
  ```

## <a name="try-the-code"></a>尝试代码

下面的代码进行了全面注释。  使用整个脚本或将其部分用于自己的脚本。

在此示例中，将生成一个随机数用于命名操作，以便可以将命名对象标识为运行脚本时一起创建的组。  随机数字是可选的，可以在完成脚本测试后将其删除。

在此示例中，我们未将 SAS URL 用于输入资产。

[!code-python[Main](../../../media-services-v3-python/BasicEncoding/basic-encoding.py)]

## <a name="delete-resources"></a>删除资源

完成此快速入门后，请删除资源组中创建的资源。

## <a name="next-steps"></a>后续步骤

熟悉[媒体服务 Python SDK](/python/api/azure-mgmt-media/)

## <a name="resources"></a>资源

- 参阅 Azure 媒体服务[管理 API](/python/api/overview/azure/mediaservices/management)。
- 了解如何[将存储 API 与 Python 配合使用](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd)
- 详细了解[适用于 Python 的 Azure 标识客户端库](/python/api/overview/azure/identity-readme#environment-variables)
- 详细了解 [Azure 媒体服务 v3](./media-services-overview.md)。
- 了解 [Azure Python SDK](/azure/developer/python)
- 详细了解 [Azure Python SDK 的使用模式](/azure/developer/python/azure-sdk-library-usage-patterns)
- 在 [Azure Python SDK 索引](/azure/developer/python/azure-sdk-library-package-index)中查找更多 Azure Python SDK
- [Azure 存储 Blob Python SDK 参考](/python/api/azure-storage-blob/)