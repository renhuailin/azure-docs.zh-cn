---
title: BareMetal for Oracle 的修补注意事项
description: 了解适用于 BareMetal Infrastructure for Oracle 的操作系统/内核修补注意事项。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 1c7a4a4f9e2395bbe29ab8bf27b18afb21f98917
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578561"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>BareMetal for Oracle 的修补注意事项

本文介绍 BareMetal Infrastructure for Oracle 的重要的操作系统/内核修补注意事项。

为了实现适当的网络性能和系统稳定性，请安装 eNIC 和 fNIC 驱动程序的特定于 OS 的版本，如以下兼容性表中所示。 

服务器交付到具有兼容版本的客户。 但在操作系统 (OS)/内核修补期间，驱动程序可以回滚到默认的驱动程序版本。 因此，请务必确认适当的驱动程序版本运行以下 OS/内核修补操作。

| OS 供应商 | OS 包版本 | 固件版本 | eNIC 驱动程序 | fNIC 驱动程序 |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>后续步骤

了解 BareMetal for Oracle 的以太网配置。

> [!div class="nextstepaction"]
> [BareMetal for Oracle 的以太网配置](oracle-baremetal-ethernet.md)

