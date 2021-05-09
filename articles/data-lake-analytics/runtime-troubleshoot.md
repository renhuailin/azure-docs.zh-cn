---
title: 如何排查 Azure Data Lake Analytics U-SQL 运行时故障
description: 了解如何排查 U-SQL 运行时故障。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030607"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>了解如何排查由于运行时更改而导致的 U-SQL 运行时故障

Azure Data Lake U-SQL 运行时（包括编译器、优化器和作业管理器）用于处理 U SQL 代码。

## <a name="choosing-your-u-sql-runtime-version"></a>选择 U-SQL 运行时版本

从 Visual Studio、ADL SDK 或 Azure Data Lake Analytics 门户提交 U-SQL 作业时，你的作业将使用当前可用的默认运行时。 U-SQL 运行时会定期发布新版本，其中包含次要更新和安全修补程序。

当你想尝试新的更新但仍需要保留较早版本的运行时，或针对报告的问题已提供修补程序但你不想等待定期新的更新时，可以选择自定义运行时版本。

> [!CAUTION]
> 选择与默认运行时不同的运行时可能会中断 U-SQL 作业。 使用其他版本仅供测试。

在极少数情况下，Microsoft 支持部门可能会将其他版本的运行时锁定为你的帐户的默认版本。 请确保尽快还原此锁定。 如果保持锁定该版本，不久后它将会过期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>监视作业 U-SQL 运行时版本

可以通过 Visual Studio 的作业浏览器或 Azure 门户的作业历史记录，在帐户的作业历史记录中查看过去作业使用的运行时版本的历史记录。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 选择“查看所有作业”。 将显示帐户中所有活动作业和最近已完成作业的列表。
3. 根据需要单击“筛选器”，帮助按“时间范围”、“作业名称”和“作者”值查找作业。
4. 可以看到已完成作业中使用的运行时。

![显示过去作业的运行时版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的运行时版本随时间发生变化。 默认运行时始终称为“默认”，至少将上一个运行时保持可用一段时间，并针对各种原因使特殊运行时可用。 显式命名的运行时通常遵循以下格式（斜体用于变量部分，[] 表示可选部分）：

release_YYYYMMDD_adl_buildno[_modifier]

例如，release_20190318_adl_3394512_2 表示 2019 年 3 月 18 日的运行时版本的 3394512 版本的第二个版本，而 release_20190318_adl_3394512_private 则表示同一版本的专用版本。 注意：日期与该版本的最后一次签入时间有关，不一定是正式发布日期。


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>排查 U-SQL 运行时版本问题

你可能会遇到两个可能的运行时版本问题：

1. 脚本或一些用户代码的行为因版本而异。 此类中断性变更通常会在发行说明发布之前提前传达。 如果遇到此类中断性变更，请联系 Microsoft 支持部门报告此中断行为（假如尚未记录），并提交使用较早运行时版本的作业。

2. 帐户锁定了非默认运行时且已显式或隐式地使用了它，而一段时间后该运行时被移除。 如果遇到运行时缺失的情况，请升级脚本以使用当前默认运行时来运行。 如果需要额外时间，请联系 Microsoft 支持部门

## <a name="known-issues"></a>已知问题

1. 在 USQL 脚本中引用 Newtonsoft.Json 文件 12.0.3 版本或更高版本将导致以下编译故障：

    *“很抱歉，Data Lake Analytics 帐户中运行的作业可能运行更加缓慢或无法完成。意外问题导致无法为 Azure Data Lake Analytics 帐户自动恢复此功能。已联系 Azure Data Lake 工程师进行调查。”*  

    其中调用堆栈将包含：  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **解决方案**：请使用 Newtonsoft.Json 文件 v12.0.2 版本或更低版本。
2. 客户可能会在其存储中看到临时文件和文件夹。 它们在正常作业执行期间生成，但是通常在客户看到它们之前就已被删除。 在某些罕见且随机的情况下，它们可能会在一段时间内保持可见。 它们最终会被删除，永远不会计为用户存储的一部分，且无论如何也不会产生任何形式的费用。 根据客户的作业逻辑，它们可能会导致问题。 例如，如果作业枚举文件夹中的所有文件，然后比较文件列表，则可能由于意外存在的临时文件而失败。 同样的，如果下游作业枚举给定文件夹中的所有文件以进行进一步处理，它也可能会枚举临时文件。  

    **解决方案**：在运行时中标识修补程序，在此期间临时文件将存储在帐户级别的临时文件夹中，而不是当前的输出文件夹中。 临时文件将被写入此新的临时文件夹，并在作业执行结束时删除。  
    由于该修补程序将处理客户数据，因此在发布之前在 MSFT 中对该修补程序进行充分验证是极其重要的。 预计将在 2021 年年中作为 beta 版本运行时并在 2021 年下半年作为默认运行时提供此修补程序。 


## <a name="see-also"></a>请参阅

- [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
- [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 门户监视 Azure Data Lake Analytics 中的作业](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
