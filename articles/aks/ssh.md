---
title: 通过 SSH 登录到 Azure Kubernetes 服务 (AKS) 群集节点
description: 了解如何与 Azure Kubernetes 服务 (AKS) 群集节点建立 SSH 连接，以完成故障排除和维护任务。
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 8deba37750c6e498e87b87ab2c49e4aa54561475
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743961"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 连接到 Azure Kubernetes 服务 (AKS) 群集节点以进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期内，可能需要访问 AKS 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 可以使用 SSH 访问 AKS 节点（包括 Windows Server 节点）。 还可以[使用远程桌面协议 (RDP) 连接功能连接到 Windows Server 节点][aks-windows-rdp]。 出于安全考虑，AKS 节点不会在 Internet 中公开。 若要通过 SSH 连接到 AKS 节点，需使用 `kubectl debug` 或专用 IP 地址。

本文介绍如何创建与 AKS 节点的 SSH 连接。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

本文假设你已有一个 SSH 密钥。 可以使用 [macOS 或 Linux][ssh-nix] 或 [Windows][ssh-windows] 创建 SSH 密钥。 如果使用 PuTTY Gen 来创建密钥对，请在保存密钥对时使用 OpenSSH 格式而不是默认的 PuTTy 私钥格式（.ppk 文件）。

还需安装并配置 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-the-ssh-connection-to-a-linux-node"></a>创建与 Linux 节点的 SSH 连接

若要创建与 AKS 节点的 SSH 连接，请使用 `kubectl debug` 在节点上运行特权容器。 若要列出节点，请使用 `kubectl get nodes`：

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

使用 `kubectl debug` 在节点上运行容器映像以连接到该映像。

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

此命令在节点上启动特权容器，然后通过 SSH 连接到该容器。

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

此特权容器提供对节点的访问权限。

## <a name="create-the-ssh-connection-to-a-windows-node"></a>创建与 Windows 节点的 SSH 连接

目前，无法通过使用 `kubectl debug` 直接而使用 SSH 连接到 Windows Server 节点。 相反，需要先连接到群集中的另一个节点，然后使用 SSH 从该节点连接到 Windows Server 节点。 此外，可以[使用远程桌面协议 (RDP) 连接连接到 Windows Server 节点][aks-windows-rdp]，而不需使用 SSH。

若要连接到群集中的另一个节点，请使用 `kubectl debug`。 有关详细信息，请参阅[创建与 Linux 节点的 SSH 连接][ssh-linux-kubectl-debug]。

若要从另一个节点创建与 Windows Server 节点的 SSH 连接，请使用创建 AKS 群集时提供的 SSH 密钥和 Windows Server 节点的内部 IP 地址。

打开新的终端窗口，并使用 `kubectl get pods` 获取 `kubectl debug` 启动的 Pod 的名称。

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

在以上示例中，node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx 是 `kubectl debug` 启动的 Pod 的名称。

将 SSH 私钥复制到 `kubectl debug` 创建的 pod 中。 此私钥用于创建与 Windows Server AKS 节点的 SSH。 如果需要，请将 `~/.ssh/id_rsa` 更改为 SSH 私钥的位置：

```azurecli-interactive
kubectl cp ~/.ssh/id_rsa node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx:/id_rsa
```

使用 `kubectl get nodes` 显示 Windows Server 节点的内部 IP 地址：

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

在以上示例中，10.240.0.67 是 Windows Server 节点的内部 IP 地址。

返回到由 `kubectl debug` 启动的终端，并更新复制到 Pod 的专用 SSH 密钥的权限。

```azurecli-interactive
chmod 0400 id_rsa
```

使用内部 IP 地址创建与 Windows Server 节点的 SSH 连接。 AKS 节点的默认用户名为 *azureuser*。 接受提示以继续进行连接。 然后，系统会提供 Windows Server 节点的 bash 提示：

```output
$ ssh -i id_rsa azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

## <a name="remove-ssh-access"></a>删除 SSH 访问

完成后，运行 `exit` 退出 SSH 会话，然后运行 `exit` 退出交互式容器会话。 此容器会话关闭后，将删除用于从 AKS 群集进行 SSH 访问的 pod。

## <a name="next-steps"></a>后续步骤

如需更多的故障排除数据，可以[查看 kubelet 日志][view-kubelet-logs]或[查看 Kubernetes 主节点日志][view-master-logs]。


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: monitor-aks-reference.md#resource-logs
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node