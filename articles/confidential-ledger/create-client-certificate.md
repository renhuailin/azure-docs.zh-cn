---
title: 创建具有 Microsoft Azure 机密账本的客户端证书
description: 创建具有 Microsoft Azure 机密账本的客户端证书
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 6d384ea07c49a8b7936e3cdf634b9775847018f0
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384928"
---
# <a name="creating-a-client-certificate"></a>创建客户端证书

机密账本 API 需要基于客户端证书的身份验证。 只有在创建账本或更新账本期间添加到允许列表的证书才可用于调用机密账本功能 API。

需要使用 PEM 格式的证书。 可以创建多个证书，并使用账本更新 API 添加或删除它们。

## <a name="openssl"></a>OpenSSL

建议使用 OpenSSL 生成证书。 如果已安装 git，则可以在 git shell 中运行 OpenSSL。 否则，可以为 OS 安装 OpenSSL。

- **Windows**：安装 [用于 Windows 的 chocolatey](https://chocolatey.org/install)，在管理模式下打开 PowerShell 终端窗口，然后运行 `choco install openssl`。 或者，可以直接从[此处](http://gnuwin32.sourceforge.net/packages/openssl.htm)安装用于 Windows 的 OpenSSL。
- **Linux**：运行 `sudo apt-get install openssl`

然后，可以通过在 Bash 或 PowerShell 终端窗口中运行 `openssl` 来生成证书：

```bash
openssl req -new -newkey rsa:1024 -days 365 -nodes -x509 -keyout user_privk.pem -out user_cert.pem -subj=/CN="User Client Certificate"
```

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)