---
title: 适用于 Linux 容器的 SSH 访问
description: 可以在 Azure 应用服务中打开 Linux 容器的 SSH 会话。 支持自定义 Linux 容器，并对自定义映像进行了一些修改。
keywords: azure 应用服务, web 应用, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 1e30af51207d525daee5cfd4806877b02b044867
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769919"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>在 Azure 应用服务中打开 Linux 容器的 SSH 会话

[安全外壳 (SSH)](https://wikipedia.org/wiki/Secure_Shell) 通常用于通过命令行终端以远程方式执行管理命令。 Linux 上的应用服务在应用容器中提供 SSH 支持。 

![Linux 应用服务 SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

还可以使用 SSH 和 SFTP 直接从本地开发计算机连接到容器。

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>将 SSH 支持与自定义 Docker 映像配合使用

请参阅[在自定义容器中配置 SSH](configure-custom-container.md#enable-ssh)。

## <a name="open-ssh-session-from-remote-shell"></a>从远程 shell 打开 SSH 会话

> [!NOTE]
> 此功能目前为预览版。
>

使用 TCP 隧道，可以通过经身份验证的 WebSocket 连接在开发计算机与适用于容器的 Web 应用之间创建网络连接。 这样就可以通过所选客户端使用在应用服务中运行的容器打开一个 SSH 会话。

若要开始，需安装 [Azure CLI](/cli/azure/install-azure-cli)。 若要了解它在不安装 Azure CLI 的情况下的工作方式，请打开 [Azure Cloud Shell](../cloud-shell/overview.md)。 

使用 [az webapp remote-connection create](/cli/azure/webapp#az_webapp_create_remote_connection) 命令打开到应用的远程连接。 为应用指定 \<subscription-id>、\<group-name> 和 \_\<app-name>_。

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> 命令末尾的 `&` 只是为了方便起见（如果你是使用 Cloud Shell）。 它在后台运行此进程，因此你可以在同一 shell 中运行下一命令。

> [!NOTE]
> 如果此命令失败，请确保通过以下命令禁用[远程调试](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)：
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

命令输出提供的信息是打开 SSH 会话所需的。

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

请使用本地端口通过所选客户端打开容器的 SSH 会话。 以下示例使用默认的 [ssh](https://ss64.com/bash/ssh.html) 命令：

```bash
ssh root@127.0.0.1 -p <port>
```

出现提示时，请键入 `yes` 继续进行连接。 然后，系统会提示输入密码。 请使用 `Docker!`（此前已向你显示过）。

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

进行身份验证以后，会看到会话欢迎屏幕。

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

现在已连接到连接器。  

请尝试运行 [top](https://ss64.com/bash/top.html) 命令。 应该可以在进程列表中看到应用的进程。 在下面的示例输出中，它带有 `PID 263`。

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>后续步骤

如有问题和疑问，请在 [Azure 论坛](/answers/topics/azure-webapps.html)上发帖。

有关用于容器的 Web 应用的详细信息，请参阅：

* [Introducing remote debugging of Node.js apps on Azure App Service from VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)（使用 VS Code 对 Azure 应用服务上的 Node.js 应用进行远程调试简介）
* [快速入门：在应用服务上运行自定义容器](quickstart-custom-container.md?pivots=container-linux)
* [在 Linux 上的 Azure 应用服务中使用 Ruby](quickstart-ruby.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](faq-app-service-linux.yml)
