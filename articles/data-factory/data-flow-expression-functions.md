---
title: 映射数据流中的表达式函数
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解映射数据流中的表达式函数。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dc0bdf8f8d40b23f4f6e8338ab6b608278428149
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060264"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>映射数据流中的数据转换表达式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

本文详述了映射数据流中 Azure 数据工厂和 Azure Synapse Analytics 支持的表达式和函数。


## <a name="expression-functions"></a>表达式函数

在数据工厂和 Synapse 管道中，使用映射数据流功能的表达式语言来配置数据转换。

| 表达式函数 | 任务 |
|-----|-----|
| [abs](data-flow-expression-functions.md#abs) | 数字的绝对值。  |
| [acos](data-flow-expression-functions.md#acos) | 计算反余弦值。  |
| [add](data-flow-expression-functions.md#add) | 添加一对字符串或数字。 将日期添加到日期数。 将持续时间添加到时间戳。 将类似类型的数组追加到另一个数组。 与 + 运算符相同。  |
| [addDays](data-flow-expression-functions.md#addDays) | 将日期添加到日期或时间戳。 与日期的 + 运算符相同。  |
| [addMonths](data-flow-expression-functions.md#addMonths) | 将月份添加到日期或时间戳。 可以选择性地传递时区。  |
| [and](data-flow-expression-functions.md#and) | “逻辑与”运算符。 与 && 相同。  |
| [asin](data-flow-expression-functions.md#asin) | 计算反正弦值。  |
| [atan](data-flow-expression-functions.md#atan) | 计算反正切值。  |
| [atan2](data-flow-expression-functions.md#atan2) | 返回平面正 X 轴与给定坐标点之间的弧角。  |
| [between](data-flow-expression-functions.md#between) | 检查第一个值是否在两个其他值之间（含这两个值）。 可以比较数字、字符串和日期值   |
| [bitwiseAnd](data-flow-expression-functions.md#bitwiseAnd) | 跨整数类型的位和运算符。 与 & 运算符相同   |
| [bitwiseOr](data-flow-expression-functions.md#bitwiseOr) | 跨整数类型的位或运算符。 与 \| 运算符相同  |
| [bitwiseXor](data-flow-expression-functions.md#bitwiseXor) | 跨整数类型的位或运算符。 与 \| 运算符相同  |
| [blake2b](data-flow-expression-functions.md#blake2b) | 如果给定位长只能是 8 和 512 之间的 8 的倍数，计算具有不同基元数据类型的列集的 Blake2 摘要。 它可用于计算行的指纹   |
| [blake2bBinary](data-flow-expression-functions.md#blake2bBinary) | 如果给定位长只能是 8 和 512 之间的 8 的倍数，计算具有不同基元数据类型的列集的 Blake2 摘要。 它可用于计算行的指纹   |
| [case](data-flow-expression-functions.md#case) | 根据备用条件应用一个值或另一个值。 如果输入的数字是偶数，对于最后一个条件，另一个值将默认为 NULL。  |
| [cbrt](data-flow-expression-functions.md#cbrt) | 计算一个数的立方根。  |
| [ceil](data-flow-expression-functions.md#ceil) | 返回不小于该数字的最小整数。  |
| [coalesce](data-flow-expression-functions.md#coalesce) | 返回一组输入中的第一个非 NULL 值。 所有输入应属于同一类型。  |
| columnNames[](data-flow-expression-functions.md#columnNames) | 获取流的所有输出列的名称。 可以将可选流名称作为第二个参数传递。  |
| [列](data-flow-expression-functions.md#columns) | 获取流的所有输出列的值。 可以将可选流名称作为第二个参数传递。   |
| [compare](data-flow-expression-functions.md#compare) | 比较同一类型的两个值。 如果 value1 < value2，返回负整数；如果 value1 == value2，返回 0；如果 value1 > value2，则返回正值。  |
| [concat](data-flow-expression-functions.md#concat) | 将字符串的可变数字连接在一起。 与包含字符串的 + 运算符相同。  |
| [concatWS](data-flow-expression-functions.md#concatWS) | 使用分隔符将字符串的可变数字连接在一起。 第一个参数是分隔符。  |
| [cos](data-flow-expression-functions.md#cos) | 计算余弦值。  |
| [cosh](data-flow-expression-functions.md#cosh) | 计算某个值的双曲余弦值。  |
| [crc32](data-flow-expression-functions.md#crc32) | 根据位长度计算不同基元数据类型的列集的 CRC32 哈希，值只能为 0(256)、224、256、384、512。 它可用于计算行的指纹。  |
| [currentDate](data-flow-expression-functions.md#currentDate) | 当此作业开始运行时获取当前日期。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). |
| [currentTimestamp](data-flow-expression-functions.md#currentTimestamp) | 当作业开始运行时，获取采用当地时区的当前时间戳。  |
| [currentUTC](data-flow-expression-functions.md#currentUTC) | 获取 UTC 格式的当前时间戳。 如果希望在与群集时区不同的时区中解释当前时间，则可以采用“GMT”、“PST”、“UTC”或“America/Cayman”格式传递一个可选时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 若要将 UTC 时间转换为其他时区，请使用 `fromUTC()`。  |
| [dayOfMonth](data-flow-expression-functions.md#dayOfMonth) | 获取给定日期的月份日期。  |
| [dayOfWeek](data-flow-expression-functions.md#dayOfWeek) | 获取给定日期的星期日期。 1 - 星期日，2 - 星期一…7 - 星期六。  |
| [dayOfYear](data-flow-expression-functions.md#dayOfYear) | 获取给定日期的年份日期。  |
| [days](data-flow-expression-functions.md#days) | 天数的持续时间（以毫秒为单位）。  |
| [degrees](data-flow-expression-functions.md#degrees) | 将弧度转换为度。  |
| [divide](data-flow-expression-functions.md#divide) | 将数字对相除。 与 `/` 运算符相同。  |
| [dropLeft](data-flow-expression-functions.md#dropLeft) | 删除字符串左侧任意数目的字符。 如果请求删除的字符数超过了该字符串的长度，则返回空字符串。|
| [dropRight](data-flow-expression-functions.md#dropRight) | 删除字符串右侧任意数目的字符。 如果请求删除的字符数超过了该字符串的长度，则返回空字符串。|
| [endsWith](data-flow-expression-functions.md#endsWith) | 检查字符串是否以提供的字符串结尾。  |
| [equals](data-flow-expression-functions.md#equals) | “等于”比较运算符。 与 == 运算符相同。  |
| [equalsIgnoreCase](data-flow-expression-functions.md#equalsIgnoreCase) | 忽略大小写的“等于”比较运算符。 与 <=> 运算符相同。  |
| escape | 根据格式转义字符串。 可接受的格式的文本值为 "json"、"xml"、"ecmascript"、"html"、"java"。|
| [expr](data-flow-expression-functions.md#expr) | 从字符串生成表达式。 这与在非文本窗体中编写此表达式的效果一样。 这可以用来以字符串表示形式传递参数。|
| [阶乘](data-flow-expression-functions.md#factorial) | 计算一个数的阶乘。  |
| [false](data-flow-expression-functions.md#false) | 始终返回 false 值。 如果存在名为“false”的列，则使用函数 `syntax(false())`。  |
| [floor](data-flow-expression-functions.md#floor) | 返回不大于该数字的最大整数。  |
| [fromBase64](data-flow-expression-functions.md#fromBase64) | 对给定的 base64 编码的字符串进行解码。|
| [fromUTC](data-flow-expression-functions.md#fromUTC) | 转换为 UTC 格式的时间戳。 可以选择以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [greater](data-flow-expression-functions.md#greater) | “大于”比较运算符。 与 > 运算符相同。  |
| [greaterOrEqual](data-flow-expression-functions.md#greaterOrEqual) | “大于等于”比较运算符。 与 >= 运算符相同。  |
| [greatest](data-flow-expression-functions.md#greatest) | 返回输入值列表中的最大值，跳过 NULL 值。 如果所有输入均为 NULL，则返回 NULL。  |
| [hasColumn](data-flow-expression-functions.md#hasColumn) | 在流中按名称检查列值。 可以将可选流名称作为第二个参数传递。 设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  |
| hour  | 获取时间戳的小时值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [小时数](data-flow-expression-functions.md#hours) | 小时数的持续时间（以毫秒为单位）。  |
| [iif](data-flow-expression-functions.md#iif) | 根据条件应用一个值或另一个值。 如果未指定另一个值，则将它视为 NULL。 这两个值必须兼容（数字、字符串…）。  |
| [iifNull](data-flow-expression-functions.md#iifNull) | 检查第一个参数是否为 NULL。 如果不为 NULL，则返回第一个参数。 如果为 NULL，则返回第二个参数。 如果指定了三个参数，则行为与 iif(isNull(value1), value2, value3) 相同，如果第一个值不为 NULL，则返回第三个参数。  |
| [initCap](data-flow-expression-functions.md#initCap) | 将每个单词的第一个字母转换为大写。 通过空格分隔符识别单词。  |
| [instr](data-flow-expression-functions.md#instr) | 在字符串中查找子字符串的位置（从 1 开始）。 如果未找到，则返回 0。  |
| [isDelete](data-flow-expression-functions.md#isDelete) | 检查该行是否标记为删除。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isError](data-flow-expression-functions.md#isError) | 检查该行是否标记为错误。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isIgnore](data-flow-expression-functions.md#isIgnore) | 检查该行是否标记为忽略。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isInsert](data-flow-expression-functions.md#isInsert) | 检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isMatch](data-flow-expression-functions.md#isMatch) | 检查查找时是否匹配该行。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isNull](data-flow-expression-functions.md#isNull) | 检查值是否为 NULL。  |
| [isUpdate](data-flow-expression-functions.md#isUpdate) | 检查该行是否标记为更新。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [isUpsert](data-flow-expression-functions.md#isUpsert) | 检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  |
| [jaroWinkler](data-flow-expression-functions.md#jaroWinkler) | 获取两个字符串之间的 JaroWinkler 距离。 |
| [lastDayOfMonth](data-flow-expression-functions.md#lastDayOfMonth) | 获取给定日期的最后一个月份日期。  |
| [least](data-flow-expression-functions.md#least) | “小于等于”比较运算符。 与 <= 运算符相同。  |
| [left](data-flow-expression-functions.md#left) | 从索引 1 处开始提取包含字符数的子字符串。 与 SUBSTRING(str, 1, n) 相同。  |
| [length](data-flow-expression-functions.md#length) | 返回字符串的长度。  |
| [lesser](data-flow-expression-functions.md#lesser) | “小于”比较运算符。 与 < 运算符相同。  |
| [lesserOrEqual](data-flow-expression-functions.md#lesserOrEqual) | “小于等于”比较运算符。 与 <= 运算符相同。  |
| [levenshtein](data-flow-expression-functions.md#levenshtein) | 获取两个字符串之间的 levenshtein 距离。  |
| [like](data-flow-expression-functions.md#like) | 模式是按原义匹配的字符串。 以下特殊符号除外：_ 与输入中的任何一个字符匹配（类似于 ```posix``` 正则表达式中的 .）|
| [locate](data-flow-expression-functions.md#locate) | 从特定的位置开始，在字符串中查找子字符串的位置（从 1 开始）。 如果省略位置，则视为从字符串开头查找。 如果未找到，则返回 0。  |
| [log](data-flow-expression-functions.md#log) | 计算对数值。 可以提供可选的底，否则使用欧拉数。  |
| [log10](data-flow-expression-functions.md#log10) | 以 10 为底计算对数值。  |
| [lower](data-flow-expression-functions.md#lower) | 将字符串小写。  |
| [lpad](data-flow-expression-functions.md#lpad) | 在左侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于特定长度，则进行剪裁。  |
| [ltrim](data-flow-expression-functions.md#ltrim) | 在左侧裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  |
| [md5](data-flow-expression-functions.md#md5) | 计算不同基元数据类型的列集的 MD5 摘要，并返回 32 字符十六进制字符串。 它可用于计算行的指纹。  |
| millisecond  | 获取日期的毫秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| milliseconds | 毫秒数的持续时间（以毫秒为单位）。  |
| [minus](data-flow-expression-functions.md#minus) | 减去数字。 从日期数中减去日期。 从时间戳中减去持续时间。 减去两个时间戳以获取差异（以毫秒为单位）。 与 - 运算符相同。  |
| minute  | 获取时间戳的分钟值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [minutes](data-flow-expression-functions.md#minutes) | 分钟数的持续时间（以毫秒为单位）。  |
| [mod](data-flow-expression-functions.md#mod) | 将数字对取模。 与 % 运算符相同。  |
| month  | 获取日期或时间戳的月份值。  |
| [monthsBetween](data-flow-expression-functions.md#monthsBetween) | 获取两个日期之间的月数。 可以舍入计算。可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [multiply](data-flow-expression-functions.md#multiply) | 将数字对相乘。 与 * 运算符相同。  |
| [negate](data-flow-expression-functions.md#negate) | 对数字求反。 将正数转换为负数，或反之。  |
| [nextSequence](data-flow-expression-functions.md#nextSequence) | 返回下一个唯一序列。 该数字仅在分区中是连续的，带有 partitionId 前缀。  |
| [规范化](data-flow-expression-functions.md#normalize) | 规范化字符串值以分隔重音的 unicode 字符。  |
| [not](data-flow-expression-functions.md#not) | 逻辑求反运算符。  |
| [notEquals](data-flow-expression-functions.md#notEquals) | “不等于”比较运算符。 与 != 运算符相同。  |
| [notNull](data-flow-expression-functions.md#notNull) | 检查值是否不为 NULL。  |
| [null](data-flow-expression-functions.md#null) | 返回 NULL 值。 如果存在名为“null”的列，则使用函数 `syntax(null())`。 使用该函数的任何操作将导致 NULL。  |
| [or](data-flow-expression-functions.md#or) | “逻辑或”运算符。 与 \|\| 相同。  |
| [pMod](data-flow-expression-functions.md#pMod) | 将数字对正数取模。  |
| [partitionId](data-flow-expression-functions.md#partitionId) | 返回输入行所在的当前分区 ID。  |
| [power](data-flow-expression-functions.md#power) | 以一个数为底、另一数为幂求值。  |
| [radians](data-flow-expression-functions.md#radians) | 将度数转换成弧度|
| [random](data-flow-expression-functions.md#random) | 返回给定分区内的可选种子的随机数。 种子应为固定值，与 partitionId 一起用于生成随机值   |
| [regexExtract](data-flow-expression-functions.md#regexExtract) | 提取给定正则表达式模式的匹配子字符串。 最后一个参数标识匹配组，如果省略，则默认为 1。 使用 `<regex>`（反引号）匹配字符串且不进行转义。  |
| [regexMatch](data-flow-expression-functions.md#regexMatch) | 检查字符串是否与给定的正则表达式模式相匹配。 使用 `<regex>`（反引号）匹配字符串且不进行转义。  |
| [regexReplace](data-flow-expression-functions.md#regexReplace) | 将给定字符串中出现的正则表达式模式全部替换为另一个子字符串。使用 `<regex>`（反引号）匹配字符串且不进行转义。  |
| [regexSplit](data-flow-expression-functions.md#regexSplit) | 基于分隔符和正则表达式拆分字符串，并返回字符串数组。  |
| [replace](data-flow-expression-functions.md#replace) | 将给定字符串中出现的一个子字符串全部替换为另一个子字符串。 如果省略最后一个参数，则默认为空字符串。  |
| [reverse](data-flow-expression-functions.md#reverse) | 反转字符串。  |
| [right](data-flow-expression-functions.md#right) | 从右侧提取包含字符数的子字符串。 与 SUBSTRING(str, LENGTH(str) - n, n) 相同。  |
| [rlike](data-flow-expression-functions.md#rlike) | 检查字符串是否与给定的正则表达式模式相匹配。  |
| [round](data-flow-expression-functions.md#round) | 根据可选的小数位数和可选的舍入模式将数字四舍五入。 如果省略小数位数，则默认为 0。 如果省略模式，则默认为 ROUND_HALF_UP(5)。 用于舍入的值包括|
| [rpad](data-flow-expression-functions.md#rpad) | 在右侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于特定长度，则进行剪裁。  |
| [rtrim](data-flow-expression-functions.md#rtrim) | 在右侧裁剪掉尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  |
| second  | 获取日期的秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [seconds](data-flow-expression-functions.md#seconds) | 秒数的持续时间（以毫秒为单位）。  |
| [sha1](data-flow-expression-functions.md#sha1) | 计算不同基元数据类型的列集的 SHA-1 摘要，并返回 40 字符十六进制字符串。 它可用于计算行的指纹。  |
| [sha2](data-flow-expression-functions.md#sha2) | 根据位长度计算不同基元数据类型的列集的 SHA-2 摘要，值只能为 0(256)、224、256、384、512。 它可用于计算行的指纹。  |
| [sin](data-flow-expression-functions.md#sin) | 计算正弦值。  |
| [sinh](data-flow-expression-functions.md#sinh) | 计算双曲正弦值。  |
| [soundex](data-flow-expression-functions.md#soundex) | 获取字符串的 ```soundex``` 代码。  |
| [split](data-flow-expression-functions.md#split) | 基于分隔符拆分字符串，并返回字符串数组。  |
| [sqrt](data-flow-expression-functions.md#sqrt) | 计算一个数的平方根。  |
| [startsWith](data-flow-expression-functions.md#startsWith) | 检查字符串是否以提供的字符串开头。  |
| [subDays](data-flow-expression-functions.md#subDays) | 从日期或时间戳中减去天数。 与日期的 - 运算符相同。  |
| [subMonths](data-flow-expression-functions.md#subMonths) | 从日期或时间戳中减去月份。  |
| [substring](data-flow-expression-functions.md#substring) | 从某个位置提取特定长度的子字符串。 位置从 1 开始。 如果省略长度，则默认为字符串的末尾。  |
| [tan](data-flow-expression-functions.md#tan) | 计算正切值。  |
| [tanh](data-flow-expression-functions.md#tanh) | 计算双曲正切值。  |
| [translate](data-flow-expression-functions.md#translate) | 将字符串中的一组字符替换为另一组字符。 对字符进行 1 对 1 的替换.  |
| [trim](data-flow-expression-functions.md#trim) | 裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  |
| true | 始终返回 true 值。 如果存在名为“true”的列，则使用函数 `syntax(true())`。  |
| [typeMatch](data-flow-expression-functions.md#typeMatch) | 匹配列的类型。 只可在模式表达式中使用。number 匹配短整型数、整数、长整型数、双精度数、浮点数或小数，integral 匹配短整型数、整数、长整型数，fractional 匹配双精度数、浮点数、小数，datetime 匹配日期或时间戳类型。  |
| [unescape](data-flow-expression-functions.md#unescape) | 根据格式取消转义字符串。 可接受的格式的文本值为 "json"、"xml"、"ecmascript"、"html"、"java"。|
| [upper](data-flow-expression-functions.md#upper) | 将字符串大写。  |
| [uuid](data-flow-expression-functions.md#uuid) | 返回生成的 UUID。  |
| [weekOfYear](data-flow-expression-functions.md#weekOfYear) | 获取给定日期的年份周次。  |
| [weeks](data-flow-expression-functions.md#weeks) | 周数的持续时间（以毫秒为单位）。  |
| [xor](data-flow-expression-functions.md#xor) | 逻辑 XOR 运算符。 与 ^ 运算符相同。  |
| [year](data-flow-expression-functions.md#year) | 获取日期的年份值。  |
|||

## <a name="aggregate-functions"></a>聚合函数
以下函数仅可用于聚合、透视、逆透视和窗口转换。

| 聚合函数 | 任务 |
|----|----|
| [approxDistinctCount](data-flow-expression-functions.md#approxDistinctCount) | 获取列的相异值的近似聚合计数。 可选的第二个参数用于控制估计误差。|
| [avg](data-flow-expression-functions.md#avg) | 获取列值的平均值。  |
| [avgIf](data-flow-expression-functions.md#avgIf) | 根据条件获取列值的平均值。  |
| [collect](data-flow-expression-functions.md#collect) | 将聚合组中表达式的所有值收集到一个数组中。 在此过程中，可以收集结构并将其转换为备用结构。 项目数将等于该组中的行数，并且可以包含 NULL 值。 收集的项目数应较小。  |
| [计数](data-flow-expression-functions.md#count) | 获取值的聚合计数。 如果指定了可选的列，则忽略计数中的 NULL 值。  |
| [countDistinct](data-flow-expression-functions.md#countDistinct) | 获取列集的非重复值的聚合计数。  |
| [countIf](data-flow-expression-functions.md#countIf) | 根据条件获取值的聚合计数。 如果指定了可选的列，则忽略计数中的 NULL 值。  |
| [covariancePopulation](data-flow-expression-functions.md#covariancePopulation) | 获取两个列之间的总体协方差。  |
| [covariancePopulationIf](data-flow-expression-functions.md#covariancePopulationIf) | 根据条件获取两个列的总体协方差。  |
| [covarianceSample](data-flow-expression-functions.md#covarianceSample) | 获取两个列的样本协方差。  |
| [covarianceSampleIf](data-flow-expression-functions.md#covarianceSampleIf) | 根据条件获取两个列的样本协方差。  |
| [first](data-flow-expression-functions.md#first) | 获取列组的第一个值。 如果省略第二个参数 ignoreNulls，则假定为 false。  |
| [isDistinct](data-flow-expression-functions.md#isDistinct) | 确定一列或一组列是否是非重复值。 它不将 null 计为非重复值 |
| [kurtosis](data-flow-expression-functions.md#kurtosis) | 获取列的峰度。  |
| [kurtosisIf](data-flow-expression-functions.md#kurtosisIf) | 根据条件获取列的峰度。  |
| [last](data-flow-expression-functions.md#last) | 获取列组的最后一个值。 如果省略第二个参数 ignoreNulls，则假定为 false。  |
| [max](data-flow-expression-functions.md#max) | 获取列的最大值。  |
| [maxIf](data-flow-expression-functions.md#maxIf) | 根据条件获取列的最大值。  |
| [平均值](data-flow-expression-functions.md#mean) | 获取列值的中间值。 与 AVG 相同。  |
| [meanIf](data-flow-expression-functions.md#meanIf) | 根据条件获取列值的中间值。 与 avgIf 相同。  |
| [min](data-flow-expression-functions.md#min) | 获取列的最小值。  |
| [minIf](data-flow-expression-functions.md#minIf) | 根据条件获取列的最小值。  |
| [skewness](data-flow-expression-functions.md#skewness) | 获取列的偏度。  |
| [skewnessIf](data-flow-expression-functions.md#skewnessIf) | 根据条件获取列的偏度。  |
| [stddev](data-flow-expression-functions.md#stddev) | 获取列的标准偏差。  |
| [stddevIf](data-flow-expression-functions.md#stddevIf) | 根据条件获取列的标准偏差。  |
| [stddevPopulation](data-flow-expression-functions.md#stddevPopulation) | 获取列的总体标准偏差。  |
| [stddevPopulationIf](data-flow-expression-functions.md#stddevPopulationIf) | 根据条件获取列的总体标准偏差。  |
| [stddevSample](data-flow-expression-functions.md#stddevSample) | 获取列的样本标准偏差。  |
| [stddevSampleIf](data-flow-expression-functions.md#stddevSampleIf) | 根据条件获取列的样本标准偏差。  |
| [sum](data-flow-expression-functions.md#sum) | 获取数字列的聚合总数。  |
| [sumDistinct](data-flow-expression-functions.md#sumDistinct) | 获取数字列的非重复值的聚合总数。  |
| [sumDistinctIf](data-flow-expression-functions.md#sumDistinctIf) | 根据条件获取数字列的聚合总数。 条件可以基于任何列。  |
| [sumIf](data-flow-expression-functions.md#sumIf) | 根据条件获取数字列的聚合总数。 条件可以基于任何列。  |
| [variance](data-flow-expression-functions.md#variance) | 获取列的方差。  |
| [varianceIf](data-flow-expression-functions.md#varianceIf) | 根据条件获取列的方差。  |
| [variancePopulation](data-flow-expression-functions.md#variancePopulation) | 获取列的总体方差。  |
| [variancePopulationIf](data-flow-expression-functions.md#variancePopulationIf) | 根据条件获取列的总体方差。  |
| [varianceSample](data-flow-expression-functions.md#varianceSample) | 获取列的无偏方差。  |
| [varianceSampleIf](data-flow-expression-functions.md#varianceSampleIf) | 根据条件获取列的无偏方差。  |
|||

## <a name="array-functions"></a>数组函数
数组函数对属于数组的数据结构执行转换。 其中包括用于对数组元素和索引进行寻址的特殊关键字：

* ```#acc``` 表示在减少数组时希望包含在单个输出中的值
* ```#index``` 表示当前的数组索引以及数组索引编号 ```#index2, #index3 ...```
* ```#item``` 表示数组中的当前元素值

| 数组函数 | 任务 |
|----|----|
| [array](data-flow-expression-functions.md#array) | 创建项的数组。 所有项应属于同一类型。 如果未指定任何项，则默认值为空字符串数组。 与 [] 创建运算符相同。  |
| [at](data-flow-expression-functions.md#at) | 查找数组索引处的元素。 索引从 1 开始。 如果索引超出界限，则返回 null 值。 在给定了键的情况下，在映射中查找值。 如果未找到该键，则返回 null。|
| [contains](data-flow-expression-functions.md#contains) | 如果所提供的数组中的任何元素在提供的谓词中计算结果为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素作为 #item。  |
| [distinct](data-flow-expression-functions.md#distinct) | 返回数组中的相异项集。|
| [except](data-flow-expression-functions.md#except) | 从另一组删除重复项中返回一个数组的差异集。|
| [filter](data-flow-expression-functions.md#filter) | 筛选出数组中不满足所提供谓词的元素。 Filter 需要引用谓词函数中的一个元素作为 #item。  |
| [find](data-flow-expression-functions.md#find) | 查找数组中与条件相匹配的第一项。 它采用筛选器函数，你可以在其中将数组中的项作为 #item 进行寻址。 对于深层嵌套的映射，可使用 #item_n(#item_1, #item_2...) 表示法引用父映射。  |
| [flatten](data-flow-expression-functions.md#flatten) | 将一个或多个数组平展成单个数组。 原子项数组将按原样返回。 最后一个参数是可选的，默认为 false，表示以递归方式平展多个层。|
| [in](data-flow-expression-functions.md#in) | 检查某个项是否在数组中。  |
| [intersect](data-flow-expression-functions.md#intersect) | 返回 2 个数组中不同项的交集。|
| [map](data-flow-expression-functions.md#map) | 使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用表达式函数中的一个元素作为 #item。  |
| [mapIf](data-flow-expression-functions.md#mapIf) | 有条件地将数组映射到长度相同或更短的另一个数组。 这些值可以是任何数据类型，包括 structTypes。 它采用一个映射函数，在其中可以将数组中的项作为 #item 进行寻址，将当前索引作为 #index 进行寻址。 对于深层嵌套的映射，可使用 ``#item_[n](#item_1, #index_1...)`` 表示法引用父映射。|
| [mapIndex](data-flow-expression-functions.md#mapIndex) | 使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用表达式函数中的一个元素作为 #item，并需要引用元素索引作为 #index。  |
| [mapLoop](data-flow-expression-functions.md#mapLoop) | 从 1 到某个长度进行循环，以创建具有该长度的数组。 它采用一个映射函数，在其中可以将数组中的索引作为 #index 进行寻址。 对于深层嵌套的映射，可使用 #index_n(#index_1, #index_2...) 表示法引用父映射。|
| [reduce](data-flow-expression-functions.md#reduce) | 累积数组中的元素。 Reduce 需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且需要在第二个表达式函数中使用生成值作为 #result。  |
| [大小](data-flow-expression-functions.md#size) | 查找数组或映射类型的大小  |
| [slice](data-flow-expression-functions.md#slice) | 从位置提取数组的子集。 位置从 1 开始。 如果省略长度，则默认为字符串的末尾。  |
| [sort](data-flow-expression-functions.md#sort) | 使用提供的谓词函数对数组进行排序。 Sort 需要引用表达式函数中的两个连续元素作为 #item1 和 #item2。  |
| [unfold](data-flow-expression-functions.md#unfold) | 将数组展开为一组行并在每行中重复剩余列的值。|
| [union](data-flow-expression-functions.md#union) | 返回 2 个数组中不同项的并集。|
|||

## <a name="cached-lookup-functions"></a>缓存的 lookup 函数
只有在包含缓存的接收器的情况下使用缓存的查找时，以下函数才可用。

| 缓存的查找函数 | 任务 |
|----|----|
| [lookup](data-flow-expression-functions.md#lookup) | 使用与缓存接收器中的键匹配的指定键查找缓存接收器中的第一行。|
| [mlookup](data-flow-expression-functions.md#mlookup) | 使用与缓存接收器中的键匹配的指定键查找缓存接收器中的所有匹配行。|
| [output](data-flow-expression-functions.md#output) | 返回缓存接收器结果的第一行 |
| [outputs](data-flow-expression-functions.md#outputs) | 返回缓存接收器结果的整个输出行集 |
|||

## <a name="conversion-functions"></a>转换函数

转换函数用于转换数据和数据类型测试

| 转换函数 | 任务 |
|----|----|
| [isBitSet](data-flow-expression-functions.md#isBitSet) | 检查是否在此位集中设置了位的位置 |
| [setBitSet](data-flow-expression-functions.md#setBitSet) | 在此位集中设置位的位置 |
| [isBoolean](data-flow-expression-functions.md#isBoolean) | 根据 ``toBoolean()`` 规则检查 string 值是否是 boolean 值|
| [isByte](data-flow-expression-functions.md#isByte) | 根据 ``toByte()`` 规则检查 string 值是否是给定可选格式的 byte 值|
| [isDate](data-flow-expression-functions.md#isDate) | 使用可选输入日期格式检查输入日期字符串是否为日期。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略输入日期格式，则默认格式为 ``yyyy-[M]M-[d]d``。 接受的格式为 ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``|
| [isShort](data-flow-expression-functions.md#isShort) | 根据 ``toShort()`` 规则检查 string 值是否是给定可选格式的 short 值|
| [isInteger](data-flow-expression-functions.md#isInteger) | 根据 ``toInteger()`` 规则检查 string 值是否是给定可选格式的 integer 值|
| [isLong](data-flow-expression-functions.md#isLong) | 根据 ``toLong()`` 规则检查 string 值是否是给定可选格式的 long 值|
| [isNan](data-flow-expression-functions.md#isNan) | 检查该值是否不是数字。|
| [isFloat](data-flow-expression-functions.md#isFloat) | 根据 ``toFloat()`` 规则检查 string 值是否是给定可选格式的 float 值|
| [isDouble](data-flow-expression-functions.md#isDouble) | 根据 ``toDouble()`` 规则检查 string 值是否是给定可选格式的 double 值|
| [isDecimal](data-flow-expression-functions.md#isDecimal) | 根据 ``toDecimal()`` 规则检查 string 值是否是给定可选格式的 decimal 值|
| [isTimestamp](data-flow-expression-functions.md#isTimestamp) | 使用可选输入时间戳格式检查输入日期字符串是否为时间戳。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略时间戳，则使用默认模式 ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 时间戳最多支持毫秒准确度，值为 999。有关可用格式，请参阅 Java 的 SimpleDateFormat。|
| [toBase64](data-flow-expression-functions.md#toBase64) | 以 base64 编码给定字符串。  |
| [toBinary](data-flow-expression-functions.md#toBinary) | 将任何数字/日期/时间戳/字符串转换为二进制表示形式。  |
| [toBoolean](data-flow-expression-functions.md#toBoolean) | 可将 ('t', 'true', 'y', 'yes', '1') 值转换为 true，将 ('f', 'false', 'n', 'no', '0') 值转换为 false，将其他任何值转换为 NULL。  |
| [toByte](data-flow-expression-functions.md#toByte) | 将任何数字或字符串转换为字节值。 可以使用可选的 Java 十进制格式进行转换。  |
| [toDate](data-flow-expression-functions.md#toDate) | 使用可选输入日期格式将输入日期字符串转换为日期。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 如果省略输入日期格式，则默认格式为 yyyy-[M]M-[d]d。 接受的格式包括：[ yyyy、yyyy-[M]M、yyyy-[M]M-[d]d、yyyy-[M]M-[d]dT* ]。  |
| [toDecimal](data-flow-expression-functions.md#toDecimal) | 将任何数字或字符串转换为小数值。 如果未指定精度和小数位数，则默认为 (10,2)。可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言形式（如 en-US、de、zh-CN）的可选区域设置格式。  |
| [toDouble](data-flow-expression-functions.md#toDouble) | 将任何数字或字符串转换为双精度值。 可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言形式（如 en-US、de、zh-CN）的可选区域设置格式。  |
| [toFloat](data-flow-expression-functions.md#toFloat) | 将任何数字或字符串转换为浮点值。 可以使用可选的 Java 十进制格式进行转换。 截断任何双精度数。  |
| [toInteger](data-flow-expression-functions.md#toInteger) | 将任何数字或字符串转换为整数值。 可以使用可选的 Java 十进制格式进行转换。 截断任何长整型数、浮点数、双精度数。  |
| [toLong](data-flow-expression-functions.md#toLong) | 将任何数字或字符串转换为长值。 可以使用可选的 Java 十进制格式进行转换。 截断任何浮点数、双精度数。  |
| [toShort](data-flow-expression-functions.md#toShort) | 将任何数字或字符串转换为短值。 可以使用可选的 Java 十进制格式进行转换。 截断任何整数、长整型数、浮点数、双精度数。  |
| [toString](data-flow-expression-functions.md#toString) | 将基元数据类型转换为字符串。 对于数字和日期，可以指定格式。 如果未指定，则选择系统默认值。对数字使用 Java 十进制格式。 有关所有可能的日期格式，请参阅 Java SimpleDateFormat；默认格式为 yyyy-MM-dd。  |
| [toTimestamp](data-flow-expression-functions.md#toTimestamp) | 根据可选的时间戳格式将字符串转换为时间戳。 如果省略时间戳，则使用默认模式 yyyy-[M]M-[d]d hh:mm:ss[.f...]。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 时间戳支持高达 999 毫秒的精度值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [toUTC](data-flow-expression-functions.md#toUTC) | 将时间戳转换为 UTC。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
|||

## <a name="map-functions"></a>映射函数

  映射函数对映射数据类型执行操作

| 映射函数 | 任务 |
|----|----|
| [associate](data-flow-expression-functions.md#associate) | 创建键/值的映射。 所有键和值应属于同一类型。 如果未指定任何项，则默认为字符串到字符串类型的映射。与 ```[ -> ]``` 创建运算符相同。 键和值应该相互交替。|
| [keyValues](data-flow-expression-functions.md#keyValues) | 创建键/值的映射。 第一个参数是键数组，第二个参数是值数组。 这两个数组的长度应相等。|
| [mapAssociation](data-flow-expression-functions.md#mapAssociation) | 通过将键关联到新值来转换映射。 返回数组。 它采用映射函数，可在其中将项处理为 #key，并将当前值处理为 #value。 |
| [reassociate](data-flow-expression-functions.md#reassociate) | 通过将键关联到新值来转换映射。 它采用映射函数，可在其中将项处理为 #key，并将当前值处理为 #value。  |
|||

## <a name="metafunctions"></a>元函数

元函数主要用于处理数据流中的元数据

| 元函数  | 任务 |
|----|----|
| [byItem](data-flow-expression-functions.md#byItem) | 在结构或结构数组中查找子项，如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换操作（? date、? string…）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换   |
| [byOrigin](data-flow-expression-functions.md#byOrigin) | 在源流中按名称选择列值。 第二个参数是源流名称。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING...）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  |
| [byOrigins](data-flow-expression-functions.md#byOrigins) | 在流中按名称选择列的数组。 第二个参数是其源流。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING…）转换的类型设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。|
| [byName](data-flow-expression-functions.md#byName) | 在流中按名称选择列值。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING...）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  |
| [byNames](data-flow-expression-functions.md#byNames) | 在流中按名称选择列的数组。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果列没有匹配项，则整个输出为 NULL 值。 返回的值需要类型转换函数（toDate、toString...）。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。|
| [byPath](data-flow-expression-functions.md#byPath) | 在流中按名称查找分层路径。 可以将可选流名称作为第二个参数传递。 如果找不到此类路径，则返回 null。 设计时已知的列名/路径应该仅通过其名称或点表示法路径来寻址。 不支持计算输入，但可以使用参数替换。  |
| [byPosition](data-flow-expression-functions.md#byPosition) | 根据列在流中的相对位置（从 1 开始）选择列值。 如果位置超出界限，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING…）转换的类型不支持计算输入，但可以使用参数替换。  |
| [hasPath](data-flow-expression-functions.md#hasPath) | 按名称检查流中是否存在某个分层路径。 可以将可选流名称作为第二个参数传递。 设计时已知的列名/路径应该仅通过其名称或点表示法路径来寻址。 不支持计算输入，但可以使用参数替换。  |
| [originColumns](data-flow-expression-functions.md#originColumns) | 获取从中创建了列的源流的所有输出列。 必须包含在另一个函数中。|
| [hex](data-flow-expression-functions.md#hex) | 返回二进制值的十六进制字符串表示形式 |
| [unhex](data-flow-expression-functions.md#unhex) | 对字符串表示形式的二进制值进行反向十六进制转换。 这可以用于联合 sha2 和 md5 从字符串表示形式转换为二进制表示形式 |
|||

## <a name="window-functions"></a>开窗函数

以下函数仅可用于窗口转换。

| 开窗函数 | 任务 |
|----|----|
| [cumeDist](data-flow-expression-functions.md#cumeDist) | CumeDist 函数计算某个值相对于分区中所有值的位置。 结果是前面的行数，或者等于当前行在分区中的顺序除以窗口分区中的总行数。 顺序中的任何关联值将计算为相同的位置。  |
| [denseRank](data-flow-expression-functions.md#denseRank) | 计算在窗口的 order by 子句中指定的一组值中的值排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值不会在序列中生成空隙。 即使数据未排序，也能进行密集排名，并且会查找值的变化。  |
| [lag](data-flow-expression-functions.md#lag) | 获取当前行之前由第一个参数计算的 n 行的值。 第二个参数是要反向查找的行数，默认值为 1。 如果行数不多，则返回 NULL 值，除非指定了默认值。  |
| [lead](data-flow-expression-functions.md#lead) | 获取当前行之后由第一个参数计算的 n 行的值。 第二个参数是要正向查找的行数，默认值为 1。 如果行数不多，则返回 NULL 值，除非指定了默认值。  |
| [nTile](data-flow-expression-functions.md#nTile) | ```NTile``` 函数将每个窗口分区的行分割为从 1 到 `n` 的 `n` 桶。 桶值最大相差 1。 如果分区中的行数不能均匀分割成桶数，则余值将逐个分布在每个桶中，从第一个桶开始。 ```NTile``` 函数适合用于计算 ```tertiles```、四分位数、十分位数和其他常见的摘要统计数据。 在初始化期间，该函数将计算两个变量：常规桶的大小将额外添加一行。 这两个变量都以当前分区的大小为基础。 在计算过程中，该函数将跟踪当前行号、当前桶号，以及发生桶更改的行号 (bucketThreshold)。 如果当前行号达到桶的阈值，则桶值将会加 1，阈值将按桶大小增加（如果当前桶已填充，则额外加 1）。  |
| [rank](data-flow-expression-functions.md#rank) | 计算在窗口的 order by 子句中指定的一组值中的值排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值将在序列中生成空隙。 即使数据未排序，也能进行排名，并且会查找值的变化。  |
| [rowNumber](data-flow-expression-functions.md#rowNumber) | 为窗口中的行分配一个顺序行号，从 1 开始。  |
|||

## <a name="alphabetical-listing-of-all-functions"></a>按字母顺序列出所有函数

下面按字母顺序列出了在映射数据流中可用的所有函数。

<a name="abs" ></a>

### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数字的绝对值。  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   


<a name="acos" ></a>

### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算反余弦值。  
* ``acos(1) -> 0.0``  
___


<a name="add" ></a>

### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
添加一对字符串或数字。 将日期添加到日期数。 将持续时间添加到时间戳。 将类似类型的数组追加到另一个数组。 与 + 运算符相同。  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="addDays" ></a>

### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
将日期添加到日期或时间戳。 与日期的 + 运算符相同。  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___


<a name="addMonths" ></a>

### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
将月份添加到日期或时间戳。 可以选择性地传递时区。  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___


<a name="and" ></a>

### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
“逻辑与”运算符。 与 && 相同。  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___


<a name="approxDistinctCount" ></a>

### <code>approxDistinctCount</code>
<code><b>approxDistinctCount(<i>&lt;value1&gt;</i> : any, [ <i>&lt;value2&gt;</i> : double ]) => long</b></code><br/><br/>
获取列的相异值的近似聚合计数。 可选的第二个参数用于控制估计误差。
* ``approxDistinctCount(ProductID, .05) => long``  
___


<a name="array" ></a>

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
创建项的数组。 所有项应属于同一类型。 如果未指定任何项，则默认值为空字符串数组。 与 [] 创建运算符相同。  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___


<a name="asin" ></a>

### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算反正弦值。  
* ``asin(0) -> 0.0``  
___


<a name="associate" ></a>

### <code>associate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
创建键/值的映射。 所有键和值应属于同一类型。 如果未指定任何项，则默认为字符串到字符串类型的映射。与 ```[ -> ]``` 创建运算符相同。 键和值应该相互交替。
*   ``associate('fruit', 'apple', 'vegetable', 'carrot' )=> ['fruit' -> 'apple', 'vegetable' -> 'carrot']``
___


<a name="at" ></a>

### <code>at</code>
<code><b>at(<i>&lt;value1&gt;</i> : array/map, <i>&lt;value2&gt;</i> : integer/key type) => array</b></code><br/><br/>
查找数组索引处的元素。 索引从 1 开始。 如果索引超出界限，则返回 null 值。 在给定了键的情况下，在映射中查找值。 如果未找到该键，则返回 null。
*   ``at(['apples', 'pears'], 1) => 'apples'``
*   ``at(['fruit' -> 'apples', 'vegetable' -> 'carrot'], 'fruit') => 'apples'``
___


<a name="atan" ></a>

### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算反正切值。  
* ``atan(0) -> 0.0``  
___


<a name="atan2" ></a>

### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
返回平面正 X 轴与给定坐标点之间的弧角。  
* ``atan2(0, 0) -> 0.0``  
___


<a name="avg" ></a>

### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取列值的平均值。  
* ``avg(sales)``  
___


<a name="avgIf" ></a>

### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根据条件获取列值的平均值。  
* ``avgIf(region == 'West', sales)``  
___


<a name="between" ></a>

### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
检查第一个值是否在两个其他值之间（含这两个值）。 可以比较数字、字符串和日期值 * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___


<a name="bitwiseAnd" ></a>

### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
跨整数类型的位和运算符。 与 & 运算符相同 * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___


<a name="bitwiseOr" ></a>

### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
跨整数类型的位或运算符。 与 | 运算符相同 * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___


<a name="bitwiseXor" ></a>

### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
跨整数类型的位或运算符。 与 | 运算符相同 * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___


<a name="blake2b" ></a>

### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
如果给定位长只能是 8 和 512 之间的 8 的倍数，计算具有不同基元数据类型的列集的 Blake2 摘要。 它可用于计算行的指纹 * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___


<a name="blake2bBinary" ></a>

### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
如果给定位长只能是 8 和 512 之间的 8 的倍数，计算具有不同基元数据类型的列集的 Blake2 摘要。 它可用于计算行的指纹 * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___


<a name="byItem" ></a>

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
在结构或结构数组中查找子项，如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换操作（? date、? string…）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换 * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
___


<a name="byName" ></a>

### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
在流中按名称选择列值。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING...）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___


<a name="byNames" ></a>

### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
在流中按名称选择列的数组。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果列没有匹配项，则整个输出为 NULL 值。 返回的值需要类型转换函数（toDate、toString...）。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___


<a name="byOrigin" ></a>

### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
在源流中按名称选择列值。 第二个参数是源流名称。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING...）转换的类型。设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___


<a name="byOrigins" ></a>

### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
在流中按名称选择列的数组。 第二个参数是其源流。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING…）转换的类型设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___


<a name="byPath" ></a>

### <code>byPath</code>
<code><b>byPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => any</b></code><br/><br/>
在流中按名称查找分层路径。 可以将可选流名称作为第二个参数传递。 如果找不到此类路径，则返回 null。 设计时已知的列名/路径应该仅通过其名称或点表示法路径来寻址。 不支持计算输入，但可以使用参数替换。  
* ``byPath('grandpa.parent.child') => column`` 
___


<a name="byPosition" ></a>

### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
根据列在流中的相对位置（从 1 开始）选择列值。 如果位置超出界限，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING…）转换的类型不支持计算输入，但可以使用参数替换。  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___


<a name="case" ></a>

### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
根据备用条件应用一个值或另一个值。 如果输入的数字是偶数，对于最后一个条件，另一个值将默认为 NULL。  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___


<a name="cbrt" ></a>

### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算一个数的立方根。  
* ``cbrt(8) -> 2.0``  
___


<a name="ceil" ></a>

### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
返回不小于该数字的最小整数。  
* ``ceil(-0.1) -> 0``  
___


<a name="coalesce" ></a>

### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
返回一组输入中的第一个非 NULL 值。 所有输入应属于同一类型。  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___


<a name="collect" ></a>

### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
将聚合组中表达式的所有值收集到一个数组中。 在此过程中，可以收集结构并将其转换为备用结构。 项目数将等于该组中的行数，并且可以包含 NULL 值。 收集的项目数应较小。  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___


<a name="columnNames" ></a>

### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
获取流的所有输出列的名称。 可以将可选流名称作为第二个参数传递。  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___


<a name="columns" ></a>

### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
获取流的所有输出列的值。 可以将可选流名称作为第二个参数传递。   
* ``columns()``
* ``columns('DeriveStream')``
___


<a name="compare" ></a>

### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
比较同一类型的两个值。 如果 value1 < value2，返回负整数；如果 value1 == value2，返回 0；如果 value1 > value2，则返回正值。  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___


<a name="concat" ></a>

### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
将字符串的可变数字连接在一起。 与包含字符串的 + 运算符相同。  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___


<a name="concatWS" ></a>

### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
使用分隔符将字符串的可变数字连接在一起。 第一个参数是分隔符。  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___


<a name="contains" ></a>

### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
如果所提供的数组中的任何元素在提供的谓词中计算结果为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素作为 #item。  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___


<a name="cos" ></a>

### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算余弦值。  
* ``cos(10) -> -0.8390715290764524``  
___


<a name="cosh" ></a>

### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算某个值的双曲余弦值。  
* ``cosh(0) -> 1.0``  
___


<a name="count" ></a>

### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
获取值的聚合计数。 如果指定了可选的列，则忽略计数中的 NULL 值。  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___


<a name="countDistinct" ></a>

### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
获取列集的非重复值的聚合计数。  
* ``countDistinct(custId, custName)``  
___


<a name="countIf" ></a>

### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
根据条件获取值的聚合计数。 如果指定了可选的列，则忽略计数中的 NULL 值。  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___


<a name="covariancePopulation" ></a>

### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
获取两个列之间的总体协方差。  
* ``covariancePopulation(sales, profit)``  
___


<a name="covariancePopulationIf" ></a>

### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取两个列的总体协方差。  
* ``covariancePopulationIf(region == 'West', sales)``  
___


<a name="covarianceSample" ></a>

### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
获取两个列的样本协方差。  
* ``covarianceSample(sales, profit)``  
___


<a name="covarianceSampleIf" ></a>

### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取两个列的样本协方差。  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___



<a name="crc32" ></a>

### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
根据位长度计算不同基元数据类型的列集的 CRC32 哈希，值只能为 0(256)、224、256、384、512。 它可用于计算行的指纹。  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___


<a name="cumeDist" ></a>

### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 函数计算某个值相对于分区中所有值的位置。 结果是前面的行数，或者等于当前行在分区中的顺序除以窗口分区中的总行数。 顺序中的任何关联值将计算为相同的位置。  
* ``cumeDist()``  
___


<a name="currentDate" ></a>

### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
当此作业开始运行时获取当前日期。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___


<a name="currentTimestamp" ></a>

### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
当作业开始运行时，获取采用当地时区的当前时间戳。  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___


<a name="currentUTC" ></a>

### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
获取 UTC 格式的当前时间戳。 如果希望在与群集时区不同的时区中解释当前时间，则可以采用“GMT”、“PST”、“UTC”或“America/Cayman”格式传递一个可选时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 若要将 UTC 时间转换为其他时区，请使用 `fromUTC()`。  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="dayOfMonth" ></a>

### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期的月份日期。  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___


<a name="dayOfWeek" ></a>

### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期的星期日期。 1 - 星期日，2 - 星期一…7 - 星期六。  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___


<a name="dayOfYear" ></a>

### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期的年份日期。  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___


<a name="days" ></a>

### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
天数的持续时间（以毫秒为单位）。  
* ``days(2) -> 172800000L``  
___


<a name="degrees" ></a>

### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
将弧度转换为度。  
* ``degrees(3.141592653589793) -> 180``  
___


<a name="denseRank" ></a>

### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
计算在窗口的 order by 子句中指定的一组值中的值排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值不会在序列中生成空隙。 即使数据未排序，也能进行密集排名，并且会查找值的变化。  
* ``denseRank()``  
___


<a name="distinct" ></a>

### <code>distinct</code>
<code><b>distinct(<i>&lt;value1&gt;</i> : array) => array</b></code><br/><br/>
返回数组中的相异项集。
* ``distinct([10, 20, 30, 10]) => [10, 20, 30]``
___


<a name="divide" ></a>

### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
将数字对相除。 与 `/` 运算符相同。  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``
___


<a name="dropLeft" ></a>

### <code>dropLeft</code>
<code><b>dropLeft(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
删除字符串左侧任意数目的字符。 如果请求删除的字符数超过了该字符串的长度，则返回空字符串。
*   dropLeft('bojjus', 2) => 'jjus' *   dropLeft('cake', 10) => '' ___


<a name="dropRight" ></a>

### <code>dropRight</code>
<code><b>dropRight(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
删除字符串右侧任意数目的字符。 如果请求删除的字符数超过了该字符串的长度，则返回空字符串。
*   dropRight('bojjus', 2) => 'bojj' *   dropRight('cake', 10) => '' ___


<a name="endsWith" ></a>

### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否以提供的字符串结尾。  
* ``endsWith('dumbo', 'mbo') -> true``  
___


<a name="equals" ></a>

### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“等于”比较运算符。 与 == 运算符相同。  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___


<a name="equalsIgnoreCase" ></a>

### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
忽略大小写的“等于”比较运算符。 与 <=> 运算符相同。  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___


<a name="escape" ></a>

### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
根据格式转义字符串。 可接受的格式的文本值为 "json"、"xml"、"ecmascript"、"html"、"java"。
___


<a name="except" ></a>

### <code>except</code>
<code><b>except(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
从另一组删除重复项中返回一个数组的差异集。
* ``except([10, 20, 30], [20, 40]) => [10, 30]``  
___


<a name="expr" ></a>

### <code>expr</code>
<code><b>expr(<i>&lt;expr&gt;</i> : string) => any</b></code><br/><br/>
从字符串生成表达式。 这与在非文本窗体中编写此表达式的效果一样。 这可以用来以字符串表示形式传递参数。
*    expr('price * discount') => any ___


<a name="factorial" ></a>

### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
计算一个数的阶乘。  
* ``factorial(5) -> 120``  
___


<a name="false" ></a>

### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
始终返回 false 值。 如果存在名为“false”的列，则使用函数 `syntax(false())`。  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___


<a name="filter" ></a>

### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
筛选出数组中不满足所提供谓词的元素。 Filter 需要引用谓词函数中的一个元素作为 #item。  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___


<a name="find" ></a>

### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
查找数组中与条件相匹配的第一项。 它采用筛选器函数，你可以在其中将数组中的项作为 #item 进行寻址。 对于深层嵌套的映射，可使用 #item_n(#item_1, #item_2...) 表示法引用父映射。  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___


<a name="first" ></a>* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___


<a name="first" ></a>

### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
获取列组的第一个值。 如果省略第二个参数 ignoreNulls，则假定为 false。  
* ``first(sales)``  
* ``first(sales, false)``  
___



<a name="flatten" ></a>

### <code>flatten</code>
<code><b>flatten(<i>&lt;array&gt;</i> : array, <i>&lt;value2&gt;</i> : array ..., <i>&lt;value2&gt;</i> : boolean) => array</b></code><br/><br/>
将一个或多个数组平展成单个数组。 原子项数组将按原样返回。 最后一个参数是可选的，默认为 false，表示以递归方式平展多个层。
*   ``flatten([['bojjus', 'girl'], ['gunchus', 'boy']]) => ['bojjus', 'girl', 'gunchus', 'boy']``
*   ``flatten([[['bojjus', 'gunchus']]] , true) => ['bojjus', 'gunchus']``
___


<a name="floor" ></a>

### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
返回不大于该数字的最大整数。  
* ``floor(-0.1) -> -1``  
___


<a name="fromBase64" ></a>

### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
对给定的 base64 编码的字符串进行解码。
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___


<a name="fromUTC" ></a>

### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
转换为 UTC 格式的时间戳。 可以选择以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="greater" ></a>

### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“大于”比较运算符。 与 > 运算符相同。  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="greaterOrEqual" ></a>

### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“大于等于”比较运算符。 与 >= 运算符相同。  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___


<a name="greatest" ></a>

### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
返回输入值列表中的最大值，跳过 NULL 值。 如果所有输入均为 NULL，则返回 NULL。  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="hasColumn" ></a>

### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
在流中按名称检查列值。 可以将可选流名称作为第二个参数传递。 设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换。  
* ``hasColumn('parent')``  
___


<a name="hasPath" ></a>

### <code>hasPath</code>
<code><b>hasPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => boolean</b></code><br/><br/>
按名称检查流中是否存在某个分层路径。 可以将可选流名称作为第二个参数传递。 设计时已知的列名/路径应该仅通过其名称或点表示法路径来寻址。 不支持计算输入，但可以使用参数替换。  
* ``hasPath('grandpa.parent.child') => boolean``
___  


<a name="hex" ></a>

### <code>hex</code>
<code><b>hex(<i>\<value1\></i>: binary) => string</b></code><br/><br/>
返回二进制值的十六进制字符串表示形式 * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___


<a name="hour" ></a>

### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
获取时间戳的小时值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___


<a name="hours" ></a>

### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
小时数的持续时间（以毫秒为单位）。  
* ``hours(2) -> 7200000L``  
___


<a name="iif" ></a>

### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
根据条件应用一个值或另一个值。 如果未指定另一个值，则将它视为 NULL。 这两个值必须兼容（数字、字符串…）。* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___


<a name="iifNull" ></a>

### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
检查第一个参数是否为 NULL。 如果不为 NULL，则返回第一个参数。 如果为 NULL，则返回第二个参数。 如果指定了三个参数，则行为与 iif(isNull(value1), value2, value3) 相同，如果第一个值不为 NULL，则返回第三个参数。  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___


<a name="in" ></a>

### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
检查某个项是否在数组中。  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___


<a name="initCap" ></a>

### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
将每个单词的第一个字母转换为大写。 通过空格分隔符识别单词。  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___


<a name="instr" ></a>

### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
在字符串中查找子字符串的位置（从 1 开始）。 如果未找到，则返回 0。  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___


<a name="intersect" ></a>

### <code>intersect</code>
<code><b>intersect(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
返回 2 个数组中不同项的交集。
* ``intersect([10, 20, 30], [20, 40]) => [20]``  
___


<a name="isBitSet" ></a>

### <code>isBitSet</code>
<code><b>isBitSet (<i><i>\<value1\></i></i> : array, <i>\<value2\></i>:integer ) => boolean</b></code><br/><br/>
检查是否在此位集中设置了位的位置 * ``isBitSet(toBitSet([10, 32, 98]), 10) => true``
___


<a name="isBoolean" ></a>

### <code>isBoolean</code>
<code><b>isBoolean(<i>\<value1\></i>: string) => boolean</b></code><br/><br/>
根据 ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___


<a name="isByte" ></a> 规则检查 string 值是否是 boolean 值

### <code>isByte</code>
<code><b>isByte(<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
根据 ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___


<a name="isDate" ></a> 规则检查 string 值是否是给定可选格式的 byte 值

### <code>isDate</code>
<code><b>isDate (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
使用可选输入日期格式检查输入日期字符串是否为日期。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略输入日期格式，则默认格式为 ``yyyy-[M]M-[d]d``。 接受的格式为 ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___


<a name="isDecimal" ></a>

### <code>isDecimal</code>
<code><b>isDecimal (<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
根据 ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___


<a name="isDelete" ></a> 规则检查 string 值是否是给定可选格式的 decimal 值

### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为删除。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isDelete()``  
* ``isDelete(1)``  
___


<a name="isDistinct" ></a>

### <code>isDistinct</code>
<code><b>isDistinct(<i>&lt;value1&gt;</i> : any , <i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
确定一列或一组列是否是非重复值。 它不将 null 计为非重复值 *    ``isDistinct(custId, custName) => boolean``
___



<a name="isDouble" ></a>

### <code>isDouble</code>
<code><b>isDouble (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
根据 ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___


<a name="isError" ></a> 规则检查 string 值是否是给定可选格式的 double 值

### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为错误。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isError()``  
* ``isError(1)``  
___


<a name="isFloat" ></a>

### <code>isFloat</code>
<code><b>isFloat (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
根据 ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___


<a name="isIgnore" ></a> 规则检查 string 值是否是给定可选格式的 float 值

### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为忽略。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isIgnore()``  
* ``isIgnore(1)``  
___


<a name="isInsert" ></a>

### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isInsert()``  
* ``isInsert(1)``  
___


<a name="isInteger" ></a>

### <code>isInteger</code>
<code><b>isInteger (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
根据 ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___


<a name="isLong" ></a> 规则检查 string 值是否是给定可选格式的 integer 值

### <code>isLong</code>
<code><b>isLong (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
根据 ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___


<a name="isMatch" ></a> 规则检查 string 值是否是给定可选格式的 long 值

### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查查找时是否匹配该行。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isMatch()``  
* ``isMatch(1)``  
___


<a name="isNan" ></a>

### <code>isNan</code>
<code><b>isNan (<i>\<value1\></i> : integral) => boolean</b></code><br/><br/>
检查该值是否不是数字。
* ``isNan(10.2) => false``
___


<a name="isNull" ></a>

### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
检查值是否为 NULL。  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___


<a name="isShort" ></a>

### <code>isShort</code>
<code><b>isShort (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
根据 ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___


<a name="isTimestamp" ></a> 规则检查 string 值是否是给定可选格式的 short 值

### <code>isTimestamp</code>
<code><b>isTimestamp (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
使用可选输入时间戳格式检查输入日期字符串是否为时间戳。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略时间戳，则使用默认模式 ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 时间戳最多支持毫秒准确度，值为 999。有关可用格式，请参阅 Java 的 SimpleDateFormat。
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___


<a name="isUpdate" ></a>

### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为更新。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isUpdate()``  
* ``isUpdate(1)``  
___


<a name="isUpsert" ></a>

### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从 1 开始）索引。 流索引应为 1 或 2，默认值为 1。  
* ``isUpsert()``  
* ``isUpsert(1)``  
___


<a name="jaroWinkler" ></a>

### <code>jaroWinkler</code>
<code><b>jaroWinkler(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => double</b></code><br/><br/>
获取两个字符串之间的 JaroWinkler 距离。 
* ``jaroWinkler('frog', 'frog') => 1.0``  
___


<a name="keyValues" ></a>

### <code>keyValues</code>
<code><b>keyValues(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => map</b></code><br/><br/>
创建键/值的映射。 第一个参数是键数组，第二个参数是值数组。 这两个数组的长度应相等。
*   ``keyValues(['bojjus', 'appa'], ['gunchus', 'ammi']) => ['bojjus' -> 'gunchus', 'appa' -> 'ammi']``
___ 


<a name="kurtosis" ></a>

### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的峰度。  
* ``kurtosis(sales)``  
___


<a name="kurtosisIf" ></a>

### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的峰度。  
* ``kurtosisIf(region == 'West', sales)``  
___


<a name="lag" ></a>

### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
获取当前行之前由第一个参数计算的 n 行的值。 第二个参数是要反向查找的行数，默认值为 1。 如果行数不多，则返回 NULL 值，除非指定了默认值。  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___


<a name="last" ></a>

### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
获取列组的最后一个值。 如果省略第二个参数 ignoreNulls，则假定为 false。  
* ``last(sales)``  
* ``last(sales, false)``  
___


<a name="lastDayOfMonth" ></a>

### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
获取给定日期的最后一个月份日期。  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___


<a name="lead" ></a>

### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
获取当前行之后由第一个参数计算的 n 行的值。 第二个参数是要正向查找的行数，默认值为 1。 如果行数不多，则返回 NULL 值，除非指定了默认值。  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___


<a name="least" ></a>

### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
“小于等于”比较运算符。 与 <= 运算符相同。  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___


<a name="left" ></a>

### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
从索引 1 处开始提取包含字符数的子字符串。 与 SUBSTRING(str, 1, n) 相同。  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___


<a name="length" ></a>

### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
返回字符串的长度。  
* ``length('dumbo') -> 5``  
___


<a name="lesser" ></a>

### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“小于”比较运算符。 与 < 运算符相同。  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="lesserOrEqual" ></a>

### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“小于等于”比较运算符。 与 <= 运算符相同。  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___


<a name="levenshtein" ></a>

### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
获取两个字符串之间的 levenshtein 距离。  
* ``levenshtein('boys', 'girls') -> 4``  
___


<a name="like" ></a>

### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
模式是按原义匹配的字符串。 以下特殊符号除外：_ 与输入中的任何一个字符匹配（类似于 ```posix``` 正则表达式中的 .）。% 匹配输入中的零个或多个字符（类似于 ```posix``` 正则表达式中的 .*）。
转义字符为 ''。 如果转义字符的之前带有特殊符号或其他转义字符，则在字面上匹配后面的字符。 转义其他任何字符的操作无效。  
* ``like('icecream', 'ice%') -> true``  
___


<a name="locate" ></a>

### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
从特定的位置开始，在字符串中查找子字符串的位置（从 1 开始）。 如果省略位置，则视为从字符串开头查找。 如果未找到，则返回 0。  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___


<a name="log" ></a>

### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
计算对数值。 可以提供可选的底，否则使用欧拉数。  
* ``log(100, 10) -> 2``  
___


<a name="log10" ></a>

### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
以 10 为底计算对数值。  
* ``log10(100) -> 2``  
___


<a name="lookup" ></a>

### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
使用与缓存接收器中的键匹配的指定键查找缓存接收器中的第一行。
* ``cacheSink#lookup(movieId)``  
___


<a name="lower" ></a>

### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
将字符串小写。  
* ``lower('GunChus') -> 'gunchus'``  
___


<a name="lpad" ></a>

### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
在左侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于特定长度，则进行剪裁。  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___


<a name="ltrim" ></a>

### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
在左侧裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___


<a name="map" ></a>

### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用表达式函数中的一个元素作为 #item。  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___


<a name="mapAssociation" ></a>

### <code>mapAssociation</code>
<code><b>mapAssociation(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => array</b></code><br/><br/>
通过将键关联到新值来转换映射。 返回数组。 它采用映射函数，可在其中将项处理为 #key，并将当前值处理为 #value。 
*   ``mapAssociation(['bojjus' -> 'gunchus', 'appa' -> 'ammi'], @(key = #key, value = #value)) => [@(key = 'bojjus', value = 'gunchus'), @(key = 'appa', value = 'ammi')]``
___ 


<a name="mapIf" ></a>

### <code>mapIf</code>
<code><b>mapIf (<i>\<value1\></i> : array, <i>\<value2\></i> : binaryfunction, \<value3\>: binaryFunction) => any</b></code><br/><br/>
有条件地将数组映射到长度相同或更短的另一个数组。 这些值可以是任何数据类型，包括 structTypes。 它采用一个映射函数，在其中可以将数组中的项作为 #item 进行寻址，将当前索引作为 #index 进行寻址。 对于深层嵌套的映射，可使用 ``#item_[n](#item_1, #index_1...)`` 表示法引用父映射。
*    ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___


<a name="mapIndex" ></a>

### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用表达式函数中的一个元素作为 #item，并需要引用元素索引作为 #index。  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___


<a name="mapLoop" ></a>

### <code>mapLoop</code>
<code><b>mapLoop(<i>\<value1\></i> : integer, <i>\<value2\></i> : unaryfunction) => any</b></code><br/><br/>
从 1 到某个长度进行循环，以创建具有该长度的数组。 它采用一个映射函数，在其中可以将数组中的索引作为 #index 进行寻址。 对于深层嵌套的映射，可使用 #index_n(#index_1, #index_2...) 表示法引用父映射。
*    ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___


<a name="max" ></a>

### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
获取列的最大值。  
* ``max(sales)``  
___


<a name="maxIf" ></a>

### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根据条件获取列的最大值。  
* ``maxIf(region == 'West', sales)``  
___


<a name="md5" ></a>

### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
计算不同基元数据类型的列集的 MD5 摘要，并返回 32 字符十六进制字符串。 它可用于计算行的指纹。  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___


<a name="mean" ></a>

### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取列值的中间值。 与 AVG 相同。  
* ``mean(sales)``  
___


<a name="meanIf" ></a>

### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根据条件获取列值的中间值。 与 avgIf 相同。  
* ``meanIf(region == 'West', sales)``  
___


<a name="millisecond" ></a>

### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
获取日期的毫秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="milliseconds" ></a>

### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
毫秒数的持续时间（以毫秒为单位）。  
* ``milliseconds(2) -> 2L``  
___


<a name="min" ></a>

### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
获取列的最小值。  
* ``min(sales)``  
___


<a name="minIf" ></a>

### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根据条件获取列的最小值。  
* ``minIf(region == 'West', sales)``  
___


<a name="minus" ></a>

### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
减去数字。 从日期数中减去日期。 从时间戳中减去持续时间。 减去两个时间戳以获取差异（以毫秒为单位）。 与 - 运算符相同。  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___


<a name="minute" ></a>

### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
获取时间戳的分钟值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___


<a name="minutes" ></a>

### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
分钟数的持续时间（以毫秒为单位）。  
* ``minutes(2) -> 120000L``  
___


<a name="mlookup" ></a>

### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
使用与缓存接收器中的键匹配的指定键查找缓存接收器中的所有匹配行。
* ``cacheSink#mlookup(movieId)``  
___


<a name="mod" ></a>

### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
将数字对取模。 与 % 运算符相同。  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___


<a name="month" ></a>

### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取日期或时间戳的月份值。  
* ``month(toDate('2012-8-8')) -> 8``  
___


<a name="monthsBetween" ></a>

### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
获取两个日期之间的月数。 可以舍入计算。可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___


<a name="multiply" ></a>

### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
将数字对相乘。 与 * 运算符相同。  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___


<a name="negate" ></a>

### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
对数字求反。 将正数转换为负数，或反之。  
* ``negate(13) -> -13``  
___


<a name="nextSequence" ></a>

### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
返回下一个唯一序列。 该数字仅在分区中是连续的，带有 partitionId 前缀。  
* ``nextSequence() == 12313112 -> false``  
___


<a name="normalize" ></a>

### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
规范化字符串值以分隔重音的 unicode 字符。  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___


<a name="not" ></a>

### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
逻辑求反运算符。  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___


<a name="notEquals" ></a>

### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“不等于”比较运算符。 与 != 运算符相同。  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___


<a name="notNull" ></a>

### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
检查值是否不为 NULL。  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___


<a name="nTile" ></a>

### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile``` 函数将每个窗口分区的行分割为从 1 到 `n` 的 `n` 桶。 桶值最大相差 1。 如果分区中的行数不能均匀分割成桶数，则余值将逐个分布在每个桶中，从第一个桶开始。 ```NTile``` 函数适合用于计算 ```tertiles```、四分位数、十分位数和其他常见的摘要统计数据。 在初始化期间，该函数将计算两个变量：常规桶的大小将额外添加一行。 这两个变量都以当前分区的大小为基础。 在计算过程中，该函数将跟踪当前行号、当前桶号，以及发生桶更改的行号 (bucketThreshold)。 如果当前行号达到桶的阈值，则桶值将会加 1，阈值将按桶大小增加（如果当前桶已填充，则额外加 1）。  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___


<a name="null" ></a>

### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
返回 NULL 值。 如果存在名为“null”的列，则使用函数 `syntax(null())`。 使用该函数的任何操作将导致 NULL。  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___


<a name="or" ></a>

### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
“逻辑或”运算符。 与 || 相同。  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___


<a name="originColumns" ></a>

### <code>originColumns</code>
<code><b>originColumns(<i>&lt;streamName&gt;</i> : string) => any</b></code><br/><br/>
获取从中创建了列的源流的所有输出列。 必须包含在另一个函数中。
* ``array(toString(originColumns('source1')))``
___  


<a name="output" ></a>

### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
返回缓存接收器结果的第一行 * ``cacheSink#output()``  
___


<a name="outputs" ></a>

### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
返回缓存接收器结果的整个输出行集 * ``cacheSink#outputs()``
___



<a name="partitionId" ></a>

### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
返回输入行所在的当前分区 ID。  
* ``partitionId()``  
___


<a name="pMod" ></a>

### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
将数字对正数取模。  
* ``pmod(-20, 8) -> 4``  
___


<a name="power" ></a>

### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
以一个数为底、另一数为幂求值。  
* ``power(10, 2) -> 100``  
___


<a name="radians" ></a>

### <code>radians</code>
<code><b>radians(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
将度转换为弧度 * ``radians(180) => 3.141592653589793``  
___


<a name="random" ></a>

### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
返回给定分区内的可选种子的随机数。 种子应为固定值，与 partitionId 一起用于生成随机值 * ``random(1) == 1 -> false``
___


<a name="rank" ></a>

### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
计算在窗口的 order by 子句中指定的一组值中的值排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值将在序列中生成空隙。 即使数据未排序，也能进行排名，并且会查找值的变化。  
* ``rank()``  
___


<a name="reassociate" ></a>

### <code>reassociate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
通过将键关联到新值来转换映射。 它采用映射函数，可在其中将项处理为 #key，并将当前值处理为 #value。  
* ``reassociate(['fruit' -> 'apple', 'vegetable' -> 'tomato'], substring(#key, 1, 1) + substring(#value, 1, 1)) => ['fruit' -> 'fa', 'vegetable' -> 'vt']``
___
  


<a name="reduce" ></a>

### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
累积数组中的元素。 Reduce 需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且需要在第二个表达式函数中使用生成值作为 #result。  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___


<a name="regexExtract" ></a>

### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
提取给定正则表达式模式的匹配子字符串。 最后一个参数标识匹配组，如果省略，则默认为 1。 使用 `<regex>`（反引号）匹配字符串且不转义。  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___


<a name="regexMatch" ></a>

### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否与给定的正则表达式模式相匹配。 使用 `<regex>`（反引号）匹配字符串且不转义。  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___


<a name="regexReplace" ></a>

### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
将给定字符串中出现的一个正则表达式模式全部替换为另一个子字符串。使用 `<regex>`（反引号）匹配字符串且不转义。  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___


<a name="regexSplit" ></a>

### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
基于分隔符和正则表达式拆分字符串，并返回字符串数组。  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___


<a name="replace" ></a>

### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
将给定字符串中出现的一个子字符串全部替换为另一个子字符串。 如果省略最后一个参数，则默认为空字符串。  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___


<a name="reverse" ></a>

### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
反转字符串。  
* ``reverse('gunchus') -> 'suhcnug'``  
___


<a name="right" ></a>

### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
从右侧提取包含字符数的子字符串。 与 SUBSTRING(str, LENGTH(str) - n, n) 相同。  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___


<a name="rlike" ></a>

### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否与给定的正则表达式模式相匹配。  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___


<a name="round" ></a>

### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
根据可选的小数位数和可选的舍入模式将数字四舍五入。 如果省略小数位数，则默认为 0。 如果省略模式，则默认为 ROUND_HALF_UP(5)。 舍入运算的值包括：1 - ROUND_UP；2 - ROUND_DOWN；3 - ROUND_CEILING；4 - ROUND_FLOOR；5 - ROUND_HALF_UP；6 - ROUND_HALF_DOWN；7 - ROUND_HALF_EVEN；8 - ROUND_UNNECESSARY。  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___


<a name="rowNumber" ></a>

### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
为窗口中的行分配一个顺序行号，从 1 开始。  
* ``rowNumber()``  



<a name="rpad" ></a>

### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
在右侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于特定长度，则进行剪裁。  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___


<a name="rtrim" ></a>

### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
在右侧裁剪掉尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___


<a name="second" ></a>

### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
获取日期的秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 当地时区用作默认值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___


<a name="seconds" ></a>

### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
秒数的持续时间（以毫秒为单位）。  
* ``seconds(2) -> 2000L``  
___


<a name="setBitSet" ></a>

### <code>setBitSet</code>
<code><b>setBitSet (<i>\<value1\></i>: array, <i>\<value2\></i>:array) => array</b></code><br/><br/>
在此位集中设置位的位置 * ``setBitSet(toBitSet([10, 32]), [98]) => [4294968320L, 17179869184L]``
___  


<a name="sha1" ></a>

### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
计算不同基元数据类型的列集的 SHA-1 摘要，并返回 40 字符十六进制字符串。 它可用于计算行的指纹。  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___


<a name="sha2" ></a>

### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
根据位长度计算不同基元数据类型的列集的 SHA-2 摘要，值只能为 0(256)、224、256、384、512。 它可用于计算行的指纹。  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___


<a name="sin" ></a>

### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算正弦值。  
* ``sin(2) -> 0.9092974268256817``  
___


<a name="sinh" ></a>

### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算双曲正弦值。  
* ``sinh(0) -> 0.0``  
___


<a name="size" ></a>

### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
查找数组或映射类型的大小 * ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___


<a name="skewness" ></a>

### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的偏度。  
* ``skewness(sales)``  
___


<a name="skewnessIf" ></a>

### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的偏度。  
* ``skewnessIf(region == 'West', sales)``  
___


<a name="slice" ></a>

### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
从位置提取数组的子集。 位置从 1 开始。 如果省略长度，则默认为字符串的末尾。  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___


<a name="sort" ></a>

### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
使用提供的谓词函数对数组进行排序。 Sort 需要引用表达式函数中的两个连续元素作为 #item1 和 #item2。  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___


<a name="soundex" ></a>

### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
获取字符串的 ```soundex``` 代码。  
* ``soundex('genius') -> 'G520'``  
___


<a name="split" ></a>

### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
基于分隔符拆分字符串，并返回字符串数组。  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___


<a name="sqrt" ></a>

### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算一个数的平方根。  
* ``sqrt(9) -> 3``  
___


<a name="startsWith" ></a>

### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否以提供的字符串开头。  
* ``startsWith('dumbo', 'du') -> true``  
___


<a name="stddev" ></a>

### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的标准偏差。  
* ``stdDev(sales)``  
___


<a name="stddevIf" ></a>

### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的标准偏差。  
* ``stddevIf(region == 'West', sales)``  
___


<a name="stddevPopulation" ></a>

### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的总体标准偏差。  
* ``stddevPopulation(sales)``  
___


<a name="stddevPopulationIf" ></a>

### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的总体标准偏差。  
* ``stddevPopulationIf(region == 'West', sales)``  
___


<a name="stddevSample" ></a>

### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的样本标准偏差。  
* ``stddevSample(sales)``  
___


<a name="stddevSampleIf" ></a>

### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的样本标准偏差。  
* ``stddevSampleIf(region == 'West', sales)``  
___


<a name="subDays" ></a>

### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
从日期或时间戳中减去天数。 与日期的 - 运算符相同。  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___


<a name="subMonths" ></a>

### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
从日期或时间戳中减去月份。  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___


<a name="substring" ></a>

### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
从某个位置提取特定长度的子字符串。 位置从 1 开始。 如果省略长度，则默认为字符串的末尾。  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___


<a name="sum" ></a>

### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取数字列的聚合总数。  
* ``sum(col)``  
___


<a name="sumDistinct" ></a>

### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取数字列的非重复值的聚合总数。  
* ``sumDistinct(col)``  
___


<a name="sumDistinctIf" ></a>

### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根据条件获取数字列的聚合总数。 条件可以基于任何列。  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___


<a name="sumIf" ></a>

### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根据条件获取数字列的聚合总数。 条件可以基于任何列。  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___


<a name="tan" ></a>

### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算正切值。  
* ``tan(0) -> 0.0``  
___


<a name="tanh" ></a>

### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算双曲正切值。  
* ``tanh(0) -> 0.0``  
___


<a name="toBase64" ></a>

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
以 base64 编码给定字符串。  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___


<a name="toBinary" ></a>

### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
将任何数字/日期/时间戳/字符串转换为二进制表示形式。  
* ``toBinary(3) -> [0x11]``  
___


<a name="toBoolean" ></a>

### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
可将 ('t', 'true', 'y', 'yes', '1') 值转换为 true，将 ('f', 'false', 'n', 'no', '0') 值转换为 false，将其他任何值转换为 NULL。  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___


<a name="toByte" ></a>

### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
将任何数字或字符串转换为字节值。 可以使用可选的 Java 十进制格式进行转换。  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___


<a name="toDate" ></a>

### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
使用可选输入日期格式将输入日期字符串转换为日期。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 如果省略输入日期格式，则默认格式为 yyyy-[M]M-[d]d。 接受的格式包括：[ yyyy、yyyy-[M]M、yyyy-[M]M-[d]d、yyyy-[M]M-[d]dT* ]。  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___


<a name="toDecimal" ></a>

### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
将任何数字或字符串转换为小数值。 如果未指定精度和小数位数，则默认为 (10,2)。可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言形式（如 en-US、de、zh-CN）的可选区域设置格式。  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___


<a name="toDouble" ></a>

### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
将任何数字或字符串转换为双精度值。 可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言形式（如 en-US、de、zh-CN）的可选区域设置格式。  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___


<a name="toFloat" ></a>

### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
将任何数字或字符串转换为浮点值。 可以使用可选的 Java 十进制格式进行转换。 截断任何双精度数。  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___


<a name="toInteger" ></a>

### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
将任何数字或字符串转换为整数值。 可以使用可选的 Java 十进制格式进行转换。 截断任何长整型数、浮点数、双精度数。  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___


<a name="toLong" ></a>

### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
将任何数字或字符串转换为长值。 可以使用可选的 Java 十进制格式进行转换。 截断任何浮点数、双精度数。  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___


<a name="toShort" ></a>

### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
将任何数字或字符串转换为短值。 可以使用可选的 Java 十进制格式进行转换。 截断任何整数、长整型数、浮点数、双精度数。  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___


<a name="toString" ></a>

### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
将基元数据类型转换为字符串。 对于数字和日期，可以指定格式。 如果未指定，则选择系统默认值。对数字使用 Java 十进制格式。 有关所有可能的日期格式，请参阅 Java SimpleDateFormat；默认格式为 yyyy-MM-dd。  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___


<a name="toTimestamp" ></a>

### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
根据可选的时间戳格式将字符串转换为时间戳。 如果省略时间戳，则使用默认模式 yyyy-[M]M-[d]d hh:mm:ss[.f...]。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 时间戳支持高达 999 毫秒的精度值。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="toUTC" ></a>

### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
将时间戳转换为 UTC。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 默认为当前时区。 如需查看可用的格式，请参阅 Java 的 `SimpleDateFormat` 类。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  



<a name="translate" ></a>

### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
将字符串中的一组字符替换为另一组字符。 对字符进行 1 对 1 的替换.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___


<a name="trim" ></a>

### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，剪裁掉第二个参数中指定的任何字符。  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___


<a name="true" ></a>

### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
始终返回 true 值。 如果存在名为“true”的列，则使用函数 `syntax(true())`。  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___


<a name="typeMatch" ></a>

### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
匹配列的类型。 只可在模式表达式中使用。number 匹配短整型数、整数、长整型数、双精度数、浮点数或小数，integral 匹配短整型数、整数、长整型数，fractional 匹配双精度数、浮点数、小数，datetime 匹配日期或时间戳类型。  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___


<a name="unescape" ></a>

### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
根据格式取消转义字符串。 可接受的格式的文本值为 "json"、"xml"、"ecmascript"、"html"、"java"。
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___


<a name="unfold" ></a>

### <code>unfold</code>
<code><b>unfold (<i>&lt;value1&gt;</i>: array) => any</b></code><br/><br/>
将数组展开为一组行并在每行中重复剩余列的值。
*   ``unfold(addresses) => any``
*   ``unfold( @(name = salesPerson, sales = salesAmount) ) => any``
___  


<a name="unhex" ></a>

### <code>unhex</code>
<code><b>unhex(<i>\<value1\></i>: string) => binary</b></code><br/><br/>
对字符串表示形式的二进制值进行反向十六进制转换。 这可以用于联合 sha2 和 md5 从字符串表示形式转换为二进制表示形式 *    ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*    ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``



<a name="union" ></a>

### <code>union</code>
<code><b>union(<i>&lt;value1&gt;</i>: array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
返回 2 个数组中不同项的并集。
* ``union([10, 20, 30], [20, 40]) => [10, 20, 30, 40]``
___  
  


<a name="upper" ></a>

### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
将字符串大写。  
* ``upper('bojjus') -> 'BOJJUS'``  
___


<a name="uuid" ></a>

### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
返回生成的 UUID。  
* ``uuid()``  
___


<a name="variance" ></a>

### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的方差。  
* ``variance(sales)``  
___


<a name="varianceIf" ></a>

### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的方差。  
* ``varianceIf(region == 'West', sales)``  
___


<a name="variancePopulation" ></a>

### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的总体方差。  
* ``variancePopulation(sales)``  
___


<a name="variancePopulationIf" ></a>

### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的总体方差。  
* ``variancePopulationIf(region == 'West', sales)``  
___


<a name="varianceSample" ></a>

### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列的无偏方差。  
* ``varianceSample(sales)``  
___


<a name="varianceSampleIf" ></a>

### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根据条件获取列的无偏方差。  
* ``varianceSampleIf(region == 'West', sales)``  



<a name="weekOfYear" ></a>

### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期的年份周次。  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___


<a name="weeks" ></a>

### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
周数的持续时间（以毫秒为单位）。  
* ``weeks(2) -> 1209600000L``  
___


<a name="xor" ></a>

### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
逻辑 XOR 运算符。 与 ^ 运算符相同。  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___


<a name="year" ></a>

### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取日期的年份值。  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="next-steps"></a>后续步骤

[了解如何使用表达式生成器](concepts-data-flow-expression-builder.md)。