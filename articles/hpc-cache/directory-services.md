---
title: 在 Azure HPC 缓存中使用扩展组
description: 如何配置目录服务，使客户端能够访问 Azure HPC 缓存中的存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563362"
---
# <a name="configure-directory-services"></a>配置目录服务

“目录服务”设置可让 Azure HPC 缓存使用外部源对访问后端存储的用户进行身份验证。

如果工作流包含属于 16 个以上的组的 NFS 存储目标和客户端，则可能需要启用“扩展组”。

单击此按钮以启用扩展组后，必须选择由 Azure HPC 缓存用来获取用户和组凭据的源。

* [Active Directory](#configure-active-directory) - 从外部 Active Directory 服务器获取凭据。 不能使用 Azure Active Directory 来完成此任务。
* [平面文件](#configure-file-download) - 从网络位置下载 `/etc/group` 和 `/etc/passwd` 文件。
* [LDAP](#configure-ldap) - 从轻型目录访问协议 (LDAP) 兼容的源获取凭据。

> [!NOTE]
> 确保缓存可以从其安全子网内部访问其组信息源。<!-- + details/examples -->

“下载的用户名”字段显示最新组信息的下载状态。

![门户中目录服务设置页的屏幕截图，其中“扩展组”选择了“是”选项，并标有“下载源”的下拉菜单已打开](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>配置 Active Directory

本部分介绍如何设置缓存，以从外部 Active Directory (AD) 服务器获取用户和组凭据。

在“Active Directory 详细信息”下提供以下值：

* **主要 DNS** - 指定可供缓存用来解析 AD 域名的域名服务器的 IP 地址。

* **辅助 DNS**（可选的）- 输入当主服务器不可用时要使用的名称服务器的地址。

* **AD DNS 域名** - 提供缓存为了获取凭据而要加入到的 AD 服务器的完全限定域名。

* **缓存服务器名称(计算机帐户)** - 设置当此 HPC 缓存加入 AD 域时要向其分配的名称。 请指定用于方便识别此缓存的名称。 该名称的长度最多为 15 个字符，可以包含大写或小写字母、数字和连字符 (-)。

在“凭据”部分，提供可供 Azure HPC 缓存用来访问 AD 服务器的 AD 管理员用户名和密码。 此信息在存储时会经过加密，且不可查询。

单击页面顶部的按钮保存设置。

![“下载详细信息”部分的屏幕截图，其中填充了 Active Directory 值](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>配置文件下载

若要下载包含用户和组信息的文件，需要提供这些值。 这些文件必须采用标准的 Linux/UNIX `/etc/group` 和 `/etc/passwrd` 格式。

* **用户文件 URI** - 输入 `/etc/passwrd` 文件的完整 URI。
* **组文件 URI** - 输入 `/etc/group` 文件的完整 URI。

![用于下载平面文件的“下载详细信息”部分的屏幕截图](media/group-download-details-file.png)

## <a name="configure-ldap"></a>配置 LDAP

若要使用非 AD LDAP 源获取用户和组凭据，请填充这些值。 如需有关这些值的帮助，请咨询 LDAP 管理员。

* **LDAP 服务器** - 输入要使用的 LDAP 服务器的完全限定域名或 IP 地址。 <!-- only one, not up to 3 -->

* **LDAP 基准 DN** - 以 DN 格式指定 LDAP 域的基准可分辨名称。 如果你不知道基准 DN，请咨询 LDAP 管理员。

要使 LDAP 正常工作，服务器和基准 DN 是唯一必需的设置，但其他选项能使连接更安全。

![目录服务设置页的 LDAP 配置区域的屏幕截图](media/group-download-details-ldap.png)

在“安全访问”部分，可对 LDAP 连接启用加密和证书验证。 单击“是”启用加密后，可以使用以下选项：

* **验证证书** - 设置此选项后，将根据下面的 URI 字段中的证书颁发机构验证 LDAP 服务器的证书。

* **CA 证书 URI** - 指定权威证书的路径。 此路径可以是 CA 已验证的证书或自签名证书的链接。 必须填写此字段才能使用外部验证的证书设置。

* **自动下载证书** - 若要在提交这些设置后立即尝试下载证书，请选择“是”。

若要使用静态凭据来确保 LDAP 安全性，请填写“凭据”部分。 此信息在存储时会经过加密，且不可查询。

* **绑定 DN** - 输入用于向 LDAP 服务器进行身份验证的绑定可分辨名称。 （使用 DN 格式。）
* **绑定密码** - 提供绑定 DN 的密码。

## <a name="next-steps"></a>后续步骤

* 在[装载 Azure HPC 缓存](hpc-cache-mount.md)中详细了解客户端访问
* 如果你的凭据未正确下载，请咨询凭据源的管理员。 如果需要，请创建[支持票证](hpc-cache-support-ticket.md)。
