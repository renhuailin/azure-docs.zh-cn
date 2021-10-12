---
title: 开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy | Microsoft Docs
description: 演练 Azure Sentinel ML 笔记本的 Azure Sentinel 入门指南，通过 MSTICPy 和查询了解 Azure Sentinel 笔记本的基础知识。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: e5dc225d96021761fcdf12d5adf0f1fa371ebdb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594354"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-azure-sentinel"></a>教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy

本教程介绍如何运行“Azure Sentinel ML 笔记本入门指南”笔记本，该笔记本设置了用于在 Azure Sentinel 中运行 Jupyter 笔记本和运行简单数据查询的基本配置。

“Azure Sentinel ML 笔记本入门指南”笔记本使用 MSTICPy，这是一个由 Microsoft 构建的网络安全工具 Python 库，可提供威胁搜寻和调查功能。

MSTICPy 减少了客户需要为 Azure Sentinel 编写的代码量，并提供以下功能：

- 数据查询功能，针对 Azure Sentinel 表、Microsoft Defender for Endpoint、Splunk 和其他数据源。
- 使用 TI 提供商（例如 VirusTotal 和 AlienVault OTX）进行威胁情报查找。
- 扩充功能，如 IP 地址的地理定位、入侵指标 (IoC) 提取和 WhoIs 查找。
- 使用事件时间线、流程树和地理映射的可视化工具。
- 高级分析，例如时序分解、异常检测和聚类分析。

本教程中的步骤介绍如何在 Azure ML 工作区中通过 Azure Sentinel 来运行“Azure Sentinel ML 笔记本入门指南”笔记本。 在其他环境（包括本地）中执行类似步骤以运行笔记本时，也可以使用此教程作为指南。

有关更多信息，请参阅[使用笔记本进行调查](hunting.md#use-notebooks-to-power-investigations)和[使用 Jupyter 笔记本搜寻安全威胁](notebooks.md)。

> [!NOTE]
> 某些 Azure Sentinel 笔记本不使用 MSTICPy，例如“凭据扫描器”笔记本或 PowerShell 和 C# 示例。 如果笔记本不使用 MSTICpy，则不需要本文中所述的 MSTICPy 配置。
>

## <a name="prerequisites"></a>先决条件

- 若要在 Azure Sentinel 中使用笔记本，请确保你具有所需的权限。 有关详细信息，请参阅[管理对 Azure Sentinel 笔记本的访问](notebooks.md#manage-access-to-azure-sentinel-notebooks)。

- 若要执行本教程中的步骤，需要 Python 3.6 或更高版本。 在 Azure ML 中，可以使用 Python 3.8 内核（推荐）或 Python 3.6 内核。

- 此笔记本使用 [MaxMind GeoLite2](https://www.maxmind.com) 地理定位查找服务来查找 IP 地址。 若要使用 MaxMind GeoLite2 服务，需要帐户密钥。 您可以在 [Maxmind 注册页面](https://www.maxmind.com/en/geolite2/signup)注册一个免费帐户和密钥。

- 此笔记本使用 [VirusTotal](https://www.virustotal.com) (VT) 作为威胁情报源。 若要使用 VirusTotal 威胁情报查找，需要一个 VirusTotal 帐户和 API 密钥。

    可以在 [VirusTotal 入门页](https://developers.virustotal.com/v3.0/reference#getting-started)上注册一个免费的 VT 帐户。 如果已经是 VirusTotal 用户，可以使用现有密钥。

    > [!WARNING]
    > 如果使用 VT 企业密钥，请存储在 Azure 密钥保管库，而不是存储在 msticpyconfig.yaml 文件中。 有关详细信息，请参阅 MSTICPY 文档中的[将机密指定为密钥保管库机密](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)。
    >
    > 如果不想立即设置 Azure 密钥保管库，请注册并使用免费帐户，直到可以设置密钥保管库存储。

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>运行并初始化“入门指南”笔记本

此过程介绍如何启动笔记本并初始化 MSTICpy。


1. 在 Azure Sentinel 中，在左侧选择“笔记本”。

1. 从“模板”选项卡中，选择“Azure Sentinel ML 笔记本入门指南” > “保存笔记本”以将其保存到 Azure ML 工作区。  

    选择“启动笔记本”以运行笔记本。 该笔记本包含一系列单元：

    - Markdown 单元包含文本和图形以及使用笔记本的说明
    - “代码”单元包含执行笔记本功能的可执行代码

    **读取和运行代码单元**

    请按顺序读取并运行代码单元。 跳过单元或乱序运行可能会导致笔记本随后出现错误。

    选择每个单元左侧的播放按钮即可运行每个单元。 根据正在执行的功能，单元中的代码可能运行得非常快，或者可能需要几秒钟才能完成。

    运行时，播放按钮变为加载微调控件，并且在单元底部显示状态“`Executing`”以及已用时间。

    > [!TIP]
    > 如果笔记本似乎没有按照描述的那样工作，请重新启动内核并从头开始运行笔记本。 例如，如果“入门指南”笔记本中的任何单元运行时间超过一分钟，请尝试重新启动内核并重新运行笔记本。
    >
    > “入门指南”笔记本包含有关 Jupyter 笔记本基本使用的说明，包括重新启动 Jupyter 内核。
    >

    阅读并运行“Jupyter Notebook 简介”部分中的单元后，便可以开始配置任务，从“设置笔记本环境”部分开始。 

1. 运行笔记本的“设置笔记本环境”部分中的第一个代码单元，其中包括以下代码：

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    初始化状态显示在输出中。 由于尚未配置任何内容，应该会出现有关 `Missing msticpyconfig.yaml` 文件中缺少设置的配置警告。

> [!NOTE]
> 大多数 Azure Sentinel 笔记本以 MSTICpy 初始化单元开始，该单元的作用是：
>
> - 定义笔记本所需的 Python 和 MSTICPy 的最低版本。
> - 确保安装了最新版本的 MSTICPy。
> - 导入并运行 `init_notebook` 函数。
>

## <a name="create-your-configuration-file"></a>创建配置文件

基本初始化后，便可以使用基本设置创建配置文件，以便使用 MSTICPy。

许多 Azure Sentinel 笔记本连接到 [VirusTotal](https://www.virustotal.com) (VT) 等外部服务以收集和扩充数据。 若要连接到这些服务，需要设置和存储配置详细信息，例如身份验证令牌。 请在配置文件中包含此数据，这样便无需在每次使用笔记本时都必须输入身份验证令牌和工作区详细信息。

MSTICPy 使用 msticpyconfig.yaml 存储各种配置详细信息。  默认情况下，一个 msticpyconfig.yaml 文件由笔记本初始化函数生成。 如果[从 Azure Sentinel 门户克隆此笔记本](#run-and-initialize-the-getting-started-guide-notebook)，配置文件将填充 Azure Sentinel 工作区数据。 此数据是从 config.json 文件中读取的，该文件是在启动笔记本时在 Azure ML 工作区中创建的。 有关详细信息，请参阅配置 [MSTICPy 包配置文档](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)。

以下部分介绍了如何向 msticpyconfig.yaml 文件添加其他配置详细信息。

> [!NOTE]
> 如果再次运行“入门指南”笔记本，并且已经有一个最低配置的 msticpyconfig.yaml 文件，则 `init_notebook` 函数不会覆盖或修改你现有的文件。
>

> [!TIP]
> 在任何时间点，选择 MSTICPy 配置工具中的 -Help 下拉菜单即可获得更多说明和详细文档的链接。
>

### <a name="display-the-msticpy-settings-editor"></a>显示 MSTICPy 设置编辑器

1. 在代码单元中，运行以下代码以导入 `MpConfigEdit` 工具并显示 msticpyconfig.yaml 文件的设置编辑器：

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    例如：

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="MSTICPy 设置编辑器的屏幕截图。":::

    设置编辑器中显示的自动创建的 msticpyconfig.yaml 文件包含 Azure Sentinel 部分中的两个条目。 这些都填充了从中克隆笔记本的 Azure Sentinel 工作区的详细信息。 一个条目包含你的工作区名称，另一个条目名为 Default。

    MSTICPy 允许存储多个 Azure Sentinel 工作区的配置并允许切换配置。 Default 条目可在默认情况下对“主”工作区进行身份验证，而无需明确命名。 如果添加其他工作区，可以将其中任何一个配置为 Default 条目。

    > [!NOTE]
    > 在 Azure ML 环境中，设置编辑器可能需要 10 到 20 秒才会出现。

1. 验证当前的设置，然后选择“保存设置”。

### <a name="add-threat-intelligence-provider-settings"></a>添加威胁情报提供程序设置

此过程描述了如何将 [VirusTotal API 密钥](#prerequisites)存储在 msticpyconfig.yaml 文件中。 可以选择将 API 密钥上传到 Azure 密钥保管库，但必须先配置密钥保管库设置。 有关详细信息，请参阅[配置密钥保管库设置](#configure-key-vault-settings)。

**在 MSTICPy 设置编辑器中添加 VirusTotal 详细信息：**

1. 在代码单元中输入以下代码并运行：

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. 在“TI 提供程序”选项卡中，选择“添加提供程序” > “VirusTotal” > “添加”。   

1. 在“身份验证密钥”下，选择“存储”选项旁边的“文本”。  

1. 在“值”字段中，粘贴 API 密钥。

1. 选择更新，然后选择设置编辑器底部的“保存设置”。 

> [!TIP]
> 有关其他受支持的威胁情报提供程序的详细信息，请参阅 MSTICPy 文档中的[威胁情报提供程序](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html)和 [Azure Sentinel 中的威胁情报集成](threat-intelligence-integration.md)。
>
### <a name="add-geoip-provider-settings"></a>添加 GeoIP 提供程序设置

此过程描述了如何在 msticpyconfig.yaml 文件中存储 [MaxMind GeoLite2 帐户密钥](#prerequisites)，这样你的笔记本便可以使用针对 IP 地址的地理定位查找服务。

在 MSTICPy 设置编辑器中添加 GeoIP 提供程序设置：

1. 在一个空白的代码单元中输入以下代码并运行：

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. 在“GeoIP 提供程序”选项卡中，选择“添加提供程序” > “GeoIPLite” > “添加”。   

1. 在“值”字段中，输入 MaxMind 帐户密钥。

1. 如果需要，更新用于存储下载的 GeoIP 数据库的默认 ~/.msticpy 文件夹。

    - 在 Windows 上，此文件夹映射到 %USERPROFILE%/.msticpy。
    - 在 Linux 或 macOS 上，此路径映射到主文件夹中的 .msticpy 文件夹。


> [!TIP]
> 有关其他支持的地理定位查找服务的更多信息，请参阅 [MSTICPy GeoIP 提供程序文档](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)。
>

### <a name="configure-azure-cloud-settings"></a>配置 Azure Cloud 设置

如果你的组织不使用 Azure 公有云，则必须在设置中指定此项才能成功进行身份验证并使用来自 Azure Sentinel 和 Azure 的数据。 有关详细信息，请参阅[指定 Azure 云和默认 Azure 身份验证方法](#specify-the-azure-cloud-and-azure-authentication-methods)。

### <a name="validate-settings"></a>验证设置

在设置编辑器中选择“验证设置”。

应该会出现有关配置缺少的警告消息，但不应收到任何有关威胁情报提供程序或 GeoIP 提供程序设置的警告消息。

根据你的环境，可能还需要[配置密钥保管库设置](#configure-key-vault-settings)或[指定 Azure 云](#specify-the-azure-cloud-and-azure-authentication-methods)。

如果由于验证而需要进行任何更改，请进行这些更改，然后选择“保存设置”。

完成后，选择“关闭”按钮以隐藏验证输出。

有关详细信息，请参阅[适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置](notebooks-msticpy-advanced.md)

## <a name="load-saved-msticpy-settings"></a>加载已保存的 MSTICPy 设置

在[创建配置文件](#create-your-configuration-file)过程中，你已将设置保存到你的本地 msticpyconfig 文件。

但在重新启动内核或运行另一个笔记本之前，MSTICPy 不会自动重新加载这些设置。 若要强制 MSTICPy 从新配置文件重新加载，请使用以下代码继续下一个代码单元并运行它：

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>测试笔记本

由于已经初始化了你的环境并为工作区配置了基本设置，因此要使用 MSTICPy `QueryProvider` 类来测试笔记本。 `QueryProvider`  查询数据源，在本例中为 Azure Sentinel 工作区，并使查询的数据可用于在笔记本中查看和分析。

使用以下过程创建 `QueryProvider` 类的实例、从笔记本向 Azure Sentinel 进行身份验证，以及使用各种不同的参数选项查看和运行查询。

> [!TIP]
> 可以加载 `QueryProvider` 的多个实例以用于多个 Azure Sentinel 工作区或其他数据提供程序，例如 Microsoft Defender for Endpoint。
>

### <a name="load-the-queryprovider"></a>加载 QueryProvider

要为 `AzureSentinel` 加载 `QueryProvider`，请使用以下代码转到单元并运行该单元：

```python
# Initialize a QueryProvider for Azure Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> 如果在加载 Azure Sentinel 驱动程序时看到警告 `Runtime dependency of PyGObject is missing`，请参阅[错误: 缺少 PyGObject 的运行时依赖项](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error)。
此警告不会影响笔记本功能。
>

### <a name="authenticate-to-your-azure-sentinel-workspace-from-your-notebook"></a>从笔记本对 Azure Sentinel 工作区进行身份验证

使用带有 Azure 凭据的[设备授权](/azure/active-directory/develop/v2-oauth2-device-code)对 Azure Sentinel 工作区进行身份验证。

设备授权可以生成作为身份验证过程过程中必须提供的一次性设备代码，从而增加了额外一层身份验证。

使用设备授权进行身份验证：

1. 运行以下代码单元以生成并显示设备代码：

   ```python
   # Get the Azure Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Azure Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    例如：

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="显示设备授权代码的屏幕截图。":::

1. 选择指定的代码并将其复制到剪贴板。 然后，转到 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 并在提示的位置粘贴代码。

1. 看到已登录的确认消息时，关闭浏览器选项卡返回到 Azure Sentinel 中的笔记本。

   笔记本中将显示类似于以下内容的输出：

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="显示设备授权过程已完成的屏幕截图。":::

**使用 Azure CLI 缓存登录令牌**

若要避免在重新启动内核或运行其他笔记本的情况下重新进行身份验证，可以使用 Azure CLI 来缓存登录令牌。

计算实例上的 Azure CLI 组件缓存一个刷新令牌，可供重复使用，直到令牌超时。MSTICPy 会自动使用 Azure CLI 凭据（如果可用）。

若要使用进行身份验证 Azure CLI，请在空白单元中输入以下内容并运行：

```python
!az login
```

> [!NOTE]
> 如果重启计算实例或切换到其他实例，将需要重新进行身份验证。 有关详细信息，请参阅 Azure Sentinel 笔记本 GitHub 存储库 wiki 中的[使用 Azure CLI 缓存凭据部分](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI)。
>

### <a name="view-the-azure-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>查看 Azure Sentinel 工作区数据架构和内置的 MSTICPy 查询

连接到 Azure Sentinel QueryProvider 后，可以通过查询 Azure Sentinel 工作区数据架构来了解可查询的数据类型。

Azure Sentinel QueryProvider 有一个 `schema_tables` 属性，可提供架构表的列表，还有一个 `schema` 属性，其中还包括每个表的列名和数据类型。

查看 Azure Sentinel 架构中的前 10 个表：

转到包含以下代码的下一个单元，并运行该单元。 可以省略 `[:10]` 以列出工作区中的所有表。

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

随即显示以下输出：

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy 还包含许多可供运行的内置查询。 使用 `.list_queries()` 列出可用查询，如果在调用时包含问号 (`?`) 作为参数，则可以获取有关查询的特定详细信息。 或者，也可以在查询浏览器中查看查询列表和关联帮助。

查看可用查询的示例：

转到包含以下代码的下一个单元，并运行该单元。 可以省略 `[::5]` 以列出所有查询。

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

随即显示以下输出：

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

通过传递 `?` 作为参数来获取有关查询的帮助：

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

随即显示以下输出：

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

在可滚动的可筛选列表中查看表和查询：

转到包含以下代码的下一个单元，并运行该单元：

```python
qry_prov.browse_queries()
```

对于选定的查询，将显示所有必需的参数和可选参数，以及查询的完整文本。 例如：

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="在可滚动、可筛选列表中显示的表和查询的屏幕截图。":::

有关更多信息，请参阅 MSTICPy 文档中的[运行预定义查询](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query)。

> [!NOTE]
> 虽然无法从浏览器运行查询，但可以在每个查询的末尾复制并粘贴示例，以便在笔记本中的其他位置运行。
>

### <a name="run-queries-with-time-parameters"></a>运行带有时间参数的查询

大多数查询都需要时间参数。 日期/时间字符串输入起来很乏味，而且在多个地方修改它们很容易出错。

每个查询提供程序都具有查询的默认开始时间和结束时间参数。 只要调用时间参数，默认情况下都会使用这些时间参数。 可以通过打开 `query_time` 控件更改默认时间范围。 更改将一直有效，直到再次更改。

转到包含以下代码的下一个单元，并运行该单元：


```python
# Open the query time control for your query provider
qry_prov.query_time
```

根据需要设置 `start` 和 `end` 时间。 例如：

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="为查询设置默认时间参数的屏幕截图。":::

### <a name="run-a-query-using-the-built-in-time-range"></a>使用内置时间范围运行查询

将以 [Pandas DataFrame](https://pandas.pydata.org) 形式返回查询结果，这是一种表格数据结构，类似于电子表格或数据库表。 可以使用 [pandas 函数](https://pandas.pydata.org/docs/user_guide/10min.html)对查询结果执行额外的筛选和分析。

下面的代码单元使用查询提供程序默认时间设置运行查询。 可以更改此范围，并再次运行代码单元以查询新的时间范围。

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

输出显示结果的前五行。 例如：

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="使用内置时间范围进行查询运行的屏幕截图。":::

如果没有数据，则只显示列标题。

### <a name="run-a-query-using-a-custom-time-range"></a>使用自定义时间范围运行查询

还可以创建一个新的查询时间对象并将其作为参数传递给查询，这样你便可以针对不同的时间范围运行一次性查询，而不会影响查询提供程序的默认值。

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

设置所需的时间范围后，可以将时间范围传递给查询函数，在与之前代码不同的单元中运行以下代码：

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

还可以使用 `start` 和 `end` 参数将日期时间值作为 Python 日期时间或日期时间字符串传递：

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>自定义查询

可以通过添加额外的查询逻辑来自定义内置查询，或使用 `exec_query` 函数来运行完整查询。

例如，大多数内置查询都支持 `add_query_items` 参数，可以使用该参数将筛选器或其他操作附加到查询中。

1. 运行以下代码单元以添加按警报名称汇总警报数量的数据框：

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. 将完整的 KQL 查询字符串传递给查询提供程序。 查询针对连接的工作区运行，数据以 panda DataFrame 形式返回。 运行：

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

有关详细信息，请参阅：

- [MSTICPy 查询参考](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [运行 MSTICPy 预定义查询](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>测试 VirusTotal 和 GeoLite2

检查 VirusTotal 数据中的 IP 地址：

若要使用威胁情报查看某个 IP 地址是否出现在 VirusTotal 数据中，请使用以下代码运行单元：

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

输出会显示有关结果的详细信息。 例如：

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="VirusTotal 数据中出现的 IP 地址的屏幕截图。":::

请务必向下滚动以查看完整结果。 有关更多信息，请参阅 [MSTICPy 中的威胁情报查找](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html)。

测试地理位置 IP 查找：

若要使用 MaxMind 服务获取 IP 地址的地理位置详细信息，请使用以下代码运行单元：

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

输出将显示 IP 地址的地理位置信息。 例如：

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> 第一次运行此代码时，应该会看到 GeoLite 驱动程序正在下载其数据库。
>

有关详细信息，请参阅 [MSTICPy GeoIP 提供程序](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)。

## <a name="configure-key-vault-settings"></a>配置密钥保管库设置

仅当在 Azure 密钥保管库中存储机密时，本节才有意义。

在 Azure 密钥保管库中存储机密时，需要先在 [Azure 全局密钥保管库管理门户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults)中创建密钥保管库。

必需的设置是你从 Vault 属性中获得的所有值，尽管有些可能具有不同的名称。 例如：

- VaultName 显示在 Azure 密钥保管库的“属性”屏幕的左上角 
- TenantId 显示为“目录 ID” 
- AzureRegion 显示为“位置” 
- Authority 表示 Azure 服务的云。

要从 Vault 检索机密，只需要 VaultName 、 TenantId 和 Authority 值。   如果选择从 MSTICPy 创建保管库，则需要其他值。 有关详细信息，请参阅[将机密指定为密钥保管库机密](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)。

默认情况下选择“使用密钥环”选项，并允许在本地密钥环中缓存密钥保管库凭据。 有关详细信息，请参阅[密钥环文档](https://keyring.readthedocs.io/en/latest/index.html)。

> [!CAUTION]
> 如果不完全信任运行笔记本的“计算”主机，请勿使用“使用密钥环”选项。
>
> 在我们的例子中，“计算”是指运行笔记本内核的 Jupyter 中心服务器，而不一定是运行浏览器的机器。 如果使用的是 Azure ML，则“计算”将是所选的 Azure ML 计算实例。 密钥环在运行笔记本内核的主机上进行缓存。
>

在 MSTICPy 设置编辑器中添加密钥保管库设置：

1.  转到包含以下代码的下一个单元，并运行该单元：

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. 输入密钥保管库的保管库详细信息。 例如：

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="密钥保管库设置部分的屏幕截图":::

1. 选择“保存”，然后“保存设置”。 

### <a name="test-key-vault"></a>测试密钥保管库

若要测试密钥保管库，请检查能否连接并查看机密。 如果尚未添加机密，则看不到任何详细信息。 如果需要，将测试机密从 Azure 密钥保管库门户添加到保管库，并检查该机密是否显示在 Azure Sentinel。

例如：

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> 不要让输出显示在你保存的笔记本中。 如果输出中有真正的秘密，请在保存笔记本之前使用笔记本的“清除输出”命令。
>
> 此外，请删除笔记本的缓存副本。 例如，查看笔记本目录的 .ipynb_checkpoints 子文件夹，并删除此笔记本的所有已发现副本。 保存清除了输出的笔记本应该会覆盖检查点副本。
>

配置密钥保管库后，可以使用数据提供程序和 TI 提供程序部分中的“上传到 KV”按钮将所选设置移动到保管库。 MSTICPy 将根据设置的路径为秘密生成一个默认名称，例如 `TIProviders-VirusTotal-Args-AuthKey`.

如果值成功上传，则设置编辑器中“值”字段的内容将被删除，底层设置将替换为占位符值。 MSTICPy 将使用此占位符值来指示在尝试检索密钥时应自动生成密钥保管库路径。

如果已将所需的机密存储在密钥保管库中，则可以在“值”字段中输入机密名称。 如果机密未存储在你的默认保管库中（在“[密钥保管库](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault)”部分中指定的值），则可以指定路径 **VaultName/SecretName**。

当前不支持从不同租户中的保管库获取设置。 有关详细信息，请参阅[将机密指定为密钥保管库机密](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)。

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>指定 Azure 云和 Azure 身份验证方法

如果使用的是主权或政府 Azure 云，而不是公共或全球 Azure 云，则必须在你的设置中选择适当的云。 对于大多数组织，全球云是默认云。

还可以使用这些 Azure 设置来定义 Azure 身份验证类型的默认首选项。

要指定 Azure 云和 Azure 身份验证方法：

1.  转到包含以下代码的下一个单元，并运行该单元：

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. 选择你的组织使用的云，或保留默认选择的“全球”选项。

1. 选择以下一种或多种方法：

    - 选择 env 可将 Azure 凭据存储在环境变量中。
    - 选择 msi 可使用托管服务标识，这是分配给运行 Jupyter 中心的主机或虚拟机的标识。 Azure ML Compute 实例目前不支持 MSI。
    - 选择 cli 可使用来自经过身份验证的 Azure CLI 会话的凭据。
    - 选择 interactive 可使用采用了[一次性设备代码](#authenticate-to-your-azure-sentinel-workspace-from-your-notebook)的交互式设备授权流程。

    > [!TIP]
    > 在大多数情况下，我们建议选择多种方法，例如 cli 和 interactive。  Azure 身份验证将按照上面列出的顺序尝试每种配置的方法，直到成功为止。
    >

1. 选择“保存”，然后“保存设置”。 

例如：

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="为 Azure 政府云定义的设置的屏幕截图。":::


## <a name="next-steps"></a>后续步骤

本文介绍了在 Azure Sentinel 中将 MSTICPy 与 Jupyter 笔记本一起使用的基础知识。 有关详细信息，请参阅[适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置](notebooks-msticpy-advanced.md)。

还可以尝试存储在 [Azure Sentinel 笔记本 GitHub 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks)中的其他笔记本，例如：

- [网络安全功能导览](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [机器学习示例](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- [实体资源管理器系列](https://github.com/Azure/Azure-Sentinel-Notebooks/)笔记本，可以让你深入了解有关主机、帐户、IP 地址和其他实体的详细信息。

> [!TIP]
> 如果在另一个 Jupyter 环境中使用本教程中描述的笔记本，则可以使用任何支持 Python 3.6 或更高版本的内核。
>
> 若要在 Azure Sentinel 和 Azure 机器学习 (ML) 之外使用 MSTICPy 笔记本，还需要配置 Python 环境。 使用 Anaconda 发行版安装 Python 3.6 或更高版本，其中包含许多必需的包。
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>有关 MSTICPy 和笔记本的更多读物

下表列出了有关学习 MSTICPy、Azure Sentinel 和 Jupyter 笔记本的更多参考资料。

|使用者  |更多参考资料  |
|---------|---------|
|MSTICPy     |      - [MSTICPy 包配置](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 设置编辑器](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [配置笔记本环境](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb)。<br>    - [MPSettingsEditor 笔记本](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb)。 <br><br>注意：Azure-Sentinel-Notebooks GitHub 存储库还包含 msticpyconfig.yaml 模板文件，其中包含注释掉的部分，可能会对你了解设置有所帮助。      |
|**Azure Sentinel 和 Jupyter Notebook**     |      - [Jupyter Notebook 简介](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy 文档](https://msticpy.readthedocs.io/)<br>    - [Azure Sentinel Notebook 文档](notebooks.md)<br>    - [Infosec Jupyter 书籍](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux 主机资源管理器 Notebook 演练](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [为什么使用 Jupyter 进行安全调查](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [使用 Azure Sentinel 和 Notebook 进行安全调查](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas 文档](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh 文档](https://docs.bokeh.org/en/latest/)       |
|     |         |

