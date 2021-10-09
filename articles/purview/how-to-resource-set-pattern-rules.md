---
title: 如何创建资源集模式规则
description: 了解如何创建资源集模式规则，以覆盖资产分组到资源集中的方式
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c2d6f90bf9a3e3b3f7c03db1ab0a4fbf258703eb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218862"
---
# <a name="create-resource-set-pattern-rules"></a>创建资源集模式规则

大规模数据处理系统通常将单个表作为多个文件存储在存储中。 此概念在 Azure Purview 中使用资源集来表示。 资源集是数据目录中的单个对象，表示存储中的大量资产。 若要了解详细信息，请参阅[了解资源集](concept-resource-sets.md)。

扫描存储帐户时，Azure Purview 使用一组定义的模式来确定一组资产是否为资源集。 在某些情况下，Azure Purview 的资源集分组可能无法准确反映数据资产。 通过资源集模式规则，可自定义或替代 Azure Purview 检测哪些资产可分组为资源集的方式，以及其在目录中的显示方式。

以下源类型当前支持模式规则：
- Azure Data Lake Storage Gen2
- Azure Blob 存储
- Azure 文件
- Amazon S3

必须启用高级资源集功能集才能创建资源集模式规则。 若要了解详细信息，请参阅[了解高级资源集](concept-resource-sets.md#advanced-resource-sets)。

## <a name="how-to-create-a-resource-set-pattern-rule"></a>如何创建资源集模式规则

按照以下步骤创建新的资源集模式规则：

1. 转到数据映射。 从“源管理”标题下的菜单中选择“模式规则”。 选择“+ 新建”以创建新规则集。

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="创建新的资源集模式规则" border="true":::

1. 输入资源集模式规则的范围。 选择要在其上创建规则集的存储帐户类型和存储帐户的名称。 每组规则都是相对于“文件夹路径”字段中指定的文件夹路径范围应用的。

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="创建资源集模式规则配置" border="true":::

1. 若要为配置范围输入规则，请选择“+ 新建规则”。

1. 在以下字段中输入以创建规则：

   1. **规则名称：** 配置规则的名称。 此字段对规则适用于的资产没有影响。

   1. **限定名称：** 一个限定路径，该路径使用文本、动态替换器和静态替换器的组合将资产与配置规则匹配。 此路径与配置规则的范围有关。 有关如何指定限定名称的详细说明，请参阅下面的[语法](#syntax)部分。

   1. **显示名称：** 资产的显示名称。 此字段可选。 使用纯文本和静态替换器来自定义资产在目录中的显示方式。 有关更多详细说明，请参阅下面的[语法](#syntax)部分。

   1. **不作为资源集分组：** 如果启用该项，则不会将匹配的资源分组到资源集中。

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="创建新配置规则。" border="true":::

1. 选择“添加”以保存规则。

> [!NOTE]
> 创建模式规则后，所有新扫描将在引入过程中应用规则。 数据目录中的现有资产将通过后台进程进行更新，该过程可能需要几个小时。 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> 模式规则语法

创建资源集模式规则时，请使用下面的语法来指定应用规则的资产。

### <a name="dynamic-replacers-single-brackets"></a>动态替换器（单括号）

在模式规则中，将单括号用作“动态替换器”。 使用格式 `{<replacerName:<replacerType>}` 指定限定名称中的动态替换器。 如果匹配，动态替换器将用作分组条件，指示资产应表示为资源集。 如果将资产分组到资源集中，则资源集限定路径将包含指定替换器的 `{replacerName}`。

例如，如果 `folder1/file-1.csv` 和 `folder2/file-2.csv` 两个资产与规则 `{folder:string}/file-{NUM:int}.csv` 匹配，则资源集将是单个实体 `{folder}/file-{NUM}.csv`。

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>特例：未分组到资源集时使用的动态替换器

如果为模式规则启用了“不分组为资源集”，则替换器名称是可选字段。 `{:<replacerType>}` 是有效的语法。 例如，`file-{:int}.csv` 将成功匹配 `file-1.csv` 和 `file-2.csv`，并创建两个不同的资产而不是资源集。

### <a name="static-replacers-double-brackets"></a>静态替换器（双括号）

在模式规则的限定名称中，将双括号用作“静态替换器”。 使用格式 `{{<replacerName>:<replacerType>}}` 指定限定名称中的静态替换器。 如果匹配，则每组唯一静态替换器值将创建不同的资源集分组。

例如，如果 `folder1/file-1.csv` 和与 `folder2/file-2.csv` 两个资产与规则 `{{folder:string}}/file-{NUM:int}.csv` 匹配，则将创建 `folder1/file-{NUM}.csv` 和 `folder2/file-{NUM}.csv` 两个资源集。

静态替换器可用于指定与模式规则匹配的资产的显示名称。 在规则的显示名称中使用 `{{<replacerName>}}` 将使用资产名称中的匹配值。

### <a name="available-replacement-types"></a>可用替换类型

下面是可在静态和动态替换器中使用的可用类型：

| 类型 | 结构 |
| ---- | --------- |
| string | 由 1 个或多个 Unicode 字符（包括空格等分隔符）组成的序列。 |
| int | 由 1 个或多个 0-9 ASCII 字符组成的序列，可以以 0 为前缀（例如 0001）。 |
| guid | 作为 [RFC 4122](https://tools.ietf.org/html/rfc4122) 中 defineddefa 的 UUID 的 32 或 8-4-4-4-12 字符串表示形式序列。 |
| date | [RFC 3339](https://tools.ietf.org/html/rfc3339) 中指定的由 6 个或 8 个 0-9 ASCII 字符组成的序列，可以选择使用分隔符 yyyymmdd、yyyy-mm-dd、yymmdd 和 yy-mm-dd。 |
| time | [RFC 3339](https://tools.ietf.org/html/rfc3339) 中指定的由 4 个或 6 个 0-9 ASCII 字符组成的序列，可以选择使用分隔符 HHmm、HH:mm、HHmmss 和 HH:mm:ss。 |
| timestamp | [RFC 3339](https://tools.ietf.org/html/rfc3339) 中指定的由 12 个或 14 个 0-9 ASCII 字符组成的序列，可以选择使用分隔符 yyyy-mm-ddTHH:mm、yyyymmddhhmm、yyyy-mm-ddTHH:mm:ss 和 yyyymmddHHmmss。 |
| boolean | 可以包含“true”或“false”，不区分大小写。 |
| 数字 | 由 0 或多个 0-9 ASCII 字符组成的序列，可以以 0 为前缀（例如 0001），后跟一个点“.”（可选）以及由 1 个或更多 0-9 ASCII 字符组成的序列，可以以 0 为后缀（例如 .100） |
| hex | 由集 0-1 和 A-F 的组成的包含 1 个或多个 ASCII 字符的序列，该值可以以 0 为前缀 |
| 区域设置 | 与 [RFC 5646](https://tools.ietf.org/html/rfc5646) 中指定的语法匹配的字符串。 |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>应用资源集模式规则的顺序

下面是应用模式规则的操作顺序：

1. 如果资产与两个规则匹配，则将优先应用更具体的范围。 例如，范围 `container/folder` 中的规则将在范围 `container` 中的规则之前应用。

1. 特定范围内规则的顺序。 这可以在 UX 中进行编辑。

1. 如果某个资产与任何指定的规则都不匹配，则将应用默认的资源集试探法。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

将 SAP 数据提取到完整和增量负载中

#### <a name="inputs"></a>输入

文件：

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>模式规则

**范围：** `https://myazureblob.blob.core.windows.net/bar/`

**显示名称：** “外部客户”

**限定名称：** `customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**资源集：** true

#### <a name="output"></a>输出

一个资源集资产

**显示名称：** 外部客户

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>示例 2

avro 格式的 IoT 数据

#### <a name="inputs"></a>输入

文件：

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>模式规则

**范围：** `https://myazureblob.blob.core.windows.net/bar/`

规则 1

**显示名称：** “machine-89”

**限定名称：** `raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**资源集：** true

规则 2

**显示名称：** “machine-90”

**限定名称：** `raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**资源集：** true

#### <a name="outputs"></a>输出

2 个资源集

资源集 1

**显示名称：** machine-89

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

资源集 2

**显示名称：** machine-90

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>示例 3

avro 格式的 IoT 数据

#### <a name="inputs"></a>输入

文件：

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>模式规则

**范围：** `https://myazureblob.blob.core.windows.net/bar/`

**显示名称：** “Machine-{{machineid}}”

**限定名称：** `raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**资源集：** true

#### <a name="outputs"></a>输出

资源集 1

**显示名称：** machine-89

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

资源集 2

**显示名称：** machine-90

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>示例 4

不分组到资源集中

#### <a name="inputs"></a>输入

文件：

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>模式规则

**范围：** `https://myazureblob.blob.core.windows.net/bar/`

**显示名称：** `Machine-{{machineid}}`

**限定名称：** `raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**资源集：** false

#### <a name="outputs"></a>输出

4 项个人资产

资产 1

**显示名称：** machine-89

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

资产 2

**显示名称：** machine-89

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

资产 3

**显示名称：** machine-89

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

资产 4

**显示名称：** machine-90

**限定名称：** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>后续步骤

从[注册和扫描 Azure Data Lake Gen2 存储帐户](register-scan-adls-gen2.md)开始。