---
title: 登录 Azure 存储资源管理器 | Microsoft Docs
description: 有关登录 Azure 存储资源管理器的文档
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: 92447c4d0c8d0a1fac6e59a11a81dc7beae52516
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451459"
---
# <a name="sign-in-to-storage-explorer"></a>登录到存储资源管理器

登录是使用存储资源管理器访问 Azure 存储资源的建议方式。 通过登录，可利用 Azure AD 支持的权限，例如 RBAC 和 Gen2 POSIX ACL。 

## <a name="how-to-sign-in"></a>登录方法

若要登录存储资源管理器，请打开“连接”对话框。 可以从左侧垂直工具栏打开“连接”对话框，或通过单击“帐户面板”上的“添加帐户...”来打开该对话框  。

打开该对话框后，选择“订阅”作为要连接到的资源类型，然后单击“下一步” 。

现在需要选择要登录的 Azure 环境。 可以从任何已知环境（如 Azure 或 Azure 中国）中进行选择，也可以添加自己的环境。 选择环境后，单击“下一步”。

此时，操作系统的默认 Web 浏览器会启动，并且登录页面会打开。 为获得最佳结果，只要是在使用存储资源管理器，或是至少在执行所有预期 MFA 之前，使此浏览器窗口保持打开状态。 完成登录后，可以返回到存储资源管理器。

## <a name="managing-accounts"></a>管理帐户

可以从“帐户面板”管理和删除已登录的 Azure 帐户。 可以通过单击左侧垂直工具栏上的“管理帐户”按钮来打开“帐户面板” 。

在“帐户面板”中，你会看到已登录的所有帐户。 每个帐户下是：
- 帐户所属的租户
- 对于每个租户，你有权访问的订阅

默认情况下，存储资源管理器仅使你登录你的主租户。 如果要查看其他租户的订阅和资源，需要激活该租户。 若要激活租户，请选中它旁边的复选框。 使用完租户后，可以取消选中其复选框以停用它。 无法停用主租户。

激活租户后，可能需要重新输入凭据，然后存储资源管理器才能从租户加载订阅或访问资源。 必须重新输入凭据通常是因为存在条件访问 (CA) 策略，如多重身份验证 (MFA)。 即使你可能已为另一个租户执行了 MFA，仍可能必须再次执行。 若要重新输入凭据，只需单击“重新输入凭据...”。还可以单击“错误详细信息...”以查看订阅未能加载的确切原因 。

订阅加载后，可以通过选中或取消选中复选框来选择要筛选包含/排除的订阅。

如果要删除整个 Azure 帐户，请单击帐户旁的“删除”。

## <a name="changing-where-sign-in-happens"></a>更改进行登录的位置

默认情况下，登录会在操作系统的默认 Web 浏览器中进行。 使用默认 Web 浏览器登录可简化对通过 CA 策略（如 MFA）保护的资源进行访问的方式。 如果由于某种原因，使用操作系统的默认 Web 浏览器登录不起作用，则可以更改存储资源管理器执行登录的位置或方式。

在“设置”（左侧的齿轮图标） > “应用程序” > “登录”下，查找“登录方式”设置   。 有三个选项：
- 默认 Web 浏览器：登录会在操作系统的默认 Web 浏览器中进行。 建议使用此选项。
- 集成登录：登录会在存储资源管理器窗口中进行。 如果使用 **默认 Web 浏览器** 登录时出现问题，此选项可能非常有用。
- 设备代码流：存储资源管理器会向你提供用于进入浏览器窗口的代码。 不建议使用此选项。 设备代码流与许多 CA 策略不兼容。

## <a name="troubleshooting-sign-in-issues"></a>排查登录问题

如果登录时遇到问题，或在登录后遇到 Azure 帐户问题，请参阅[存储资源管理器故障排除指南中的登录部分](./storage-explorer-troubleshooting.md#sign-in-issues)。

## <a name="next-steps"></a>后续步骤

* [使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md)
* [排查登录问题](./storage-explorer-troubleshooting.md#sign-in-issues)
