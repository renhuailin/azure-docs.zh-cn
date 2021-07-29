---
title: 使用 Azure Active Directory 控制对 IoT 中心的访问 | Microsoft Docs
description: 开发人员指南 - 如何使用 Azure AD 和 Azure RBAC 为后端应用控制对 IoT 中心的访问。
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 196afc38c24254c4628173180205a858d1085eeb
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489699"
---
# <a name="control-access-to-iot-hub-using-azure-active-directory"></a>使用 Azure Active Directory 控制对 IoT 中心的访问

Azure IoT 中心支持使用 Azure Active Directory (AAD) 来验证对其服务 API（例如创建设备标识或调用直接方法）发出的请求。 此外，IoT 中心还支持使用 Azure 基于角色的访问控制 (Azure RBAC) 进行这些服务 API 的授权。 同时，你可以向 AAD 安全主体（可以是用户、组或应用程序服务主体）授权访问 IoT 中心的服务 API。

与使用[安全令牌](iot-hub-dev-guide-sas.md)相比，使用 Azure AD 对访问进行身份验证并使用 Azure RBAC 控制权限能够提供更高的安全性与易用性。 为了最大限度地减少安全令牌中固有的潜在安全漏洞，Microsoft 建议尽可能对 IoT 中心使用 Azure AD。

> [!NOTE]
> IoT 中心的设备 API（例如设备到云的消息和更新报告属性）不支持使用 Azure AD 进行身份验证。 请使用[对称密钥](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry)或 [X.509](iot-hub-x509ca-overview.md) 向 IoT 中心进行设备身份验证。

## <a name="authentication-and-authorization"></a>身份验证和授权

当某个 Azure AD 安全主体请求访问 IoT 中心服务 API 时，首先会对该主体的标识进行身份验证。 此步骤要求请求在运行时包含 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 `https://iothubs.azure.net`。 如果应用程序在 Azure VM、Azure 函数应用或应用服务应用之类的 Azure 资源中运行，可将其表示为[托管标识](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)。 

对 Azure AD 主体进行身份验证后，第二步是授权。 在此步骤中，IoT 中心将与 Azure AD 的角色分配服务核对，以确定该主体拥有哪些权限。 如果该主体的权限与所请求的资源或 API 匹配，则 IoT 中心将为请求授权。 因此，此步骤要求将一个或多个 Azure 角色分配给安全主体。 IoT 中心提供一些拥有常用权限组的内置角色。

## <a name="manage-access-to-iot-hub-using-azure-rbac-role-assignment"></a>使用 Azure RBAC 角色分配管理对 IoT 中心的访问

使用 Azure AD 和 RBAC 时，IoT 中心要求请求 API 的主体拥有适当的权限级别，这样才能为其授权。 若要为主体授予权限，需为该主体分配角色。 

- 如果主体是用户、组或应用程序服务主体，请按照[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)进行操作。
- 如果主体是托管标识，请按照[使用 Azure 门户为托管标识分配资源访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)进行操作。

为确保符合最低特权原则，请始终在尽量最小的[资源范围](#resource-scope)（可能是 IoT 中心范围）分配适当的角色。

IoT 中心提供以下 Azure 内置角色用于通过 Azure AD 和 RBAC 授权访问 IoT 中心服务 API：

| 角色 | 说明 | 
| ---- | ----------- | 
| [IoT 中心数据参与者](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | 允许对 IoT 中心数据平面操作进行完全访问。 |
| [IoT 中心数据读取者](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | 允许对 IoT 中心数据平面属性进行完全读取访问。 |
| [IoT 中心注册表参与者](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | 允许对 IoT 中心设备注册表进行完全访问。 |
| [IoT 中心孪生参与者](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | 允许对所有 IoT 中心设备和模块孪生进行读写访问。 |

还可以通过组合所需的[权限](#permissions-for-iot-hub-service-apis)来定义要对 IoT 中心使用的自定义角色。 有关详细信息，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。

### <a name="resource-scope"></a>资源范围

向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

以下列表描述了可在哪些范围级别访问 IoT 中心，从最窄的范围开始：

- IoT 中心。 在此范围，角色分配将应用于 IoT 中心。 没有比单个 IoT 中心更小的范围。 不支持在更小的范围（例如单个设备标识或孪生部分）进行角色分配。
- **资源组。** 在此范围，角色分配将应用于资源组中的所有 IoT 中心。
- **订阅。** 在此范围，角色分配将应用于订阅中所有资源组内的所有 IoT 中心。
- **管理组。** 在此范围，角色分配将应用于管理组的所有订阅中所有资源组内的所有 IoT 中心。

## <a name="permissions-for-iot-hub-service-apis"></a>IoT 中心服务 API 的权限

下表描述了可用于 IoT 中心服务 API 操作的权限。 要使客户端能够调用特定的操作，请确保该客户端的已分配 RBAC 角色为该操作提供足够的权限。

| RBAC 操作 | 说明 |
|-|-|
| Microsoft.Devices/IotHubs/devices/read | 读取任何设备或模块标识 |
| Microsoft.Devices/IotHubs/devices/write  | 创建或更新任何设备或模块标识  |
| Microsoft.Devices/IotHubs/devices/delete | 删除任何设备或模块标识 |
| Microsoft.Devices/IotHubs/twins/read | 读取任何设备或模块孪生 |
| Microsoft.Devices/IotHubs/twins/write | 写入任何设备或模块孪生 |
| Microsoft.Devices/IotHubs/jobs/read | 返回作业列表 |
| Microsoft.Devices/IotHubs/jobs/write | 创建或更新任何作业 |
| Microsoft.Devices/IotHubs/jobs/delete | 删除任何作业 |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action | 向任何设备发送云到设备的消息  |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action | 接收、完成或丢弃云到设备的消息反馈通知 |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action | 删除设备的所有挂起命令  |
| Microsoft.Devices/IotHubs/directMethods/invoke/action | 在设备上调用直接方法 |
| Microsoft.Devices/IotHubs/fileUpload/notifications/action  | 接收、完成或丢弃文件上传通知 |
| Microsoft.Devices/IotHubs/statistics/read | 读取设备和服务统计信息 |
| Microsoft.Devices/IotHubs/configurations/read | 读取设备管理配置 |
| Microsoft.Devices/IotHubs/configurations/write | 创建或更新设备管理配置 |
| Microsoft.Devices/IotHubs/configurations/delete | 删除任何设备管理配置 |
| Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action  | 将配置内容应用于边缘设备 |
| Microsoft.Devices/IotHubs/configurations/testQueries/action | 验证配置的目标条件和自定义指标查询 |

> [!TIP]
> - [批量注册表更新](/rest/api/iothub/service/bulkregistry/updateregistry)操作需要 `Microsoft.Devices/IotHubs/devices/write` 和 `Microsoft.Devices/IotHubs/devices/delete` 。
> - [孪生查询](/rest/api/iothub/service/query/gettwins)操作需要 `Microsoft.Devices/IotHubs/twins/read`。
> - [获取数字孪生](/rest/api/iothub/service/digitaltwin/getdigitaltwin)需要 `Microsoft.Devices/IotHubs/twins/read`，而[更新数字孪生](/rest/api/iothub/service/digitaltwin/updatedigitaltwin)需要 `Microsoft.Devices/IotHubs/twins/write`
> - [调用组件命令](/rest/api/iothub/service/digitaltwin/invokecomponentcommand)和[调用根级别命令](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)都需要 `Microsoft.Devices/IotHubs/directMethods/invoke/action`。

## <a name="azure-ad-access-from-azure-portal"></a>从 Azure 门户进行 Azure AD 访问

当你尝试访问 IoT 中心时，Azure 门户首先会检查是否已为你分配了拥有 Microsoft.Devices/iotHubs/listkeys/action 权限的 Azure 角色。 如果已分配此类角色，则 Azure 门户将使用共享访问策略中的密钥来访问 IoT 中心。 如果未分配此类角色，则 Azure 门户将尝试使用你的 Azure AD 帐户来访问数据。 

若要使用 Azure AD 帐户从 Azure 门户访问 IoT 中心，需要有权访问 IoT 中心数据资源（例如设备和孪生），并且还需要有权在 Azure 门户中导航到 IoT 中心资源。 IoT 中心提供的内置角色授予对设备和孪生等资源的访问权限，但不授予对 IoT 中心资源的访问权限。 因此，若要访问门户，还需要分配有[读取者](../role-based-access-control/built-in-roles.md#reader)等 Azure 资源管理器 (ARM) 角色。 “读取者”角色是个很好的选择，因为它是最具限制性但又可以让你在门户中导航的角色，并且它不包括 Microsoft.Devices/iotHubs/listkeys/action 权限（使用该权限可以通过共享访问策略访问所有 IoT 中心数据资源）。 

为了确保帐户的访问权限不超出分配的权限，创建自定义角色时请不要包括 Microsoft.Devices/iotHubs/listkeys/action 权限。 例如，若要创建一个可以读取设备标识但不能创建或删除设备的自定义角色，请创建符合以下要求的自定义角色：
- 拥有 Microsoft.Devices/IotHubs/devices/read 数据操作权限
- 不拥有 Microsoft.Devices/IotHubs/devices/write 数据操作权限
- 不拥有 Microsoft.Devices/IotHubs/devices/delete 数据操作权限
- 不拥有 Microsoft.Devices/iotHubs/listkeys/action 操作权限

然后，确保该帐户没有任何其他拥有 Microsoft.Devices/iotHubs/listkeys/action 权限的角色 - 例如[所有者](../role-based-access-control/built-in-roles.md#owner)或[参与者](../role-based-access-control/built-in-roles.md#contributor)。 要使该帐户拥有资源访问权限并可以在门户中导航，请分配[读取者](../role-based-access-control/built-in-roles.md#reader)角色。

## <a name="built-in-event-hub-compatible-endpoint-doesnt-support-azure-ad-authentication"></a>与内置事件中心兼容的终结点不支持 Azure AD 身份验证

[内置终结点](iot-hub-devguide-messages-read-builtin.md)不支持 Azure AD 集成。 无法使用安全主体或托管标识访问该终结点。 若要访问内置终结点，请像过去一样使用连接字符串（共享访问密钥）。

## <a name="sdk-samples"></a>SDK 示例

- [.NET Microsoft.Azure.Devices SDK 示例](https://aka.ms/iothubaadcsharpsample)
- [Java SDK 示例](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>后续步骤

- 有关在应用程序中使用 Azure AD 的优势的详细信息，请参阅[与 Azure Active Directory 集成](../active-directory/develop/active-directory-how-to-integrate.md)。
- 有关为用户和服务主体从 Azure AD 请求访问令牌的详细信息，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)。