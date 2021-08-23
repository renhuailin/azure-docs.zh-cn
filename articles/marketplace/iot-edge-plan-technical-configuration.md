---
title: 在 Azure 市场上为 IoT Edge 模块产品/服务设置计划技术配置
description: 在 Azure 市场上为 IoT Edge 模块产品/服务设置计划技术配置。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: acae63eff8be41453c4483a2d8364531ee109f71
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955704"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>为 IoT Edge 模块产品/服务设置计划技术配置

IoT Edge 模块产品/服务类型是在 IoT Edge 设备上运行的一种特定类型的容器。 通过计划的“技术配置”选项卡，你可以为 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)中的容器映像存储库提供参考信息，以及提供有助于客户使用模块的配置设置。

在你提交产品/服务后，IoT Edge 容器映像会被复制到 Azure 市场的特定公共容器注册表。 Azure 用户的所有使用模块的请求都是由 Azure 市场公共容器注册表（而不是专用容器注册表）提供服务。

可以定目标到多个平台，并使用标记提供模块容器映像的多个版本。 若要详细了解标记和版本控制，请参阅[准备 IoT Edge 模块技术资产](iot-edge-technical-asset.md)。

## <a name="image-repository-details"></a>映像存储库详细信息

选择“Azure 容器注册表”作为映像源。

提供用于报告资源使用情况以及为包含容器映像的 Azure 容器注册表进行服务计费的订阅 ID。 可以在 Azure 门户的[“订阅”页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此 ID。

提供包含具有容器映像的 Azure 容器注册表的 [Azure 资源组名称](../azure-resource-manager/management/manage-resource-groups-portal.md)。 资源组必须可以通过上面的订阅 ID 访问。 可以在 Azure 门户的[“资源组”](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到此名称。

提供包含容器映像的 [Azure 容器注册表名称](../container-registry/container-registry-intro.md)。 容器注册表必须位于你前面提供的 Azure 资源组中。 只提供注册表名称，而不提供完整的登录服务器名称。 从名称中省略 azurecr.io。 可以在 Azure 门户的[“容器注册表”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到注册表名称。

提供与包含容器映像的 Azure 容器注册表关联的 [Azure 容器注册表的管理员用户名](../container-registry/container-registry-authentication.md#admin-account)。 用户名和密码（下一步）必需提供，以确保公司有权访问注册表。 若要获取管理员用户名和密码，请使用 Azure 命令行接口 (CLI) 将“管理员已启用”属性设置为“True”。 在 Azure 门户中，可以视需要选择将“管理员用户”设置为“启用”。

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="展示了“更新容器注册表”对话框。":::

#### <a name="call-out-description"></a>调用说明

1. 管理员用户

<br>提供包含容器映像且与 Azure 容器注册表相关联的管理员用户的 Azure 容器注册表管理员密码。 用户名和密码是必需的，以确保公司有权访问注册表。 可以从 Azure 门户中获取密码，具体方法为依次转到“容器注册表” > “访问密钥”，或通过 Azure CLI 运行 [show 命令](/cli/azure/acr/credential#az-acr-credential-show)。

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="展示 Azure 门户中的“访问密钥”屏幕。":::

#### <a name="call-out-descriptions"></a>调用说明

1. 访问密钥
2. 用户名
3. 密码

提供包含映像的 Azure 容器注册表内的存储库名称。 可以在将映像推送到注册表时指定存储库名称。 若要查找存储库名称，可以依次转到[容器注册表](https://azure.microsoft.com/services/container-registry/) > “存储库”页。 有关详细信息，请参阅[在 Azure 门户中查看容器注册表存储库](../container-registry/container-registry-repositories.md)。 请注意，名称一经设置便不可更改。 请对你帐户中的每个产品/服务使用唯一名称。

> [!NOTE]
> 对于 Edge 模块认证，我们不支持加密的 Azure 容器注册表。 应在不启用加密的情况下创建 Azure 容器注册表。

## <a name="image-versions"></a>映像版本

当你发布更新时，客户必须能够从 Azure 市场自动获取更新。 如果客户不想更新，他们必须能够继续使用特定版本的映像。 为此，可以在每次更新映像时添加新的映像标记。

选择“添加映像版本”可在所有受支持平台上包含一个指向最新版映像的图像标记。 它还必须包含版本标记（例如，以 xx.xx.xx 开头，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)来将多个平台作为目标。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 所有清单标记（最新标记除外）必须以 X.Y- 或 X.Y.Z- 开头，其中，X、Y 和 Z 为整数。 例如，如果最新标记指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32` 和 `1.0.1-windows-arm32`，则需要在此字段中添加这六个标记。 若要详细了解标记和版本控制，请参阅[准备 IoT Edge 模块技术资产](iot-edge-technical-asset.md)。

> [!TIP]
> 向映像添加一个测试标记，以便在测试期间标识该映像。

## <a name="default-deployment-settings"></a>默认部署设置

定义最常见的设置来部署 IoT Edge 模块（可选）。 通过让客户使用这些默认设置以“开箱即用”的方式启动 IoT Edge 模块来优化客户部署。

默认路由。 IoT Edge 中心管理模块、IoT 中心与设备之间的通信。 可以为模块和 IoT 中心之间的数据输入和输出设置默认路由，这样就能灵活地将消息发送到所需的位置，不必使用其他服务来处理消息，也无需编写额外的代码。 路由是使用名称/值对构造而成的。 最多可以定义五个默认路由名称，每个名称最长可包含 512 个字符。

在路由值中使用正确的[路由语法](../iot-edge/module-composition.md#declare-routes)（通常定义为 FROM/message/* INTO $upstream）。 也就是说，任何模块发送的任何消息都发送到 IoT 中心。 若要引用模块，请使用它的默认模块名称，即不包含空格或特殊字符的产品/服务名称。 若要引用未知的其他模块，请使用 <FROM_MODULE_NAME> 约定来告知客户他们需要更新此信息。 若要详细了解 IoT Edge 路由，请参阅[声明路由](../iot-edge/module-composition.md#declare-routes)。

例如，如果模块 ContosoModule 侦听 ContosoInput 上的输入数据和 ContosoOutput 上的输出数据，最好定义以下两个默认路由：

- 名称 #1：ToContosoModule
- 值 #1：FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 名称 #2：FromContosoModuleToCloud
- 值 #2：FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

默认模块孪生所需属性。 模块孪生是 IoT 中心内的 JSON 文档，用于存储模块实例状态信息，包括所需属性。 所需属性与报告的属性一起用于同步模块配置或条件。 解决方案后端可以设置所需属性，模块可以读取它们。 模块还可以接收所需属性的更改通知。 所需属性是使用最多五个名称/值对创建的，每个默认值必须少于 512 个字符。 最多可以定义五个名称/值孪生所需属性。 孪生所需属性的值必须是有效的 JSON、未转义且不包含数组，最多可包含四级嵌套层次结构。 如果默认值所需的参数（例如客户服务器的 IP 地址）没有意义，可以添加一个参数作为默认值。 若要详细了解孪生体所需属性，请参阅[定义或更新所需属性](../iot-edge/module-composition.md#define-or-update-desired-properties)。

例如，如果某个模块使用孪生所需属性支持可动态配置的刷新频率，最好定义以下默认孪生所需属性：

- 名称 #1：RefreshRate
- 值 #1：60

默认环境变量。 环境变量提供了有助于配置过程的模块的补充信息。 环境变量是使用名称/值对创建的。 每个默认环境变量名称和值都必须少于 512 个字符，并且最多可以定义五个。 如果默认值所需的参数（例如客户服务器的 IP 地址）没有意义，可以添加一个参数作为默认值。

例如，如果某个模块在启动之前需要接受使用条款，则你可以定义以下环境变量：

- 名称 #1：ACCEPT_EULA
- 值 #1：Y

默认容器创建选项。 容器创建选项指导 IoT Edge 模块 Docker 容器的创建。 IoT Edge 支持 Docker 引擎 API 创建容器选项。 有关所有选项，请查看[列出容器](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)。 “创建选项”字段必须是有效的 JSON、未转义且少于 512 个字符。

例如，如果某个模块需要端口绑定，请定义以下创建选项：

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>示例

下面是 Azure 市场计划详细信息的示例（列出的所有价格仅供举例使用，不反映实际成本）：

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="说明 Azure 市场计划详细信息。":::

#### <a name="call-out-descriptions"></a>调用说明

1. 产品名称
2. 计划名称
3. 计划说明

<br>下面是 Azure 门户计划详细信息的示例（列出的所有价格仅供举例使用，不反映实际成本）：

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="说明 Azure 门户计划详细信息。":::

#### <a name="call-out-descriptions"></a>调用说明

1. 产品名称
2. 计划名称
3. 计划说明

选择“保存草稿”，然后在左侧导航菜单中选择“← 计划概述”以返回到“计划概述”页。

## <a name="next-steps"></a>后续步骤

- 若要与 Microsoft（可选）联合销售，请在左侧导航菜单中选择该项。 有关详细信息，请参阅[联合销售合作伙伴参与](./co-sell-overview.md)。
- 如果未设置联合销售或已完成设置，可以[查看并发布产品/服务](review-publish-offer.md)。