---
title: Azure Stack Edge 预览版发行说明 | Microsoft Docs
description: 介绍运行预览版的 Azure Stack Edge 的关键未结问题和解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: bf41c34b205bc9dd7951ad9910c8a9c6df62104d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460868"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>带有 GPU 的 Azure Stack Edge Pro 预览版发行说明

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

以下发行说明列出了带有 GPU 的 Azure Stack Edge Pro 设备 2008 预览版的相关重要未结问题和已解决问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Azure Stack Edge Pro 设备之前，请仔细查看发行说明中包含的信息。

本文适用于以下软件版本 - Azure Stack Edge Pro 2008。

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>新变化

在 Azure Stack Edge 2008 版本中添加了以下新功能。 根据你所运行的特定预览版软件版本，你可能会看到这些功能的子集。 

- **存储类** - 在以前的版本中，你只能通过 SMB 或 NFS 共享来静态预配存储，用于 Azure Stack Edge Pro 设备上运行的 Kubernetes 群集中部署的监控状态的应用程序。 在此版本中，添加了允许动态预配存储的存储类。 有关详细信息，请参阅 [Azure Stack Edge Pro GPU 设备上的 Kubernetes 存储管理](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)。 
- **利用指标服务器的 Kubernetes 仪表板** - 在此版本中，通过指标服务器加载项添加了 Kubernetes 仪表板。 你可以使用仪表板大致了解在 Azure Stack Edge Pro 设备上运行的应用程序，查看 Kubernetes 群集资源的状态，并查看设备上发生的任何错误。 指标服务器聚合设备上的 Kubernetes 资源中的 CPU 和内存使用情况。 有关详细信息，请参阅[使用 Kubernetes 仪表板监视 Azure Stack Edge Pro GPU 设备](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。
- **适用于 Azure Stack Edge Pro 的 Azure Arc** - 从此版本开始，可通过 Azure Arc 在 Azure Stack Edge Pro 设备上部署应用程序工作负载。Azure Arc 是一种混合管理工具，允许你在 Kubernetes 群集上部署应用程序。 有关详细信息，请参阅[在 Azure Stack Edge Pro 设备上通过 Azure Arc 部署工作负载](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。  

## <a name="known-issues"></a>已知问题 

下表汇总了 Azure Stack Edge Pro 设备的已知问题。

| 不是。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | 创建 SQL 数据库需要管理员访问权限。   |执行以下步骤，而不是 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) 中的步骤 1-2。 <ul><li>在设备的本地 UI 中，启用计算接口。 选择“计算”>“端口 #”>“启用计算”>“应用”。</li><li>在客户端计算机上下载 [sqlcmd 实用工具](/sql/tools/sqlcmd-utility)。 </li><li>连接到计算机接口 IP 地址（已启用的端口），并在该地址的末尾添加“1401”。</li><li>最终命令如下所示：sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd"。</li>完成此操作后，当前文档中的步骤 3-4 应相同。 </li></ul> |
| **2.** |刷新| 不支持对通过“刷新”还原的 blob 进行增量更改 |对于 Blob 终结点，刷新后对 blob 进行部分更新可能导致更新不会上载到云中。 例如类似于以下的操作序列：<ul><li>在云中创建 blob。 或从设备中删除之前上传的 blob。</li><li>使用刷新功能，将 blob 从云中刷新到设备中。</li><li>使用 Azure SDK REST API 仅更新部分 blob。</li></ul>这些操作可能导致 blob 的更新部分不在云中更新。 <br>**解决方法**：使用 robocopy 等工具或通过资源管理器或命令行执行的常规文件复制操作来替换整个 blob。|
|**3.**|限制|在带宽限制期间，如果不允许新写入设备，NFS 客户端完成的写入会失败并出现“权限被拒绝”错误。| 此错误将如下所示：<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir：无法创建目录“test”：权限被拒绝|
|**4.**|Blob 存储引入|使用 AzCopy 版本 10 进行 Blob 存储引入时，请使用以下参数运行 AzCopy：`Azcopy <other arguments> --cap-mbps 2000`| 如果没有为 AzCopy 提供这些限制，则可能会将大量请求发送到设备，导致服务出现问题。|
|**5.**|分层存储帐户|使用分层存储帐户时，以下内容适用：<ul><li> 仅支持块 blob。 页 blob 不受支持。</li><li>不提供快照或复制 API 支持。</li><li> 不支持通过 `distcp` 引入 Hadoop 工作负载，因为前者会大量使用复制操作。</li></ul>||
|**6.**|NFS 共享连接|如果将多个进程复制到相同的共享，并且未使用 `nolock` 属性，则在复制过程中可能会出现错误。|必须将 `nolock` 属性传递给 mount 命令，才能将文件复制到 NFS 共享。 例如：`C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`。|
|**7.**|Kubernetes 群集|在运行 kubernetes 群集的设备上应用更新时，kubernetes 虚拟机将重新启动并重新引导。 在这种情况下，更新后只会自动还原使用指定副本部署的 pod。  |如果在未指定副本集的情况下在复制控制器之外创建了单独的 pod，则设备更新后不会自动还原这些 pod。 你需要还原这些 pod。<br>系统会使用副本集替换因任何原因被删除或终止的 pod（例如节点故障或中断性节点升级）。 出于此原因，建议使用副本集，即使你的应用程序只需要一个 Pod。|
|**8.**|Kubernetes 群集|只有 Helm v3 或更高版本支持 Azure Stack Edge Pro 上的 Kubernetes。 有关详细信息，请参阅[常见问题解答：删除 Tiller](https://v3.helm.sh/docs/faq/)。|
|**9.**|Azure Arc + Azure Stack Edge Pro|如果在 Azure Stack Edge Pro 设备上配置 Web 代理，则不支持 Azure Arc 部署。||
|**10.**|Kubernetes |将端口 31000 保留给 Kubernetes 仪表板。 同样，在默认配置中，IP 地址 172.28.0.1 和 172.28.0.10 分别保留用于 Kubernetes 服务和核心 DNS 服务。|不要使用保留的 IP。|
|**11.**|Kubernetes |Kubernetes 当前不允许多协议 LoadBalancer 服务。 例如，需要同时侦听 TCP 和 UDP 的 DNS 服务。 |若要解决 Kubernetes 与 MetalLB 的这一限制，可以在同一 pod 选择器上创建两个服务（一个用于 TCP，一个用于 UDP）。 这些服务使用相同的共享密钥和 loadBalancerIP 来共享相同的 IP 地址。 如果服务数多于可用 IP 地址，则还可以共享 IP。 <br> 有关详细信息，请参阅 [IP 地址共享](https://metallb.universe.tf/usage/#ip-address-sharing)。|
|**12.**|Kubernetes 群集|现有 Azure IoT Edge 商城模块将不会在 Kubernetes 群集上作为 Azure Stack Edge 设备上 IoT Edge 的托管平台运行。|在将这些模块部署到 Azure Stack Edge 设备上之前，需要对其进行修改。 有关详细信息，请参阅“修改来自市场的 Azure IoT Edge 模块以使其在 Azure Stack Edge 设备上运行”。<!-- insert link-->|
|**13.**|Kubernetes |Azure Stack Edge 设备上 Kubernetes 上的 Azure IoT Edge 不支持基于文件的绑定装载。|IoT Edge 使用转换层将 `ContainerCreate` 选项转换为 Kubernetes 构造。 将 `Binds` 映射创建到 `hostpath` 目录或仅创建此映射，因此基于文件的绑定装载不能绑定到 IoT Edge 容器中的路径。|
|**14.**|Kubernetes |如果针对 IoT Edge 使用自己的证书，并将其添加到 Azure Stack Edge 设备上，则不会在 Helm 图表更新过程中选取新证书。|若要解决此问题，请[连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md)。 重启 `iotedged` 和 `edgehub` pod。|
|**15.**|证书 |在某些情况下，本地 UI 中的证书状态可能需要几秒钟才能更新。 |本地 UI 中的以下方案可能会受到影响。<ul><li>“证书”页上的“状态”列 。</li><li>“入门”页面上的“安全性”磁贴 。</li><li>“概述”页中的“配置”磁贴 。</li></ul>  |

## <a name="next-steps"></a>后续步骤

- [准备部署带有 GPU 的 Azure Stack Edge Pro 设备](azure-stack-edge-gpu-deploy-prep.md)