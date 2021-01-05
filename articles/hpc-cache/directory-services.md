---
title: 在 Azure HPC 缓存中使用扩展组
description: 如何配置目录服务以便客户端访问 Azure HPC 缓存中的存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/22/2020
ms.author: v-erkel
ms.openlocfilehash: 28265861c98cceaedf7d2662f6526a9f62fe68de
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803759"
---
# <a name="configure-directory-services"></a>配置目录服务

" **目录服务** " 设置允许 Azure HPC 缓存使用外部源来对用户进行身份验证，以便访问后端存储。

如果工作流包括 NFS 存储目标和超过16组成员的客户端，则可能需要启用 **扩展组** 。

单击此按钮以启用扩展组后，你必须选择 Azure HPC 缓存将用于获取用户和组凭据的源。

* [Active Directory](#configure-active-directory) -从外部 Active Directory 服务器获取凭据。 不能将 Azure Active Directory 用于此任务。
* [平面文件](#configure-file-download) - `/etc/group` `/etc/passwd` 从网络位置下载和文件。
* [Ldap](#configure-ldap) -从轻型目录访问协议获取凭据 (与 LDAP) 兼容的源。

> [!NOTE]
> 请确保缓存可以从其安全的子网中访问其组信息源。<!-- + details/examples -->

" **下载的用户名** " 字段显示最新的组信息下载的状态。

![门户中 "目录服务" 页设置页的屏幕截图，为扩展组选择了 "是" 选项，并将标记为 "下载源" 的下拉菜单](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>配置 Active Directory

本部分介绍如何设置缓存，以获取外部 Active Directory (AD) 服务器的用户和组凭据。

在 " **Active directory 详细信息**" 下，提供以下值：

* **主 DNS** -指定可用于解析 AD 域名的域名服务器的 IP 地址。

* **辅助 DNS** (可选的) -如果主服务器不可用，请输入要使用的名称服务器的地址。

* **AD DNS 域名** -提供要用于获取凭据的 ad 服务器的完全限定的域名。

* **(计算机帐户) 缓存服务器名称** -设置在加入 AD 域时将分配给此 HPC 缓存的名称。 指定易于识别为此缓存的名称。 该名称最多可包含15个字符，并且可以包含大写或小写字母、数字和连字符， ( ) 。

在 " **凭据** " 部分中，提供 Azure HPC 缓存可用于访问 AD 服务器的 ad 管理员用户名和密码。 此信息在存储时已加密，无法查询。

通过单击页面顶部的按钮保存设置。

!["下载详细信息" 部分的屏幕截图，其中填充了 Active Directory 值](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>配置文件下载

如果要下载具有用户和组信息的文件，则需要这些值。 文件必须采用标准 Linux/UNIX `/etc/group` 和 `/etc/passwrd` 格式。

* **用户文件 uri** -输入文件的完整 URI `/etc/passwrd` 。
* **组文件 URI** -输入文件的完整 URI `/etc/group` 。

![平面文件下载的下载详细信息部分的屏幕截图](media/group-download-details-file.png)

## <a name="configure-ldap"></a>配置 LDAP

如果要使用非 AD LDAP 源获取用户和组凭据，请填写这些值。 如果需要有关这些值的帮助，请咨询 LDAP 管理员。

* **Ldap 服务器** -输入要使用的 ldap 服务器的完全限定的域名或 IP 地址。 <!-- only one, not up to 3 -->

* **Ldap 基本 DN** -以 DN 格式指定 LDAP 域的基本可分辨名称。 如果你不知道基本 DN，请询问你的 LDAP 管理员。

服务器和基 DN 是使 LDAP 正常工作所必需的设置，但其他选项可使连接更安全。

!["目录服务页面设置" 页的 "LDAP 配置" 区域屏幕截图](media/group-download-details-ldap.png)

在 " **安全访问** " 部分中，可以启用 LDAP 连接的加密和证书验证。 单击 **"是"** 以启用加密后，可以使用以下选项：

* **需要有效证书** -设置此项后，将根据下面的 URI 字段中的证书颁发机构验证 LDAP 服务器的证书。

* **CA 证书 URI** -指定权威证书的路径。 这可以是指向 CA 验证的证书或自签名证书的链接。 此字段对于使用外部验证的证书设置是必需的。

* **自动下载证书** -如果想要在提交这些设置后立即尝试下载证书，请选择 **"是"** 。

如果要使用静态凭据作为 LDAP 安全性，请填写 " **凭据** " 部分。

* **绑定 DN** -输入要用于向 LDAP 服务器进行身份验证的绑定可分辨名称。  (使用 DN 格式。 ) 
* **绑定密码** -为绑定 DN 提供密码。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[安装 AZURE HPC 缓存中的](hpc-cache-mount.md)客户端访问
* 如果你的凭据没有正确下载，请咨询你的凭据源的管理员。 如果需要，请打开 [支持票证](hpc-cache-support-ticket.md) 。
