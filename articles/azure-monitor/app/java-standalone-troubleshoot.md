---
title: 对适用于 Java 的 Azure Monitor Application Insights 进行故障排除
description: 了解如何对 Azure Monitor Application Insights 的 Java 代理进行故障排除
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: d88662e174caffb56358b9c9c584b6b849ca45c4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616923"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>故障排除指南：适用于 Java 的 Azure Monitor Application Insights

本文介绍了使用 Application Insights 的 Java 代理来检测 Java 应用程序时可能会遇到的一些常见问题。 本文还介绍了解决这些问题的步骤。 Application Insights 是 Azure Monitor 平台服务的一项功能。

## <a name="check-the-self-diagnostic-log-file"></a>检查自诊断日志文件

默认情况下，Application Insights Java 3.x 在保存 `applicationinsights-agent-3.2.0.jar` 文件的同一目录中生成名为 `applicationinsights.log` 的日志文件。

针对你可能遇到的问题查找线索时，此日志文件是要检查的第一个位置。

## <a name="jvm-fails-to-start"></a>JVM 无法启动

如果 JVM 无法启动并显示“打开 zip 文件时出错或缺少 JAR 清单”，请尝试重新下载代理 jar 文件，因为它在文件传输过程中可能已损坏。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 进行升级

如果你已在应用程序中使用 Application Insights Java 2.x SDK，则可以继续使用它。
Application Insights Java 3.x 代理会检测到它，并捕获和关联你通过 2.x SDK 发送的任何自定义遥测，同时禁止 2.x SDK 执行的任何自动收集以防止重复遥测。
有关详细信息，请参阅[从 Java 2.x SDK 进行升级](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-application-insights-java-30-preview"></a>从 Application Insights Java 3.0 预览版进行升级

如果要从 Java 3.0 预览版代理进行升级，请仔细检查所有[配置选项](./java-standalone-config.md)。 JSON 结构在 3.0 正式发布版 (GA) 中已完全更改。

这些更改包括：

-  配置文件名称已从 `ApplicationInsights.json` 更改为 `applicationinsights.json`。
-  `instrumentationSettings` 节点不再存在。 `instrumentationSettings` 中的所有内容已移动到根级别。 
-  `sampling`、`jmxMetrics`、`instrumentation` 和 `heartbeat` 等配置节点已从 `preview` 移出到根级别。

## <a name="some-logging-is-not-auto-collected"></a>某些日志记录不是自动收集的

日志记录首先要符合为日志记录框架配置的级别，其次还要符合为 Application Insights 配置的级别，只有在这种情况下才会捕获日志记录。

例如，如果将日志记录框架配置为从包 `com.example` 记录 `WARN`（和更高版本），并将 Application Insights 配置为捕获 `INFO`（和更高版本），则 Application Insights 将只从包 `com.example` 捕获 `WARN`（和更高版本）。

若要了解某个特定的日志记录语句是否满足日志记录框架的已配置阈值，最好的方法是确认它是否显示在正常的应用程序日志（例如文件或控制台）中。

另请注意，如果将异常对象传递到记录器，则日志消息（和异常对象详细信息）会显示在 Azure 门户中的 `exceptions` 表（而不是 `traces` 表）下。

有关更多详细信息，请参阅[自动收集的日志记录配置](./java-standalone-config.md#auto-collected-logging)。

## <a name="import-ssl-certificates"></a>导入 SSL 证书

本部分帮助你在使用 Java 代理时进行故障排除，并可能会修复与 SSL 证书相关的异常。

下面有两种不同的方法可以解决这个问题：
* 如果使用的是默认 Java 密钥存储
* 如果使用的是自定义 Java 密钥存储

如果不确定要使用哪种方法，请检查是否有 JVM 参数 `-Djavax.net.ssl.trustStore=...`。
如果没有这样的 JVM 参数，则可能使用的是默认 Java 密钥存储。
如果确实有这样的 JVM 参数，则可能使用的是自定义密钥存储，JVM 参数会将你指向自定义密钥存储。

### <a name="if-using-the-default-java-keystore"></a>如果使用的是默认 Java 密钥存储：

通常情况下，默认的 Java 密钥存储已具有所有 CA 根证书。 但是，可能有一些例外情况，例如，引入终结点证书可能由不同的根证书进行签名。 因此，我们建议使用以下三个步骤来解决此问题：

1.  检查用于对 Application Insights 终结点进行签名的根证书是否已存在于默认密钥存储中。 默认情况下，受信任的 CA 证书存储在 `$JAVA_HOME/jre/lib/security/cacerts` 中。 若要列出 Java 密钥存储中的证书，请使用以下命令：
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    你可以将输出重定向到这样的一个临时文件（稍后可以轻松搜索）
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. 获得证书列表后，按照以下[步骤](#steps-to-download-ssl-certificate)下载用于对 Application Insights 终结点进行签名的根证书。

    下载证书后，使用以下命令基于证书生成 SHA-1 哈希：
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    复制 SHA-1 值并检查此值是否存在于你之前保存的“temp.txt”文件中。  如果在临时文件中找不到 SHA-1 值，则表示默认 Java 密钥存储中缺少下载的根证书。


3. 使用以下命令将根证书导入到默认 Java 密钥存储：
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    在本例中，该证书是
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>如果使用的是自定义 Java 密钥存储：

如果使用的是自定义 Java 密钥存储，则你可能需要将 Application Insights 终结点根 SSL 证书导入其中。
建议你执行以下两个步骤来解决此问题：
1. 按照以下[步骤](#steps-to-download-ssl-certificate)从 Application Insights 终结点下载根证书。
2. 使用以下命令将根 SSL 证书导入到自定义 Java 密钥存储：
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>下载 SSL 证书的步骤

1.  打开你习惯使用的浏览器，然后转到用于检测应用程序的连接字符串中存在的 `IngestionEndpoint` URL。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="屏幕截图显示了 Application Insights 连接字符串。" lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  选择浏览器中的“查看站点信息”（锁）图标，然后选择“证书”选项。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="屏幕截图显示了站点信息中的“证书”选项。" lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  你应当下载“根”证书，而不是下载“叶”证书，如下所示。 然后，你必须单击“证书路径”->“选择根证书”-> 单击“查看证书”。 这会弹出一个新的证书菜单，你可以通过此新菜单来下载证书。

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="屏幕截图显示了如何选择根证书。" lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  转到“详细信息”选项卡，然后选择“复制到文件”。
5.  选择“下一步”按钮，选择“Base-64 编码的 X.509 (.CER)”格式，然后再次选择“下一步”。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="证书导出向导的屏幕截图，其中选择了一种格式。" lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  指定要在其中保存 SSL 证书的文件。 然后，选择“下一步” > “完成”。  应该会看到“导出成功”消息。

> [!WARNING]
> 在当前证书到期之前，你需要重复这些步骤以获取新证书。 可以在“证书”对话框的“详细信息”选项卡上找到到期信息。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="屏幕截图显示了 SSL 证书详细信息。" lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
