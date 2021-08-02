---
title: 使用 Azure CLI 创建和管理 Azure 服务
description: 使用 Azure CLI 为 Azure 备份创建和管理 Azure 服务。
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 3581be20082d1c579caad4cf82ce6b2408f01a92
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482269"
---
# <a name="create-and-manage-azure-backup-services-using-azure-command-line-interface-azure-cli"></a>使用 Azure 命令行界面 (Azure CLI) 创建和管理 Azure 备份服务

Azure 命令行接口 (Azure CLI) 是一组用来创建和管理 Azure 资源的命令。 Azure CLI 可帮助实现自动化以及使用 Azure 备份。

本文提供了有关支持对 Azure 备份使用 Azure CLI 的服务的信息，以及指向这些文章的相应链接。

## <a name="document-references-for-cli-supported-azure-services"></a>CLI 支持的 Azure 服务的文档引用

下表列出了可用于受支持的 Azure 服务的 Azure CLI 文档引用。

Azure 服务 | CLI 文档引用
-------------------------- | ---------------------------------
Azure Vault | [删除恢复服务保管库](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli)
Azure 虚拟机 (VM) | <li>[备份 Azure VM](quick-backup-vm-cli.md)</li><li>[还原 Azure VM](tutorial-restore-disk.md)</li><li>[从 Azure VM 备份还原文件](tutorial-restore-files.md)</li><li>[更新现有 VM 备份策略](modify-vm-policy-cli.md)</li><li>[备份和还原适用于 Azure VM 的选择性磁盘](selective-disk-backup-restore.md#using-azure-cli)</li>
Azure 文件共享 | <li>[备份 Azure 文件共享](backup-afs-cli.md)</li><li>[还原 Azure 文件共享](restore-afs-cli.md)</li><li>[管理 Azure 文件共享备份](manage-afs-backup-cli.md)</li>
SAP HANA | <li>[备份 Azure VM 中的 SAP HANA 数据库](tutorial-sap-hana-backup-cli.md)</li><li>[还原 Azure VM 中的 SAP HANA 数据库](tutorial-sap-hana-restore-cli.md)</li><li>[管理 Azure VM 中的 SAP HANA 数据库](tutorial-sap-hana-manage-cli.md)</li>

