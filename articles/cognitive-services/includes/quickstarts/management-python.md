---
title: 快速入门：适用于 Python 的 Azure 管理客户端库
description: 在本快速入门中，开始使用适用于 Python 的 Azure 管理客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 4a44e65a4cda79a07c3fb94231945aa1dd1654e3
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442498"
---
[参考文档](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [包 (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Python 先决条件

* 一个有效的 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* [Python 3.x](https://www.python.org/)
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建新的 Python 应用程序，并在控制台窗口中导航到你的项目。

### <a name="install-the-client-library"></a>安装客户端库

可使用以下方式安装客户端库：

```console
pip install azure-mgmt-cognitiveservices
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

### <a name="import-libraries"></a>导入库

打开 Python 脚本并导入以下库。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>验证客户端

将以下字段添加到脚本的根目录中，并使用所创建的服务主体和 Azure 帐户信息填写其值。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

然后添加以下代码来构造 CognitiveServicesManagementClient 对象。 所有 Azure 管理操作都需要此对象。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>创建认知服务资源 (Python)

若要创建并订阅新的认知服务资源，请使用 Create 函数。 此函数向传入的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的种类，以及其定价层（或 SKU）和 Azure 位置。 下面的函数使用所有这些参数并创建资源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>选择服务和定价层

创建新资源时，需要知道要使用的服务的种类，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，将此信息和其他信息用作参数。 以下函数列出了可用的认知服务种类。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>查看资源

若要查看 Azure 帐户下的所有资源（跨所有资源组），请使用以下函数：

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>删除资源

下面的函数从给定的资源组中删除指定的资源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

如果需要恢复已删除的资源，请参阅[恢复已删除的认知服务资源](../../manage-resources.md)。

## <a name="call-management-functions"></a>调用管理函数

将以下代码添加到脚本底部，以调用上述函数。 此代码列出可用资源、创建示例资源、列出拥有的资源，然后删除示例资源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>运行应用程序

在命令行中使用 `python` 命令运行应用程序。

```console
python <your-script-name>.py
```

## <a name="see-also"></a>另请参阅

* 有关如何安全地使用认知服务的说明，请参阅 **[对 Azure 认知服务的请求进行身份验证](../../authentication.md)** 。
* 请参阅 **[什么是 Azure 认知服务？](../../what-are-cognitive-services.md)** ，以获取认知服务中不同类别的列表。
* 若要查看认知服务支持的自然语言列表，请参阅 **[自然语言支持](../../language-support.md)** 。
* 请参阅 **[使用认知服务作为容器](../../cognitive-services-container-support.md)** 以了解如何使用本地认知服务。
* 请参阅 **[计划和管理认知服务的成本](../../plan-manage-costs.md)** ，以估计使用认知服务的成本。
* 有关管理 SDK 的更多详细信息，请参阅 [Azure 管理 SDK 参考文档](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)。
