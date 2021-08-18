---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/24/2021
ms.author: alkohli
ms.openlocfilehash: bb863a2a6347b32ffcb60984edf2f08d5737d45d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113120944"
---
使用 VM 创建期间传递的专用 IP 连接到 VM。

1. 打开 SSH 会话以连接 IP 地址。

    ```powershell
    ssh -l <username> <ip address>
    ```

1. 根据提示，提供在创建 VM 时使用的密码。

   如需提供 SSH 密钥，请使用此命令。

   `ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236`

   下面是连接到 VM 时的输出示例：

    ```output
    PS C:\WINDOWS\system32> ssh -l myazuser "10.126.76.60"
    The authenticity of host '10.126.76.60 (10.126.76.60)' can't be established.
    ECDSA key fingerprint is SHA256:V649Zbo58zAYMKreeP7M6w7Na0Yf9QPg4SM7JZVV0E4.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added '10.126.76.60' (ECDSA) to the list of known hosts.
    myazuser@10.126.76.60's password:
    Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.18.0-1013-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
     System information disabled due to load higher than 1.0
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud
    
    284 packages can be updated.
    192 updates are security updates. 
       
    The programs included with the Ubuntu system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.
    
    Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
    applicable law.
    
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    myazuser@myazvmfriendlyname:~$ client_loop: send disconnect: Connection reset
    PS C:\WINDOWS\system32>
    ```
