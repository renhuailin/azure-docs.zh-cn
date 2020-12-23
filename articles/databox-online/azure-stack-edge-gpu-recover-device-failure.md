---
title: 从 Azure Stack Edge Pro 设备故障中恢复
description: 描述如何从 Azure Stack Edge Pro 故障设备进行恢复。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: e5734591bfc48469eacc1ad39cbb89f3850bfc8c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97367060"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>从失败的 Azure Stack Edge Pro GPU 设备恢复 

本文介绍如何在 Azure Stack Edge Pro GPU 设备上从不可承受的故障中恢复。 Azure Stack Edge Pro GPU 设备上无法容忍的故障需要更换设备。

## <a name="before-you-begin"></a>在开始之前

确保你有：

- 已与有关设备故障的 Microsoft 支持部门联系，并已建议更换设备。 
- 备份设备配置，如 [准备设备故障](azure-stack-edge-gpu-prepare-device-failure.md)中所述。


## <a name="configure-replacement-device"></a>配置替换设备

当设备遇到不可容忍的故障时，需要订购更换设备。 更换设备的配置步骤保持不变。 

检索从失败的设备备份的设备配置信息。 你将使用此信息来配置替换设备。  

请按照以下步骤配置更换设备：

1. 收集 [部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所需的信息。 你可以使用从以前的设备配置中保存的信息。 
1. 将同一配置的新设备与失败的设备进行排序。  若要下订单，请在 Azure 门户中 [创建新的 Azure Stack Edge 资源](azure-stack-edge-gpu-deploy-prep.md#) 。
1. [解包](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)、将设备 [安装到机架上](azure-stack-edge-gpu-deploy-install.md#rack-the-device) 并 [连接设备](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。 
1. [连接到设备的本地 UI](azure-stack-edge-gpu-deploy-connect.md)。
1. 使用用于旧设备的相同 IP 地址来配置网络。 使用相同的 IP 地址将对环境中使用的任何客户端计算机造成的影响降到最低。 请参阅如何 [配置网络设置](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
1. 分配与旧设备相同的设备名称和 DNS 域。 这样，客户端就可以使用相同的设备名称与新设备通信。 请参阅如何 [配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。
1. 采用与旧设备相同的方式在新设备上配置证书。 请记住，新设备有一个新的节点序列号。 如果在旧设备上使用了自己的证书，则需要获取新的节点证书。 请参阅如何 [配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。
1. 从 Azure 门户获取激活密钥并激活新设备。 请参阅如何 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。

你现在已准备好部署在旧设备上运行的工作负荷。

## <a name="restore-edge-cloud-shares"></a>还原边缘云共享

请按照以下步骤操作，在设备上还原 Edge 云共享上的数据：

1. [添加](azure-stack-edge-j-series-manage-shares.md#add-a-share) 以前在失败设备上创建的共享名称相同的共享。 请确保在创建共享时， **选择 "blob 容器** " 设置为 " **使用现有** 选项"，然后选择与先前设备一起使用的容器。
1. [添加](azure-stack-edge-j-series-manage-users.md#add-a-user) 有权访问上一设备的用户。
1. 添加与先前设备上的共享相关联的[存储帐户](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account)。 创建边缘存储帐户时，请选择 "从现有容器" 并指向映射到在上一设备上映射的 Azure 存储帐户的容器。 写入先前设备上的边缘存储帐户的设备中的所有数据都已上传到映射的 Azure 存储帐户中的选定存储容器。
1. [刷新共享](azure-stack-edge-j-series-manage-shares.md#refresh-shares) Azure 中的数据。 这会将现有容器中的所有云数据提取到共享中。

## <a name="restore-edge-local-shares"></a>还原边缘本地共享

若要为潜在设备故障做好准备，你可能已部署以下备份解决方案，以保护 Kubernetes 或 IoT 工作负荷中的本地共享数据：

| 第三方软件           | 对解决方案的引用                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。 |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请与 Veritas 联系。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |

完全配置替换设备后，启用设备进行本地存储。 

请按照以下步骤从本地共享恢复数据：

1. [在设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. [添加本地共享](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) 。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的引用。

## <a name="restore-vm-files-and-folders"></a>还原 VM 文件和文件夹

若要为潜在设备故障做好准备，你可能已部署以下备份解决方案之一来保护 Vm 上的数据：



| 备份解决方案        | 支持的 OS   | 参考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| 用于 Azure 备份的 (MARS) 代理 Microsoft Azure 恢复服务 | Windows        | [关于 MARS 代理](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 集成，备份 & 恢复解决方案 brief](https://www.cohesity.com/solution/cloud/azure) <br>有关详细信息，请联系 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> 有关详细信息，请与 Veritas 联系。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |

完全配置替换设备后，你可以使用以前使用的 VM 映像重新部署 Vm。 

请按照以下步骤来恢复 Vm 中的数据：
 
1. 从设备上的[vm 映像部署 vm](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 。 
1. 在 VM 上安装所选的数据保护解决方案。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的引用。

## <a name="restore-a-kubernetes-deployment"></a>还原 Kubernetes 部署

如果通过 Azure Arc 执行了 Kubernetes 部署，则可以在无法容忍的设备出现故障后还原部署。 你需要从 `git` 存储应用程序定义的存储库中重新部署客户应用程序/容器。 [有关将 Kubernetes 与 Azure Arc 一起部署的信息](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>后续步骤

- 了解如何 [返回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)。
