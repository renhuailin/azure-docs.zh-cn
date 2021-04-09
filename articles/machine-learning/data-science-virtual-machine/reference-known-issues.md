---
title: 参考：已知问题与故障排除
titleSuffix: Azure Data Science Virtual  Machine
description: 获取 Azure Data Science Virtual Machine 的已知问题、解决方法和故障排除的列表。
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "101656850"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 的已知问题和故障排除方法

本文可帮助你查找和更正使用 Azure Data Science Virtual Machine 时可能遇到的错误或失败。

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