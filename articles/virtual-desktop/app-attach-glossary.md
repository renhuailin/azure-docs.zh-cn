---
title: Azure 虚拟桌面 MSIX 应用附加术语表 - Azure
description: MSIX 应用附加术语和概念的术语表。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9972a7bcff534f49f0c656dd1a9c3f66389c44b5
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114708791"
---
# <a name="msix-app-attach-glossary"></a>MSIX 应用附加术语表

本文介绍与 .MSIX 应用附加相关的关键术语和概念的定义列表。

## <a name="msix-container"></a>MSIX 容器

MSIX 容器是运行 MSIX 应用的位置。 要了解详细信息，请参阅 [MSIX 容器](/windows/msix/msix-container)。

## <a name="msix-application"></a>MSIX 应用程序 

存储在 .MSIX 文件中的应用程序。

## <a name="msix-package"></a>MSIX 包 

MSIX 包是 MSIX 文件或应用程序。

## <a name="msix-share"></a>MSIX 共享

MSIX 共享是保存扩展的 MSIX 包的网络共享。 MSIX 共享必须支持 SMB 3 或更高版本。 主机池系统帐户中的虚拟机 (VM) 还必须可以访问共享。 MSIX 包从 MSIX 共享进行暂存，无需将应用程序文件移到系统驱动器。 

## <a name="msix-image"></a>MSIX 映像

MSIX 映像为 VHD、VHDx 或 CIM 文件，其中包含一个或多个 MSIX 封装应用程序。 每个应用程序都使用 MSIXMGR 工具在 MSIX 映像中提供。

## <a name="repackage"></a>重新打包

重新打包操作将获取非 MSIX 应用程序，并使用 MSIX 打包工具 (MPT) 将其转换为 MSIX。 有关详细信息，请参阅 [MSIX 打包工具概述](/windows/msix/packaging-tool/tool-overview)。

## <a name="expand-an-msix-package"></a>展开 MSIX 包

展开 MSIX 包是一个多步骤过程。 展开操作将获取 MSIX 文件，并将其内容放入VHD(x) 或 CIM 文件中。 

要展开 MSIX 包，请执行以下操作：

1. 获取 MSIX 包（MSIX 文件）。
2. 将 MSIX 文件重命名为 .zip 文件。
3. 将生成的 .zip 文件解压缩到文件夹中。
4. 创建与该文件夹大小相同的 VHD。
5. 装载 VHD。
6. 初始化磁盘。
7. 创建分区。
8. 格式化分区。
9. 将解压缩的内容复制到 VHD 中。
10. 使用 MSIXMGR 工具可对 VHD 的内容应用 ACL。
11. 卸载 VHD(x) 或 [CIM](#cim)。

## <a name="upload-an-msix-package"></a>上传 MSIX 包 

上传 MSIX 包需要将包含扩展的 .MSIX 包的 VHD(x) 或 [CIM](#cim) 上传到 MSIX 共享。

在 Azure 虚拟桌面中，每个 MSIX 共享发生一次上传。 上传包后，同一订阅中的所有主机池都可以引用这个包。

## <a name="add-an-msix-package"></a>添加 MSIX 包

在 Azure 虚拟桌面中，添加 MSIX 包会将其链接到主机池。

## <a name="publish-an-msix-package"></a>发布 MSIX 包 

在 Azure 虚拟桌面中，必须将发布的 MSIX 包分配到 Active Directory 域服务 (AD DS) 或 Azure Active Directory (Azure AD) 用户或用户组。

## <a name="staging"></a>过渡

暂存涉及两个问题：

- 将 VHD(x) 或 [CIM](#cim) 装载到 VM。
- 通知操作系统 MSIX 包可用于注册。

## <a name="registration"></a>注册

注册后，可以为用户提供暂存的 MSIX 包。 注册是基于每个用户的。 如果没有为特定用户显式注册应用程序，则用户将无法运行该应用程序。

有两种类型的注册：定期和延迟。

### <a name="regular-registration"></a>定期注册

在定期注册中，分配给用户的每个应用程序都是完全注册的。 注册在用户登录到会话期间发生，这可能会影响用户开始使用 Azure 虚拟桌面所花费的时间。

### <a name="delayed-registration"></a>延迟注册

在延迟注册中，分配给用户的每个应用程序只是部分注册的。 部分注册是指开始菜单磁贴和双击文件关联已注册。 注册会在用户登录到其会话时进行，因此对开始使用 Azure 虚拟桌面所需的时间的影响最小。 仅当用户在 MSIX 包中运行该应用程序时，才完成注册。

延迟注册当前是 MSIX 应用附加的默认配置。

## <a name="deregistration"></a>注销

取消注册将删除用户的已注册但未运行的 MSIX 包。 当用户从会话中注销时，就会取消注册。 在注销过程中，MSIX 应用附加会将特定于用户的应用程序数据推送到本地用户配置文件。

## <a name="destage"></a>转储

暂存通知操作系统：无法卸载当前未运行且未为任何用户暂存的 MSIX 包或应用程序。 这会在操作系统中删除对这个包的所有引用。

## <a name="cim"></a>CIM

.CIM 是与合成图像文件系统 (CimFS) 相关的新文件扩展名。 安装和卸载 CIM 文件会加快 VHD 文件的安装速度。 CIM 消耗的 CPU 和内存也少于 VHD。

CIM 文件是扩展名为 .CIM 的文件，该文件包含元数据和至少两个其他包含实际数据的文件。 CIM 文件中的文件不包含扩展名。 下表列出了可在 CIM 中找到的示例文件：

| 文件名 | 扩展名 | 大小 |
|-----------|-----------|------|
| VSC | CIM | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264,132 KB |

下表是 VHD 和 CimFS 之间的性能比较。 这些数字是通过测试运行的结果而得出的，这些测试在 DSv4 计算机上运行，包含 500 个各种格式的 300 MB 大小的文件。

|  规格                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| 平均装入时间     | 356 毫秒                     | 255 毫秒      |
| 平均卸载时间   | 1615 毫秒                    | 36 毫秒       |
| 内存占用率 | 6%（共 8 GB）                      | 2%（共 8 GB）       |
| CPU（计算高峰）          | 多次达到最大值 | 无影响 |

## <a name="next-steps"></a>后续步骤

如果想了解有关 MSIX 应用程序附加的更多信息，请查看我们的[概述](what-is-app-attach.md)和[常见问题解答](app-attach-faq.yml)。 或者直接开始[安装应用附加](app-attach.md)。
