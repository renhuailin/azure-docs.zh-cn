---
title: Java Application Insights Azure Monitor 疑难解答
description: 了解如何排查 Azure Monitor 的 Java agent Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 14644f76b7de53b2b6ee3f04131daaf59267a5ff
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507636"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>故障排除指南： Azure Monitor Java Application Insights

本文介绍使用 Java agent for Application Insights 检测 Java 应用程序时可能遇到的一些常见问题。 还介绍了解决这些问题的步骤。 Application Insights 是 Azure Monitor 平台服务的一项功能。

## <a name="check-the-self-diagnostic-log-file"></a>检查自诊断日志文件

默认情况下，Application Insights 的 Java 3.0 代理 `applicationinsights.log` 会在保存文件的同一目录中生成一个名为的日志文件 `applicationinsights-agent-3.0.0.jar` 。

此日志文件是检查你可能遇到的任何问题的提示的第一个位置。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 升级

如果已在应用程序中使用 Application Insights Java 2.x SDK，则可以继续使用它。 Java 3.0 代理将检测到它。 有关详细信息，请参阅 [从 Java 2.X SDK 升级](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-application-insights-java-30-preview"></a>从 Application Insights Java 3.0 预览版升级

如果要从 Java 3.0 预览代理升级，请仔细检查所有 [配置选项](./java-standalone-config.md) 。 JSON 结构已在3.0 公开上市 (GA) 版本中进行了完全更改。

这些更改包括：

-  配置文件名称已从更改 `ApplicationInsights.json` 为 `applicationinsights.json` 。
-  `instrumentationSettings`节点不再存在。 中的所有内容 `instrumentationSettings` 都将移动到根级别。 
-  诸如、、和之类的配置节点 `sampling` `jmxMetrics` `instrumentation` `heartbeat` 移出 `preview` 到根级别。

## <a name="some-logging-is-not-auto-collected"></a>某些日志记录不是自动收集的

仅在第一次满足日志记录框架配置的阈值时才捕获日志记录，另一种方法还满足 Application Insights 配置的阈值。

了解特定记录语句是否满足日志记录框架的配置阈值的最佳方式是确认它显示在普通的应用程序日志中 (例如文件或控制台) 。

有关更多详细信息，请参阅 [自动收集的日志记录配置](./java-standalone-config.md#auto-collected-logging) 。

## <a name="import-ssl-certificates"></a>导入 SSL 证书

如果使用默认的 Java 密钥存储，则它已具有所有 CA 根证书。 不需要导入更多的 SSL 证书。

如果使用的是自定义 Java 密钥存储，则可能需要将 Application Insights 的终结点 SSL 证书导入其中。

### <a name="key-terminology"></a>关键术语
*密钥* 存储是证书、公钥和私钥的存储库。 通常，Java 开发工具包分发有一个可执行文件来管理它们： `keytool` 。

下面的示例是一个简单的命令，用于将 SSL 证书导入到密钥存储：

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>下载和添加 SSL 证书的步骤

1.  打开你最喜欢的浏览器，然后前往 `IngestionEndpoint` 用于检测应用程序的连接字符串中提供的 URL。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="显示 Application Insights 连接字符串的屏幕截图。":::

2.  在浏览器中选择 " **查看站点信息** (锁定) " 图标，然后选择 " **证书** " 选项。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="&quot;站点信息&quot; 中的 &quot;证书&quot; 选项的屏幕截图。":::

3.  请参阅 " **详细信息** " 选项卡并选择 " **复制到文件**"。
4.  选择 " **下一步** " 按钮，选择 " **64 编码的 x.509 (。CER)** 格式，然后再次选择 " **下一步** "。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="&quot;证书导出向导&quot; 的屏幕截图，其中选择了格式。":::

5.  指定要在其中保存 SSL 证书的文件。 然后选择 "**下一步**"  >  。 应该会看到 "导出成功" 消息。
6.  获得证书后，即可将证书导入到 Java 密钥存储中。 使用 [前面的命令](#key-terminology) 导入证书。

> [!WARNING]
> 在当前证书到期之前，你需要重复这些步骤以获取新证书。 可以在 "**证书**" 对话框的 "**详细信息**" 选项卡上找到过期信息。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="显示 SSL 证书详细信息的屏幕截图。":::
