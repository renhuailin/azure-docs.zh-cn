---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "68361539"
---
### <a name="bastion-tier"></a>堡垒层

堡垒主机是一个可选组件，你可以将其用作跳转服务器来访问应用程序和数据库实例。 可以为堡垒主机 VM 分配公共 IP，但建议使用本地网络设置 ExpressRoute 连接或站点到站点 VPN，以便进行安全访问。 此外，只应为传入流量打开 SSH（Linux：端口 22）或 RDP（Windows Server：端口 3389）。 为实现高可用性，请在两个可用性区域或单个可用性集中部署一个堡垒主机。

你还可在 VM 上启用 SSH 代理转发，这样便可通过转发来自堡垒主机的凭据来访问虚拟网络中的其他 VM。 或者使用 SSH 隧道来访问其他实例。

下面是代理转发的示例：

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

此命令连接到堡垒，然后立即再次运行 `ssh`，这样你就可以获得目标实例上的终端。 如果群集配置不同，则可能需要在目标实例上指定非根用户。 `-A` 参数转发代理连接，以便自动使用本地计算机上的私钥。 请注意，代理转发是一个链，因此第二个 `ssh` 命令还包含 `-A`，以便所有从目标实例启动的后续 SSH 连接也使用本地私钥。