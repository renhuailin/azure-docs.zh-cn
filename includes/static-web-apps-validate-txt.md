---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/10/2021
ms.author: buhollan
ms.openlocfilehash: 89e61c1cb472f7b6bb62f1f2bd84d083e103d650
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066100"
---
#### <a name="validate-txt-record"></a>验证 TXT 记录

1. 返回到 Azure 门户中的“验证 + 配置”屏幕。

在此步骤中，Azure 将通过 DNS 提供程序自动验证 TXT 记录。 验证过程完成后，添加的域旁边会出现一个绿色指示器。

:::image type="content" source="../articles/static-web-apps/media/custom-domain/txt-record-ready.png" alt-text="绿色指示器指示 TXT 记录已验证":::

当你在“自定义域”屏幕中的域旁看到绿色指示器时，可以完成第二步，即如果你要添加根域，则添加 ALIAS 记录，如果要添加子域，则添加 CNAME 记录。
