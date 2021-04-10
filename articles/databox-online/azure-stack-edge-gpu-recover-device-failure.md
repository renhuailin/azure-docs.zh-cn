---
title: 从 Azure Stack Edge Pro 设备故障中恢复
description: 描述如何从 Azure Stack Edge Pro 故障设备中恢复。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636634"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>从出现故障的 Azure Stack Edge Pro GPU 设备恢复 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文介绍如何从 Azure Stack Edge Pro GPU 设备上的不可容忍的故障中恢复。 要解决 Azure Stack Edge Pro GPU 设备上不可容忍的故障，需要更换设备。

## <a name="before-you-begin"></a>准备阶段

确保你有：

- 已就设备故障与 Microsoft 支持部门联系，他们已建议更换设备。 
- 已备份设备配置，如[为设备故障做准备](azure-stack-edge-gpu-prepare-device-failure.md)中所述。


## <a name="configure-replacement-device"></a>配置更换设备

当设备遇到不可容忍的故障时，需要订购更换设备。 更换设备的配置步骤保持不变。 

检索从出现故障的设备备份的设备配置信息。 你将使用此信息来配置更换设备。  

遵循以下步骤配置更换设置：

1. 收集[部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所需的信息。 可以使用从以前的设备配置中保存的信息。 
1. 订购与出现故障的设备配置相同的新设备。  若要下达订单，请在 Azure 门户中[创建新的 Azure Stack Edge 资源](azure-stack-edge-gpu-deploy-prep.md#)。
1. [解包](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)、[装载机架](azure-stack-edge-gpu-deploy-install.md#rack-the-device)，并[将电缆接到设备](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。 
1. [连接到设备的本地 UI](azure-stack-edge-gpu-deploy-connect.md)。
1. 使用用于旧设备的 IP 地址配置网络。 使用相同的 IP 地址可以将对环境中使用的任何客户端计算机造成的影响降到最低。 请参阅如何[配置网络设置](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
1. 指定与旧设备相同的设备名称和 DNS 域。 这样，客户端就可以使用相同的设备名称与新设备通信。 请参阅如何[配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。
1. 采用与旧设备相同的方式在新设备上配置证书。 请记住，新设备有一个新的节点序列号。 如果在旧设备上使用了自己的证书，则需要获取新的节点证书。 请参阅如何[配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。
1. 从 Azure 门户获取激活密钥并激活新设备。 请参阅如何[激活设备](azure-stack-edge-gpu-deploy-activate.md)。

你现在已准备好部署在旧设备上运行的工作负载。

## <a name="restore-edge-cloud-shares"></a>还原 Edge 云共享

请按照以下步骤操作，在设备上还原 Edge 云共享上的数据：

1. 使用出现故障的设备上之前创建的共享名称[添加共享](azure-stack-edge-gpu-manage-shares.md#add-a-share)。 请确保在创建共享时，将“选择 blob 容器”设置为“使用现有容器”选项，然后选择之前设备使用的容器。
1. [添加用户](azure-stack-edge-gpu-manage-users.md#add-a-user)，使其有权访问之前的设备。
1. [添加与设备上之前的共享相关联的存储帐户](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account)。 创建 Edge 存储帐户时，请从现有容器中选择并指向映射到在之前设备上映射的 Azure 存储帐户的容器。 设备中的写入到之前设备上的 Edge 存储帐户中的数据都将上传到映射的 Azure 存储帐户中的所选存储容器。
1. [刷新来自 Azure 的共享数据](azure-stack-edge-gpu-manage-shares.md#refresh-shares)。 这会将现有容器中的所有云数据提取到共享中。

## <a name="restore-edge-local-shares"></a>还原 Edge 本地共享

若要为潜在设备故障做好准备，建议部署以下备份解决方案之一，以保护 Kubernetes 或 IoT 工作负载中的本地共享数据：

| 第三方软件           | 解决方案参考信息                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。 |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请联系 Veritas。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |

在完全配置替换设备后，启用设备的本地存储。 

按照以下步骤操作，从本地共享恢复数据：

1. [在设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. [添加回本地共享](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的参考。

## <a name="restore-vm-files-and-folders"></a>还原 VM 文件和文件夹

若要为潜在设备故障做好准备，建议部署以下备份解决方案之一来保护 VM 上的数据：



| 备份解决方案        | 支持的 OS   | 参考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| 适用于 Azure 备份的 Microsoft Azure 恢复服务 (MARS) 代理 | Windows        | [关于 MARS 代理](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 集成、备份和恢复解决方案简介](https://www.cohesity.com/solution/cloud/azure) <br>有关详细信息，请联系 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> 有关详细信息，请联系 Veritas。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |

完全配置更换设备后，可以使用以前使用的 VM 映像重新部署 VM。 

请按照以下步骤来恢复 VM 中的数据：
 
1. 在设备上[从 VM 映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)。 
1. 在 VM 上安装所选的数据保护解决方案。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的参考。

## <a name="restore-a-kubernetes-deployment"></a>还原 Kubernetes 部署

如果通过 Azure Arc 执行了 Kubernetes 部署，则可以在不可容忍的设备故障出现后还原部署。 需要从存储应用程序定义的 `git` 存储库重新部署客户应用程序/容器。 [有关使用 Azure Arc 部署 Kubernetes 的信息](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>后续步骤

- 了解如何[退回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)。
