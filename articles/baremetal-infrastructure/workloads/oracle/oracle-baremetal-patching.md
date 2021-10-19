---
title: BareMetal for Oracle 的修补注意事项
description: 了解适用于 BareMetal Infrastructure for Oracle 的操作系统/内核修补注意事项。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: c842ad56e6777817e5db84e51a4d1302aebf5a63
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657485"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>BareMetal for Oracle 的修补注意事项

本文介绍 BareMetal Infrastructure for Oracle 的重要的操作系统 (OS)/内核修补注意事项。

为了保证网络性能和系统稳定性，请安装 eNIC 和 fNIC 驱动程序的特定于 OS 的版本。 请参阅以下兼容性表。 

服务器交付到具有兼容版本的客户。 但是，在 OS/内核修补期间，驱动程序可以回退到默认的驱动程序版本。 因此，请务必确认在进行 OS/内核修补之后，确认运行的驱动程序版本是正确的。

| OS 供应商 | OS 包版本 | 固件版本 | eNIC 驱动程序 | fNIC 驱动程序 |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>后续步骤

了解 BareMetal for Oracle 的以太网配置。

> [!div class="nextstepaction"]
> [BareMetal for Oracle 的以太网配置](oracle-baremetal-ethernet.md)

