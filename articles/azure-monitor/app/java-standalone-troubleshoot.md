---
title: 故障排除-Azure Monitor Application Insights Java
description: Application Insights Java Azure Monitor 疑难解答
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855585"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Application Insights Java Azure Monitor 疑难解答

本文介绍了一些用户可能遇到的常见问题，同时使用 java 代理检测 java 应用程序以及解决这些问题的步骤。

## <a name="self-diagnostic-log-file"></a>自诊断日志文件

默认情况下，Application Insights Java 3.0 将生成一个名为的日志文件， `applicationinsights.log` 该文件 `applicationinsights-agent-3.0.0.jar` 位于文件所在的同一目录中。

此日志文件是检查你可能遇到的任何问题的提示的第一个位置。

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 升级

请参阅 [从 1.X SDK 升级](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-30-preview"></a>从3.0 预览版升级

如果从3.0 预览版升级，请仔细检查所有 [配置选项](./java-standalone-config.md) ，因为在 3.0 GA 版本中已完全更改 json 结构。

这些更改包括：

1.  配置文件的名称本身已从更改 `ApplicationInsights.json` 为 `applicationinsights.json` 。
2.  `instrumentationSettings`节点不再存在。 中的所有内容 `instrumentationSettings` 都将移动到根级别。 
3.  诸如、和之类的配置节点 `sampling` `jmxMetrics` `instrumentation` `heartbeat` 移出 `preview` 到根级别。

## <a name="ssl-certificate-issues"></a>SSL 证书问题

如果你使用的是默认的 Java 密钥存储，则它已具有所有 CA 根证书，因此你无需导入任何其他 SSL 证书。

如果使用的是自定义 Java 密钥存储，则可能需要将 Application Insights 的终结点 SSL 证书导入其中。

### <a name="some-key-terminology"></a>一些关键术语：
*密钥* 存储是证书、公钥和私钥的存储库。 通常，JDK 分发版具有用于管理它们的可执行文件 `keytool` 。

下面的示例是一个简单的命令，用于将 SSL 证书导入到密钥存储：

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>下载并添加 SSL 证书的步骤：

1.  打开你最喜欢的浏览器，然后前往 `IngestionEndpoint` 用于检测应用程序的连接字符串中存在的 url，如下所示

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights 连接字符串":::

2.  单击浏览器上的 "查看站点信息" (锁定) "图标，然后单击" 证书 "选项，如下所示

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL 证书捕获":::

3.  请参阅 "详细信息" 选项卡，然后单击 "复制到文件"。
4.  单击 "下一步" 按钮，然后选择 "64 编码的 x.509 (。CER) "格式"，然后选择 "下一步"。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL 证书 ExportWizard":::

5.  指定要保存 SSL 证书的文件。 最后单击 "下一步" 和 "完成"。 应该会看到 "导出成功" 消息。
6.  一旦将证书导入到 Java 密钥库，便会获得证书。 使用上述 [命令](#some-key-terminology) 导入证书。

> [!WARNING]
> 在当前证书到期之前，你将需要重复这些步骤以获取新证书。 可以在证书弹出窗口的 "详细信息" 选项卡中找到过期信息，如下所示

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 证书详细信息":::
