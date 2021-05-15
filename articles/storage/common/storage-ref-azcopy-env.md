---
title: azcopy env | Microsoft Docs
description: 本文提供有关 azcopy env 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ceddc8848df2f8e0456d2b0f4dab66a76e6eff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879116"
---
# <a name="azcopy-env"></a>azcopy env

显示可配置 AzCopy 行为的环境变量。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑从命令行历史记录中清除包含凭据的变量。 若要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="options"></a>选项

|选项|说明|
|--|--|
|-h、--help|显示 env 命令的帮助内容。 |
|--show-sensitive|显示 sensitive/secret 环境变量。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串  | 指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全起见，应只在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
