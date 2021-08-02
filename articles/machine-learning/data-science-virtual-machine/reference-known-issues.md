---
title: 参考：已知问题与故障排除
titleSuffix: Azure Data Science Virtual  Machine
description: 获取 Azure Data Science Virtual Machine 的已知问题、解决方法和故障排除的列表。
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/27/2021
ms.openlocfilehash: 8e3393f236152e591b054ee2d7c4c2753c2ff670
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408742"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 的已知问题和故障排除方法

本文可帮助你查找和更正使用 Azure Data Science Virtual Machine 时可能遇到的错误或失败。

## <a name="prompted-for-password-when-running-sudo-command-ubuntu"></a>运行 sudo 命令时提示输入密码 (Ubuntu)

在 Ubuntu 计算机上运行 `sudo` 命令时，系统可能会要求你再次输入密码，以确认你确实是登录用户。 这是预期行为，也是 Linux 系统（如 Ubuntu）中的默认行为。
但是，在某些情况下，不需要重复身份验证，因为这很麻烦。

若要在大多数情况下禁用重新身份验证，可以在终端中运行以下命令。

`echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

重启终端后，sudo 不会再次要求登录，并且会认为来自会话登录的身份验证已足够。

## <a name="accessing-sql-server-windows"></a>访问 SQL Server (Windows)

尝试连接到预安装的 SQL Server 实例时，可能会遇到“登录失败”错误。 若要成功连接到 SQL Server 实例，需要在管理员模式下 运行要连接的程序，例如 SQL Server Management Studio (SSMS)。 管理员模式是必需的，因为根据 DSVM 的默认设置，只允许管理员进行连接。

## <a name="python-package-installation-issues"></a>Python 包安装问题

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>在 Linux 上安装具有 pip 中断依赖项的包

安装包时，请使用 `sudo pip install` 而不是 `pip install`。

## <a name="disk-encryption-issues"></a>磁盘加密问题

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁盘加密失败

Ubuntu DSVM 目前不支持 Azure 磁盘加密 (ADE)。 可以考虑配置 [Azure 托管磁盘的服务器端加密](../../virtual-machines/disk-encryption.md)作为一种解决方法。

## <a name="tool-appears-disabled"></a>工具显示为已禁用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V 在 Windows DSVM 上不起作用

该 Hyper-V 最初在 Windows 上不起作用是预期的行为。 为了保障启动性能，我们禁用了一些服务。 若要启用 Hyper-V：

1. 请在 Windows DSVM 中打开搜索栏
1. 键入“服务”，
1. 将所有 Hyper-V 服务设置为“手动”
1. 将“Hyper-V 虚拟机管理”设置为“自动”

最终屏幕应如下所示：

   ![启用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)