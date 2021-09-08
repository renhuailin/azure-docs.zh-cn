---
title: 有关在 Azure Active Directory 应用程序预配中编写特性映射表达式的参考
description: 了解如何在 Azure Active Directory 中自动预配 SaaS 应用对象期间，使用表达式映射将属性值转换为可接受的格式。 包括函数参考列表。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/30/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: c91d4f98928f2d446a15b123a4155b971377159a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223835"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>有关在 Azure Active Directory 中编写特性映射表达式的参考

将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 对于这些映射，必须编写一个类似于脚本的表达式，以便能够将用户的数据转换为 SaaS 应用程序更可接受的格式。

## <a name="syntax-overview"></a>语法概述

属性映射的表达式语法让人联想到 Visual Basic for Applications (VBA) 函数。

* 整个表达式必须按函数来定义，名称后接带括号的自变量：FunctionName(`<<argument 1>>`,`<<argument N>>`)
* 函数之间可以相互嵌套。 例如：FunctionOne(FunctionTwo(`<<argument1>>`))
* 可以将三种不同类型的参数传递给函数：
  
  1. 属性，必须括在方括号中。 例如：[attributeName]
  2. 字符串常量必须括在双引号内。 例如："美国"
  3. 其他函数。 例如：FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 对于字符串常量，如果字符串中需要反斜杠 ( \ ) 或引号 ( " )，则必须使用反斜杠 ( \ ) 符号进行转义。 例如："Company name: \\"Contoso\\""
* 语法区分大小写，在函数中以字符串形式键入或者从此处直接复制粘贴时，必须考虑到这一点。 

## <a name="list-of-functions"></a>函数列表

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [AppRoleAssignmentsComplex](#approleassignmentscomplex) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateDiff](#datediff) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IgnoreFlowIfNullOrEmpty](#ignoreflowifnullorempty) &nbsp;&nbsp;&nbsp;&nbsp;[IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) &nbsp;&nbsp; &nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>附加

函数：Append(source, suffix)

说明：取源字符串值，并将后缀追加到其末尾。

**参数：**

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| **suffix** |必须 |String |要附加到源值末尾的字符串。 |


#### <a name="append-constant-suffix-to-user-name"></a>将常量后缀附加到用户名
示例：如果使用 Salesforce 沙盒，则可能需要先向你的所有用户名追加额外后缀，然后才对其进行同步。

表达式： 
`Append([userPrincipalName], ".test")`

**示例输入/输出：** 

* **输入**：(userPrincipalName)：“John.Doe@contoso.com”
* **输出**：“John.Doe@contoso.com.test”

---
### <a name="approleassignmentscomplex"></a>AppRoleAssignmentsComplex

函数：AppRoleAssignmentsComplex([appRoleAssignments])

说明：用于为一个用户预配多个角色。 有关详细使用情况，请参阅[教程 - 为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md#provisioning-a-role-to-a-scim-app)。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必须 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="bitand"></a>BitAnd
函数：BitAnd(value1, value2)

说明：此函数将两个参数转换为二进制表示形式，并将一个位设置为：

- 0 - 如果 value1 和 value2 中的一个或两个相应位为 0
- 1 - 如果两个相应位均为 1。

换而言之，除了当两个参数的相应位均为 1 时之外，所有情况下均返回 0。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **value1** |必需 |num |应与 value2 进行 AND 运算的数字值|
| **value2** |必需 |num |应与 value1 进行 AND 运算的数字值|

**示例：** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111，因此 `BitAnd` 返回 7，即 00000111 的二进制值。

---
### <a name="cbool"></a>CBool
函数： 
`CBool(Expression)`

说明： 
`CBool` 基于求值的表达式返回布尔值。 如果该表达式求值为非零值，则 `CBool` 返回 True，否则返回 False 。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 | 表达式 | 任何有效表达式 |

示例：  
`CBool([attribute1] = [attribute2])`                                                                    
如果两个属性具有相同的值，则返回 True。

---
### <a name="cdate"></a>CDate
**函数：**  
`CDate(expression)`

**说明：**  
CDate 函数通过字符串返回 UTC DateTime。 DateTime 不是本机属性类型，但可用在 [FormatDateTime](#formatdatetime) 和 [DateAdd](#dateadd) 等日期函数内。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 | 表达式 | 表示日期/时间的任何有效字符串。 有关支持的格式，请参阅 [.NET 自定义日期和时间格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

**备注：**  
返回的字符串始终采用 UTC 形式，采用 M/d/yyyy h:mm:ss tt 格式。

**示例 1：** <br> 
`CDate([StatusHireDate])`  
**示例输入/输出：** 

* 输入 (StatusHireDate)：“2020-03-16-07:00”
* 输出：“3/16/2020 7:00:00 AM”<-- 注意，返回上述 DateTime 的 UTC 等同格式

**示例 2：** <br> 
`CDate("2021-06-30+08:00")`  
**示例输入/输出：** 

* 输入：“2021-06-30+08:00”
* 输出：“6/29/2021 4:00:00 PM”<-- 注意，返回上述 DateTime 的 UTC 等同格式

示例 3： <br> 
`CDate("2009-06-15T01:45:30-07:00")`  
**示例输入/输出：** 

* 输入：“2009-06-15T01:45:30-07:00”
* 输出：“6/15/2009 8:45:30 AM”<-- 注意，返回上述 DateTime 的 UTC 等同格式

---
### <a name="coalesce"></a>Coalesce
函数：Coalesce(source1, source2, ..., defaultValue)

说明：返回非 NULL 的第一个源值。 如果所有自变量均为 NULL 且 defaultValue 存在，则会返回 defaultValue。 如果所有自变量均为 NULL 且 defaultValue 不存在，则 Coalesce 返回 NULL。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **source1  … sourceN** | 必须 | String |必需，次数可变。 通常是来自源对象的属性的名称。 |
| **defaultValue** | 可选 | String | 当所有源值为 NULL 时要使用的默认值。 可以是空字符串 ("")。

#### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>如果不为 NULL，则传送 mail 值，否则传送 userPrincipalName
示例：你希望当 mail 特性存在时传送该特性。 如果该特性不存在，则你希望改为传送 userPrincipalName 的值。

表达式： 
`Coalesce([mail],[userPrincipalName])`

**示例输入/输出：** 

* 输入 (mail): NULL
* 输入 (userPrincipalName): "John.Doe@contoso.com"
* **输出**：“John.Doe@contoso.com”


---
### <a name="converttobase64"></a>ConvertToBase64
函数：ConvertToBase64(source)

说明：ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要转换为 base64 的字符串|

**示例：** 
`ConvertToBase64("Hello world!")`

返回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
函数：ConvertToUTF8Hex(source)

说明：ConvertToUTF8Hex 函数将字符串转换为 UTF8 十六进制编码值。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要转换为 UTF8 十六进制值的字符串|

**示例：** 
`ConvertToUTF8Hex("Hello world!")`

返回 48656C6C6F20776F726C6421

---
### <a name="count"></a>计数
函数：Count(attribute)

说明：Count 函数返回多值特性中的元素数目

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| attribute |必需 |attribute |将要统计元素数目的多值特性|

---
### <a name="cstr"></a>CStr
函数：CStr(value)

说明：CStr 函数将值转换为字符串数据类型。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| value  |必须 | 数字、引用或布尔值 | 可以是数字值、引用属性或布尔值。 |

**示例：** 
`CStr([dn])`

返回“cn=Joe,dc=contoso,dc=com”

---
### <a name="dateadd"></a>DateAdd
**函数：**  
`DateAdd(interval, value, dateTime)`

**描述：**  
返回一个日期/时间字符串，表示某个已添加指定时间间隔的日期。 返回的日期采用以下格式：M/d/yyyy h:mm:ss tt。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **interval** |必须 | 字符串 | 要添加的时间间隔。 请参阅此表下方的可接受值。 |
| value  |必须 | Number | 要添加的单位数。 它可以是正值（以获取将来的日期）或负值（以获取过去的日期）。 |
| **dateTime** |必需 | DateTime | 表示间隔添加到其中的日期的 DateTime。 |

间隔字符串必须具有下列值之一： 
 * yyyy Year 
 * m Month
 * d Day
 * ww Week
 * h Hour
 * n Minute
 * s Second

示例 1：向雇用日期添加 7 天  
`DateAdd("d", 7, CDate([StatusHireDate]))`
* 输入 (StatusHireDate)：2012-03-16-07:00
* 输出：3/23/2012 7:00:00 AM

示例 2：获取雇用日期前 10 天的日期  
`DateAdd("d", -10, CDate([StatusHireDate]))`
* 输入 (StatusHireDate)：2012-03-16-07:00
* 输出：3/6/2012 7:00:00 AM

示例 3：向雇用日期添加 2 周  
`DateAdd("ww", 2, CDate([StatusHireDate]))`
* 输入 (StatusHireDate)：2012-03-16-07:00
* 输出：3/30/2012 7:00:00 AM

示例 4：向雇用日期添加 10 个月  
`DateAdd("m", 10, CDate([StatusHireDate]))`
* 输入 (StatusHireDate)：2012-03-16-07:00
* 输出：1/16/2013 7:00:00 AM

示例 5：向雇用日期添加 2 年  
`DateAdd("yyyy", 2, CDate([StatusHireDate]))`
* 输入 (StatusHireDate)：2012-03-16-07:00
* 输出：3/16/2014 7:00:00 AM
---
### <a name="datediff"></a>DateDiff
**函数：**  
`DateDiff(interval, date1, date2)`

**描述：**  
此函数使用 interval 参数返回一个数字，该数字表示两个输入日期之间的差异。 返回  
  * 正数（如果 date2 > date1）， 
  * 负数（如果 date2 < date1）， 
  * 0（如果 date2 == date1）

**参数：** 

| 名称 | 必需/可选 | 类型 | 说明 |
| --- | --- | --- | --- |
| **interval** |必须 | 字符串 | 用于计算差值的时间间隔。 |
| **date1** |必需 | DateTime | 表示有效日期的日期/时间。 |
| **date2** |必需 | DateTime | 表示有效日期的日期/时间。 |

间隔字符串必须具有下列值之一： 
 * yyyy Year 
 * m Month
 * d Day
 * ww Week
 * h Hour
 * n Minute
 * s Second

**示例 1：将当前日期与 Workday 中具有不同间隔的雇佣日期进行比较** <br>
`DateDiff("d", Now(), CDate([StatusHireDate]))`

| 示例 | interval | date1 | date2 | output |
| --- | --- | --- | --- | --- |
| 两个日期之间的正数差（以天为单位） | d | 2021-08-18+08:00 | 2021-08-31+08:00 | 13 |
| 两个日期之间的负数差（以天为单位） | d | 2021/8/25 晚上 5:41:18 | 2012-03-16-07:00 | -3449 |
| 两个日期之间的差值（以周为单位） | ww | 2021/8/25 晚上 5:41:18 | 2012-03-16-07:00 | -493 | 
| 两个日期之间的差值（以月为单位） | m | 2021/8/25 晚上 5:41:18 | 2012-03-16-07:00 | -113 | 
| 两个日期之间的差值（以年为单位） | yyyy | 2021/8/25 晚上 5:41:18 | 2012-03-16-07:00 | -9 | 
| 两个日期相同时的差值 | d | 2021-08-31+08:00 | 2021-08-31+08:00 | 0 | 
| 两个日期之间的差值（以小时为单位） | h | 2021-08-24 | 2021-08-25 | 24 | 
| 两个日期之间的差值（以分钟为单位） | n | 2021-08-24 | 2021-08-25 | 1440 | 
| 两个日期之间的差值（以秒为单位） | s | 2021-08-24 | 2021-08-25 | 86400 | 

**示例 2：将 DateDiff 与 IIF 函数合并用于设置属性值** <br>
如果某个帐户在 Workday 中处于活动状态，则仅当雇用日期的范围在接下来的 5 天内时，才将用户的 accountEnabled 属性设置为 True。 

```
Switch([Active], , 
  "1", IIF(DateDiff("d", Now(), CDate([StatusHireDate])) > 5, "False", "True"), 
  "0", "False")
```

---

### <a name="datefromnum"></a>DateFromNum
函数：DateFromNum(value)

说明：DateFromNum 函数将 AD 日期格式的值转换为日期/时间类型。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| value  |必须 | Date | 要转换为日期/时间类型的 AD 日期 |

**示例：** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

返回表示 2012 年 1 月 1 日 11:00PM 的日期/时间。

---
### <a name="formatdatetime"></a>FormatDateTime
函数：FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

说明：取一种格式的日期字符串并将其转换为不同的格式。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| dateTimeStyles | 可选 | String | 使用此参数可以指定格式设置选项，这些选项可自定义用于某些日期和时间分析方法的字符串分析。 有关支持的值，请参阅 [DateTimeStyles 文档](/dotnet/api/system.globalization.datetimestyles)。如果留空，则使用的默认值为 DateTimeStyles.RoundtripKind, DateTimeStyles.AllowLeadingWhite, DateTimeStyles.AllowTrailingWhite  |
| **inputFormat** |必须 |String |源值的预期格式。 有关支持的格式，请参阅 [.NET 自定义日期和时间格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |
| **outputFormat** |必须 |String |输出日期的格式。 |



#### <a name="output-date-as-a-string-in-a-certain-format"></a>输出日期是一种特定格式的字符串
示例：你想要以特定格式将日期发送到 ServiceNow 等 SaaS 应用程序。 可以考虑使用以下表达式。 

**表达式：** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**示例输入/输出：**

* **输入** (extensionAttribute1)：“20150123105347.1Z”
* **输出**：“2015-01-23”


---
### <a name="guid"></a>Guid
函数：Guid()

说明：函数 Guid 生成新的随机 GUID

**示例：** <br>
`Guid()`<br>
示例输出：“1088051a-cd4b-4288-84f8-e02042ca72bc”

---
### <a name="ignoreflowifnullorempty"></a>IgnoreFlowIfNullOrEmpty
函数：IgnoreFlowIfNullOrEmpty(expression)

说明：如果包含的函数或属性为 NULL 或为空，则 IgnoreFlowIfNullOrEmpty 函数会指示预配服务忽略属性，并将其从流中删除。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** | 必需 | 表达式 | 要对其求值的表达式 |

示例 1：如果属性为 null，则不流式传输 <br>
`IgnoreFlowIfNullOrEmpty([department])` <br>
如果值为 null 或为空，则上述表达式将从预配流中删除部门属性。 <br>

示例 2：如果表达式映射的计算结果为空字符串或 null，则不流式传输属性 <br>
假设 SuccessFactors 属性 prefix 通过以下表达式映射映射到本地 Active Directory 属性 personalTitle： <br>
`IgnoreFlowIfNullOrEmpty(Switch([prefix], "", "3443", "Dr.", "3444", "Prof.", "3445", "Prof. Dr."))` <br>
上述表达式将首先计算 [Switch](#switch) 函数的结果。 如果 prefix 属性没有 Switch 函数内列出的任何值，则 Switch 将返回一个空字符串，并且 personalTitle 属性不会包括在本地 Active Directory 的预配流中。

---
### <a name="iif"></a>IIF
函数：IIF(condition,valueIfTrue,valueIfFalse)

说明：IIF 函数基于指定的条件返回一组可能值中的一个。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **条件** |必需 |变量或表达式 |计算结果为 true 或 false 的任何值或表达式。 |
| **valueIfTrue** |必需 |变量或字符串 | 如果条件计算结果为 true，则为返回值。 |
| **valueIfFalse** |必需 |变量或字符串 |如果条件计算结果为 false，则为返回值。|

**示例：** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
函数：InStr(value1, value2, start, compareType)

说明：InStr 函数查找字符串中第一次出现的某个子字符串

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **value1** |必须 |String |要在其中进行搜索的字符串 |
| **value2** |必须 |String |要查找的字符串 |
| **start** |可选 |整数 |查找子字符串时使用的起始位置|
| compareType |可选 |枚举 |可为 vbTextCompare 或 vbBinaryCompare |

**示例：** 
`InStr("The quick brown fox","quick")`

求值为 5

`InStr("repEated","e",3,vbBinaryCompare)`

计算结果为 7

---
### <a name="isnull"></a>IsNull
函数：IsNull(Expression)

说明：如果表达式求值为 Null，则 IsNull 函数返回 true。 对于属性，Null 表示缺少属性。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 |表达式 |要对其求值的表达式 |

**示例：** 
`IsNull([displayName])`

如果该特性不存在，则返回 True。

---
### <a name="isnullorempty"></a>IsNullorEmpty
函数：IsNullOrEmpty(Expression)

说明：如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。 对于属性，如果属性不存在，或存在但为空字符串，此语法计算结果则为 True。
此函数的逆函数命名为 IsPresent。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 |表达式 |要对其求值的表达式 |

**示例：** 
`IsNullOrEmpty([displayName])`

如果该特性不存在，或者该特性是空字符串，则返回 True。

---
### <a name="ispresent"></a>IsPresent
函数：IsPresent(Expression)

说明：如果表达式求值为非 Null 且非空的字符串，则 IsPresent 函数返回 true。 此函数的逆函数被命名为 IsNullOrEmpty。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 |表达式 |要对其求值的表达式 |

**示例：** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
函数：IsString(Expression)

说明：如果表达式的值可为字符串类型，则 IsString 函数的值为 True。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **expression** |必需 |表达式 |要对其求值的表达式 |

---
### <a name="item"></a>项
函数：Item(attribute, index)

说明：Item 函数返回多值字符串/特性中的一个项。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| attribute |必需 |属性 |要搜索的多值特性。 |
| **index** |必需 |整数 | 多值字符串中某个项的索引|

**示例：** 
`Item([proxyAddresses], 1)` 返回多值特性中的第一个项。 不得使用索引 0。 

---
### <a name="join"></a>加入
函数：Join(separator, source1, source2, …)

说明：Join() 类似于 Append()，只不过它可以将多个源字符串值合并到单个字符串中，每个值由分隔符字符串分隔 。

如果其中一个源值是多值属性，那么该属性中的每个值都将联接在一起，由分隔符值分隔。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| separator  |必须 |String |用于在将源值连接为一个字符串时分隔源值的字符串。 如果不需要分隔符，则可以是 ""。 |
| **source1  … sourceN** |必选，次数可变 |字符串 |要联接在一起的字符串值。 |

---
### <a name="left"></a>Left
函数：Left(String,NumChars)

说明：Left 函数从字符串左侧起返回指定数量的字符。 如果 numChar = 0，则返回空字符串。
如果 numChar < 0，则返回输入字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的字符数比 numChars 中指定的数量少，则返回与该字符串相同的字符串（即，包含参数 1 中的所有字符）。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **字符串** |必需 |属性 | 要从中返回字符的字符串 |
| NumChars |必需 |整数 | 一个数字，用于指示要从字符串开头（左侧）返回的字符数|

**示例：** 
`Left("John Doe", 3)`

返回 “Joh”。

---
### <a name="mid"></a>Mid
函数：Mid(source, start, length)

说明：返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是属性的名称。 |
| **start** |必需 |integer |**source** 字符串中的索引，子字符串应从这里开始。 字符串中第一个字符的索引为 1，第二个字符的索引为 2，依此类推。 |
| **length** |必需 |integer |子字符串的长度。 如果长度超出 **source** 字符串，则函数将返回从 **start** 索引到 **source** 字符串末尾的子字符串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
函数：NormalizeDiacritics(source)

说明：需要一个字符串自变量。 返回字符串，但将任何标注字符替换为等效的非标注字符。 通常用于将包含标注字符（重音符号）的名字和姓氏转换为可用于各种用户标识符（例如用户主体名称、SAM 帐户名称和电子邮件地址）的合法值。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String | 通常是名字或姓氏属性。 |


| 带有音调符号的字符  | 规范化字符 | 带有音调符号的字符  | 规范化字符 | 
| --- | --- | --- | --- | 
| ä, à, â, ã, å, á, ą, ă | a | Ä, À, Â, Ã, Å, Á, Ą, Ă | A | 
| æ | ae | Æ | AE | 
| ç, č, ć | c | Ç, Č, Ć | C | 
| ď | d | Ď | D | 
| ë, è, é, ê, ę, ě, ė | e | Ë, È, É, Ê, Ę, Ě, Ė | E | 
| ğ | g | Ğ | G | 
| Ï, Î, Ì, Í, İ | I | ï, î, ì, í, ı | i | 
| ľ, ł | l |  Ł, Ľ | L | 
| ñ, ń, ň | n |  Ñ, Ń, Ň | N | 
| ö, ò, ő, õ, ô, ó | o |  Ö, Ò, Ő, Õ, Ô, Ó | O | 
| ø | oe |  Ø | OE | 
| ř | r |  Ř | R | 
| ß | ss | | | 
| š, ś, ș, ş | s |  Š, Ś, Ș, Ş | S | 
| ť, ț | t | Ť, Ț | T | 
| ü, ù, û, ú, ů, ű | u |  Ü, Ù, Û, Ú, Ů, Ű | U | 
| ÿ, ý | y | Ÿ, Ý | Y | 
| ź, ž, ż | z | Ź, Ž, Ż | Z | 


#### <a name="remove-diacritics-from-a-string"></a>从字符串中删除音调符号
示例：你需要将包含重音符号的字符替换为不包含重音符号的等效字符。

表达式：NormalizeDiacritics([givenName])

**示例输入/输出：** 

* **输入** (givenName)：“Zoë”
* **输出**：“Zoe”


---
### <a name="not"></a>Not
函数：Not(source)

说明：对 source 的布尔值取反。 如果 source 值为 True，则返回 False。 否则返回 True。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必需 |布尔型字符串 |预期的 **source** 值为“True”或“False”。 |

---
### <a name="now"></a>Now
函数：Now()

**描述：**  
Now 函数返回表示当前 UTC DateTime 的字符串，格式为 M/d/yyyy h:mm:ss tt。

**示例：** 
`Now()` <br>
返回的示例值：7/2/2021 3:33:38 PM

---
### <a name="numfromdate"></a>NumFromDate
函数：NumFromDate(value)

**说明：** NumFromDate 函数将日期/时间值转换为 Active Directory 格式，设置 [accountExpires](/windows/win32/adschema/a-accountexpires) 等特性时必须采用此格式。 使用此函数可将从云 HR 应用（例如 Workday 和 SuccessFactors）收到的日期/时间值转换为其等效的 AD 表示形式。 

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| value  |必须 | String | 采用受支持格式的日期/时间字符串。 有关支持的格式，请参阅 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**示例：**
* Workday 示例：假设你要将 Workday 中采用 2020-12-31-08:00 格式的 ContractEndDate 特性映射到 AD 中的 accountExpires 字段，那么你可以按下面的方式使用此函数并更改时区偏差，使之与你的区域设置相匹配  。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), " 23:59:59-08:00"))`

* SuccessFactors 示例：假设你要将 SuccessFactors 中采用 M/d/yyyy hh:mm:ss tt 格式的 endDate 特性映射到 AD 中的 accountExpires 字段，那么你可以按下面的方式使用此函数并更改时区偏差，使之与你的区域设置相匹配  。
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd")," 23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
函数：RemoveDuplicates(attribute)

说明：RemoveDuplicates 函数取多值字符串，并确保每个值唯一。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| attribute |必需 |多值特性 |将删除其中的重复值的多值特性|

示例：
`RemoveDuplicates([proxyAddresses])` 返回净化的 proxyAddress 特性，其中的所有重复值已删除。

---
### <a name="replace"></a>Replace
函数：Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

说明：以区分大小写的方式替换字符串中的值。 该函数的行为因提供的参数而异：

* 当提供了 **oldValue** 和 **replacementValue** 时：
  
  * 将 **source** 中出现的所有 **oldValue** 替换为 **replacementValue**
* 当提供了 **oldValue** 和 **template** 时：
  
  * 将 **template** 中出现的所有 **oldValue** 替换为 **source** 值
* 如果已提供 **regexPattern** 和 **replacementValue**：

  * 函数将 **regexPattern** 应用到 **source** 字符串，你可以使用 regex 组名称来构造 **replacementValue** 的字符串。
* 当提供 **regexPattern**、**regexGroupName**、**replacementValue** 时：
  
  * 函数将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为 **replacementValue**
* 如果已提供 **regexPattern**、**regexGroupName** 和 **replacementAttributeName**：
  
  * 如果 **source** 没有值，则返回 **source**
  * 如果 **source** 具有值，则函数会将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为与 **replacementAttributeName** 关联的值

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是 **source** 对象中的属性的名称。 |
| **oldValue** |可选 |字符串 |要在 **source** 或 **template** 中替换的值。 |
| **regexPattern** |可选 |字符串 |**source** 中要替换的值的正则表达式模式。 或者，当使用 **replacementPropertyName** 时，从 **replacementPropertyName** 中提取值的模式。 |
| **regexGroupName** |可选 |字符串 |**regexPattern** 中的组名称。 仅当使用 **replacementPropertyName** 时，才会从 **replacementPropertyName** 中以 **replacementValue** 的形式提取此组的值。 |
| **replacementValue** |可选 |字符串 |用于替换旧值的新值。 |
| **replacementAttributeName** |可选 |字符串 |用于替换值的属性的名称 |
| **template** |可选 |字符串 |当提供 **template** 值时，会在模板中查找 **oldValue** 并将其替换为 **source** 值。 |

#### <a name="replace-characters-using-a-regular-expression"></a>使用正则表达式替换字符
示例：你需要查找与正则表达式值匹配的字符并将其删除。

**表达式：** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**示例输入/输出：**

* **INPUT** (mailNickname: "john_doe72"
* 输出："72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
函数：SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

说明：需要至少两个自变量，这些自变量是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。 将返回找到的第一个唯一值。 如果所有值都已存在于目标中，则会托管该条目并在审核日志中记录原因。 可以提供的参数数目没有上限。


 - 这是一个顶级函数，不能嵌套。
 - 此函数不能应用到具有匹配优先级的属性。     
 - 此函数仅供用于创建条目。 将其与属性一起使用时，请将“应用映射”属性设置为“仅在创建对象期间”。
 - 目前只有“Workday 到 Azure Active Directory 的用户预配”和“SuccessFactors 到 Active Directory 的用户预配”支持此函数。 此函数不可用于其他预配应用程序。 


**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |需要至少 2 个，没有上限 |字符串 | 要评估的唯一值生成规则的列表。 |

#### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>为 userPrincipalName (UPN) 属性生成唯一值
示例：你需要根据用户的名字、中间名和姓氏为 UPN 特性生成值，并在将该值分配给 UPN 特性之前在目标 AD 目录中检查其唯一性。

**表达式：** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**："John.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 尚未存在于目录中
* **输出**："J.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 已存在于目录中
* **输出**："Jo.Smith@contoso.com"，如果上面的两个值已存在于目录中



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
函数：SingleAppRoleAssignment([appRoleAssignments])

说明：对于给定的应用程序，从向一个用户分配的所有 appRoleAssignments 列表中返回单个 appRoleAssignment。 需要此函数才能将 appRoleAssignments 对象转换为单个角色名称字符串。 请注意，最佳做法是确保每次只向一个用户分配一个 appRoleAssignment，如果分配了多个角色，则返回的角色字符串可能是不可预测的。 

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必须 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="split"></a>拆分
函数：Split(source, delimiter)

说明：使用指定的分隔符将字符串拆分为多值数组。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |
| **delimiter** |必须 |String |指定将用来拆分字符串的字符（示例：“,”） |

#### <a name="split-a-string-into-a-multi-valued-array"></a>将字符串拆分为多值数组
示例：你需要提取一个以逗号分隔的字符串列表，并将这些字符串拆分为可插入到多值特性（例如 Salesforce 的 PermissionSets 特性）的数组。 在此示例中，Azure AD 中的 extensionAttribute5 中填充了一个权限集列表。

表达式：Split([extensionAttribute5], ",")

**示例输入/输出：** 

* **输入** (extensionAttribute5)："PermissionSetOne, PermissionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
函数：StripSpaces(source)

说明：从源字符串中删除所有空格 (" ") 字符。

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |

---
### <a name="switch"></a>开关
函数：Switch(source, defaultValue, key1, value1, key2, value2, …)

说明：当源值匹配某个键时，返回该键的值   。 当 **source** 值未与任何 key 匹配时，则返回 **defaultValue**。  **Key** 和 **value** 参数必须始终成对出现。 该函数始终需要偶数个参数。 不应该对 manager 等引用特性使用该函数。 

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |
| **defaultValue** |可选 |字符串 |当 source 不匹配任何 key 时使用的默认值。 可以是空字符串 ("")。 |
| key  |必须 |String |用来比较 **source** 值的 **key**。 |
| value  |必须 |String |与该 key 匹配的 **source** 的替换值。 |

#### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根据预定义的选项集替换值
示例：你需要根据 Azure AD 中存储的状态代码来定义用户的时区。 如果状态代码与任何预定义选项都不匹配，则使用默认值“澳大利亚/悉尼”。

表达式： 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**示例输入/输出：**

* **输入** (state)：“QLD”
* **输出**：“澳大利亚/布里斯班”


---
### <a name="tolower"></a>ToLower
函数：ToLower(source, culture)

说明：取源字符串值，并使用指定的区域性规则将其转换为小写。 如果没有指定任何区域性信息，则使用固定区域性。

如果要将目标系统中的现有值设置为小写，请[更新目标应用程序的架构](./customize-application-attributes.md#editing-the-list-of-supported-attributes)，并将所需属性的属性 caseExact 设置为“true”。 

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称 |
| **culture** |可选 |字符串 |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2，其中 languagecode2 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

#### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>将生成的 userPrincipalName (UPN) 值转换为小写
示例：你要通过连接 PreferredFirstName 和 PreferredLastName 源字段并将所有字符转换为小写，来生成 UPN 值。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**：“john.smith@contoso.com”


---
### <a name="toupper"></a>ToUpper
函数：ToUpper(source, culture)

说明：取源字符串值，并使用指定的区域性规则将其转换为大写。 如果没有指定任何区域性信息，则使用固定区域性。

如果要将目标系统中的现有值设置为大写，请[更新目标应用程序的架构](./customize-application-attributes.md#editing-the-list-of-supported-attributes)，并将所需属性的属性 caseExact 设置为“true”。 

**参数：** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| **culture** |可选 |字符串 |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2，其中 languagecode2 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="word"></a>Word
函数：Word(String,WordNumber,Delimiters)

说明：Word 函数根据描述要使用的分隔符以及要返回的单词数的参数，返回字符串中包含的单词。 字符串中的字符由分隔符中其中一个字符分隔的每个字符串被标识为单词：

如果数字 < 1，则返回空字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的单词少于应返回数字或字符串不包含由分隔符标识的任何单词，则返回空字符串。

**参数：** 

| 名称 | 必选/重复 | 类型 | 注释 |
| --- | --- | --- | --- |
| **字符串** |必需 |多值特性 |要从中返回单词的字符串。|
| WordNumber |必需 | 整数 | 用来标识应返回的单词个数的数字|
| delimiters |必须 |String| 一个字符串，表示应该用来标识单词的分隔符|

**示例：** 
`Word("The quick brown fox",3," ")`

返回“brown”。

`Word("This,string!has&many separators",3,",!&#")`

返回“has”。

---

## <a name="examples"></a>示例
本部分提供更多的表达式函数用法示例。 

### <a name="strip-known-domain-name"></a>删除已知域名
你需要从用户的电子邮件中去除已知域名，以获取用户名。 例如，如果域为“contoso.com”，则可以使用以下表达式：

表达式： 
`Replace([mail], "@contoso.com", , ,"", ,)`

**示例输入/输出：** 

* **输入** (mail)：“john.doe@contoso.com”
* **输出**：“john.doe”


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>通过连接名字和姓氏部分来生成用户别名
需要使用用户名字的前 3 个字母和用户姓氏的前 5 个字母来生成用户别名。

表达式： 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**示例输入/输出：** 

* **输入** (givenName)：“John”
* **输入** (surname)：“Doe”
* **输出**：“JohDoe”


## <a name="related-articles"></a>相关文章
* [在 SaaS 应用中自动预配和取消预配用户](../app-provisioning/user-provisioning.md)
* [为用户预配自定义属性映射](../app-provisioning/customize-application-attributes.md)
* [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帐户预配通知](../app-provisioning/user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
