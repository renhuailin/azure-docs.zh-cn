---
title: 使用 Azure Active Directory 控制对 IoT 中心的访问
description: 开发人员指南。 如何使用 Azure AD 和 Azure RBAC 为后端应用控制对 IoT 中心的访问。
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a8387a5732ab51add02495b03236b42f9cd4e671
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609433"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>使用 Azure Active Directory 控制对 IoT 中心的访问

可以使用 Azure Active Directory (Azure AD) 来对向 Azure IoT 中心服务 API（例如创建设备标识和调用直接方法）发出的请求进行身份验证。 还可以使用 Azure 基于角色的访问控制 (Azure RBAC) 对这些服务 API 进行授权。 将这些技术一起使用，可以向 Azure AD 安全主体授予访问 IoT 中心服务 API 的权限。 此安全主体可以是用户、组或应用程序服务主体。

与使用[安全令牌](iot-hub-dev-guide-sas.md)相比，使用 Azure AD 对访问进行身份验证并使用 Azure RBAC 控制权限能够提供更高的安全性与易用性。 为了最大限度地减少安全令牌中固有的潜在安全问题，我们建议[尽可能将 Azure AD 与 IoT 中心配合使用](#azure-ad-access-and-shared-access-policies)。 

> [!NOTE]
> IoT 中心设备 API（例如设备到云的消息和更新报告属性）不支持使用 Azure AD 进行身份验证。 请使用[对称密钥](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry)或 [X.509](iot-hub-x509ca-overview.md) 向 IoT 中心进行设备身份验证。

## <a name="authentication-and-authorization"></a>身份验证和授权

当某个 Azure AD 安全主体请求访问 IoT 中心服务 API 时，首先会对该主体的标识进行身份验证。 要进行身份验证，请求需要在运行时包含 OAuth 2.0 访问令牌。 用于请求令牌的资源名称是 `https://iothubs.azure.net`。 如果应用程序在 Azure VM、Azure Functions 应用或 Azure 应用服务应用之类的 Azure 资源中运行，可将其表示为[托管标识](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)。 

对 Azure AD 主体进行身份验证后，下一步是授权。 在此步骤中，IoT 中心将使用 Azure AD 角色分配服务来确定该主体拥有哪些权限。 如果主体的权限与请求的资源或 API 匹配，IoT 中心将对请求进行授权。 因此，此步骤要求将一个或多个 Azure 角色分配给安全主体。 IoT 中心提供了一些具有常见权限组的内置角色。

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>使用 Azure RBAC 角色分配管理对 IoT 中心的访问

使用 Azure AD 和 RBAC，IoT 中心要求请求 API 的主体具有适当级别的授权权限。 若要为主体授予权限，需为该主体分配角色。 

- 如果主体是用户、组或应用程序服务主体，请按照[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)中的指导进行操作。
- 如果主体是托管标识，请按照[使用 Azure 门户为托管标识分配资源访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)中的指导进行操作。

为确保符合最低特权原则，请始终在尽量最小的[资源范围](#resource-scope)（可能是 IoT 中心范围）分配适当的角色。

IoT 中心提供以下 Azure 内置角色，用于通过 Azure AD 和 RBAC 授权访问 IoT 中心服务 API：

| 角色 | 说明 | 
| ---- | ----------- | 
| [IoT 中心数据参与者](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | 允许对 IoT 中心数据平面操作进行完全访问。 |
| [IoT 中心数据读取者](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | 允许对 IoT 中心数据平面属性进行完全读取访问。 |
| [IoT 中心注册表参与者](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | 允许对 IoT 中心设备注册表进行完全访问。 |
| [IoT 中心孪生体参与者](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | 允许对所有 IoT 中心设备和模块孪生进行读写访问。 |

还可以通过组合所需的[权限](#permissions-for-iot-hub-service-apis)来定义要对 IoT 中心使用的自定义角色。 有关详细信息，请参阅[为 Azure 基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。

### <a name="resource-scope"></a>资源范围

向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 无论何时，最好都只授予最窄的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

此列表描述了可在哪些范围级别访问 IoT 中心，从最窄的范围开始：

- **IoT 中心。** 在此范围，角色分配将应用于 IoT 中心。 没有比单个 IoT 中心更小的范围。 不支持在更小的范围（例如单个设备标识或孪生部分）进行角色分配。
- **资源组。** 在此范围，角色分配将应用于资源组中的所有 IoT 中心。
- **订阅。** 在此范围，角色分配将应用于订阅中所有资源组内的所有 IoT 中心。
- **管理组。** 在此范围，角色分配将应用于管理组的所有订阅中所有资源组内的所有 IoT 中心。

## <a name="permissions-for-iot-hub-service-apis"></a>IoT 中心服务 API 的权限

下表描述了可用于 IoT 中心服务 API 操作的权限。 要使客户端能够调用特定的操作，请确保该客户端的已分配 RBAC 角色为该操作提供足够的权限。

| RBAC 操作 | 说明 |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | 读取任何设备或模块标识。 |
| `Microsoft.Devices/IotHubs/devices/write`  | 创建或更新任何设备或模块标识。  |
| `Microsoft.Devices/IotHubs/devices/delete` | 删除任何设备或模块标识。 |
| `Microsoft.Devices/IotHubs/twins/read` | 读取任何设备或模块孪生。 |
| `Microsoft.Devices/IotHubs/twins/write` | 写入任何设备或模块孪生。 |
| `Microsoft.Devices/IotHubs/jobs/read` | 返回作业列表。 |
| `Microsoft.Devices/IotHubs/jobs/write` | 创建或更新任何作业。 |
| `Microsoft.Devices/IotHubs/jobs/delete` | 删除任何作业。 |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | 向任何设备发送云到设备的消息。  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | 接收、完成或丢弃云到设备的消息反馈通知。 |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | 删除设备的所有挂起命令。  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | 在任何设备或模块上调用直接方法。 |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | 接收、完成或丢弃文件上传通知。 |
| `Microsoft.Devices/IotHubs/statistics/read` | 读取设备和服务统计信息。 |
| `Microsoft.Devices/IotHubs/configurations/read` | 读取设备管理配置。 |
| `Microsoft.Devices/IotHubs/configurations/write` | 创建或更新设备管理配置。 |
| `Microsoft.Devices/IotHubs/configurations/delete` | 删除任何设备管理配置。 |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | 将配置内容应用于边缘设备。 |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | 验证配置的目标条件和自定义指标查询。 |

> [!TIP]
> - [批量注册表更新](/rest/api/iothub/service/bulkregistry/updateregistry)操作需要 `Microsoft.Devices/IotHubs/devices/write` 和 `Microsoft.Devices/IotHubs/devices/delete`。
> - [孪生查询](/rest/api/iothub/service/query/gettwins)操作需要 `Microsoft.Devices/IotHubs/twins/read`。
> - [获取数字孪生体](/rest/api/iothub/service/digitaltwin/getdigitaltwin)需要 `Microsoft.Devices/IotHubs/twins/read`。 [更新数字孪生体](/rest/api/iothub/service/digitaltwin/updatedigitaltwin)需要 `Microsoft.Devices/IotHubs/twins/write`。
> - [调用组件命令](/rest/api/iothub/service/digitaltwin/invokecomponentcommand)和[调用根级别命令](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)均需要 `Microsoft.Devices/IotHubs/directMethods/invoke/action`。

> [!NOTE]
> 若要使用 Azure AD 从 IoT 中心获取数据，请[设置通往独立事件中心的路由](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint)。 若要访问[与事件中心兼容的内置终结点](iot-hub-devguide-messages-read-builtin.md)，请像之前一样使用连接字符串（共享访问密钥）方法。 

## <a name="azure-ad-access-and-shared-access-policies"></a>Azure AD 访问和共享访问策略

默认情况下，IoT 中心支持通过 Azure AD 以及[共享访问策略和安全令牌](iot-hub-dev-guide-sas.md)访问服务 API。 若要最大程度地减少安全令牌中固有的潜在安全漏洞，请禁止使用共享访问策略进行访问： 

1. 确保服务客户端和用户对 IoT 中心拥有[足够的访问权限](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment)。 遵循[最低特权原则](../security/fundamentals/identity-management-best-practices.md)。
1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。
1. 在左侧窗格中，选择“共享访问策略”。
1. 在“使用共享访问策略连接”下，选择“拒绝” 。
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="显示如何禁用 IoT 中心共享访问策略的屏幕截图。":::
1. 查看警告，然后选择“保存”。

现在只能通过 Azure AD 和 RBAC 访问 IoT 中心服务 API。

## <a name="azure-ad-access-from-the-azure-portal"></a>从 Azure 门户访问 Azure AD

当你尝试访问 IoT 中心时，Azure 门户首先会检查是否已为你分配了拥有 `Microsoft.Devices/iotHubs/listkeys/action` 权限的 Azure 角色。 如果是，则 Azure 门户将使用共享访问策略中的密钥来访问 IoT 中心。 否则，Azure 门户会尝试使用 Azure AD 帐户来访问数据。 

若要使用 Azure AD 帐户从 Azure 门户访问 IoT 中心，需要有权访问 IoT 中心数据资源（例如设备和孪生体）。 此外，还需要有权访问 Azure 门户中的 IoT 中心资源。 IoT 中心提供的内置角色授予对设备和孪生体等资源的访问权限。 但这些角色不授予对 IoT 中心资源的访问权限。 因此，若要访问门户，还需要分配有[读取者](../role-based-access-control/built-in-roles.md#reader)等 Azure 资源管理器角色。 读取者角色是一种不错的选择，因为它是允许在门户中导航的最受限制的角色。 它不包含 `Microsoft.Devices/iotHubs/listkeys/action` 权限（该权限通过共享访问策略提供对所有 IoT 中心数据资源的访问）。 

若要确保帐户不具备分配的权限之外的访问权限，在创建自定义角色时，请不要包含 `Microsoft.Devices/iotHubs/listkeys/action` 权限。 例如，若要创建一个可以读取设备标识但不能创建或删除设备的自定义角色，请创建符合以下要求的自定义角色：
- 具有 `Microsoft.Devices/IotHubs/devices/read` 数据操作。
- 没有 `Microsoft.Devices/IotHubs/devices/write` 数据操作。
- 没有 `Microsoft.Devices/IotHubs/devices/delete` 数据操作。
- 没有 `Microsoft.Devices/iotHubs/listkeys/action` 操作。

然后，确保该帐户没有任何其他拥有 `Microsoft.Devices/iotHubs/listkeys/action` 权限的角色 - 例如[所有者](../role-based-access-control/built-in-roles.md#owner)或[参与者](../role-based-access-control/built-in-roles.md#contributor)。 要使该帐户拥有资源访问权限并可以在门户中导航，请分配[读取者](../role-based-access-control/built-in-roles.md#reader)角色。

## <a name="azure-iot-extension-for-azure-cli"></a>适用于 Azure CLI 的 Azure IoT 扩展

大多数针对 IoT 中心的命令均支持 Azure AD 身份验证。 可以使用 `--auth-type` 参数（接受 `key` 或 `login` 值）来控制用于运行命令的身份验证类型。 `key` 值为默认值。

- 当 `--auth-type` 的值为 `key` 时，将与之前一样，CLI 在与 IoT 中心交互时会自动发现合适的策略。

- 当 `--auth-type` 的值为 `login` 时，将使用主体中记录的来自 Azure CLI 的访问令牌进行操作。

有关详细信息，请参阅[适用于 Azure CLI 的 Azure IoT 扩展发布页](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12)。

## <a name="sdk-samples"></a>SDK 示例

- [.NET Microsoft.Azure.Devices SDK 示例](https://aka.ms/iothubaadcsharpsample)
- [Java SDK 示例](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>后续步骤

- 若要详细了解在应用程序中使用 Azure AD 的优点，请参阅[与 Azure Active Directory 集成](../active-directory/develop/active-directory-how-to-integrate.md)。
- 有关为用户和服务主体从 Azure AD 请求访问令牌的详细信息，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)。
