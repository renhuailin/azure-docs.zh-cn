---
title: 教程：初始化硬件 - Azure FXT Edge Filer
description: 了解如何连接到硬件节点以及在 Azure FXT Edge Filer 节点上设置初始密码。
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6177a9173bf9e85160bdc1561cb6e5aafee18bb8
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414119"
---
# <a name="tutorial-set-hardware-passwords"></a>教程：设置硬件密码

首次打开 Azure FXT Edge Filer 节点电源时，必须设置 root 密码。 硬件节点未随附默认密码。

在设置密码并且 root 用户登录之前，网络端口处于禁用状态。

请在安装节点并布线之后、尝试创建群集之前执行此步骤。

本教程介绍如何连接到硬件节点和设置密码。 还介绍了如何添加 BIOS 设置密码来帮助保护节点。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
>
> * 将键盘和监视器连接到节点，然后打开节点电源
> * 设置 BIOS 设置密码
> * 在此节点上设置 iDRAC 端口和 root 用户的密码
> * 以 root 身份登录

针对要在群集中使用的每个节点重复这些步骤。

完成本教程大约需要 15 分钟。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，请完成以下步骤：

* 在设备机架中[安装](install.md)每个 Azure FXT Edge Filer 节点，并根据[前面的教程](network-power.md)中所述，连接电源线并设置网络访问。
* 找到可接入硬件节点的已连接 USB 端口的键盘以及已连接 VGA 端口的监视器。 （在设置密码之前，节点的串行端口处于非活动状态。）

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>将键盘和监视器连接到节点

以物理方式将监视器和键盘连接到 Azure FXT Edge Filer 节点。

* 将监视器连接到 VGA 端口。
* 将键盘连接到 USB 端口之一。

根据此参考图在机箱背面找到端口。

> [!NOTE]
> 在设置密码之前，串行端口处于非活动状态。

![标有串行、VGA 和 USB 端口的 Azure FXT Edge Filer 背面示意图](media/fxt-back-serial-vga-usb.png)

若要将多个节点连接到相同的外围设备，可以使用 KVM 交换机。

按下正面的电源按钮，打开节点的电源。

![Azure FXT Edge Filer 正面示意图 - 右上角附近标有圆形电源按钮](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>创建 BIOS 设置密码

BIOS 设置密码可防止节点的 BIOS 设置出现意外或未经授权的更改。 此密码不是创建群集所必需的，但强烈建议将其作为群集安全策略的一部分。

创建 BIOS 设置密码：

1. 打开或重启节点，并立即按 F2 打开系统设置实用工具。

1. 在“系统设置主菜单”屏幕上，选择“系统 BIOS” > “系统安全”  。

1. 请确保“密码状态”设置为“未锁定” 。

1. 使用“设密码”字段设置密码。 （若要使用密码，也可以在此屏幕中设置系统 BIOS 密码。）

1. 按 Esc 返回系统 BIOS 屏幕，然后再按一次 Esc。 此时，系统将显示一条消息，提示你保存更改。 如果系统未自动重启，请重启系统以转到常规启动屏幕。<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>设置初始密码

启动时，Azure FXT Edge Filer 节点会在监视器中输出各种消息。 几分钟后，它会显示如下所示的初始设置屏幕：

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

输入的密码用于两个目的：

* 用作此 Azure FXT Edge Filer 节点的临时 root 密码。

  使用此节点创建群集，或者将此节点添加到群集时，此密码将会更改。 群集管理密码（与用户 ``admin`` 关联）也是群集中所有节点的 root 密码。

* 它是 iDRAC/IPMI 硬件管理端口的长期密码。

  请务必记住该密码，以防今后在排查硬件问题时需要使用 IPMI 登录。

输入并确认密码：

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

输入密码后，系统将继续启动。 启动完成后，它会显示 ``login:`` 提示符。

## <a name="sign-in-as-root"></a>以 root 身份登录

使用刚刚设置的密码以 ``root`` 身份登录。

```
login: root
Password:**********
```

以 root 身份登录后，网络端口将处于活动状态，并且会联系 DHCP 服务器来获取 IP 地址。

## <a name="next-steps"></a>后续步骤

现已准备好将节点加入群集。 可以使用该节点创建 Azure FXT Edge Filer 群集，或者[将其添加到现有群集](add-nodes.md)。

> [!div class="nextstepaction"]
> [创建群集](cluster-create.md)
