---
title: azcopy jobs resume | Microsoft Docs
description: 本文提供有关 azcopy jobs resume 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503331"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

恢复具有给定作业 ID 的现有作业。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

## <a name="options"></a>选项

|选项|说明|
|--|--|
|--destination-sas 字符串|给定作业 ID 的目标的目标 SAS。|
|--exclude 字符串|筛选器：恢复作业时排除这些失败的传输。 文件应由 ";" 分隔。|
|-h、--help|显示 resume 命令的帮助内容。|
|--include 字符串|筛选器：恢复作业时仅包括这些失败的传输。 文件应由 ";" 分隔。|
|--source-sas 字符串 |作业 ID 的源的源 SAS。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   |指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全起见，应只在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy jobs](storage-ref-azcopy-jobs.md)
