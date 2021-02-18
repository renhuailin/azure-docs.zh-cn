---
title: 配置 Azure 前门标准/高级规则集匹配条件
description: 本文提供了 Azure 前门标准/高级规则集可用的各种匹配条件的列表。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098736"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure 前门标准/高级 (预览) 规则集匹配条件

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本教程演示如何在 Azure 门户中使用第一组规则创建规则集。 在 Azure 前门标准/高级 [规则集中](concept-rule-set.md)，规则由零个或多个匹配条件和操作组成。 本文详细说明了可在 Azure 前门标准/高级规则集中使用的匹配条件。

规则的第一部分是一个匹配条件或一组匹配条件。 一个规则最多可包含 10 个匹配条件。 匹配条件指定要对其执行所定义操作的特定请求类型。 如果使用多个匹配条件，则匹配条件使用 AND 逻辑组合在一起。 对于支持多个值（标记为“空格分隔”）的所有匹配条件，将采用“OR”运算符。

例如，可以使用匹配条件：

* 基于特定的 IP 地址、国家或地区筛选请求。
* 按标头信息筛选请求。
* 筛选来自移动设备或桌面设备的请求。
* 筛选请求文件名称和文件扩展名的请求。
* 筛选请求 URL、协议、路径、查询字符串、post 参数等的请求。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

以下匹配条件可用于 Azure 前门标准/高级规则集：

## <a name="device-type"></a>设备类型

识别请求是从移动设备发出的还是从桌面设备发出的。  

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | 移动、桌面

## <a name="post-argument"></a>Post 参数

根据为请求中使用的 POST 请求方法定义的参数识别请求。

#### <a name="required-fields"></a>Required fields

参数名称 | 操作员 | 参数值 | 大小写转换
--------------|----------|----------------|---------------
字符串 | [操作员列表](#operator-list) | 字符串、整数 | 小写、大写

## <a name="query-string"></a>查询字符串

标识那些包含特定查询字符串参数的请求。 此参数设置为一个与特定模式匹配的值。 请求 URL 中的查询字符串参数（例如 parameter=value）确定是否满足此条件。 此匹配条件按名称标识查询字符串参数，并接受一个或多个值作为参数值。

#### <a name="required-fields"></a>Required fields

操作员 | 查询字符串 | 大小写转换
---------|--------------|---------------
[操作员列表](#operator-list) | 字符串、整数 | 小写、大写

## <a name="remote-address"></a>远程地址

根据请求者的位置或 IP 地址识别请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|-----------------
地理位置匹配 | 国家/地区代码
IP 匹配 | IP 地址（以空格分隔）
非地理位置匹配 | 国家/地区代码
非 IP 匹配 | IP 地址（以空格分隔）

#### <a name="key-information"></a>重要信息

* 使用 CIDR 表示法。
* 对于多个 IP 地址和 IP 地址块，将运行 "OR" 逻辑。
    * **IPv4 示例**：如果你添加两个 IP 地址 *1.2.3.4* 和 *10.20.30.40*，则在从 address 1.2.3.4 或10.20.30.40 收到的任何请求时，将匹配条件。
    * **IPv6 示例**：如果你添加了两个 IP 地址 *1:2:3:4:5:6:7:8* 和 *10:20:30:40:50:60:70:80*，则在从地址1:2:3:4:5:6:7:8 或10:20:30:40:50:60:70:80 收到的任何请求时，将匹配条件。
* IP 地址块的语法为 IP 基址后跟正斜杠和前缀大小。 例如：
    * **IPv4 示例**：5.5.5.64/26 匹配来自地址 5.5.5.64 到 5.5.5.127 的所有请求。
    * **IPv6 示例**：1:2:3:/48 匹配来自地址 1:2:3:0:0:0:0:0 到 1:2:3: ffff:ffff:ffff:ffff:ffff 的所有请求。

## <a name="request-body"></a>请求正文

根据请求正文中出现的特定文本识别请求。

#### <a name="required-fields"></a>Required fields

操作员 | 请求正文 | 大小写转换
---------|--------------|---------------
[操作员列表](#operator-list) | 字符串、整数 | 小写、大写

## <a name="request-header"></a>请求标头

识别那些使用了特定标头的请求。

#### <a name="required-fields"></a>Required fields

标头名称 | 操作员 | 标头值 | 大小写转换
------------|----------|--------------|---------------
字符串 | [操作员列表](#operator-list) | 字符串、整数 | 小写、大写

## <a name="request-method"></a>请求方法

识别那些使用了指定请求方法的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要信息

只有 GET 请求方法可在 Azure Front Door 中生成缓存的内容。 其他所有请求方法均通过网络代理。

## <a name="request-protocol"></a>请求协议

识别那些使用了指定协议的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | HTTP、HTTPS

## <a name="request-url"></a>请求 URL

识别那些与指定 URL 匹配的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 请求 URL | 大小写转换
---------|-------------|---------------
[操作员列表](#operator-list) | 字符串、整数 | 小写、大写

#### <a name="key-information"></a>重要信息

使用此规则条件时，请确保包含协议信息。 例如： https://www.\<yourdomain\>.com。

## <a name="request-file-extension"></a>请求文件扩展名

识别那些在请求 URL 的文件名中包含了指定文件扩展名的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 分机 | 大小写转换
---------|-----------|---------------
[操作员列表](#operator-list)  | 字符串、整数 | 小写、大写

#### <a name="key-information"></a>重要信息

对于扩展名，请不要包含前导句点，例如，使用 html 而非 .html。

## <a name="request-file-name"></a>请求文件名

识别那些在请求 URL 中包含了指定文件名的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 文件名 | 大小写转换
---------|-----------|---------------
[操作员列表](#operator-list)| 字符串、整数 | 小写、大写

## <a name="request-path"></a>请求路径

识别那些在请求 URL 中包含了指定路径的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 值 | 大小写转换
---------|-------|---------------
[操作员列表](#operator-list) | 字符串、整数 | 小写、大写

## <a name="operator-list"></a><a name = "operator-list"></a>运算符列表

对于那些接受标准运算符列表中的值的规则，以下运算符有效：

* Any
* 等于
* 包含
* 开头为
* 结尾为
* 小于
* 小于或等于
* 大于
* 大于或等于
* 非任何项
* 不包含
* 开头不是
* 结尾不是
* 不小于
* 不小于或等于
* 不大于
* 不大于或等于
* 正则表达式

对于数字运算符（例如“小于”和“大于或等于”），使用的比较基于长度。 匹配条件中的值应该是一个等于要比较的长度的整数。

## <a name="regular-expression"></a>正则表达式

Regex 不支持以下操作：

* 反向引用和捕获子表达式
* 任意零宽度断言
* 子程序引用和递归模式
* 条件模式
* 回溯控制谓词
* \C 单字节指令
* \R 换行符 match 指令
* \K start of match reset 指令
* 标注和嵌入代码
* 原子分组和所有格限定符

## <a name="next-steps"></a>后续步骤

* 了解有关 [规则集](concept-rule-set.md)的详细信息。
* 了解如何 [配置首个规则集](how-to-configure-rule-set.md)。
* 详细了解 [规则集操作](concept-rule-set-actions.md)。
