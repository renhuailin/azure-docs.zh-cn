---
title: 排查 Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中 Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM 连接器的问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8552cbcb79522933e0b2cf9ffe369cefdf900b79
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390388"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中 Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM 连接器的问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中 Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM 连接器问题的建议。

## <a name="error-code-dynamicscreateserviceclienterror"></a>错误代码：DynamicsCreateServiceClientError

- **消息**：`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **原因**：此问题是 Dynamics 服务器端的暂时性问题。

- **建议**：重新运行管道。 如果再次失败，请尝试降低并行度。 如果问题仍然存在，请联系 Azure 支持部门。


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>导入架构或预览数据时缺少列

- **故障描述**：导入架构或预览数据时，缺少某些列。 错误消息：`The valid structure information (column name and type) are required for Dynamics source.`

- **原因**：此问题是设计使然的，因为数据工厂和 Synapse 管道无法在前 10 条记录中显示不包含值的列。 请确保添加的列格式正确。 

- **建议**：在“映射”选项卡中手动添加列。


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>错误代码：DynamicsMissingTargetForMultiTargetLookupField

- **消息**：`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**：源或列映射中不存在目标列。

- **建议**：  
  1. 请确保源包含目标列。 
  2. 在列映射中添加目标列。 确保接收器列的格式为“{fieldName}@EntityReference”。


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>错误代码：DynamicsInvalidTargetForMultiTargetLookupField

- 消息：`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **原因：** 提供了错误的实体名称作为多目标查找字段的目标实体。

- **建议**：为多目标查找字段提供有效的实体名称。


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>错误代码：DynamicsInvalidTypeForMultiTargetLookupField

- **消息**：`The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**：目标列中的值不是字符串。

- **建议**：在“多目标查找目标”列中提供有效字符串。


## <a name="error-code-dynamicsfailedtorequetserver"></a>错误代码：DynamicsFailedToRequetServer

- **消息**：`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **原因**：Dynamics 服务器不稳定或无法访问，或者网络出现问题。

- **建议**：有关详细信息，请检查网络连接或查看 Dynamics 服务器日志。 如需更多帮助，请联系 Dynamics 支持部门。


## <a name="error-code-dynamicsfailedtoconnect"></a>错误代码：DynamicsFailedToConnect 
 
 - **消息**：`Failed to connect to Dynamics: %message;` 
 
 - **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 你会看到 `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` 或者 `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'`。如果用例符合以下三个条件： <li>正在连接到 Dynamics 365、Common Data Service 或 Dynamics CRM。</li><li>正在使用 Office365 身份验证。</li><li>在 Azure Active Directory 中为租户和用户配置了[条件访问](../active-directory/conditional-access/overview.md)且/或需要多重身份验证（请参阅此 Dataverse 文档[链接](/powerapps/developer/data-platform/authenticate-office365-deprecation)）。</li>  在这种情况下，连接曾在 2021/6/8 之前成功。 由于区域发现服务弃用，从 2021/6/9 开始，连接将会失败（请参阅此[链接](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)）。| 如果租户和用户在 Azure Active Directory 中配置为[条件访问](../active-directory/conditional-access/overview.md)和/或多重身份验证是必需验证方法，则在 2021 年 6 月 8 日后必须使用“Azure AD 服务主体”进行身份验证。 请点击此[链接](./connector-dynamics-crm-office-365.md#prerequisites)，了解详细步骤。|
    |如果在错误消息中看到 `Office 365 auth with OAuth failed`，则表明服务器可能有一些与 OAuth 不兼容的配置。| <li>若要获取帮助，请联系 Dynamics 支持团队并提供详细的错误消息。</li><li>使用服务主体身份验证，你可以参考以下文章：[示例：使用 Azure AD 服务主体和证书身份验证的 Dynamics 联机](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)。</li>
    |如果在错误消息中看到 `Unable to retrieve authentication parameters from the serviceUri`，则表示你输入了错误的 Dynamics 服务 URL 或代理/防火墙来拦截流量。 |<li>请确保已将正确的服务 URI 置于链接服务中。</li><li>如果使用自承载 IR，请确保防火墙/代理不会拦截对 Dynamics 服务器的请求。</li> |
    |如果在错误消息中看到 `An unsecured or incorrectly secured fault was received from the other party`，则表示从服务器端获得了意外响应。  | <li>如果使用 Office 365 身份验证，请确保用户名和密码正确。 </li><li> 请确保已输入正确的服务 URI。</li><li>如果使用区域 CRM URL（URL 在“crm”后有一个数字），请确保使用正确的区域标识符。</li><li>请联系 Dynamics 支持团队以获得帮助。</li>|
    |如果在错误消息中看到 `No Organizations Found`，则表示你的组织名有误，或者你在服务 URL 中使用了错误的 CRM 区域标识符。|<li>请确保已输入正确的服务 URI。</li><li>如果使用区域 CRM URL（URL 在“crm”后有一个数字），请确保使用正确的区域标识符。</li><li>请联系 Dynamics 支持团队以获得帮助。</li>|
    | 如果看到 `401 Unauthorized` 和 AAD 相关的错误消息，则表示服务主体存在问题。 |按照错误消息中的指导解决服务主体问题。 |
   |对于其他错误，问题通常出在服务器端。 |使用 [XrmToolBox](https://www.xrmtoolbox.com/) 建立连接。 如果错误仍然存在，请与 Dynamics 支持团队联系以获取帮助。 |

## <a name="error-code-dynamicsoperationfailed"></a>错误代码：DynamicsOperationFailed 
 
- **消息**：`Dynamics operation failed with error code: %code;, error message: %message;.` 

- **原因**：此操作在服务器端失败。 

- **建议**：从错误消息 `Dynamics operation failed with error code: {code}` 中提取 Dynamics 操作的错误代码，并参阅 [Web 服务错误代码](/powerapps/developer/data-platform/org-service/web-service-error-codes)一文以了解更多详细信息。 如有必要，可联系 Dynamics 支持团队。 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>错误代码：DynamicsInvalidFetchXml 
  
- **消息**：`The Fetch Xml query specified is invalid.` 

- **原因**：提取 XML 中存在错误。  

- **建议**：修复提取 XML 中的错误。 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>错误代码：DynamicsMissingKeyColumns 
 
- **消息**：`Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **原因**：源数据不包含接收器实体的键列。 

- **建议**：确认键列在源数据中或将源列映射到接收器实体上的键列。 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>错误代码：DynamicsPrimaryKeyMustBeGuid 
 
- **消息**：`The primary key attribute '%attribute;' must be of type guid.` 
 
- **原因**：主键列的类型不是“Guid”。 
 
- **建议**：请确保源数据中的主键列是“Guid”类型。 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>错误代码：DynamicsAlternateKeyNotFound 
 
- **消息**：`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **原因**：提供的备用键不存在，这可能是由错误的键名称或权限不足所引起。 
 
- **建议**： <br/> 
    - 更正键名称中的拼写错误。<br/> 
    - 请确保对该实体具有足够的权限。 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>错误代码：DynamicsInvalidSchemaDefinition 
 
- **消息**：`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **原因**：列映射中的接收器列缺少“type”属性。 
 
- **建议**：使用门户上的 JSON 编辑器，可在列映射中将“type”属性添加到这些列。 

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
