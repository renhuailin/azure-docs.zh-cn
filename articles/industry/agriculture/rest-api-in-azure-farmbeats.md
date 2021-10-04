---
title: Azure FarmBeats API
description: 了解 Azure FarmBeats API，它为农业企业提供了具有基于 JSON 的响应的标准化 RESTful 接口。
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 4ddbe9ff6a3dfa195d0739205c3e32070b170f30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560745"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats API

本文介绍了 Azure FarmBeats API。 Azure FarmBeats API 为农业企业提供了标准化的 RESTful 接口，并提供基于 JSON 的响应，帮助用户充分利用 Azure FarmBeats 功能，例如：

- 用于获取传感器、照相机、无人机、天气、卫星和特选地面数据的 API。
- 跨通用数据提供程序的数据的规范化和处境化。
- 架构化对所有引入数据进行访问和查询。
- 自动生成可根据 agronomic 功能查询的元数据。
- 自动生成的用于快速构建模型的时间序列聚合。
- 集成的 Azure Data Factory 引擎，可轻松构建自定义数据处理渠道。

## <a name="application-development"></a>应用程序开发

FarmBeats  API 包含 Swagger 技术文档。 有关 API 及其相应的请求或响应的详细信息，请参阅“[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)”。

下表汇总了 FarmBeats Datahub 中的所有对象和资源：

| 对象和资源 | 说明
--- | ---|
Farm | 场对应于 FarmBeats 系统中感兴趣的物理位置。 每个场都有一个场名称和唯一的场 ID。 |
设备  | Device 对应于存在于场上的物理设备。 每台设备都具有唯一的设备 ID。 通常会将设备设置为具有场 ID 的场。
DeviceModel  | DeviceModel 对应于设备的元数据，如制造商和设备类型（网关或节点）。
传感器  | Sensor 对应于记录值的物理传感器。 传感器通常连接到具有设备 ID 的设备。
SensorModel  | SensorModel 对应于传感器的元数据，如制造商、传感器类型（模拟或数字）以及传感器测量参数值（如环境温度和压力）。
遥测  | 遥测具有读取特定传感器和时间范围的遥测消息的功能。
作业  | 作业对应于在 FarmBeats 系统中执行的任何活动工作流以获取所需的输出。 每个作业都与作业 ID 和作业类型相关联。
JobType  | JobType 对应于系统支持的不同作业类型。 包含系统定义和用户定义的作业类型。
ExtendedType  | ExtendedType 对应于系统中的系统定义类型和用户定义类型的列表。 ExtendedType 可帮助在 FarmBeats 系统中设置新的传感器、场景或场景文件类型。
Partner  | 合作伙伴对应于 FarmBeats 的传感器和图像集成合作伙伴。
场景  | 场景对应于在场的上下文中生成的任何输出。 每个场景都具有与之关联的场景 ID、场景源、场景类型和场 ID。 每个场景 ID 都可以有多个与之关联的场景文件。
SceneFile |SceneFile 对应于针对单个场景生成的所有文件。 一个场景 ID 可以有多个与之关联的 SceneFile ID。
规则  |规则对应于与场相关的用于触发警报的数据的条件。 每个规则都在场的数据上下文中。
警报  | 警报对应于在满足规则条件时生成的通知。 每个警报都在规则的上下文中。
RoleDefinition  | RoleDefinition 为角色定义允许和禁止的操作。
RoleAssignment  |RoleAssignment 对应于将角色分配给用户或服务主体。

### <a name="data-format"></a>数据格式

JSON 是一种与语言无关的常见数据格式，该格式提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 [JSON 网站](https://www.json.org/)。

## <a name="authentication-and-authorization"></a>身份验证和授权

对 REST API 的 HTTP 请求通过 Azure Active Directory (Azure AD) 进行保护。
若要向 REST API 发出经过身份验证的请求，客户端代码需要使用有效凭据进行身份验证，然后才能调用 API。 身份验证是通过 Azure AD 在不同的参与者之间进行协调的。 它会向客户端提供一个访问令牌作为身份验证的证明。 然后在 REST API 请求的 HTTP 授权标头中发送令牌。 如需详细了解 Azure AD 身份验证，请参阅开发人员使用的 [Azure Active Directory](https://portal.azure.com)。

在后续 API 请求中必须发送访问令牌，如以下标头部分所示：

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 请求标头

下面是在对 Azure FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头。


**标头** | **说明和示例**
--- | ---
Content-Type  | 请求格式 (Content-Type: application/\<format\>)。 对于 Azure FarmBeats 数据中心 API，格式为 JSON。 Content-Type: application/json
授权  | 指定进行 API 调用所需的访问令牌。 授权：持有者 \<Access-Token\>
Accept | 响应格式。 对于 Azure FarmBeats 数据中心 API，格式为 JSON。 Accept: application/json

### <a name="api-requests"></a>API 请求

若要发出 REST API 请求，请将 HTTP（GET、POST 、PUT 或 DELETE）方法、API 服务的 URL、用于查询、提交数据、更新或删除资源的 URI 以及一个或多个 HTTP 请求标头组合在一起。

API 服务的 URL 是你的 Datahub URL，例如 `https://<yourdatahub-website-name>.azurewebsites.net`。

或者，可在 GET 调用中包含查询参数以筛选数据、限制数据的大小，并对响应中的数据进行排序。

以下示例请求用于获取设备列表。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

以下示例请求的目的是创建设备。 此请求具有带请求正文的输入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>查询参数

对于 REST GET 调用，用户可以通过在请求 URI 上包含一个或多个查询参数来筛选、限制数据的大小，并对数据进行排序。 有关查询参数的详细说明，请参阅 API 文档和单个 GET 调用。
例如，在查询设备列表时（在设备上调用 GET），可以指定以下查询参数：

![设备列表](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>错误处理。

Azure FarmBeats Datahub API 返回标准 HTTP 错误。 最常见的错误代码列示如下：

 |错误代码             | 说明 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 创建 (Post) 成功 |
 |400                    | 错误的请求。 请求存在错误。 |
 |401                    | 未授权。 API 的调用方无权访问该资源。 |
 |404                    | 找不到资源 |
 |5XX                    | 内部服务器错误。 从 5XX 开始的错误代码意味着服务器上出现一些错误。 有关更多详细信息，请参阅服务器日志和以下部分。 |


除了标准 HTTP 错误外，Azure FarmBeats Datahub API 还会返回以下格式的内部错误：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

在该示例中，在创建场时，输入负载中未指定必填字段“Name”。 将生成如下错误消息：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>将用户或应用程序注册添加到 Azure Active Directory

可以通过 Azure Active Directory 中的用户或应用程序注册来访问 Azure FarmBeats API。 若要在 Azure Active Directory 中创建应用程序注册，请按照以下步骤操作：

1. 访问 [Azue 门户](https://portal.azure.com)，然后依次选择 **Azure Active Directory** >  **“应用程序注册”**  >  **“新注册”** 。 或者，用户可以使用现有帐户。
2. 对于新帐户，请执行以下操作：

    - 输入名称。
    - 仅 **在此组织目录选择帐户**。
    - 保留其余字段中的默认值。
    - 选择“注册”。

3. 在“新建和现有应用程序注册 **概述**”窗格中，执行以下操作：

    - 捕获 **客户端 ID** 和 **租户 ID**。
    - 请参阅“**证书和机密**”以生成新的客户端密钥并捕获“**客户端密钥**”。
    - 返回到“**概述**”，并选择“**管理本地目录中的应用程序**”旁边的链接。
    - 请参阅“**属性**”以捕获“**对象 ID**”。

4. 请访问 Datahub Swagger (`https://<yourdatahub>.azurewebsites.net/swagger/index.html`) 并执行以下操作：
    - 请访问 **RoleAssignment API**。
    - 执行 POST，为刚创建的 **对象 ID** 创建 **RoleAssignment** 对象。
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > 有关如何添加用户和 Active Directory 注册的详细信息，请参阅“[Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md)”。

完成前面的步骤后，应用程序注册（客户端）可以通过使用者身份验证使用访问令牌调用 Azure FarmBeats API。

在后续 API 请求中必须发送访问令牌，如以下标头部分所示：

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```