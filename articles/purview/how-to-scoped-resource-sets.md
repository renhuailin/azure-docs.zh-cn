---
title: 如何：创建作用域内资源集配置
description: 了解如何创建作用域资源集配置规则，以覆盖资产如何分组到资源集中
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 8d7d482f38d58c8d6a8959acb51c94c0fb814697
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668429"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>创建作用域内资源集配置规则

大规模数据处理系统通常将单个表存储在磁盘上作为多个文件。 此概念在 Azure 监控范围中使用资源集来表示。 资源集是数据目录中的单个对象，用于表示存储中的大量资产。 若要了解详细信息，请参阅 [了解资源集](concept-resource-sets.md)。

在扫描存储帐户时，Azure 监控范围使用一组定义的模式来确定一组资产是否为资源集。 在某些情况下，Azure 监控范围的资源集分组可能无法准确反映数据空间。 作用域内的资源集规则允许你自定义或重写 Azure 监控范围如何检测哪些资产被分组为资源集，以及它们在目录中的显示方式。

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>如何创建作用域内资源集配置

按照以下步骤创建新的作用域内资源集配置：

1. 请前往管理中心。 从菜单中选择 " **作用域内资源集** "。 单击 " **+ 新建** " 以创建新的配置规则集。
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="创建新的作用域内资源集规则" border="true":::

1. 输入作用域资源集配置的作用域。 选择要在其上创建规则集的存储帐户类型和存储帐户的名称。 每组规则相对于 " **文件夹路径** " 字段中指定的文件夹路径范围应用。 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="创建新的作用域内资源集规则" border="true":::

1. 若要为配置范围输入规则，请选择 " **+ 新建规则**"。
1. 在以下字段中输入以创建规则：
    1. **规则名称：** 配置规则的名称。 此字段对规则应用到的资产不起作用。
    1. **限定名：** 一个限定路径，该路径使用文本、动态 replacers 和静态 replacers 的组合将资产与配置规则匹配。 此路径相对于配置规则的作用域。 有关如何指定限定名称的详细说明，请参阅下面的 [语法](#syntax) 部分。 
    1. **显示名称：** 资产的显示名称。 此字段是可选字段。 使用纯文本和静态 replacers 自定义资产在目录中的显示方式。 有关更多详细说明，请参阅下面的 [语法](#syntax) 部分。
    1. **不作为资源集分组：** 如果启用，则不会将匹配的资源分组到资源集中。 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="创建新的作用域内资源集规则" border="true"::: 
1. 单击 " **添加**" 保存规则。 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> 作用域的资源集语法

创建作用域内资源集规则时，请使用以下语法来指定应用规则的规则。

### <a name="dynamic-replacers-single-brackets"></a>动态 replacers (单个方括号) 

在作用域内的资源集规则中，将单个方括号用作 **动态 replacers** 。 使用格式指定限定名称中的动态替换器 `{<replacerName:<replacerType>}` 。 如果匹配，动态 replacers 将用作分组条件，指示资产应表示为资源集。 如果将资产分组到资源集中，则资源集限定路径将包含 `{replacerName}` 指定替换器的位置。

例如，如果有两个资产， `folder1/file-1.csv` 并与 `folder2/file-2.csv` 规则匹配 `{folder:string}/file-{NUM:int}.csv` ，则资源集将是单个实体 `{folder}/file-{NUM}.csv` 。

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>特例：当未分组到资源集中时，动态 replacers

如果为作用域内的资源集规则启用了 " *不分组为资源集* "，则替换器名称是一个可选字段。 `{:<replacerType>}` 是有效的语法。 例如， `file-{:int}.csv` 将成功匹配和， `file-1.csv` `file-2.csv` 并创建两个不同的资产，而不是资源集。

### <a name="static-replacers-double-brackets"></a>静态 replacers (双括号) 

在作用域内的资源集规则的限定名称中，双括号用作 **静态 replacers** 。 使用格式指定限定名称中的静态替换器 `{{<replacerName>:<replacerType>}}` 。 如果匹配，则每组唯一静态替换器值将创建不同的资源集分组。

例如，如果有两个资产 `folder1/file-1.csv` 和与 `folder2/file-2.csv` 规则匹配 `{{folder:string}}/file-{NUM:int}.csv` ，则将创建两个资源集 `folder1/file-{NUM}.csv` 和 `folder2/file-{NUM}.csv` 。

静态 replacers 可用于指定资产与作用域内资源集规则的显示名称。 `{{<replacerName>}}`在规则的显示名称中使用将使用资源名称中的匹配值。

### <a name="available-replacement-types"></a>可用替换类型

下面是可在静态和动态 replacers 中使用的可用类型：

| 类型 | 结构 |
| ---- | --------- |
| 字符串 | 包含1个或多个 Unicode 字符的序列，包括空格等分隔符。 |
| int | 1个或多个 0-9 ASCII 字符的序列，它可以是0前缀 (例如 0001) 。 |
| GUID | 作为 defineddefa 的 UUID 的一系列32或8-4-4-4-12 字符串表示形式 https://tools.ietf.org/html/rfc4122 |
| date | 包含6个或 8 0-9 个 ASCII 字符的序列，其分隔符如下： yyyymmdd，yyyy-mm-dd，yymmdd，yy https://tools.ietf.org/html/rfc3339 |
| time | 一系列4或 6 0-9 ASCII 字符，带有可选分隔符： HHmm，HH： mm，HHmmss，HH： mm： ss https://tools.ietf.org/html/rfc3339 |
| timestamp | 一系列带有可选分隔符的12或 14 0-9 ASCII 字符： yyyy-mm-Yyyy-mm-ddthh： mm，yyyymmddhhmm，yyyy-mm-Yyyy-mm-ddthh： mm： ss，yyyymmddHHmmss 中指定的 https://tools.ietf.org/html/rfc3339 |
| boolean | 可以包含 "true" 或 "false"，不区分大小写。 |
| 数字 | 一系列0个或更多的 0-9 ASCII 字符， (例如 0001) 后跟一个句点 "."）。 以及一系列1个或多个 0-9 ASCII 字符， (例如 100)  | 
| hex | 来自集0-1 和 A-f 的1个或多个 ASCII 字符的序列，该值可以是0作为前缀 |
| 区域设置 | 与中指定的语法匹配的字符串 https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>应用了作用域的资源集规则的顺序。

下面是应用作用域内资源集规则的操作顺序：

1. 如果资产与两个规则匹配，则更具体的作用域将优先。 例如，作用域中的规则 `container/folder` 将应用于作用域内的规则 `container` 。 
1. 特定范围内的规则顺序。 这可以在 UX 中进行编辑。
1. 如果某个资产与任何指定的规则都不匹配，则将应用默认的资源集试探法。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

SAP 数据提取到完整和增量加载中

#### <a name="inputs"></a>输入

文件：
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


#### <a name="scoped-resource-set-rule"></a>作用域资源集规则 

**作用域：**https://myazureblob.blob.core.windows.net/bar/

**显示名称：** "外部客户"

**限定名：**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**资源集：** true

#### <a name="output"></a>输出 

一个资源集资产

**显示名称：** 外部客户

**限定名：**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>示例 2

Avro 格式的 IoT 数据

#### <a name="inputs"></a>输入 

文件：
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>作用域的资源集规则 

**作用域：**https://myazureblob.blob.core.windows.net/bar/

规则 1

**显示名称：** "计算机-89"

**限定名：**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**资源集：** true

规则 2

**显示名称：** "计算机-90"

**限定名：**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*资源集： true* 

#### <a name="outputs"></a>Outputs 

2个资源集 

资源集1

**显示名称：** 计算机-89

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

资源集2

**显示名称：** 计算机-90

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>示例 3

Avro 格式的 IoT 数据

#### <a name="inputs"></a>输入 

文件：
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>作用域资源集规则 

**作用域：**https://myazureblob.blob.core.windows.net/bar/

**显示名称：** "Machine {{machineid}}"

**限定名：**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**资源集：** true

#### <a name="outputs"></a>Outputs 

资源集1

**显示名称：** 计算机-89

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

资源集2

**显示名称：** 计算机-90

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>示例 4

不分组到资源集中

#### <a name="inputs"></a>输入 

文件：
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>作用域资源集规则 

**作用域：**https://myazureblob.blob.core.windows.net/bar/

**显示名称：** "Machine {{machineid}}"

**限定名：**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**资源集：** false

#### <a name="outputs"></a>Outputs 

4个人资产

资产1

**显示名称：** 计算机-89

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

资产2

**显示名称：** 计算机-89

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

资产3

**显示名称：** 计算机-89

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

资产4

**显示名称：** 计算机-90

**限定名：**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>后续步骤

通过 [注册和扫描 Azure Data Lake Gen2 存储帐户](register-scan-adls-gen2.md)开始。