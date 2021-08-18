---
title: Apache Hadoop 组件和版本 - Azure HDInsight
description: 了解 Azure HDInsight 中的 Apache Hadoop 组件和版本。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: df177157d08bd38871efce5879cf6f6eca783760
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725357"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight 版本

HDInsight 会将 Apache Hadoop 环境组件和 HDInsight 平台捆绑到一个部署在群集中的包中。 有关更多详细信息，请参阅 [HDInsight 版本控制的工作原理](hdinsight-overview-versioning.md)。

## <a name="supported-hdinsight-versions"></a>支持的 HDInsight 版本

此表列出了在 Azure 门户和其他部署方法（如 PowerShell、CLI 和 .NET SDK）中提供的 HDInsight 版本。

| HDInsight 版本 | VM OS | 发行日期| 支持类型 | 支持到期日期 | 停用日期 | 高可用性 |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | [标准](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |是 |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | [基本](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | 标准支持到期日期 - 2021 年 6 月 30 日 <br> 基本支持到期日期 - 2022 年 4 月 3 日 |2022 年 4 月 4 日 |是 |

*从 2021 年 7 月 1 日开始，Microsoft 将为某些 HDI 3.6 群集类型提供基本支持。 请参阅 [HDInsight 3.6 组件版本](hdinsight-36-component-versioning.md)。

## <a name="release-notes"></a>发行说明

有关 HDInsight 最新版本的更多发行说明，请参阅 [HDInsight 发行说明](hdinsight-release-notes.md)。

## <a name="support-options-for-hdinsight-versions"></a>HDInsight 版本的支持选项

该支持定义为由 Microsoft 客户服务和支持部门为某个 HDInsight 版本提供支持的时间段。 HDInsight 提供两种类型的支持： 
- 标准支持是 Microsoft 在 HDInsight 群集上提供更新和支持的时间段。  
    建议使用最新的完全受支持的版本构建解决方案。 
- 基本支持是 Microsoft 为 HDInsight 资源提供程序提供有限服务的时间段。 不会为 HDInsight 映像和开源软件 (OSS) 组件提供服务。   HDInsight 群集上只会修补关键安全修补程序。  
  Microsoft 不建议在版本处于基本支持时创建新群集或构建任何全新解决方案。 建议将现有群集迁移到最新的完全受支持的版本。 

**支持到期** 意味着 Microsoft 不再为特定的 HDInsight 版本提供支持， 并且不再可以通过 Azure 门户使用该版本创建群集。

**停用** 意味着 HDInsight 版本的现有群集将继续按现样运行。 不能通过任何方式（包括 CLI 和 SDK）创建此版本的新群集。 其他控制平面功能（如手动缩放和自动缩放）不保证在停用日期过后能正常工作。 对已停用的版本不提供支持。

## <a name="versioning-considerations"></a>版本控制注意事项
- 使用映像部署群集后，该群集不会自动升级到更新的映像版本。 创建新群集时，会部署最新的映像版本。
- 使用新的 HDInsight 版本时，客户应该测试并验证应用程序是否正常运行。
- HDInsight 保留更改默认版本的权利，恕不事先通知。 如果依赖于某个版本，请在创建群集时指定 HDInsight 版本。
- 在停用 HDInsight 版本之前，HDInsight 可能会停用 OSS 组件版本。

## <a name="next-steps"></a>后续步骤

- [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
- [企业安全性套餐](./enterprise-security-package.md)
- [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)
