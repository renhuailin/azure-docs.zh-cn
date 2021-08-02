---
title: Azure FXT Edge Filer 支持的加密密码
description: FXT Edge Filer 群集使用的加密标准列表。
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: b395e0cd2358ef22a223a58bcf8041fa8052b9cd
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111377467"
---
# <a name="supported-encryption-standards-for-azure-fxt-edge-filer"></a>Azure FXT Edge Filer 支持的加密标准

本文档介绍了 Azure FXT Edge Filer 所需的加密标准。 这些标准从操作系统版本 5.1.1.2 开始实施。

这些标准适用于 [Avere vFXT for Azure](../avere-vfxt/index.yml) 以及 Azure FXT Edge Filer。

任何连接到 Azure FXT Edge Filer 缓存或单个节点的管理或基础结构系统必须满足这些标准。

（客户端计算机使用 NFS 装载缓存，因此这些加密要求不适用。 请使用其他合理的措施来确保其安全性。）

## <a name="tls-standard"></a>TLS 标准

* 必须启用 TLS1.2
* 必须禁用 SSL V2 和 V3

如果向后兼容专用对象存储是绝对必要的，可以使用 TLS1.0 和 TLS1.1，但最好将专用存储升级到现代安全标准。 有关详细信息，请与 Microsoft 客户服务与支持部门联系。

## <a name="permitted-cipher-suites"></a>允许的密码套件

Azure FXT Edge Filer 允许对以下 TLS 密码套件进行协商：

* ECDHE-ECDSA-AES128-GCM-SHA256
* ECDHE-ECDSA-AES256-GCM-SHA384
* ECDHE-RSA-AES128-GCM-SHA256
* ECDHE-RSA-AES256-GCM-SHA384
* ECDHE-ECDSA-AES128-SHA256
* ECDHE-ECDSA-AES256-SHA384
* ECDHE-RSA-AES128-SHA256
* ECDHE-RSA-AES256-SHA384

群集管理 HTTPS 接口（用于控制面板 Web GUI 和管理 RPC 连接）仅支持上述密码套件和 TLS1.2。 连接到管理接口时，不支持其他协议或密码套件。

## <a name="ssh-server-access"></a>SSH 服务器访问

这些标准适用于嵌入这些产品中的 SSH 服务器。

SSH 服务器不允许以超级用户 root 身份远程登录。 如果在 Microsoft 客户服务与支持部门的指导下需要进行远程 SSH 访问，请以具有受限 shell 的 SSH“管理员”用户身份登录。

群集 SSH 服务器上提供以下 SSH 密码套件。 请确保任何使用 SSH 连接到群集的客户端都有满足这些标准的最新软件。

### <a name="ssh-encryption-standards"></a>SSH 加密标准

| 类型 | 支持的值 |
|--|--|
| Ciphers | aes256-gcm@openssh.com</br> aes128-gcm@openssh.com</br> aes256-ctr</br> aes128-ctr |
| MAC | hmac-sha2-512-etm@openssh.com</br> hmac-sha2-256-etm@openssh.com</br> hmac-sha2-512</br> hmac-sha2-256 |
| KEX 算法 | ecdh-sha2-nistp521</br> ecdh-sha2-nistp384</br> ecdh-sha2-nistp256</br> diffie-hellman-group-exchange-sha256 |
