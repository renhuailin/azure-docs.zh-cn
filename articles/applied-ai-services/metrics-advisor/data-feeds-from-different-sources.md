---
title: 将各种数据源连接到指标顾问
titleSuffix: Azure Cognitive Services
description: 将各种数据馈送添加到指标顾问
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 2bb387863baffdd014eedb20d94ea1273860b8c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730610"
---
# <a name="how-to-connect-different-data-sources"></a>操作说明：连接各种数据源

学习本文内容，了解将不同类型的数据源连接到 Azure 指标顾问的设置和要求。 若要了解如何将数据与指标顾问结合使用，请参阅[载入数据](how-tos/onboard-your-data.md)。 

## <a name="supported-authentication-types"></a>支持的身份验证类型

| 身份验证类型 | 说明 |
| ---------------------|-------------|
|**基本** | 需要提供基本参数才能访问数据源。 例如，可以使用连接字符串或密码。 数据馈送管理员可以查看这些凭据。 |
| **Azure 托管标识** | Azure 资源的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)是 Azure Active Directory (Azure AD) 的一项功能。 它为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识功能向支持 Azure AD 身份验证的任何服务进行身份验证。|
| **Azure SQL 连接字符串**| 将 Azure SQL 连接字符串作为凭据实体存储在指标顾问中，并在每次导入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但获得授权的查看者无需了解凭据的详细信息即可创建数据馈送。 |
| **Azure Data Lake Storage Gen2 共享密钥**| 将 Data Lake 帐户密钥作为凭据实体存储在指标顾问中，并在每次导入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但获得授权的查看者无需了解凭据的详细信息即可创建数据馈送。|
| **服务主体**| 将[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)作为凭据实体存储在指标顾问中，并在每次导入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但获得授权的查看者无需了解凭据的详细信息即可创建数据馈送。|
| **密钥保管库中的服务主体**|将 [Key Vault 中的服务主体](/azure-stack/user/azure-stack-key-vault-store-credentials)作为凭据实体存储在指标顾问中，并在每次导入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但查看者无需了解凭据的详细信息即可创建数据馈送。 |


## <a name="data-sources-and-corresponding-authentication-types"></a>数据源和相应的身份验证类型

| 数据源 | 身份验证类型 |
|-------------| ---------------------|
|[Application Insights](#appinsights) | 基本 |
|[Azure Blob 存储 (JSON)](#blob) | 基本<br>托管标识 |
|[Azure Cosmos DB (SQL)](#cosmosdb) | 基本 |
|[Azure 数据资源管理器(Kusto)](#kusto) | 基本<br>托管标识<br>服务主体<br>密钥保管库中的服务主体 |
|[Azure Data Lake Storage Gen2](#adl) | 基本<br>Data Lake Storage Gen2 共享密钥<br>服务主体<br>密钥保管库中的服务主体 |
|[Azure 事件中心](#eventhubs) | 基本 |
|[Azure Monitor 日志](#log) | 基本<br>服务主体<br>密钥保管库中的服务主体 |
|[Azure SQL 数据库 / SQL Server](#sql) | 基本<br>托管标识<br>服务主体<br>密钥保管库中的服务主体<br>Azure SQL 连接字符串 |
|[Azure 表存储](#table) | 基本 | 
|[InfluxDB (InfluxQL)](#influxdb) | 基本 |
|[MongoDB](#mongodb) | 基本 |
|[MySQL](#mysql) | 基本 |
|[PostgreSQL](#pgsql) | 基本|
|[本地文件 (CSV)](#csv) | 基本|

以下部分指定了不同数据源方案中所有身份验证类型所需的参数。 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **应用程序 ID**：用于在使用 Application Insights API 时标识此应用程序。 若要获取应用程序 ID，请按以下步骤操作：

   1. 从 Application Insights 资源中选择“API 访问权限”。
   
      ![显示如何从 Application Insights 资源获取应用程序 ID 的屏幕截图。](media/portal-app-insights-app-id.png)

   2. 将生成的应用程序 ID 复制到指标顾问的“应用程序 ID”字段中。 

* **API 密钥**：浏览器外的应用程序使用 API 密钥访问此资源。 若要获取 API 密钥，请执行以下步骤：

   1. 从 Application Insights 资源中选择“API 访问权限”。

   2. 选择“创建 API 密钥”。

   3. 输入简短说明，选择“读取遥测”选项，然后选择”生成密钥”。

      ![显示如何在 Azure 门户中获取 API 密钥的屏幕截图。](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > 复制并保存此 API 密钥。 它永远不会再显示。 如果丢失此密钥，则必须创建一个新密钥。

   4. 将 API 密钥复制到指标顾问中的“API 密钥”字段。

* **查询**：Application Insights 日志在 Azure 数据资源管理器之上构建，Azure Monitor 日志查询使用同一 Kusto 查询语言的某个版本。 [Kusto 查询语言文档](/azure/data-explorer/kusto/query)应该是编写针对 Application Insights 的查询的主要资源。 

    示例查询：

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    你还可以参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)，获取更具体的示例。
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob 存储 (JSON)</span>

* **连接字符串**：Azure Blob 存储 (JSON) 有两种身份验证类型：

    * **基本**：有关如何检索此字符串的信息，请参阅[配置 Azure 存储连接字符串](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account)。 此外，你还可以在 Azure 门户中访问 Azure Blob 存储资源，并直接在“设置” > “访问密钥”中找到连接字符串。
    
    * **托管标识**：Azure 资源的托管标识可以授权访问 Blob 和队列数据。 该功能使用 Azure AD 凭据，后者来自 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序。 
    
        可以在 Azure 门户中为 Azure Blob 存储资源创建托管标识。 在“访问控制(IAM)”中，选择“角色分配”，然后选择“添加”。 建议的角色类型为：存储 Blob 数据读者。 有关更多详细信息，请参阅[使用托管标识访问 Azure 存储](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1)。
    
        ![显示托管标识 Blob 的屏幕截图。](media/managed-identity-blob.png)
    

* **容器**：指标顾问预计时序数据以 Blob 文件（每个时间戳一个 Blob）的形式存储在单个容器下。 这是容器名称字段。

* **Blob 模板**：指标顾问使用路径在 Blob 存储中查找 JSON 文件。 这是 Blob 文件模板的示例，用于在 Blob 存储中查找 JSON 文件：`%Y/%m/FileName_%Y-%m-%d-%h-%M.json`。 `%Y/%m` 是路径，如果路径中有 `%d`，可以将其添加到 `%m` 的后面。 如果 JSON 文件按日期命名，还可以使用 `%Y-%m-%d-%h-%M.json`。

   支持以下参数：
   
   * `%Y` 是年，格式为 `yyyy`。
   * `%m` 是月，格式为 `MM`。
   * `%d` 是日，格式为 `dd`。
   * `%h` 是小时，格式为 `HH`。
   * `%M` 是分钟，格式为 `mm`。
  
   例如，在以下数据集中，Blob 模板应为 `%Y/%m/%d/00/JsonFormatV2.json`。
  
   ![显示 Blob 模板的屏幕截图。](media/blob-template.png)
  

* **JSON 格式版本**：定义 JSON 文件中的数据架构。 指标顾问支持以下版本。 你可以选择其中一个版本来填充字段：
  
   * **v1**（默认值）

      仅接受“名称”和“值”指标 。 例如： 。
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      还接受“维度”和“时间戳”指标 。 例如： 。
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   每个 JSON 文件只能有一个时间戳。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **连接字符串**：用于访问 Azure Cosmos DB 的连接字符串。 可在 Azure 门户的 Azure Cosmos DB 资源的“密钥”中找到。 有关详细信息，请参阅[保护对 Azure Cosmos DB 中数据的访问](../../cosmos-db/secure-access-to-data.md)。
* **数据库**：要查询的数据库。 在 Azure 门户的“容器”下，转到“浏览”以查找数据库。
* **集合 ID**：要查询的集合 ID。 在 Azure 门户的“容器”下，转到“浏览”以查找集合 ID。
* **SQL 查询**：一个 SQL 查询，用于获取数据并将数据构建为多维时序数据。 可在查询中使用 `@IntervalStart` 和 `@IntervalEnd` 变量。 它们的格式应如下所示：`yyyy-MM-ddTHH:mm:ssZ`。

    示例查询：
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure 数据资源管理器(Kusto)</span>

* **连接字符串**：Azure 数据资源管理器 (Kusto) 有四种身份验证类型：基本、服务主体、Key Vault 中的服务主体和托管标识。 连接字符串中的数据源应采用 URI 格式（以“https”开头）。 可以在 Azure 门户中找到该 URI。
    
    * **基本**：指标顾问支持使用 Azure AD 应用程序身份验证访问 Azure 数据资源管理器 (Kusto)。 你需要创建并注册一个 Azure AD 应用程序，然后授权其访问 Azure 数据资源管理器数据库。 有关详细信息，请参阅[在 Azure 数据资源管理器中创建 Azure AD 应用注册](/azure/data-explorer/provision-azure-ad-app)。 下面是连接字符串的示例：
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **服务主体**：服务主体是根据应用程序对象创建的具体实例。 服务主体从该应用程序对象继承某些属性。 服务主体对象定义应用可在特定租户中实际执行的操作、可访问应用的用户以及应用可访问的资源。 若要在指标顾问中使用服务主体，请执行以下操作：
    
        1. 创建 Azure 应用程序注册。 有关详细信息，请参阅[在 Azure 数据资源管理器中创建 Azure AD 应用注册](/azure/data-explorer/provision-azure-ad-app)。

        1. 管理 Azure 数据资源管理器数据库权限。 有关详细信息，请参阅[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)。 

        1. 在指标顾问中创建凭据实体。 了解如何在指标顾问中[创建凭据实体](how-tos/credential-entity.md)，以便在为服务主体身份验证类型添加数据馈送时选择该实体。 
        
        下面是连接字符串的示例：
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Key Vault 中的服务主体**：Azure Key Vault 有助于保护云应用和服务使用的加密密钥和机密值。 通过使用 Key Vault，可对密钥和机密值进行加密。 你应该先创建服务主体，然后将服务主体存储在 Key Vault 中。 有关详细信息，请参阅[为 Key Vault 中的服务主体创建凭据实体](how-tos/credential-entity.md#sp-from-kv)，以按照设置 Key Vault 中的服务主体的详细过程进行操作。 下面是连接字符串的示例： 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **托管标识**：Azure 资源的托管标识可以授权访问 Blob 和队列数据。 托管标识使用 Azure AD 凭据，后者来自 Azure 虚拟机、函数应用、虚拟机规模集和其他服务中运行的应用程序。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随云中运行的应用程序一起存储。 了解如何[通过托管标识授权](../../storage/common/storage-auth-aad-msi.md#enable-managed-identities-on-a-vm)。 
    
        可以在 Azure 门户中为 Azure 数据资源管理器 (Kusto) 创建托管标识。 选择“权限” > “添加”。 建议的角色类型为：管理员/查看者。
        
        ![显示 Kusto 托管标识的屏幕截图。](media/managed-identity-kusto.png)

        下面是连接字符串的示例： 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **查询**：若要获取数据并将数据构建为多维时序数据，请参阅 [Kusto 查询语言](/azure/data-explorer/kusto/query)。 可在查询中使用 `@IntervalStart` 和 `@IntervalEnd` 变量。 它们的格式应如下所示：`yyyy-MM-ddTHH:mm:ssZ`。

    示例查询：
    
    ``` Kusto
   [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
   ```

    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **帐户名称**：Azure Data Lake Storage Gen2 的身份验证类型包括基本、Azure Data Lake Storage Gen2 共享密钥、服务主体和 Key Vault 中的服务主体。
    
    * **基本**：Azure Data Lake Storage Gen2 的帐户名称。 可以在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源的“访问密钥”中找到它。 

    * **Azure Data Lake Storage Gen2 共享密钥**：首先，指定用于访问 Azure Data Lake Storage Gen2 的帐户密钥（这与基本身份验证类型中的帐户密钥相同）。 可以在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源的“访问密钥”中找到它。 然后，为 Azure Data Lake Storage Gen2 共享密钥类型[创建凭据实体](how-tos/credential-entity.md)，并填写帐户密钥。 

        帐户名称与基本身份验证类型相同。
    
    * **服务主体**：服务主体是根据应用程序对象创建的具体实例，并从该应用程序对象继承某些属性。 服务主体是在使用应用程序的每个租户中创建的，并引用全局唯一应用对象。 服务主体对象定义应用可在特定租户中实际执行的操作、可访问应用的用户以及应用可访问的资源。

        帐户名称与基本身份验证类型相同。
    
        **步骤 1：** 创建并注册 Azure AD 应用程序，然后授权该应用程序访问数据库。 有关详细信息，请参阅[创建 Azure AD 应用注册](/azure/data-explorer/provision-azure-ad-app)。

        **步骤 2：** 分配角色。
        
        1. 在 Azure 门户中，转到“存储帐户”服务。
        
        2. 选择要用于此应用程序注册的 Azure Data Lake Storage Gen2 帐户。

        3. 选择“访问控制 (IAM)”。

        4. 选择“+ 添加”，然后从菜单中选择“添加角色分配” 。

        5. 将“选择”字段设置为 Azure AD 应用程序名称，将角色设置为“存储 Blob 数据参与者”。 再选择“保存”。
        
        ![显示角色分配步骤的屏幕截图。](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **步骤 3：** 在指标顾问中[创建凭据实体](how-tos/credential-entity.md)，以便在为服务主体身份验证类型添加数据馈送时选择该实体。 
        
    * **Key Vault 中的服务主体**：Key Vault 有助于保护云应用和服务使用的加密密钥和机密值。 通过使用 Key Vault，可对密钥和机密值进行加密。 首先创建服务主体，然后将服务主体存储在 Key Vault 中。 有关更多详细信息，请参阅[为 Key Vault 中的服务主体创建凭据实体](how-tos/credential-entity.md#sp-from-kv)。 帐户名称与基本身份验证类型相同。

* **帐户密钥**（仅基本身份验证类型需要）：指定用于访问 Azure Data Lake Storage Gen2 的帐户密钥。 可以在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源的“访问密钥”中找到它。

* **文件系统名称(容器)** ：对于指标顾问，将时序数据以 Blob 文件（每个时间戳一个 Blob）的形式存储在单个容器下。 这是容器名称字段。 可以在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 实例中找到它。 在“Data Lake Storage”中，选择“容器”，然后你会看到容器名称。

* **目录模板**：这是 Blob 文件的目录模板。 支持以下参数：

   * `%Y` 是年，格式为 `yyyy`。
   * `%m` 是月，格式为 `MM`。
   * `%d` 是日，格式为 `dd`。
   * `%h` 是小时，格式为 `HH`。
   * `%M` 是分钟，格式为 `mm`。

   每日指标的查询示例：`%Y/%m/%d`。

   每小时指标的查询示例：`%Y/%m/%d/%h`。

* **文件模板**：指标顾问使用路径在 Blob 存储中查找 JSON 文件。 以下是 Blob 文件模板的示例，用于在 Blob 存储中查找 JSON 文件：`%Y/%m/FileName_%Y-%m-%d-%h-%M.json`。 `%Y/%m` 是路径，如果路径中有 `%d`，可以将其添加到 `%m` 的后面。 
   
   支持以下参数：
   
   * `%Y` 是年，格式为 `yyyy`。
   * `%m` 是月，格式为 `MM`。
   * `%d` 是日，格式为 `dd`。
   * `%h` 是小时，格式为 `HH`。
   * `%M` 是分钟，格式为 `mm`。

   指标顾问支持 JSON 文件中的数据架构，如以下示例所示：

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure 事件中心</span>

* **限制**：请注意以下集成限制。

   * 在公共预览版中，指标顾问与事件中心的集成目前不支持一个指标顾问实例中有三个以上的活动数据馈送。
   * 指标顾问始终开始使用最新偏移中的消息，包括重新激活暂停的数据馈送时。
   
      * 数据馈送暂停期间的消息将丢失。
      * 创建数据馈送时，数据馈送引入开始时间会自动设置为当前的协调世界时时间戳。 这个时间仅供参考。

   * 每个使用者组只能使用一个数据馈送。 若要从另一个已删除的数据馈送重新使用使用者组，你需要在删除后至少等待十分钟。
   * 创建数据馈送后，不能修改连接字符串和使用者组。
   * 对于事件中心消息，仅支持 JSON，并且 JSON 值不能是嵌套的 JSON 对象。 顶级元素可以是 JSON 对象或 JSON 数组。
    
    有效消息如下：

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **连接字符串**：转到事件中心实例。 然后添加新策略或选择现有共享访问策略。 复制弹出面板中的连接字符串。
    ![事件中心的屏幕截图。](media/datafeeds/entities-eventhubs.jpg)
    
    ![共享访问策略的屏幕截图。](media/datafeeds/shared-access-policies.jpg)

    下面是连接字符串的示例： 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **使用者组**：[使用者组](../../event-hubs/event-hubs-features.md#consumer-groups)是整个事件中心的视图（状态、位置或偏移）。
可以在 Azure 事件中心实例的“使用者组”菜单上找到它。 一个使用者组只能服务于一个数据馈送。 为每个数据馈送创建一个新的使用者组。
* **时间戳**（可选）：如果用户数据源不包含时间戳字段，则指标顾问使用事件中心时间戳作为事件时间戳。 时间戳字段是可选的。 如果未选择时间戳列，服务将使用排队时间作为时间戳。

    时间戳字段必须符合以下两种格式之一：
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * 从 `1970-01-01T00:00:00Z` 这个纪元开始的秒数或毫秒数。
    
    时间戳将按粒度左对齐。 例如，如果时间戳为 `2019-01-01T00:03:00Z`，粒度为 5 分钟，则指标顾问将时间戳对齐到 `2019-01-01T00:00:00Z`。 如果事件时间戳为 `2019-01-01T00:10:00Z`，则指标顾问直接使用该时间戳，不进行任何对齐。 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Azure Monitor 日志</span>

Azure Monitor 日志具有以下身份验证类型：基本、服务主体和 Key Vault 中的服务主体。
* **基本**：需要填写“租户 ID”、“客户端 ID”、“客户端密码”和“工作区 ID”。
   若要获取“租户 ID” 、“客户端 ID”和“客户端密码”，请参阅[注册应用或 Web API](../../active-directory/develop/quickstart-register-app.md)。 你可以在 Azure 门户中找到“工作区 ID”。
   
    ![显示在 Azure 门户中的何处找到工作区 ID 的屏幕截图。](media/workspace-id.png)
    
* **服务主体**：服务主体是根据应用程序对象创建的具体实例，并从该应用程序对象继承某些属性。 服务主体是在使用应用程序的每个租户中创建的，并引用全局唯一应用对象。 服务主体对象定义应用可在特定租户中实际执行的操作、可访问应用的用户以及应用可访问的资源。
    
    **步骤 1：** 创建并注册 Azure AD 应用程序，然后授权该应用程序访问数据库。 有关详细信息，请参阅[创建 Azure AD 应用注册](/azure/data-explorer/provision-azure-ad-app)。

    **步骤 2：** 分配角色。
    1. 在 Azure 门户中，转到“存储帐户”服务。
    2. 选择“访问控制 (IAM)”。
    3. 选择“+ 添加”，然后从菜单中选择“添加角色分配” 。
    4. 将“选择”字段设置为 Azure AD 应用程序名称，将角色设置为“存储 Blob 数据参与者”。 再选择“保存”。
    
        ![显示如何分配角色的屏幕截图。](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **步骤 3：** 在指标顾问中[创建凭据实体](how-tos/credential-entity.md)，以便在为服务主体身份验证类型添加数据馈送时选择该实体。 
        
* **Key Vault 中的服务主体**：Key Vault 有助于保护云应用和服务使用的加密密钥和机密值。 通过使用 Key Vault，可对密钥和机密值进行加密。 首先创建服务主体，然后将服务主体存储在 Key Vault 中。 有关更多详细信息，请参阅[为 Key Vault 中的服务主体创建凭据实体](how-tos/credential-entity.md#sp-from-kv)。 

* **查询**：指定查询。 有关详细信息，请参阅 [Azure Monitor 中的日志查询](../../azure-monitor/logs/log-query-overview.md)。

    示例查询：

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL 数据库 | SQL Server</span>

* **连接字符串**：Azure SQL 数据库和 SQL Server 的身份验证类型包括基本、托管标识、Azure SQL 连接字符串、服务主体和 Key Vault 中的服务主体。
    
    * **基本**：指标顾问接受用于 SQL Server 数据源的 [ADO.NET 样式连接字符串](/dotnet/framework/data/adonet/connection-string-syntax)。
    下面是连接字符串的示例： 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**托管标识**</span>：Azure 资源的托管标识可以授权访问 Blob 和队列数据。 它使用 Azure AD 凭据来执行此操作，这些凭据来自 Azure 虚拟机、函数应用、虚拟机规模集和其他服务中运行的应用程序。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随云中运行的应用程序一起存储。 若要[启用托管实体](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)，请执行以下步骤：
    1. 启用系统分配的托管标识只需单击一次即可。 在 Azure 门户中，针对你的指标顾问工作区，转到“设置” > “标识” > “系统分配”。 然后将状态设置为“打开”。 
    
        ![显示如何将状态设置为“打开”的屏幕截图。](media/datafeeds/set-identity-status.png)

    1. 启用 Azure AD 身份验证。 在 Azure 门户中，针对你的数据源，转到“设置” > “Active Directory 管理员”。选择“设置管理员”，然后选择一个 Azure AD 用户帐户作为服务器管理员。 然后选择“选择”。 
    
        ![显示如何设置管理员的屏幕截图。](media/datafeeds/set-admin.png)

    1. 在指标顾问中启用托管标识。 可以在数据库管理工具或 Azure 门户中编辑查询。
    
        **管理工具**：在数据库管理工具中，在身份验证字段中选择“Active Directory - 支持 MFA 的通用方法”。 在“用户名”字段中，输入在步骤 2 中设置为服务器管理员的 Azure AD 帐户的名称。 例如，该名称可能是 `test@contoso.com`。
    
        ![显示如何设置连接详细信息的屏幕截图。](media/datafeeds/connection-details.png)
        
        **Azure 门户**：在 SQL 数据库中，选择“查询编辑器”，然后登录管理员帐户。
        ![显示如何在 Azure 门户中编辑查询的屏幕截图。](media/datafeeds/query-editor.png)

        然后在查询窗口中，运行以下命令（注意，其效果与管理工具方法相同）：
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > `MI Name` 是指标顾问中的托管标识名称（对于服务主体，应将其替换为服务主体名称）。 有关详细信息，请参阅[通过托管标识授权](../../storage/common/storage-auth-aad-msi.md#enable-managed-identities-on-a-vm)。 
            
        下面是连接字符串的示例： 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Azure SQL 连接字符串**： 
      

        下面是连接字符串的示例： 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **服务主体**：服务主体是根据应用程序对象创建的具体实例，并从该应用程序对象继承某些属性。 服务主体是在使用应用程序的每个租户中创建的，并引用全局唯一应用对象。 服务主体对象定义应用可在特定租户中实际执行的操作、可访问应用的用户以及应用可访问的资源。
    
        **步骤 1：** 创建并注册 Azure AD 应用程序，然后授权该应用程序访问数据库。 有关详细信息，请参阅[创建 Azure AD 应用注册](/azure/data-explorer/provision-azure-ad-app)。

        **步骤 2：** 按照之前在 [SQL Server 中的托管标识](#jump)中记录的步骤进行操作。 

        **步骤 3：** 在指标顾问中[创建凭据实体](how-tos/credential-entity.md)，以便在为服务主体身份验证类型添加数据馈送时选择该实体。 

        下面是连接字符串的示例： 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Key Vault 中的服务主体**：Key Vault 有助于保护云应用和服务使用的加密密钥和机密值。 通过使用 Key Vault，可对密钥和机密值进行加密。 首先创建服务主体，然后将服务主体存储在 Key Vault 中。 有关更多详细信息，请参阅[为 Key Vault 中的服务主体创建凭据实体](how-tos/credential-entity.md#sp-from-kv)。 你还可以在 Azure SQL Server 资源的“设置” > “连接字符串”中找到连接字符串。
        
        下面是连接字符串的示例： 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **查询**：使用 SQL 查询获取数据并将数据构建为多维时序数据。 你可以在查询中使用 `@IntervalStart` 和 `@IntervalEnd` 来帮助获取某个时间间隔内的预期指标值。 它们的格式应如下所示：`yyyy-MM-ddTHH:mm:ssZ`。


    示例查询：
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure 表存储</span>

* **连接字符串**：创建一个共享访问签名 (SAS) URL，并在此处填写。 生成 SAS URL 的最直接方法是使用 Azure 门户。 首先，在“设置”下，转到要访问的存储帐户。 然后选择“共享访问签名”。 选中“表”和“对象”复选框，然后选择“生成 SAS 和连接字符串”。 在指标顾问工作区中，将“表服务 SAS URL”复制并粘贴到文本框中。

    ![显示如何在 Azure 表存储中生成共享访问签名的屏幕截图。](media/azure-table-generate-sas.png)

* **表名**：指定要查询的表。 可以在 Azure 存储帐户实例中找到它。 在“表服务”部分中，选择“表”。

* **查询**：可以在查询中使用 `@IntervalStart` 和 `@IntervalEnd` 来帮助获取某个时间间隔内的预期指标值。 它们的格式应如下所示：`yyyy-MM-ddTHH:mm:ssZ`。

    示例查询：
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **连接字符串**：用于访问 InfluxDB 的连接字符串。
* **数据库**：要查询的数据库。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。

    示例查询：

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。

* **用户名**：这对于身份验证是可选的。 
* **密码**：这对于身份验证是可选的。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **连接字符串**：用于访问 MongoDB 的连接字符串。
* **数据库**：要查询的数据库。
* **查询**：一个命令，用于获取数据并将数据构建为多维时序数据以供引入。 在 [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html) 上验证该命令。

    示例查询：

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **连接字符串**：用于访问 MySQL DB 的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。

    示例查询：

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **连接字符串**：用于访问 PostgreSQL DB 的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。

    示例查询：

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    有关详细信息，请参阅[教程：编写有效查询](tutorials/write-a-valid-query.md)。
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">本地文件 (CSV)</span>

> [!NOTE]
> 此功能仅用于专注于异常情况检测的快速系统评估。 它只接受来自本地 CSV 的静态数据，并对单个时序数据进行异常情况检测。 若要分析多维指标，包括实时数据引入、异常情况通知、根本原因分析和跨指标事件分析，请使用其他受支持的数据源。

**对 CSV 数据的要求：**
- 至少具有一列，表示要分析的度量值。 为了获得更好更快的用户体验，请尝试使用包含两列的 CSV 文件：时间戳列和指标列。 时间戳格式应为：`2021-03-30T00:00:00Z`，`seconds` 部分最好为 `:00Z`。 每条记录之间的时间粒度应该相同。
- 时间戳列是可选的。 如果没有时间戳，指标顾问将使用从今天开始的时间戳（`00:00:00` 协调世界时）。 该服务以一小时为间隔映射行中的每个度量值。
- 在数据引入期间不会发生重新排序或间隙填充。 确保 CSV 文件中的数据按时间戳升序 (ASC) 排序。
 
## <a name="next-steps"></a>后续步骤

* 当你等待指标数据引入系统时，请阅读[如何管理数据馈送配置](how-tos/manage-data-feeds.md)。
* 引入指标数据后，可以[配置指标并微调检测配置](how-tos/configure-metrics.md)。
