---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968361"
---
1. 在 VM 中，打开命令提示符。

1. 运行以下命令来通用化该 VHD。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    有关详细信息，请参阅 [Sysprep（系统准备）概述](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

1.  在命令完成后，VM 将会关闭。 请勿重启 VM。
