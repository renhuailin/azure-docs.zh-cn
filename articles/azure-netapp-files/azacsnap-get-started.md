---
title: 适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具入门 |Microsoft Docs
description: 提供有关安装可与 Azure NetApp 文件一起使用的 Azure 应用程序一致快照工具的指南。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: c8532637e695b506e372817e6f4531f9a323936b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632580"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Azure 应用程序一致的快照工具入门 (预览版) 

本文提供了有关安装可与 Azure NetApp 文件一起使用的 Azure 应用程序一致快照工具的指南。

## <a name="getting-the-snapshot-tools"></a>获取快照工具

建议客户从 Microsoft 获取 [AzAcSnap 安装程序](https://aka.ms/azacsnapdownload) 的最新版本。

自安装文件具有关联的 [AzAcSnap 安装程序签名文件](https://aka.ms/azacsnapdownloadsignature) ，该文件使用 Microsoft 的公钥进行签名，以允许对下载的安装程序进行 GPG 验证。

完成下载后，请按照本指南中的步骤进行安装。

### <a name="verifying-the-download"></a>验证下载

安装程序（每个文件可下载）都具有一个具有文件扩展名的关联 PGP 签名文件 `.asc` 。 此文件可用于确保下载的安装程序是经过验证的 Microsoft 提供的文件。 此处提供了用于对 Linux 包进行签名的 Microsoft PGP 公钥 (<https://packages.microsoft.com/keys/microsoft.asc>) ，已用于对签名文件进行签名。

Microsoft PGP 公钥可以导入到用户的本地，如下所示：

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

以下命令信任 Microsoft PGP 公钥：

1. 列出存储区中的密钥。
2. 编辑 Microsoft 密钥。
3. 检查指纹 `fpr`
4. 签署密钥以信任它。

```bash
gpg --list-keys
```

列出的密钥：
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

交互式会话的输出 `gpg` 签名 Microsoft 公钥：
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

可以按如下所示检查安装程序的 PGP 签名文件：

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

有关使用 GPG 的更多详细信息，请参阅 [GNU 隐私手册](https://www.gnupg.org/gph/en/manual/book1.html)。

## <a name="supported-scenarios"></a>支持的方案

可以在以下方案中使用快照工具。

- 单个 SID
- 多个 SID
- HSR
- 横向扩展
- MDC (仅支持单个租户) 
- 单个容器
- SUSE 操作系统
- RHEL 操作系统
- SKU 类型 I
- SKU 类型 II

请参阅 [HANA 大型实例支持的方案](/azure/virtual-machines/workloads/sap/hana-supported-scenario)

## <a name="snapshot-support-matrix-from-sap"></a>SAP 中的快照支持矩阵

提供了以下矩阵作为有关存储快照备份的 SAP 支持 SAP HANA 版本的指导。

| 数据库版本       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|单容器数据库| √       | √      | -      | -      | -      | -      |
|MDC 单租户        | -       | -      | √      | √      | √      | √      |
|MDC 多租户     | -       | -      | -      | -      | -      | √      |
> √ = <small>SAP 支持存储快照</small>

## <a name="important-things-to-remember"></a>要记住的重要事项

- 在设置快照工具之后，连续监视可用的存储空间，并定期删除旧的快照，以避免存储填满。
- 始终使用最新的快照工具。
- 在整个环境中使用相同版本的快照工具。
- 在生产系统中使用之前，请测试快照工具并熟悉命令的必需参数和输出。
- 设置 HANA 用户进行备份时 (此文档) 下面的详细信息，则需要为每个 HANA 实例设置用户。 创建 SAP HANA 的用户帐户以访问 SYSTEMDB (下的 HANA 实例，而不是用于 MDC 的 SID 数据库) 。 在单容器环境中，可以在租户数据库下设置。
- 客户必须提供 SSH 公钥才能访问存储。 必须为每个节点和执行命令的每个用户执行此操作。
- 每个卷的快照数限制为250。
- 如果手动编辑配置文件，请始终使用 Linux 文本编辑器（例如 "vi"），而不使用 Windows 编辑器（例如记事本）。 使用 Windows 编辑器可能会损坏文件格式。
  - `hdbuserstore`为 SAP HANA 用户设置用于与 SAP HANA 通信的。
- 对于 DR：必须先在 DR 节点上测试快照工具，然后才能设置 DR。
- 定期监视磁盘空间，通过 `--trim`   `azacsnap -c backup` 用于 SAP HANA 2 及更高版本的选项来管理自动删除日志。
- **快照的风险** -快照工具只能与配置文件中指定的 SAP HANA 系统的节点进行交互。  如果此节点不可用，则没有机制自动开始与其他节点通信。  
  - 对于 **带有备用方案的 SAP HANA Scale-Out** ，通常是在主节点上安装和配置快照工具。 但如果主节点变为不可用，则备用节点将接管主节点角色。 在这种情况下，实现团队应)  (主节点和备用节点上配置快照工具，以避免丢失任何快照。 在正常状态下，主节点将获取由 crontab 启动的 HANA 快照，但在主节点故障转移后，必须从另一个节点（例如新的主节点 (前备用) ）执行这些快照。 要实现此结果，需要在备用节点上安装快照工具、启用存储通信、hdbuserstore 配置、 `azacsnap.json` 配置和在故障转移之前暂存 crontab 命令。
  - 对于 **SAP HANA HSR HA** 方案，建议在 (主) 和辅助节点上安装、配置和计划快照工具。 然后，如果主节点不可用，则辅助节点将接管辅助节点上创建的快照。 在正常状态下，主节点将使用 crontab 启动的 HANA 快照，而辅助节点将尝试拍摄快照但失败，因为主节点正常运行。  但在主节点故障转移后，将从辅助节点执行这些快照。 为实现此结果，辅助节点需要安装快照工具、启用了存储通信、 `hdbuserstore` 配置、azacsnap.js配置，以及在故障转移前启用了 crontab。

## <a name="guidance-provided-in-this-document"></a>本文档中提供的指南

提供以下指导说明了快照工具的用法。

### <a name="taking-snapshot-backups"></a>拍摄快照备份

- [存储快照的先决条件是什么](azacsnap-installation.md#prerequisites-for-installation)
  - [启用与存储的通信](azacsnap-installation.md#enable-communication-with-storage)
  - [启用与 SAP HANA 的通信](azacsnap-installation.md#enable-communication-with-sap-hana)
- [如何手动拍摄快照](azacsnap-tips.md#take-snapshots-manually)
- [如何设置自动快照备份](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [如何监视快照](azacsnap-tips.md#monitor-the-snapshots)
- [如何删除快照？](azacsnap-tips.md#delete-a-snapshot)
- [如何还原快照](azacsnap-tips.md#restore-a-snapshot)
- [如何还原 `boot` 快照](azacsnap-tips.md#restore-a-boot-snapshot)
- [了解快照有哪些关键事实](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> 同时为单一 SID 和多 SID 测试快照。

### <a name="performing-disaster-recovery"></a>执行灾难恢复

- [DR 安装程序的先决条件是什么](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [如何设置灾难恢复](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [如何监视从主站点到灾难恢复站点的数据复制](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [如何执行到 DR 站点的故障转移？](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>后续步骤

- [安装 Azure 应用程序一致性快照工具](azacsnap-installation.md)
