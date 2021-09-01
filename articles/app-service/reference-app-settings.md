---
title: 环境变量和应用设置参考
description: 描述常用的环境变量，以及可以通过应用设置修改的环境变量。
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: 39cb9b210cb7cff3e8b1b6a58a3ab32e22628833
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289990"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Azure 应用服务中的环境变量和应用设置

在 [Azure 应用服务](overview.md)中，某些设置作为环境变量可用于部署或运行时环境。 将这些设置手动设置为[应用设置](configure-common.md#configure-app-settings)时，可以自定义其中一些设置。 此参考显示了可以使用或自定义的变量。

## <a name="app-environment"></a>应用环境

以下环境变量通常与应用环境相关。

| 设置名| 说明 | 示例 |
|-|-|-|
| `WEBSITE_SITE_NAME` | 只读。 应用名称。 ||
| `WEBSITE_RESOURCE_GROUP` | 只读。 包含应用资源的 Azure 资源组名称。 ||
| `WEBSITE_OWNER_NAME` | 只读。 包含拥有应用、资源组和 webspace 的 Azure 订阅 ID。 ||
| `REGION_NAME` | 只读。 应用的区域名称。 ||
| `WEBSITE_PLATFORM_VERSION` | 只读。 应用服务平台版本。 ||
| `HOME` | 只读。 主目录的路径（例如，Windows 的 `D:\home`）。 ||
| `SERVER_PORT` | 只读。 应用应侦听的端口。 | |
| `WEBSITE_WARMUP_PATH`  | 执行 ping 操作以预热应用的相对路径，以斜杠开头。 默认为 `/`，可对根路径执行 ping 操作。 即使[应用服务身份验证](overview-authentication-authorization.md)设置为拒绝未经身份验证的客户端，未经身份验证的客户端（例如 Azure 流量管理器）也可以对特点路径执行 ping 操作。 （注意：此应用设置不会更改 AlwaysOn 使用的路径。） ||
| `WEBSITE_COMPUTE_MODE` | 只读。 指定应用在专用 (`Dedicated`) 或共享 (`Shared`) VM/s 上运行。 ||
| `WEBSITE_SKU` | 只读。 应用的 SKU。 可能值为 `Free`、`Shared`、`Basic` 和 `Standard`。 ||
| `SITE_BITNESS` | 只读。 显示应用是 32 位 (`x86`) 还是 64 位 (`AMD64`)。 ||
| `WEBSITE_HOSTNAME` | 只读。 应用的主要主机名。 此处未考虑自定义主机名。 ||
| `WEBSITE_VOLUME_TYPE` | 只读。 显示当前使用的存储卷类型。 ||
| `WEBSITE_NPM_DEFAULT_VERSION` | 应用使用的默认 npm 版本。 ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | 只读。 Websocket 的并发请求的上限。 对于标准层及以上层，该值为 `-1`，但根据 VM 大小，每个 VM 仍有一个上限（请参阅[跨 VM 数值](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)）。 ||
| `WEBSITE_PRIVATE_EXTENSIONS` | 设置为 `0` 可禁用专用站点扩展。 ||
| `WEBSITE_TIME_ZONE` | 默认情况下，应用的时区始终为 UTC。 可将其更改为 [TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)) 中列出的任何有效值。 如果无法识别指定的值，则使用 UTC。 | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | 为了防止存储卷故障转移或重新配置，应用将切换到备用存储卷。 在存储基础结构更改时，`1` 的默认设置可防止回收工作进程。 如果正在运行 Windows Communication Foundation (WCF)，可通过将其设置为 `0` 来实现禁用。 该设置特定于槽，因此应在所有槽中对其进行设置。 ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | 默认情况下，当 VM 实例使用 90% 以上的已分配内存超过 30 秒时，或者过去两分钟内请求总数的 80% 用时超过 200 秒时，便会主动“自动修复”该 VM 实例。 如果 VN 实例触发了这些规则之一，则恢复过程是实例的重叠重启。 设置为 `false` 可禁用此恢复行为。 默认为 `true`。 有关详细信息，请参阅[主动自动修复](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)。 ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | 只要应用的 VM 实例上的 w3wp.exe 进程在 24 小时内因未处理的异常而发生故障三次以上，调试器进程将附加到该实例上的主工作进程，并且当辅助进程再次发生故障时收集内存转储。 接下来会对此内存转储进行分析，并将导致故障的线程的调用堆栈记录在应用服务日志中。 设置为 `false` 可禁用此自动监视行为。 默认为 `true`。 有关详细信息，请参阅[主动故障监视](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html)。 ||
| `WEBSITE_DAAS_STORAGE_SASURI` | 在故障监视（主动或手动）期间，默认情况下会删除内存转储。 若要将内存转储保存到存储 Blob 容器，请指定 SAS URI。  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | 设置为可手动`true`启用[故障监视](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)。 还必须设置 `WEBSITE_DAAS_STORAGE_SASURI` 和 `WEBSITE_CRASHMONITORING_SETTINGS`。 默认为 `false`。 如果启用远程调试，则此设置不起作用。 此外，如果将此设置设置为 `true`，则会禁用[主动故障监视](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)。 ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | 采用以下格式的 JSON：`{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}`。 如果指定了 `WEBSITE_CRASHMONITORING_ENABLED`，则需要配置[故障监视](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)。 若要仅记录调用堆栈而不将故障转储保存在存储帐户中，请在 JSON 中添加 `,"UseStorageAccount":"false"`。 ||
| `REMOTEDEBUGGINGVERSION` | 远程调试版本。 ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 默认情况下，应用服务会在应用创建中创建共享存储。 若要改为使用自定义存储帐户，请设置为存储帐户的连接字符串。 有关函数的详细信息，请参阅 [Functions 的应用设置参考](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring)。 | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | 通过 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 指定自定义存储帐户时，应用服务会为应用创建该存储帐户中的文件共享。 若要使用自定义名称，请将此变量设置所需名称。 如果具有指定名称的文件共享不存在，应用服务会为你创建一个。 | `myapp123` |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | 默认情况下，自动生成的密钥用作加密密钥。 若要替代，请设置为所需的密匙。 如果要在多种应用之间共享令牌或会话，建议使用此密匙。 ||
| `WEBSITE_AUTH_SIGNING_KEY` | 默认情况下，自动生成的密钥用作签名密钥。 若要替代，请设置为所需的密匙。 如果要在多种应用之间共享令牌或会话，建议使用此密匙。 ||
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | 只读。 显示 Always On 是已启用 (`1`) 还是未启用 (`0`)。 ||
| `WEBSITE_SCM_SEPARATE_STATUS` | 只读。 显示 Kudu 应用是在单独的进程中运行 (`1`) 还是不在单独的进程中运行 (`0`)。 ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>变量前缀

下表显示了应用服务用于各种目的的环境变量前缀。

| 设置名 | 说明 |
|-|-|
| `APPSETTING_` | 表示客户在应用配置中将变量设置为应用设置。 它作为应用设置注入到 .NET 应用中。 |
| `MAINSITE_` | 表示变量特定于应用本身。 |
| `SCMSITE_` | 表示变量特定于 Kudu 应用。 |
| `SQLCONNSTR_` | 表示应用配置中的 SQL Server 连接字符串。 它作为连接字符串注入到 .NET 应用中。 |
| `SQLAZURECONNSTR_` | 表示应用配置中的 Azure SQL 数据库连接字符串。 它作为连接字符串注入到 .NET 应用中。 |
| `POSTGRESQLCONNSTR_` | 表示应用配置中的 PostgreSQL 连接字符串。 它作为连接字符串注入到 .NET 应用中。 |
| `CUSTOMCONNSTR_` | 表示应用配置中的自定义连接字符串。 它作为连接字符串注入到 .NET 应用中。 |
| `MYSQLCONNSTR_` | 表示应用配置中的 Azure SQL 数据库连接字符串。 它作为连接字符串注入到 .NET 应用中。 |
| `AZUREFILESSTORAGE_` | 容器应用的自定义 Azure 文件存储的连接字符串。 |
| `AZUREBLOBSTORAGE_` | 容器应用的自定义 Azure Blobs 文件存储的连接字符串。 |

## <a name="deployment"></a>部署

以下环境变量与应用部署相关。 有关与应用服务生成自动化相关的变量，请参阅[生成自动化](#build-automation)。

| 设置名| 说明 |
|-|-|
| `DEPLOYMENT_BRANCH`| 对于[本地 Git](deploy-local-git.md) 或[云 Git](deploy-continuous-deployment.md) 部署（例如 GitHub），请设置为 Azure 中要部署到的分支。 默认为 `master`。 |
| `WEBSITE_RUN_FROM_PACKAGE`| 设置为 `1`，以从本地 ZIP 包运行应用，或设置为外部 URL 的 URL，以从远程 ZIP 包运行应用。 有关详细信息，请参阅[直接从 ZIP 包运行 Azure 应用服务中的应用](deploy-run-package.md)。 |
| `WEBSITE_USE_ZIP` | 已弃用。 使用 `WEBSITE_RUN_FROM_PACKAGE`。 |
| `WEBSITE_RUN_FROM_ZIP` | 已弃用。 使用 `WEBSITE_RUN_FROM_PACKAGE`。 | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | 设置为 `false`，使 WebDeploy 停止使用 Kudu 部署引擎。 默认为 `true`。 若要使用 Visual Studio (WebDeploy/MSDeploy) 部署到 Linux 应用，请将其设置为 `false`。 |
| `MSDEPLOY_RENAME_LOCKED_FILES` | 如果 WebDeploy 部署期间无法复制 DLL，则设置为 `1` 以尝试重命名 DLL。 如果将 `WEBSITE_WEBDEPLOY_USE_SCM` 设置为 `false`，则此设置不适用。 |
| `WEBSITE_DISABLE_SCM_SEPARATION` | 默认情况下，主应用和 Kudu 应用在不同的沙盒中运行。 停止应用时，Kudu 应用仍在运行，可以继续使用 Git 部署和 MSDeploy。 每个应用都有自己的应用 ID。 关闭此分隔（设置为 `false`）是不再完全受支持的旧模式。 |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | 设置为 `1` 可确保在返回之前，REST API 对更新 `site` 和 `siteconfig` 的调用完全应用到所有实例。 如果使用 ARM 模板进行部署，则默认为 `1`，以免后续 ARM 调用出现争用条件。 |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | 在 ARM 模板部署中，在 ARM 模板中设置为 `1`，以在应用创建过程中预先启动 Kudu 应用。 |
| `WEBSITE_START_SCM_WITH_PRELOAD` | 对于 Linux 应用，设置为 `true`，以在对其 URL 执行 ping 操作来启用 Always On 时强制预加载 Kudu 应用。 默认为 `false`。 对于 Windows 应用，始终预加载 Kudu 应用。 |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>生成自动化

# <a name="kudu-windows"></a>[Kudu (Windows)](#tab/kudu)

Kudu 生成配置适用于本机 Windows 应用，用于控制基于 Git（或基于 ZIP）的部署行为。

| 设置名| 说明 | 示例 |
|-|-|-|
| `SCM_BUILD_ARGS` | 在 msbuild 命令行的末尾添加内容，以替代默认命令行的任何原有部分。 | 若要执行清洁生成，请使用：`-t:Clean;Compile`|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu 使用[此处](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2)所述的 `azure site deploymentscript` 命令生成部署脚本。 它会自动检测语言框架类型，并确定要向其传递命令的参数。 此设置将替代自动生成的参数。 | 若要将存储库视为纯内容文件，请使用：`--basic -p <folder-to-deploy>` |
| `SCM_TRACE_LEVEL` | 生成跟踪级别。 默认为 `1`。 设置为更高的值（最高为 4）以了解更多跟踪。 | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | 生成过程启动的每条命令在未生成任何输出的情况下要等待的超时时间（以秒为单位）。 此后，该命令被视为空闲并终止。 默认为 `60`（一分钟）。 在 Azure 中，还有一个在 230 秒后断开客户端连接的常规空闲请求超时。 但是，之后该命令将继续在服务器端运行。 | |
| `SCM_LOGSTREAM_TIMEOUT` | 停止日志流式处理前处于非活动状态的超时时间（以秒为单位）。 默认为 `1800`（30 分钟）。| |
| `SCM_SITEEXTENSIONS_FEED_URL` | 站点扩展库的 URL。 默认为 `https://www.nuget.org/api/v2/`。 旧源的 URL 为 `http://www.siteextensions.net/api/v2/`。 | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | 设置为 `0` 以使用 git.exe（而不是 libgit2sharp）进行 git 操作。 | |
| `WEBSITE_LOAD_USER_PROFILE` | 为了防止生成自动化 ASP.NET 期间（例如 Git 部署期间）出现错误 `The specified user does not have a valid profile.`，请将此变量设置为 `1` 以在生成环境中加载完整的用户配置文件。 只有在 `WEBSITE_COMPUTE_MODE` 为 `Dedicated` 时，此设置才适用。 | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | SCM (Kudu) 站点的超时时间（以分为单位）。 默认为 `20`。 | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | 对于 [ZIP 部署](deploy-zip.md)，部署引擎假定 ZIP 文件已准备好按原样运行，并且不运行任何生成自动化。 若要启用与 [Git 部署](deploy-local-git.md)相同的生成自动化，请设置为 `true`。 |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx (Linux)](#tab/oryx)

Oryx 生成配置适用于 Linux 应用，用于控制基于 Git （或基于 ZIP）的部署行为。 请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

-----

## <a name="language-specific-settings"></a>特定于资源的设置

本部分演示每种受支持语言框架的可配置运行时设置。 其他设置在部署时进行[生成自动化](#build-automation)期间可用。

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| 设置名 | 说明 |
|-|-|
| `PORT` | 只读。 对于 Linux 应用，为 .NET 运行时在容器中侦听的端口。 |
| `WEBSITE_ROLE_INSTANCE_ID` | 只读。 当前实例的 ID。 |
| `HOME` | 只读。 指向共享存储的目录 (`/home`)。 |
| `DUMP_DIR` | 只读。 故障转储的目录 (`/home/logs/dumps`)。 |
| `APP_SVC_RUN_FROM_COPY` | 仅限于 Linux 应用。 默认情况下，应用在 `/home/site/wwwroot` 中运行，这是所有横向扩展实例的共享目录。 将此变量设置为 `true`，以将应用复制到容器中的本地目录并在该目录中运行它。 使用此选项时，请确保不要对 `/home/site/wwwroot` 的任何引用进行硬编码。 请改为使用相对于 `/home/site/wwwroot` 的路径。 |
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051" --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| 设置名 | 说明 | 示例 |
|-|-|-|
| `JAVA_HOME` | Java 安装目录的路径 ||
| `JAVA_OPTS` | 对于 Java SE 应用，环境变量将传递到 `java` 命令中。 可以包含系统变量。 对于 Tomcat，请使用 `CATALINA_OPTS`。 | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | 自定义脚本可使用环境变量，因此在将环境变量复制到本地后，这些脚本才有 app.jar 的位置 | |
| `SKIP_JAVA_KEYSTORE_LOAD` | 将其值设置为 1，以禁止应用服务自动将证书加载到密钥存储 ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | 要使用的 .jar 文件。 如果字符串不以 .jar 结尾，请追加 .jar。 默认为 app.jar ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | 要使用的 .war 文件。 如果字符串不以 .war 结尾，请追加 .war。 默认为 app.war ||
| `JAVA_ARGS` | 不同 java webserver 所需的 java 选项。 默认为 `-noverify -Djava.net.preferIPv4Stack=true` ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | 常用 Java Web 框架用于服务器端口的环境变量。 其中包括：Spring、Micronaut、Grails、MicroProfile Thorntail、Helidon、Ratpack 和 Quarkus ||
| `JAVA_TMP_DIR` | 作为 `-Dsite.tempdir` 添加到 Java 参数。 默认为 `TEMP`。 ||
| `WEBSITE_SKIP_LOCAL_COPY` | 默认情况下，已部署的 app.jar 将从 `/home/site/wwwroot` 复制到本地位置。 若要禁用此行为并直接从 `/home/site/wwwroot` 中加载 app.jar，请将此变量设置为 `1` 或 `true`。 如果启用了本地缓存，则此设置不起作用。 | |
| `TOMCAT_USE_STARTUP_BAT` | 仅限于原生 Windows 应用。 默认情况下，Tomcat 服务器使用其 `startup.bat` 进行启动。 若要改用其 `catalina.bat` 来启动，请设置为 `0` 或 `False`。 | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | 对于 Tomcat 应用，为要传递到 `java` 命令中的环境变量。 可以包含系统变量。 | |
| `CATALINA_BASE` | 若要使用自定义 Tomcat 安装，请设置为安装的位置。 | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Java 最大堆（以 MB 为单位）。 仅当使用实验 Tomcat 版本时，此设置才有效。 | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | 通过将此变量设置为 `true` 或 `1`，手动禁用 `WEBSITE_JAVA_MAX_HEAP_MB`。 | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | 默认情况下，启用内置[身份验证](overview-authentication-authorization.md)时，将会水化以下 Tomcat [HttpServletRequest 接口](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)：`isSecure`、`getRemoteAddr`、`getRemoteHost`、`getScheme`、`getServerPort`。 若要禁用此设置，请设置为 `1`。  | |
| `WEBSITE_SKIP_FILTERS` | 若要禁用应用服务添加的所有 servlet 筛选器，请设置为 `1`。 ||
| `IGNORE_CATALINA_BASE` | 默认情况下，应用服务将检查是否已定义 Tomcat 变量 `CATALINA_BASE`。 如果还未定义，则会查找 `%HOME%\tomcat\conf\server.xml` 是否存在。 如果该文件存在，则将 `CATALINA_BASE` 设置为 `%HOME%\tomcat`。 若要禁用此行为并删除 `CATALINA_BASE`，请将此变量设置为 `1` 或 `true`。 ||
| `PORT` | 只读。 对于 Linux 应用，为 Java 运行时在容器中侦听的端口。 | |
| `WILDFLY_VERSION` | 只读。 对于 JBoss (Linux) 应用的 WildFly 版本。 | |
| `TOMCAT_VERSION` | 只读。 对于 Linux Tomcat 应用，为 Tomcat 版本。 ||
| `JBOSS_HOME` | 只读。 对于 JBoss (Linux) 应用，为 WildFly 安装路径。 | |
| `AZURE_JETTY9_CMDLINE` | 只读。 对于本机 Windows 应用，为启动 Jetty 9 的命令行参数。 | |
| `AZURE_JETTY9_HOME` | 只读。 对于本机 Windows 应用，为 Jetty 9 安装路径。| |
| `AZURE_JETTY93_CMDLINE` | 只读。 对于本机 Windows 应用，为用于启动 Jetty 9.3 的命令行参数。 | |
| `AZURE_JETTY93_HOME` | 只读。 对于本机 Windows 应用，为 Jetty 9.3 安装路径。 | |
| `AZURE_TOMCAT7_CMDLINE` | 只读。 对于本机 Windows 应用，为用于启动 Tomcat 7 的命令行参数。 | |
| `AZURE_TOMCAT7_HOME` | 只读。 对于本机 Windows 应用，为 Tomcat 7 安装路径。 | |
| `AZURE_TOMCAT8_CMDLINE` | 只读。 对于本机 Windows 应用，为用于启动 Tomcat 8 的命令行参数。 | |
| `AZURE_TOMCAT8_HOME` | 只读。 对于本机 Windows 应用，为 Tomcat 8 安装路径。 | |
| `AZURE_TOMCAT85_CMDLINE` | 只读。 对于本机 Windows 应用，为用于启动 Tomcat 8.5 的命令行参数。 | |
| `AZURE_TOMCAT85_HOME` | 只读。 对于本机 Windows 应用，为 Tomcat 8.5 安装路径。 | |
| `AZURE_TOMCAT90_CMDLINE` | 只读。 对于本机 Windows 应用，为用于启动 Tomcat 9 的命令行参数。 | |
| `AZURE_TOMCAT90_HOME` | 只读。 对于本机 Windows 应用，为 Tomcat 9 安装路径。 | |
| `AZURE_SITE_HOME` | 作为 `-Dsite.home` 添加到 Java 参数的值。 默认为 `HOME` 的值。 | |
| `HTTP_PLATFORM_PORT` | 作为 `-Dport.http` 添加到 Java 参数。 不同 Java Web 框架使用的以下环境变量也设置为此值：`SERVER_PORT`、`MICRONAUT_SERVER_PORT`、`THORNTAIL_HTTP_PORT`、`RATPACK_PORT`、`QUARKUS_HTTP_PORT` 和 `PAYARAMICRO_PORT`。 ||
| `AZURE_LOGGING_DIR` | 仅限于原生 Windows 应用。 作为 `-Dsite.logdir` 添加到 Java 参数。 默认为 `%HOME%\LogFiles\`。 ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.js](#tab/node)

| 设置名 | 说明 |
|-|-|
| `PORT` | 只读。 对于 Linux 应用，为 .Node.js 应用在容器中侦听的端口。 |
| `WEBSITE_ROLE_INSTANCE_ID` | 只读。 当前实例的 ID。 |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | 对于原生 Windows 应用，为应用使用的默认节点版本。 任何[受支持的 Node.js 版本](configure-language-nodejs.md#show-nodejs-version)皆可用于此处。 |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| 设置名 | 说明 |
|-|-|
| `APPSVC_VIRTUAL_ENV` | 只读。 |
| `PORT` | 只读。 对于 Linux 应用，为 Python 应用在容器中侦听的端口。 |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| 设置名 | 说明 | 示例|
|-|-|-|
| `PHP_Extensions` | PHP 扩展的逗号分隔列表。 | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | 对于 Windows 原生应用，设置为 XDebug 扩展路径（例如 `D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll`）。 对于 Linux 应用，设置为 `xdebug` 以使用 XDebug 版本的 PHP 容器。 ||
| `PHP_VERSION` | 只读。 选中的 PHP 版本。 ||
| `PORT` | 只读。 Apache 服务器在容器中侦听的端口。 ||
| `WEBSITE_ROLE_INSTANCE_ID` | 只读。 当前实例的 ID。 ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | 设置为 `TRUE` 以将 `xdebug.profiler_enable_trigger=1` 和添加到 `xdebug.profiler_enable=0` 默认值 `php.ini`。 ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | 设置为 `TRUE` 以记录向服务器发出的请求（将 `CustomLog \dev\stderr combined` 添加到 `/etc/apache2/apache2.conf`）。 ||
| `APACHE_SERVER_LIMIT` | Apache 特定变量。 默认为 `1000`。 ||
| `APACHE_MAX_REQ_WORKERS` | Apache 特定变量。 默认为 `256`。 ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| 设置名 | 说明 | 示例 |
|-|-|-|
| `PORT` | 只读。 Rails 应用在容器中侦听的端口。 ||
| `WEBSITE_ROLE_INSTANCE_ID` | 只读。 当前实例的 ID。 ||
| `RAILS_IGNORE_SPLASH` | 默认情况下，找不到 Gemfile 时，将显示默认初始页。 将此变量设置为任何值可禁用初始页。 ||
| `BUNDLE_WITHOUT` | 若要将 `--without` 选项添加到 `bundle install`，请将变量设置为要排除的组（用空格分隔）。 默认情况下，将安装所有 Gem。 | `test development` |
| `BUNDLE_INSTALL_LOCATION` | 用于安装 gem 的目录。 默认为 `/tmp/bundle`。 ||
| `RUBY_SITE_CONFIG_DIR` | 站点配置目录。 默认为 `/home/site/config`。 容器在此目录中检查是否有压缩的 gem。 ||
| `SECRET_KEY_BASE` | 默认情况下，会生成随机密钥基。 若要使用自定义机密密钥基，请将此变量设置为所需的密钥基。 ||
| `RAILS_ENV` | Rails 环境。 默认为 `production`。 ||
| `GEM_PRISTINE` | 将此变量设置为要运行 `gem pristine --all` 的任何值。 ||

-----

## <a name="domain-and-dns"></a>域和 DNS

| 设置名| 说明 | 示例 |
|-|-|-|
| `WEBSITE_DNS_SERVER` | 用于传出连接（例如，到后端服务）的主 DNS 服务器的 IP 地址。 应用服务的默认 DNS 服务器是 Azure DNS，其 IP 地址为 `168.63.129.16`。 默认情况下，如果应用使用 [VNet 集成](web-sites-integrate-with-vnet.md)，或者处于[应用服务环境](environment/intro.md)中，则会继承 VNet 中的 DNS 服务器配置。 | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | 用于传出连接的回退 DNS 服务器的 IP 地址。 请参阅 `WEBSITE_DNS_SERVER`。 | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tslssl"></a>TSL/SSL

有关详细信息，请参阅[在 Azure 应用服务的代码中使用 TLS/SSL 证书](configure-ssl-certificate-in-code.md)。

| 设置名| 说明 |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | 要在代码中加载的证书的指纹值（以逗号分隔），或用于在代码中加载所有证书的 `*`。 只能加载[添加到应用中的证书](configure-ssl-certificate.md)。 |
| `WEBSITE_PRIVATE_CERTS_PATH` | 只读。 Windows 容器中已加载专用证书的路径。 |
| `WEBSITE_PUBLIC_CERTS_PATH` | 只读。 Windows 容器中已加载公共证书的路径。 |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | 只读。 Windows 容器中已加载中间证书的路径。 |
| `WEBSITE_ROOT_CERTS_PATH` | 只读。 Windows 容器中已加载根证书的路径。 |

## <a name="deployment-slots"></a>部署槽 

有关部署槽位的详细信息，请参阅[设置 Azure 应用服务中的过渡环境](deploy-staging-slots.md)。

| 设置名| 说明 | 示例 |
|-|-|-|
|`WEBSITE_SLOT_NAME`| 只读。 当前部署槽位的名称。 生产槽的名称为 `Production`。 ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| 默认情况下，站点扩展的版本都特定于每个槽。 这可以防止由于交换后更改扩展版本导致的意外应用程序行为。 如果还想要交换扩展版本，请在所有槽上设置 `1` 。 ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| 将特定设置指定为[默认为粘滞或不可交换](deploy-staging-slots.md#which-settings-are-swapped)。 默认为 `true`。 将所有部署槽的此设置设置为 `false` 或 `0`，以便对其进行交换。 特定设置类型没有精细的控制。 ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| 执行 ping 操作以在交换中预热目标槽的路径，以斜杠开头。 默认为 `/`，可对根路径执行 ping 操作。 | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| 交换期间，预热操作的有效 HTTP 响应代码。 如果返回的状态代码不在列表中，则预热和交换操作会停止。 默认情况下，所有响应代码都是有效的。 | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | 槽交换期间，在特定 VM 实例上强制重启站点的最大超时时间次数。 默认为 `3`。 ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | 槽交换期间，在放弃之前，单个 URL 发出的最大超时请求数。 默认为 `5`。 ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | 设置为 `true` 或 `1` 以跳过 `applicationHost.config` 中的所有绑定。 默认为 `false`。 如果应用由于使用槽的交换主机名更新 `applicationHost.config` 而触发重启，请将此变量设置为 `true`，以免重启此类型。 如果正在运行 Windows Communication Foundation (WCF) 应用，请不要设置此变量。 ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>自定义容器

有关自定义容器的详细信息，请参阅[在 Azure 中运行自定义容器](quickstart-custom-container.md)。

| 设置名| 说明 | 示例 |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | 设置为 `true` 以允许跨缩放实例共享 `/home` 目录。 对于自定义容器，默认值为 `false`。 ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | 容器完成重新启动所需的等待时间（以秒为单位）。 默认值为 `230`。 可将其增加到的最大值为 `1800`。 ||
| `DOCKER_REGISTRY_SERVER_URL` | 在应用服务中运行自定义容器时，为注册表服务器的 URL。 安全起见，此变量不会传递到容器。 | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | 在 `DOCKER_REGISTRY_SERVER_URL` 上通过注册表服务器进行身份验证的用户名。 安全起见，此变量不会传递到容器。 ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | 在 `DOCKER_REGISTRY_SERVER_URL` 上通过注册表服务器进行身份验证的密码。 安全起见，此变量不会传递到容器。 ||
| `WEBSITES_WEB_CONTAINER_NAME` | 在 Docker Compose 应用中，仅有一个容器可以访问 Internet。 设置为配置文件中定义的容器的名称，以替代默认容器选择。 默认情况下，可访问 Internet 的容器是第一个定义端口 80 或 8080 的容器，若未找到此容器，则为配置文件中定义的第一个容器。 |  |
| `WEBSITES_PORT` | 对于自定义容器，则为将请求路由到容器上的自定义端口号。 默认情况下，应用服务尝试对端口 80 和 8080 进行自动端口检测。 ||
| `WEBSITE_CPU_CORES_LIMIT` | 默认情况下，Windows 容器将运行，其中包含所选定价层的所有可用内核。 若要减少内核的数量，请将其设置为所需的限制数量。 有关详细信息，请参阅[自定义计算内核数量](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores)。||
| `WEBSITE_MEMORY_LIMIT_MB` | 默认情况下，部署在 Azure 应用服务中的所有 Windows 容器均限制为 1 GB RAM。 设置为所需的内存限制（单位为 MB）。 在同一计划的各个应用中，此设置的累计总和不得超过所选定价层允许的数额。 有关详细信息，请参阅[自定义容器内存](configure-custom-container.md?pivots=container-windows#customize-container-memory)。 ||
| `MACHINEKEY_Decryption` | 对于 Windows 容器，此变量会注入到容器中以启用 ASP.NET 加密例程（请参阅 [machineKey Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))）。 若要替代默认 `decryption` 值，请将其设置为应用设置。 ||
| `MACHINEKEY_DecryptionKey` | 对于 Windows 容器，此变量会注入到容器中以启用 ASP.NET 加密例程（请参阅 [machineKey Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))）。 若要替代自动生成的 `decryptionKey` 值，请将其设置为应用设置。 ||
| `MACHINEKEY_Validation` | 对于 Windows 容器，此变量会注入到容器中以启用 ASP.NET 加密例程（请参阅 [machineKey Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))）。 若要替代默认 `validation` 值，请将其设置为应用设置。 ||
| `MACHINEKEY_ValidationKey` | 对于 Windows 容器，此变量会注入到容器中以启用 ASP.NET 加密例程（请参阅 [machineKey Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))）。 若要替代自动生成的 `validationKey` 值，请将其设置为应用设置。 ||
| `CONTAINER_WINRM_ENABLED` | 对于 Windows 容器应用，请将其设置为 `1` 以启用 Windows 远程管理 (WIN-RM)。 ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>扩展

| 设置名| 说明 |
|-|-|
| `WEBSITE_INSTANCE_ID` | 只读。 应用扩展到多个实例时，当前 VM 实例的唯一 ID。 |
| `WEBSITE_IIS_SITE_NAME` | 已弃用。 使用 `WEBSITE_INSTANCE_ID`。 |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | 重叠回收使其在应用的当前 VM 实例关闭之前启动新的 VM 实例。 在某些情况下，它可能会导致文件锁定问题。 可以设置为 `1` 来尝试将其关闭。 |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | 默认情况下，如果应用使用 Azure 文件或 Docker 容器，则可以扩展交叉标记。 设置为 `1` 或将 `true` 在应用的区域内禁用交叉标记缩放。 默认为 `0`。 将 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 设置为 `true` 或 `1` 的自定义 Docker 容器无法缩放交叉标记，因为其内容并未完全封装在 Docker 容器中。 |

## <a name="logging"></a>日志记录

| 设置名| 说明 | 示例 |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | 只读。 显示 Windows 原生应用的 Web 服务器日志记录是已启用 (`1`) 还是未启用 (`0`)。 ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | Windows 原生应用的 Web 服务器日志的保留期天数（如果已启用 Web 服务器日志）。 | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | Blob 存储容器的 SAS URL，用于存储 Windows 原生应用的 Web 服务器日志（如果已启用 Web 服务器日志）。 如果未设置，Web 服务器日志会存储在应用的文件系统中（默认共享存储）。 | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | Windows 原生应用的保留期天数（如果已启用应用程序日志）。 | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | Blob 存储容器的 SAS URL，用于存储 Windows 原生应用的应用程序日志（如果已启用应用程序日志日志）。 | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types) 日志类型要交付到 Log Analytics 的最低日志级别。 | |
| `DIAGNOSTICS_LASTRESORTFILE` | 要创建的文件名或日志目录的相对路径，用于记录内部错误来对侦听器进行故障排除。 默认为 `logging-errors.txt`。 ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | 相对于 `D:\home` 或 `/home` 的日志设置文件的路径。 默认为 `site\diagnostics\settings.json`。 | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | 相对于应用根目录（`D:\home\site\wwwroot` 或 `/home/site/wwwroot`）的日志文件夹。 | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | 日志文件的最大大小（以字节为单位）。 默认为 `131072` (128 KB)。 ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | 日志文件夹的最大大小（以字节为单位）。 默认为 `1048576` (1 MB)。 ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | 要保留的日志文件的最大数量。 默认为 `20`。 | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | 使应用程序日志记录保持启用状态的超时时间（以毫秒为单位）。 默认值为 `43200000`（12 小时）。 ||
| `WEBSITE_LOG_BUFFERING` | 日志缓冲默认为已启用状态。 设置为 `0` 可将其禁用。 ||
| `WEBSITE_ENABLE_PERF_MODE` | 对于原生 Windows 应用，设置为 `TRUE` 可关闭 10 秒钟内返回的成功请求的 IIS 日志条目。 这是通过删除扩展日志记录执行性能基准测试的一种快速方法。 ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>性能计数器

以下是“虚假”环境变量，如果对其进行枚举，则这些变量不存在，但如果单独查找它们，则会返回它们的值。 该值是动态的，每次查找时都可能发生变化。

| 设置名| 说明 |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | 包含 ASP.NET 性能计数器的 JSON 对象。 |
| `WEBSITE_COUNTERS_APP` | 包含沙盒计数器的 JSON 对象。 |
| `WEBSITE_COUNTERS_CLR` | 包含 CLR 计数器的 JSON 对象。 |
| `WEBSITE_COUNTERS_ALL` | 包含其他三个变量的组合的 JSON 对象。 |

## <a name="caching"></a>Caching

| 设置名| 说明 |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | 是否启用本地缓存。 可用选项包括： <br/>- `Default`：继承标记级全局设置。<br/>- `Always`：对应用启用。<br/>- OnStorageUnavailability<br/>- `Disabled`：对应用禁用。 |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | 本地缓存的读写选项。 可用选项包括： <br/>- `ReadOnly`：缓存为只读。<br/>- `WriteWithCopyBack`：允许写入本地缓存并定期复制到共享存储。 由于 SCM 站点指向本地缓存，此选项仅适用于单实例应用。<br/>- `WriteButDiscardChanges`：允许写入本地缓存，但要放弃在本地所做的更改。 |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | 本地缓存的大小（以 MB 为单位）。 默认值为 `1000` (1 GB)。 |
| `WEBSITE_LOCALCACHE_READY` | 只读标志，指示应用是否使用本地缓存。 |
| `WEBSITE_DYNAMIC_CACHE` | 由于网络文件允许访问多个实例的共享性质，动态缓存可在实例上本地缓存最近访问的文件来提高性能。 修改文件时，缓存无效。 缓存位置为 `%SYSTEMDRIVE%\local\DynamicCache`（应用相同的 `%SYSTEMDRIVE%\local` 配额）。 默认情况下，完整内容缓存会启用（设置为 `1`），其中包括文件内容和目录/文件元数据（时间戳、大小、目录）。 若要节省本地磁盘的使用，请设置为 `2` 以仅缓存目录/文件元数据（时间戳、大小、目录内容）。 若要关闭缓存，请设置为 `0`。 |
| `WEBSITE_READONLY_APP` | 使用动态缓存时，可以通过将此变量设置为 `1` 来禁用对应用根（`D:\home\site\wwwroot` 或 `/home/site/wwwroot`）的写入访问。 除了 `App_Data` 目录之外，不允许使用排他锁，这样部署才不会被锁定的文件阻止。 |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>网络

以下环境变量与[混合连接](app-service-hybrid-connections.md)和 [VNET 集成](web-sites-integrate-with-vnet.md)相关。

| 设置名 | 说明 |
|-|-|
| `WEBSITE_RELAYS` | 只读。 配置混合连接所需的数据（包括终结点和服务总线数据）。 |
| `WEBSITE_REWRITE_TABLE` | 只读。 在运行时用于执行查找并正确重写连接。 | 
| `WEBSITE_VNET_ROUTE_ALL` | 默认情况下，如果使用[区域 VNet 集成](web-sites-integrate-with-vnet.md#regional-vnet-integration)，则应用仅将 RFC1918 流量路由到 VNet。 设置为 `1` 以将所有出站流量路由到 VNet，并遵守相同的 NSG 和 UDR。 通过设置，可以通过 VNet 访问非 RFC1918 终结点，保护离开应用的所有出站流量，并强制将所有出站流量隧道到自己选择的网络设备。 |
| `WEBSITE_PRIVATE_IP` | 只读。 [与 VNet 集成](web-sites-integrate-with-vnet.md)时，与应用关联的 IP 地址。 对于区域性 VNet 集成，此值是委托子网的地址范围中的一个 IP；对于需要网关的 VNet 集成，此值是在虚拟网络网关上配置的点到站点地址池的地址范围中的一个 IP。 应用使用此 IP 通过 VNet 连接到资源。 此外，它还可以在所述地址范围内进行更改。 |
| `WEBSITE_PRIVATE_PORTS` | 只读。 在 VNet 集成中，显示应用可用来与其他节点通信的端口。 |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>密钥保管库引用

以下环境变量与[密钥保管库引用](app-service-key-vault-references.md)相关。

| 设置名 | 说明 |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | 只读。 包含目前在应用中配置的所有 Key Vault 引用的信息（包括各种状态的信息）。 |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | 如果将应用（使用 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`）的共享存储连接设置为 Key Vault 引用，则应用无法在应用创建或更新时解析密钥保管库引用（如果满足以下条件之一）： <br/>- 应用使用系统分配的标识访问密钥保管库。<br/>- 应用使用用户分配的标识访问密钥保管库，密钥保管库已[使用 VNet 锁定](../key-vault/general/overview-vnet-service-endpoints.md)。<br/>若要避免创建或更新时出错，请将此变量设置为 `1`。 |
| `WEBSITE_DELAY_CERT_DELETION` | 用户可以将此 env var 设置为 1，以确保工作进程所依赖的证书在退出之前不会被删除。 |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

以下环境变量与跨域资源共享配置 (CORS) 相关。

| 设置名 | 说明 |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | 只读。 显示 CORS 允许的域。 |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | 只读。 显示将 `Access-Control-Allow-Credentials` 标头设置为 `true` 是已启用 (`True`) 还是未启用 (`False`)。 |

## <a name="authentication--authorization"></a>身份验证和授权

以下环境变量与[应用服务身份验证](overview-authentication-authorization.md)相关。

| 设置名| 说明|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | 设置为 `true` 时，将在基于 ASP.NET 的 Web 应用程序（包括 v1 Function Apps）中禁用线程主体标识分配。 这旨在让开发人员可以使用身份验证来保护对其站点的访问，但仍在其应用逻辑中使用单独的登录机制。 默认为 `false`。 |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` 或 `false`。 默认值是 `false`。 这是适用于 Azure 应用服务的旧版 Azure 移动应用集成的设置。 将此选项设置为 `true` 可解决为经过身份验证的用户生成的 SID（安全 ID）可能发生更改的问题（如果用户更改其配置文件信息时）。 更改此值可能会导致现有的 Azure 移动应用用户 ID 发生更改。 大多数应用程序不需要使用此设置。 |
| `WEBSITE_AUTH_NONCE_DURATION`| 格式为 `_hours_:_minutes_:_seconds_` 的 timespan 值。 默认值为 `00:05:00`（即 5 分钟）。 此设置控制为所有浏览器驱动登录生成的[加密 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) 的生存期。 如果在指定的时间内无法完成登录，则会自动重试登录流。 此应用程序设置适用于 V1（经典）配置体验。 如果使用 V2 身份验证配置架构，则应改用 `login.nonce.nonceExpirationInterval` 配置值。 |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | 当设置为 `true` 且用户单击包含 URL 片段的应用链接时，登录过程将确保 URL 的 URL 片段部分在登录重定向过程中不会丢失。 有关详细信息，请参阅[自定义 Azure 应用服务身份验证中的登录和注销](configure-authentication-customize-sign-in-out.md#preserve-url-fragments)。 |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | 为了跨升级保持后向兼容性，身份验证模块使用 `/.auth/me` API 中名称由短到长的旧声明映射，因此将排除某些映射（例如“角色”）。 若要获取较新版本的声明映射，请将此变量设置为 `False`。 在“角色”示例中，它将映射到长声明名称“http://schemas.microsoft.com/ws/2008/06/identity/claims/role”。 |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` 或 `false`。 默认值是 `false`。 设置为 `true` 时，将 [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate) HTTP 响应头标从模块生成的 HTTP 401 响应中删除。 此应用程序设置适用于 V1（经典）配置体验。 如果使用 V2 身份验证配置架构，则应改用 `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate` 配置值。 |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | 一个本地文件系统目录路径，启用基于文件的令牌存储时会在其中存储令牌。 默认值是 `%HOME%\Data\.auth`。 此应用程序设置适用于 V1（经典）配置体验。 如果使用 V2 身份验证配置架构，则应改用 `login.tokenStore.fileSystem.directory` 配置值。 |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | 完全限定的 blob 容器 URL。 指示身份验证模块将所有加密令牌存储并加载到指定的 blob 存储容器，而不是使用默认的本地文件系统。  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | 任何正整数。 默认值为 `72`（小时）。 会话令牌过期后，此设置控制 `/.auth/refresh` API 可用于刷新会话令牌的时间量。 它主要用于依赖于会话令牌的 Azure 移动应用。 此时间段之后的刷新尝试将会失败，最终用户将需要再次登录。 此应用程序设置适用于 V1（经典）配置体验。 如果使用 V2 身份验证配置架构，则应改用 `login.tokenStore.tokenRefreshExtensionHours` 配置值。 |
| `WEBSITE_AUTH_TRACE_LEVEL`| 控制写入到[应用程序日志记录](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)中的身份验证跟踪的详细程度。 有效值为 `Off`、`Error`、`Warning`、`Information` 和 `Verbose`。 默认值是 `Verbose`。 |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` 或 `false`。 默认值是 `true`。 此值决不可设置为 `false`，除非交互式登录过程中临时调试[加密 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) 验证失败。 此应用程序设置适用于 V1（经典）配置体验。 如果使用 V2 身份验证配置架构，则应改用 `login.nonce.validateNonce` 配置值。 |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | 此环境变量由 Azure 应用服务平台自动填充，并用于配置集成身份验证模块。 此环境变量的值对应于 Azure 资源管理器中当前应用的 V2（非经典）身份验证配置。 它不用于显式配置。 |
| `WEBSITE_AUTH_ENABLED` | 只读。 注入到 Windows 或 Linux 应用，以指示是否启用了应用服务身份验证。 |

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>托管标识

以下环境变量与[托管标识](overview-managed-identity.md)相关。

|设置名 | 说明 |
|-|-|
|`IDENTITY_ENDPOINT` | 只读。 用于检索应用的[托管标识](overview-managed-identity.md)的令牌的 URL。 |
| `MSI_ENDPOINT` | 已弃用。 使用 `IDENTITY_ENDPOINT`。 |
| `IDENTITY_HEADER` | 只读。 向 `IDENTITY_ENDPOINT` 发出 HTTP GET 请求时必须添加到 `X-IDENTITY-HEADER` 标头的值。 该值由平台轮换。 |
| `MSI_SECRET` | 已弃用。 使用 `IDENTITY_HEADER`。 |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | 默认禁用？ TODO | -->

## <a name="health-check"></a>运行状况检查

以下环境变量与[运行状况检查](monitor-instances-health-check.md)相关。

| 设置名 | 说明 |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 删除实例之前失败的 ping 的最大次数。 设置为介于 `2` 和 `100` 之间的值。 纵向或横向扩展时，应用服务将对运行状况检查路径执行 ping 操作，确保新实例准备就绪。 有关详细信息，请查阅[运行状况探测](monitor-instances-health-check.md)。|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 为避免运行状况实例不堪重负，排除的实例不得过半。 例如，如果应用服务计划扩展到 4 个实例，且其中 3 个运行不正常，则最多排除 2 个。 其他 2 个实例（1 个运行正常的实例和 1 个运行不正常的实例）将继续接收请求。 在所有实例都运行不正常的最糟糕的情况下，将不排除任何实例。 若要重写此行为，请设置为介于 `0` 和 `100` 之间的值。 值越高意味着将删除越多运行不正常的实例。 默认值为 `50` (50%)。 |

## <a name="push-notifications"></a>推送通知

以下环境变量与[推送通知](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub)功能相关。

| 设置名 | 说明 |
|-|-|
| `WEBSITE_PUSH_ENABLED` | 只读。 启用推送通知时添加。 |
| `WEBSITE_PUSH_TAG_WHITELIST` | 只读。 包含通知注册中的标记。 |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | 只读。 包含通知注册中需要用户身份验证的一系列标记。 |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | 只读。 包含通知注册中自动添加的一系列标记。 | 

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>Web 作业

以下环境变量与 [Web 作业](webjobs-create.md)相关。

| 设置名| 说明 |
|-|-|
| `WEBJOBS_RESTART_TIME`|对于连续作业，为作业进程因任何原因发生故障时重新执行之前的延迟时间（以秒为单位）。 |
| `WEBJOBS_IDLE_TIMEOUT`| 对于触发的作业，为超时（以秒为单位），超过此时间后，若作业处于空闲状态，则会中止作业且没有 CPU 时间或输出。 |
| `WEBJOBS_HISTORY_SIZE`| 对于触发的作业，为每个作业的历史记录目录中保留的最大运行次数。 默认为 `50`。 |
| `WEBJOBS_STOPPED`| 设置为 `1` 以禁止运行任何作业，并停止当前正在运行的所有作业。 |
| `WEBJOBS_DISABLE_SCHEDULE`| 设置为 `1` 以关闭所有计划触发。 仍可手动调用作业。 |
| `WEBJOBS_ROOT_PATH`| Web 作业文件的绝对路径或相对路径。 对于相对路径，该值与默认根路径（`D:/home/site/wwwroot/` 或 `/home/site/wwwroot/`）组合在一起。 |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| 设置为 true 以将触发的 Web 作业的输出发送到应用程序日志管道（支持文件系统、blob 和表）。 |
| `WEBJOBS_SHUTDOWN_FILE` | 检测到关闭请求时应用服务创建的文件。 Web 作业进程负责检测此文件是否存在并启动关闭。 使用 Web 作业 SDK 时，会自动处理此部分。 |
| `WEBJOBS_PATH` | 只读。 当前正在运行的作业的根路径（在某个临时目录下）。 |
| `WEBJOBS_NAME` | 只读。 当前作业名称。 |
| `WEBJOBS_TYPE` | 只读。 当前作业类型（`triggered` 或 `continuous`）。 |
| `WEBJOBS_DATA_PATH` | 只读。 包含作业日志、历史记录和作业的任何项目的当前作业元数据路径。 |
| `WEBJOBS_RUN_ID` | 只读。 对于触发的作业，为作业的当前运行 ID。 |

## <a name="functions"></a>函数

| 设置名 | 说明 |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | 设置为 `0` 可禁用函数缓存。 |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | 设置为 `0` 可对消耗计划禁用占位符函数优化。 占位符是可[改进冷启动](../azure-functions/functions-scale.md#cold-start-behavior)的优化。 |
| `WEBSITE_PLACEHOLDER_MODE` | 只读。 显示函数应用正在占位符主机 (`generalized`) 上还是自己的主机 (`specialized`) 上运行。 |
| `WEBSITE_DISABLE_ZIP_CACHE` | 当应用从 [ZIP 包](deploy-run-package.md)运行时 (`WEBSITE_RUN_FROM_PACKAGE=1`)，最新部署的五个 ZIP 包将缓存在应用文件系统中 (D:\home\data\SitePackages)。 将此变量设置为 `1` 以禁用此缓存。 对于 Linux 消耗应用，默认情况下禁用 ZIP 包缓存。 |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->
