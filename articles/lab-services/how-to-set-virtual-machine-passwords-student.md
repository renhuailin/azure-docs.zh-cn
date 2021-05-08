---
title: 在 Azure 实验室服务中重置课堂实验室 VM 的密码 | Microsoft Docs
description: 了解如何在 Azure 实验室服务的实验室中为虚拟机 (VM) 重置密码。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436804"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>设置或重置实验室中虚拟机的密码（学生）
本文介绍学生如何为他们的 VM 设置/重置密码。 

## <a name="enable-resetting-of-passwords"></a>启用密码重置
在创建实验室时，实验室所有者可以启用或禁用“所有虚拟机使用相同密码”。 如果已启用了此选项，则学生无法重置密码。 实验室中所有的 VM 都将具有由教师设置的相同密码。 

如果禁用此选项，用户将必须在尝试首次连接到 VM 时设置密码。 学生以后还可以随时重置密码，如本文最后一部分所示。 

## <a name="reset-password-for-the-first-time"></a>首次重置密码
如果已禁用了“所有虚拟机使用相同密码”选项，那么，当用户（学生）在“我的虚拟机”页上的“实验室”磁贴上选择“连接”按钮时，该用户会看到以下用于为 VM 设置密码的对话框  ： 

![重置学生密码](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍后重置密码
学生还可以通过单击实验室磁贴上的溢出菜单（三个竖点）并选择“重置密码”来设置密码 。 

![稍后重置密码](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>后续步骤
若要了解实验室所有者可以配置的其他学生使用选项，请参阅以下文章：[配置学生使用](how-to-configure-student-usage.md)。
