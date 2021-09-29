---
title: azcopy jobs remove | Microsoft Docs
description: 本文提供有关 azcopy jobs remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ac68ea66250a906cf42c489c8ad8dcb0fe972003
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673191"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

删除与给定作业 ID 关联的所有文件。

> [!NOTE]
> 可以自定义日志和计划文件的保存位置。 有关详细信息，请参阅 [azcopy env](storage-ref-azcopy-env.md) 命令。

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

## <a name="examples"></a>示例

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>选项

--help - remove 命令的帮助。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

--cap-mbps float - 限制传输速率（以兆位/秒为单位）。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--output-type** 字符串   命令输出的格式。 选项包括：text、json。 默认值为 `text`。 （默认值 `text`）

--trusted-microsoft-suffixes 字符串指定可向其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全起见，应只在此处放置 Microsoft Azure 域。 用分号分隔多个条目。

## <a name="see-also"></a>另请参阅

- [azcopy jobs](storage-ref-azcopy-jobs.md)
