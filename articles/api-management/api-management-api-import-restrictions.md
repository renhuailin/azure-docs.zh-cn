---
title: API 格式支持的限制和详细信息
titleSuffix: Azure API Management
description: 有关 Azure API 管理中 OpenAPI、WSDL 和 WADL 格式支持的已知问题和限制详细信息。
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/24/2021
ms.author: danlep
ms.openlocfilehash: 3f16961ec4774708fa55a2a49e408a6b980cdb31
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057599"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 导入限制和已知问题

## <a name="about-this-list"></a>关于此列表

导入 API 时，你可能会遇到一些限制或需要识别并纠正问题，然后才能成功导入。 本文内容：
* 所有限制（按照 API 的导入格式进行组织）。 
* OpenAPI 导出的工作原理。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger 导入限制

如果在导入 OpenAPI 文档时收到错误，请确保事先已通过以下方式之一对该文档进行验证：
* 使用 Azure 门户中的设计器（“设计”>“前端”>“OpenAPI 规范编辑器”）或 
* 使用第三方工具（例如 <a href="https://editor.swagger.io">Swagger 编辑器</a>）。

### <a name="general"></a><a name="open-api-general"> </a>总则

- 路径和查询所需的参数必须具有唯一名称。
    - 在 OpenAPI 中，参数名称只需要在一个位置（例如路径、查询和头）内是唯一的。 
    - 在 API 管理中，我们允许操作通过路径和查询参数进行区分。 
        - 由于 OpenAPI 不支持此方式，因此我们要求参数名称在整个 URL 模板中唯一。
- 以内联方式导入到 API 管理时，OpenAPI 规范最大可以为 4 MB。 
    - 通过 URL 将 OpenAPI 文档提供给可从 API 管理服务访问的位置时，大小限制不适用。
- `\$ref` 指针不能引用外部文件。
- 只有以下扩展受支持：
    - `x-ms-paths` 
    - `x-servers` 
- 自定义扩展：
    - 导入时忽略。
    - 不会保存或保留以供导出。
- 忽略安全定义。
- `Recursion`: 
    - API 管理不支持以递归方式指定的定义。
    - 例如，引用本身的架构。
- 在 API 操作级别不支持 `server` 对象。
- 不支持 `Produces` 关键字（描述 API 返回的 MIME 类型）。 
- 可用的源文件 URL 将应用于相对服务器 URL。
- 不支持 API 操作的内联架构定义。 架构定义：
    - 在 API 范围内定义。
    - 可在 API 操作请求或响应范围内引用。
- 定义的 URL 参数需要是 URL 模板的一部分。

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 版本 2

OpenAPI 版本 2 支持仅限于 JSON 格式。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 版本 3

-   如果指定了多个 `servers`，API 管理将使用它找到的第一个 HTTPS URL。 
- 如果不存在任何 HTTPS URL，则服务器 URL 将为空。
- 不支持 `Examples`，但支持 `example`。
- 以下字段包含在 OpenAPI 版本 3.x 中，但不受支持：
    - `explode`
    - `style`
    - `allowReserved`

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 导入、更新和导出机制

### <a name="general"></a><a name="open-import-export-general"> </a>常规

从 API 管理服务导出的 API 定义：
* 主要用于需要调用 API 管理服务中托管的 API 的外部应用程序。 
* 并非用于导入到相同或不同的 API 管理服务中。 

对于跨不同服务/环境的 API 定义的配置管理，请参阅有关将 API 管理服务与 Git 结合使用的文档。 

### <a name="add-new-api-via-openapi-import"></a>通过 OpenAPI 导入添加新的 API

对于 OpenAPI 文档中所述的每个操作，将使用 Azure 资源名称和显示名称（分别设置为 `operationId` 和 `summary`）来创建新操作。 
* `operationId` 值已规范化。
    *  如果未指定 `operationId`（不存在、为 `null` 或为空），将会通过合并 HTTP 方法和路径模板来生成 Azure 资源名称值，例如 `get-foo`。
* `summary` 值按原样导入，其长度限制为 300 个字符。
    * 如果未指定 `summary`（不存在、为 `null` 或为空），`display name` 值将设置为 `operationId`。 

### <a name="update-an-existing-api-via-openapi-import"></a>通过 OpenAPI 导入更新现有的 API

在导入期间，现有的 API 操作：
* 将会更改，以匹配 OpenAPI 文档中所述的 API。 
* 通过将 OpenAPI 文档中操作的 `operationId` 值与现有操作的 Azure 资源名称进行比较，以便与该文档中的操作匹配。 
    * 如果找到匹配项，则现有操作的属性将“就地”更新。
    * 如果找不到匹配项：
        * 将通过合并 HTTP 方法和路径模板来创建新操作，例如 `get-foo`。 
        * 对于每个新操作，导入过程会尝试从具有相同 HTTP 方法和路径模板的现有操作复制策略。

所有现有的不匹配操作将被删除。

若要提高导入的可预测性，请遵循以下准则：

- 为每个操作指定 `operationId` 属性。
- 避免在完成初始导入后更改 `operationId`。
- 切勿同时更改 `operationId` 和 HTTP 方法或路径模板。

### <a name="export-api-as-openapi"></a>将 API 导出为 OpenAPI

每个操作的：
* Azure 资源名称将导出为 `operationId`。
* 显示名称将导出为 `summary`。

`operationId` 的规范化规则
- 转换为小写字母。
- 将非字母数字字符的每个序列替换为一条短划线。
    - 例如，`GET-/foo/{bar}?buzz={quix}` 将转换为 `get-foo-bar-buzz-quix-`。
- 剪裁掉两侧的短划线。
    - 例如，`get-foo-bar-buzz-quix-` 将变为 `get-foo-bar-buzz-quix`
- 截断为 76 个字符：比资源名称的最大长度限制少 4 个字符。
- 如果需要，以 `-1, -2, ..., -999` 格式使用剩余的 4 个字符作为“重复项删除”后缀。

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

可以使用 WSDL 文件创建 SOAP 直通和 SOAP 到 REST API。

- SOAP 绑定  
    - 仅支持“document”和“literal”编码样式的 SOAP 绑定。
    - 不支持“rpc”样式或 SOAP 编码。
- WSDL:Import
    - 不支持。 请改为将导入项合并到一个文档中。
- 包含多个部分的消息 
    - 不支持此消息类型。
- WCF wsHttpBinding 
    - 使用 Windows Communication Foundation 创建的 SOAP 服务应使用 `basicHttpBinding`。
    - 不支持 `wsHttpBinding`。
- **MTOM** 
    - 使用 `MTOM` 的服务可能会正常工作。 
    - 目前暂未提供官方支持。
- **递归** 
    - API 管理不支持以递归方式定义的类型。
    - 例如，引用这些类型本身的数组。
- 多个命名空间 
    - 虽然可以在架构中使用多个命名空间，但只能使用目标命名空间来定义消息部分。 
    - 不会保留除目标以外的命名空间。 
        - 这些命名空间用于定义其他输入或输出元素。
        - 虽然可以导入这样的 WSDL 文档，但在导出时，所有消息部分都将具有 WSDL 目标命名空间。
- **数组** 
    - SOAP 到 REST 转换仅支持包装的数组，如以下示例所示：

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

目前没有已知的 WADL 导入问题。
