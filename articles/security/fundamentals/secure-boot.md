---
title: 固件安全启动-Azure 安全性
description: Azure 固件安全启动技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557564"
---
# <a name="secure-boot"></a>安全启动

安全启动是 [统一可扩展固件接口](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) 的一项功能，需要在加载之前验证所有低级固件和软件组件。 在启动过程中，UEFI 安全启动检查每个启动软件的签名，其中包括 UEFI 固件驱动程序 (也称为选项 Rom) 、可扩展固件接口 (EFI) 应用程序以及操作系统驱动程序和二进制文件。 如果签名 (OEM) 的原始设备制造商有效或信任，则计算机将启动，并且固件会向操作系统提供控制权。

## <a name="components-and-process"></a>组件和过程

安全引导依赖于以下关键组件：

- 平台键 (PK) -在平台所有者 (Microsoft) 和固件之间建立信任。 Public 一半是 PKpub，而 private 一半是 PKpriv。
- 密钥注册密钥数据库 (KEK) -在 OS 和平台固件之间建立信任。 Public 一半是 KEKpub，而 private 一半是 KEKpriv。
- 签名数据库 (db) -保存受信任签署者 (公钥和证书的摘要，这些摘要和证书) 授权与平台固件交互的固件和软件模块。
- 已吊销的签名数据库 (.dbx) –持有已被识别为恶意、易受攻击、已泄露或不受信任的代码模块的摘要。 如果哈希位于签名数据库和吊销的签名数据库中，则已吊销的签名数据库采用引用单元。

下图和进程说明了如何更新这些组件：

![显示安全启动组件的关系图。](./media/secure-boot/secure-boot.png)

OEM 在生产时 (NV RAM) 在计算机的非易失性 RAM 上存储安全启动摘要。

1. 签名数据库 (db) 由 UEFI 应用程序、操作系统加载器 (（如 Microsoft 操作系统加载程序或启动管理器) ）和受信任的 UEFI 驱动程序的签名者或映像哈希填充。
2. 已吊销的签名数据库 (.dbx) 会用不再受信任的模块的摘要填充。
3.  (KEK) 数据库的密钥注册密钥使用可用于更新签名数据库和吊销的签名数据库的签名密钥进行填充。 可以通过使用正确的密钥签名的更新或通过使用固件菜单的实际授权用户进行更新来编辑数据库。
4. 添加 db、.dbx 和 KEK 数据库并完成最终固件验证和测试后，OEM 会锁定固件以进行编辑，并 (PK) 生成平台密钥。 PK 可用于对 KEK 的更新进行签名，或关闭安全启动。

在启动过程中的每个阶段，将计算固件、启动程序、操作系统、内核驱动程序和其他启动链项目的摘要，并将其与可接受的值进行比较。 不允许加载不受信任的固件和软件。 因此，可能会阻止低级恶意软件注入或预启动恶意软件攻击。

## <a name="secure-boot-on-the-azure-fleet"></a>在 Azure 汽油上安全启动
如今，载入并部署到用于托管客户工作负荷的 Azure 计算的每台计算机都是在启用了安全启动的工厂楼层中进行的。 在硬件 ring 和集成管道中的每个阶段都有目标工具和过程，以确保不会因意外或恶意目的而恢复安全启动。

验证 db 和 .dbx 摘要是否正确可确保：

- 加载项存在于其中一个 db 条目中
- 引导程序的签名有效
- 主机通过受信任的软件启动

 验证 KEKpub 和 PKpub 的签名后，可以确认只有可信方有权修改被视为受信任的软件的定义。 最后，通过确保安全启动处于活动状态，我们可以验证是否强制执行这些定义。

## <a name="next-steps"></a>后续步骤
若要详细了解如何驱动平台的完整性和安全性，请参阅：

- [固件安全性](firmware.md)
- [标准启动和主机证明](measured-boot-host-attestation.md)
- [项目 Cerberus](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)