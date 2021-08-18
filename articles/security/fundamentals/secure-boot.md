---
title: 固件安全启动 - Azure 安全性
description: Azure 固件安全启动技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e14bd525f2f81cfdf0a61f29df919f5579f0be17
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893935"
---
# <a name="secure-boot"></a>安全启动

安全启动是[统一可扩展固件接口](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) 的一项功能，要求在加载之前先验证所有低级别的固件和软件组件。 在启动过程中，UEFI 安全启动会检查每个启动软件的签名，包括 UEFI 固件驱动程序（也称为 ROM 选项）、可扩展固件接口 (EFI) 应用程序以及操作系统驱动程序和二进制文件。 如果签名有效或被原始设备制造商 (OEM) 认可，则计算机将启动，而固件将控制权交给操作系统。

## <a name="components-and-process"></a>组件和过程

安全启动依赖于以下关键组件：

- 平台密钥 (PK) - 在平台所有者 (Microsoft) 和固件之间建立信任。 公共部分是 PKpub，而专用部分是 PKpriv。
- 密钥注册密钥数据库 (KEK) - 在 OS 和平台固件之间建立信任。 公共部分是 KEKpub，而专用部分是 KEKpriv。
- 签名数据库 (db) - 包含经授权可与平台固件交互的固件和软件代码模块的受信任签名者（公共密钥和证书）的摘要。
- 吊销的签名数据库 (dbx) - 包含吊销的代码模块摘要，这些摘要已被标识为恶意、易受攻击、已泄露或不受信任。 如果签名 DB 和吊销的签名 DB 中包含哈希，则以吊销的签名数据库为准。

下图和过程说明了如何更新这些组件：

![显示安全启动组件的关系图。](./media/secure-boot/secure-boot.png)

OEM 在制造时将安全启动摘要存储在计算机的非易失性 RAM (NV-RAM) 中。

1. 签名数据库 (db) 使用 UEFI 应用程序、操作系统加载程序（例如 Microsoft 操作系统加载程序或引导管理器）和受信任的 UEFI 驱动程序的签名者或图像哈希进行填充。
2. 吊销的签名数据库 (dbx) 使用不再受信任的模块的摘要进行填充。
3. 密钥注册密钥 (KEK) 数据库使用签名密钥进行填充，这些签名密钥可用于更新签名数据库和吊销的签名数据库。 可以通过以下更新来编辑数据库：使用正确密钥签名的更新，或使用固件菜单的实际存在的授权用户的更新。
4. 添加 db、dbx 和 KEK 数据库并完成最终的固件验证和测试后，OEM 会锁定固件，使其无法编辑，并生成平台密钥 (PK)。 PK 可用于对 KEK 的更新进行签名或关闭安全启动。

在启动过程的每个阶段，都会计算固件、引导加载程序、操作系统、内核驱动程序和其他启动链工件的摘要，并将其与可接受的值进行比较。 不允许加载被视为不受信任的固件和软件。 因此，可以阻止低级恶意软件注入或预启动恶意软件攻击。

## <a name="secure-boot-on-the-azure-fleet"></a>Azure 机群上的安全启动
如今，所有已载入并部署到 Azure 计算机群以托管客户工作负载的计算机都来自启用了安全启动的工厂车间。 硬件扩展和集成管道的每个阶段都有针对性的工具和流程，以确保安全启动的启用不会因意外或恶意意图而还原。

验证 db 和 dbx 摘要正确无误可确保：

- 引导加载程序存在于其中一个 db 条目中
- 引导加载程序的签名有效
- 主机通过受信任的软件启动

 通过验证 KEKpub 和 PKpub 的签名，可以确认只有受信任方才有权修改被视为受信任软件的软件的定义。 最后，通过确保安全启动处于活动状态，可以验证是否正在强制执行这些定义。

## <a name="next-steps"></a>后续步骤
若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [固件安全性](firmware.md)
- [平台代码完整性](code-integrity.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)