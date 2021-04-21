---
title: azcopy jobs list | Microsoft Docs
description: 本文提供有关 azcopy jobs list 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 62f6e2999e9d3820404bdd4051817bc9a8785c47
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503382"
---
# <a name="azcopy-jobs-list"></a>azcopy jobs list

显示有关所有作业的信息。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs list [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

## <a name="options"></a>选项

|选项|说明|
|--|--|
|-h、--help|显示 list 命令的帮助内容。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   | 指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全起见，应只在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy jobs](storage-ref-azcopy-jobs.md)
