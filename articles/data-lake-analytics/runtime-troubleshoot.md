---
title: 如何排查 Azure Data Lake Analytics 的 U SQL 运行时故障
description: 了解如何对 SQL 运行时故障进行故障排除。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030607"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>了解如何排查由运行时更改引起的 SQL 运行时故障

Azure Data Lake U-SQL 运行时（包括编译器、优化器和作业管理器）用于处理 U SQL 代码。

## <a name="choosing-your-u-sql-runtime-version"></a>选择 U-SQL 运行时版本

当你从 Visual Studio、ADL SDK 或 Azure Data Lake Analytics 门户提交 U-SQL 作业时，作业将使用当前可用的默认运行时。 新版本的 U SQL 运行时将定期发布，同时包含次要更新和安全修补程序。

你还可以选择自定义运行时版本;由于你想要尝试新的更新，因此需要停留在较早版本的运行时，或者为报告的问题提供了一个修补程序，你无法等待定期更新。

> [!CAUTION]
> 选择与默认运行时不同的运行时可能会中断 U-SQL 作业。 仅将这些其他版本用于测试。

在极少数情况下，Microsoft 支持部门可能会将不同版本的运行时固定为你的帐户的默认版本。 请确保尽快还原此 pin。 如果仍固定到该版本，则会在以后的某个日期过期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>监视作业 U-SQL 运行时版本

你可以通过 Visual Studio 的作业浏览器或 Azure 门户的作业历史记录，查看你过去的作业在你的帐户的作业历史记录中使用的运行时版本的历史记录。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 选择 " **查看所有作业**"。 此时将显示帐户中所有活动和最近完成的作业的列表。
3. 根据需要单击“筛选器”，帮助按“时间范围”、“作业名称”和“作者”值查找作业。
4. 您可以看到在已完成的作业中使用的运行时。

![显示过去作业的运行时版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的运行时版本随时间而改变。 默认运行时始终称为 "默认值"，我们至少保留上一次运行时的可用时间，并使特殊运行时可用于多种原因。 显式命名的运行时通常遵循以下格式 (斜体用于可变部分，而 [] 表示可选部分) ：

release_YYYYMMDD_adl_buildno [_modifier]

例如，release_20190318_adl_3394512_2 表示3394512年 3 18 2019 月版的运行时版本的第二个版本，而 release_20190318_adl_3394512_private 表示同一版本的专用生成。 注意：该日期与上次签入的时间有关，而不一定是正式发布日期。


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>排查 U-SQL 运行时版本问题

可能会遇到两个可能的运行时版本问题：

1. 脚本或某些用户代码将行为从一个版本更改为下一个版本。 此类重大更改通常会在发布发行说明之前提前传达。 如果遇到此类重大更改，请联系 Microsoft 支持部门来报告此重大行为 (以防还) 记录此重大行为，并针对较旧的运行时版本提交作业。

2. 如果已将非默认运行时固定到你的帐户，但在一段时间后删除了该运行时，则表示你已显式或隐式使用了非默认运行时。 如果遇到缺少的运行时，请升级您的脚本以与当前默认运行时一起运行。 如果需要额外的时间，请联系 Microsoft 支持部门

## <a name="known-issues"></a>已知问题

1. 在 SCRIPT.USQL 脚本中引用12.0.3 或文件版本的 Newtonsoft.Js将导致以下编译失败：

    *"很抱歉;在 Data Lake Analytics 帐户中运行的作业可能会运行得更慢或无法完成。出现意外问题，导致我们无法自动将此功能还原到 Azure Data Lake Analytics 帐户。已与 Azure Data Lake 工程师联系调查。 "*  

    调用堆栈将包含的位置：  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **解决方案**：请 Newtonsoft.Js使用12.0.2 或更低版本的文件。
2. 客户可能会看到其存储区中的临时文件和文件夹。 它们是在正常作业执行过程中生成的，但通常会在客户看到它们之前删除。 在某些情况下，这种情况并不常见，它们可能会在一段时间内保持可见。 它们最终会被删除，并且永远不会计为用户存储的一部分，也不会生成任何形式的收费。 根据客户的作业逻辑，它们可能会导致问题。 例如，如果作业枚举文件夹中的所有文件，然后对文件列表进行比较，则可能会由于出现意外的临时文件而失败。 同样，如果下游作业枚举给定文件夹中的所有文件进行进一步处理，则它还可能会枚举临时文件。  

    **解决方案**：在运行时中标识了一个修补程序，临时文件将存储在与当前输出文件夹不同的帐户级别 temp 文件夹中。 临时文件将写入此新临时文件夹中，并将在作业执行结束时被删除。  
    由于此修补程序正在处理客户数据，因此，在 MSFT 中充分验证此修补程序，这一点非常重要。 此修补程序应在2021年年底提供此修补程序，作为 beta 运行时提供，在第半年2021中作为默认运行时提供。 


## <a name="see-also"></a>另请参阅

- [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
- [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 门户监视 Azure Data Lake Analytics 中的作业](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
