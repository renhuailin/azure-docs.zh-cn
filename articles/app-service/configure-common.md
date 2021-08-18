---
title: 在门户中配置应用
description: 了解如何在 Azure 门户中配置应用服务应用的常用设置。 应用设置、应用配置、连接字符串、平台、语言堆栈、容器等。
keywords: azure 应用服务, web 应用, 应用设置, 环境变量
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 1f8f8d81af6fb06bba9c48f5ae7d1fbb7f5d2e59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730641"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>在 Azure 门户中配置应用服务应用

本文介绍如何使用 [Azure 门户]配置 Web 应用、移动后端或 API 应用的常用设置。

## <a name="configure-app-settings"></a>配置应用设置

在应用服务中，应用设置是作为环境变量传递给应用程序代码的变量。 对于 Linux 应用和自定义容器，应用服务使用 `--env` 标志将应用设置传递到容器，以在容器中设置环境变量。 无论是这两种情况中的哪一种，它们都会在应用启动时注入到应用环境。 在添加、删除或编辑应用设置时，应用服务会触发应用重启。

在 [Azure 门户]中搜索并选择“应用服务”  ，然后选择应用。 

![搜索应用服务](./media/configure-common/search-for-app-services.png)

在应用的左侧菜单中，选择“配置” > “应用程序设置”。  

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员而言，在应用服务中设置应用设置类似于在 Web.config  或 appsettings.json  中的 `<appSettings>` 内进行设置，但应用服务中的值会替代 Web.config  或 appsettings.json  中的值。 可以在 Web.config 或 appsettings.json 中保留开发设置（例如本地 MySQL 密码），并在应用服务中安全地保留生产机密（例如 Azure MySQL 数据库密码） 。 相同的代码在本地调试时使用开发设置，部署到 Azure 时使用生产机密。

同样，其他语言堆栈也会在运行时获取应用设置作为环境变量。 有关特定的语言堆栈步骤，请参阅：

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [自定义容器](configure-custom-container.md#configure-environment-variables)

应用程序设置在存储时始终进行加密（静态加密）。

> [!NOTE]
> 也可以使用 [Key Vault 引用](app-service-key-vault-references.md)从 [Key Vault](../key-vault/index.yml) 解析应用设置。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，出于安全考虑，应用设置值会隐藏在门户中。 若要查看某项应用设置的隐藏值，请单击该项设置的“值”字段。  若要查看所有应用设置的值，请单击“显示值”按钮。 

### <a name="add-or-edit"></a>添加或编辑

若要添加新的应用设置，请单击“新建应用程序设置”。  在对话框中，可[将设置绑定到当前槽](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击右侧的“编辑”按钮。 

完成后，单击“更新”。  别忘了返回“配置”页并单击“保存”。  

> [!NOTE]
> 在默认 Linux 应用服务或自定义 Linux 容器中，需要在应用服务中将应用设置名称（如 `ApplicationInsights:InstrumentationKey`）中的任何嵌套 JSON 密钥结构配置为密钥名称的 `ApplicationInsights__InstrumentationKey`。 换而言之，应将任何 `:` 替换为 `__`（双下划线）。
>

### <a name="edit-in-bulk"></a>批量编辑

若要批量添加或编辑应用设置，请单击“高级编辑”按钮。  完成后，单击“更新”。  别忘了返回“配置”页并单击“保存”。  

应用设置采用以下 JSON 格式：

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

### <a name="automate-app-settings-with-the-azure-cli"></a>通过 Azure CLI 自动执行应用设置

可使用 Azure CLI 从命令行创建和管理设置。

- 使用 [az webapp config app settings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 为设置分配一个值：

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    将 `<setting-name>` 替换为设置的名称，并将 `<value>` 替换为要分配给它的值。 此方法会创建设置（如果尚不存在）。
    
- 使用 [az webapp config appsettings list](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list) 显示所有设置及其值：
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- 使用 [az webapp config app settings delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete) 删除一个或多个设置：

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    将 `<names>` 替换为用空格分隔的设置名称列表。

## <a name="configure-connection-strings"></a>配置连接字符串

在 [Azure 门户]中搜索并选择“应用服务”  ，然后选择应用。 在应用的左侧菜单中，选择“配置” > “应用程序设置”。  

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员而言，在应用服务中设置连接字符串类似于在 *Web.config* 中的 `<connectionStrings>` 内进行设置，但应用服务中设置的值会替代 *Web.config* 中的值。可将开发设置（例如，数据库文件）保留在 Web.config  中，并将生产机密（例如，SQL 数据库凭据）安全保留在应用服务中。 相同的代码在本地调试时使用开发设置，部署到 Azure 时使用生产机密。

对于其他语言堆栈，最好是改用[应用设置](#configure-app-settings)，因为连接字符串需要在变量键中使用特殊的格式才能访问值。 

> [!NOTE]
> 有一种情况你需要使用连接字符串而不是非 .NET 语言的应用设置：仅当你在应用服务应用中为数据库配置连接字符串时，某些 Azure 数据库类型才会与应用一起备份。 有关详细信息，请参阅[备份的内容](manage-backup.md#what-gets-backed-up)。 如果不需要这种自动化备份，请使用应用设置。

在运行时，连接字符串可用作环境变量，其前缀为以下连接类型：

* SQLServer：`SQLCONNSTR_`  
* MySQL： `MYSQLCONNSTR_` 
* SQLAzure：`SQLAZURECONNSTR_` 
* 自定义：`CUSTOMCONNSTR_`
* PostgreSQL：`POSTGRESQLCONNSTR_`  

例如，可以使用环境变量 `MYSQLCONNSTR_connectionString1` 的形式访问名为 *connectionstring1* 的 MySql 连接字符串。 有关特定的语言堆栈步骤，请参阅：

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [自定义容器](configure-custom-container.md#configure-environment-variables)

连接字符串在存储时始终进行加密（静态加密）。

> [!NOTE]
> 也可以使用 [Key Vault 引用](app-service-key-vault-references.md)从 [Key Vault](../key-vault/index.yml) 解析连接字符串。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，出于安全考虑，连接字符串的值会隐藏在门户中。 若要查看连接字符串的隐藏值，只需单击该字符串的“值”字段。  若要查看所有连接字符串的值，请单击“显示值”按钮。 

### <a name="add-or-edit"></a>添加或编辑

若要添加新的连接字符串，请单击“新建连接字符串”。  在对话框中，可[将连接字符串绑定到当前槽](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击右侧的“编辑”按钮。 

完成后，单击“更新”。  别忘了返回“配置”页并单击“保存”。  

### <a name="edit-in-bulk"></a>批量编辑

若要批量添加或编辑连接字符串，请单击“高级编辑”按钮。  完成后，单击“更新”。  别忘了返回“配置”页并单击“保存”。  

连接字符串采用以下 JSON 格式：

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>配置常规设置

在 [Azure 门户]中搜索并选择“应用服务”  ，然后选择应用。 在应用的左侧菜单中，选择“配置” > “常规设置”。  

![常规设置](./media/configure-common/open-general.png)

在此处可以配置应用的某些常用设置。 某些设置要求[纵向扩展到更高的定价层](manage-scale-up.md)。

- **堆栈设置**：用于运行应用的软件堆栈，包括语言和 SDK 版本。

    对于 Linux 应用和自定义容器应用，可选择语言运行时版本，并设置启动命令文件或可选的启动命令。

    ![Linux 容器的常规设置](./media/configure-common/open-general-linux.png)

- **平台设置**：用于配置托管平台的设置，包括：
    - **位数**：32 位或 64 位。 （对于在门户中创建的应用服务，默认为 32 位。）
    - **WebSocket 协议**：例如，[ASP.NET SignalR] 或 [socket.io](https://socket.io/)。
    - **Always On**：即使没有流量，也保持应用的加载状态。 如果未打开 Always On（默认），该应用会在 20 分钟后卸载，无需任何传入请求。 卸载的应用可能会因为存在预热时间而导致新请求的延迟过高。 如果打开了 Always On，前端负载均衡器会每隔五分钟向应用程序根目录发送一次 GET 请求。 连续的 ping 会阻止卸载该应用。
    
        对于连续的 WebJobs 或使用 CRON 表达式触发的 WebJobs，Always On 是必需的。
    - **托管管道版本**：IIS [管道模式]。 如果某个旧式应用需要旧版 IIS，请将此选项设置为“经典”。 
    - **HTTP 版本**：设置为“2.0”，以启用对 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 协议的支持。
    > [!NOTE]
    > 大多数新型浏览器仅支持通过 TLS 的 HTTP/2 协议，而非加密流量继续使用 HTTP/1.1。 若要确保客户端浏览器使用 HTTP/2 连接到应用，请保护自定义 DNS 名称。 有关详细信息，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。
    - **ARR 相关性**：在多实例部署中，请确保在会话的整个生存期内，将客户端路由到同一实例。 对于无状态应用程序，请将此选项设置为“关闭”。 
- **调试**：为 [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)、[ASP.NET Core](/visualstudio/debugger/remote-debugging-azure) 或 [Node.js](configure-language-nodejs.md#debug-remotely) 应用启用远程调试。 此选项在 48 小时后会自动关闭。
- **传入的客户端证书**：要求在 [相互身份验证](app-service-web-configure-tls-mutual-auth.md)中使用客户端证书。

## <a name="configure-default-documents"></a>配置默认文档

此设置仅适用于 Windows 应用。

在 [Azure 门户]中搜索并选择“应用服务”  ，然后选择应用。 在应用的左侧菜单中，选择“配置” > “默认文档”。  

![默认文档](./media/configure-common/open-documents.png)

默认文档是在网站的根 URL 中显示的网页。 使用列表中第一个匹配文件。 若要添加新的默认文档，请单击“新建文档”。  别忘了单击“保存”。 

如果应用使用的模块基于 URL 进行路由而不是提供静态内容，则无需使用默认文档。

## <a name="configure-path-mappings"></a>配置路径映射

在 [Azure 门户]中搜索并选择“应用服务”  ，然后选择应用。 在应用的左侧菜单中，选择“配置” > “路径映射”。  

![路径映射](./media/configure-common/open-path.png)

> [!NOTE] 
> “路径映射”选项卡可能会显示特定于 OS 的设置，该设置与此处所示示例不同。

### <a name="windows-apps-uncontainerized"></a>Windows 应用（未容器化）

对于 Windows 应用，可以自定义 IIS 处理程序映射和虚拟应用程序与目录。

使用处理程序映射可以添加自定义脚本处理程序用于处理特定文件扩展名的请求。 若要添加自定义处理程序，请单击“新建处理程序映射”。 按如下所述配置处理程序：

- **扩展名**。 要处理的扩展名，例如 *\*.php* 或 *handler.fcgi*。
- **脚本处理程序**。 脚本处理程序的绝对路径。 与文件扩展名匹配的文件请求由脚本处理程序处理。 使用路径 `D:\home\site\wwwroot` 表示应用的根目录。
- **参数**。 脚本处理程序的可选命令行参数

每个应用具有已映射到 `D:\home\site\wwwroot`（代码的默认部署位置）的默认根路径 (`/`)。 如果应用根位于其他文件夹中，或者存储库包含多个应用程序，则你可以在此处编辑或添加虚拟应用程序和目录。 

从“路径映射”选项卡中，单击“新建虚拟应用程序或目录”。 

- 若要将虚拟目录映射到物理路径，请将“目录”复选框保持选中状态。 指定虚拟目录，以及网站根目录相应的相对（物理）路径 (`D:\home`)。
- 若要将虚拟目录标记为 Web 应用程序，请清除“目录”复选框。
  
  ![“目录”复选框](./media/configure-common/directory-check-box.png)

### <a name="containerized-apps"></a>容器化应用

你可以[为容器化应用添加自定义存储](configure-connect-to-azure-storage.md)。 容器化应用包括所有 Linux 应用，还包括在应用服务上运行的 Windows 和 Linux 自定义容器。 单击“新 Azure 存储装载”，然后按如下所示配置自定义存储：

- **名称**：显示名称。
- **配置选项**：“基本”或“高级”。
- **存储帐户**：具有所需容器的存储帐户。
- **存储类型**：“Azure Blob”或“Azure 文件存储”。
  > [!NOTE]
  > Windows 容器应用仅支持 Azure 文件存储。
- **存储容器**：对于基本配置，为所需的容器。
- **共享名**：对于高级配置，为文件共享名。
- **访问密钥**：对于高级配置，为访问密钥。
- **装载路径**：容器中用于装载自定义存储的绝对路径。

有关详细信息，请参阅[从应用服务中的容器以网络共享形式访问 Azure 存储](configure-connect-to-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>配置语言堆栈设置

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>配置自定义容器

请参阅[为 Azure 应用服务配置自定义 Linux 容器](configure-custom-container.md)

## <a name="next-steps"></a>后续步骤

- [环境变量和应用设置参考](reference-app-settings.md)
- [在 Azure 应用服务中配置自定义域名]
- [设置 Azure 应用服务中的过渡环境]
- [在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
- [启用诊断日志](troubleshoot-diagnostic-logs.md)
- [在 Azure 应用服务中缩放应用]
- [在 Azure 应用服务中监视基础知识]
- [使用 applicationHost.xdt 更改 applicationHost.config 设置](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 门户]: https://portal.azure.com/
[在 Azure 应用服务中配置自定义域名]: ./app-service-web-tutorial-custom-domain.md
[设置 Azure 应用服务中的过渡环境]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[在 Azure 应用服务中监视基础知识]: ./web-sites-monitor.md
[管道模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure 应用服务中缩放应用]: ./manage-scale-up.md
