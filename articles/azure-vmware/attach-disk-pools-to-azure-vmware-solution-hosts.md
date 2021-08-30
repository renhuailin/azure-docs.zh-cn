---
title: 将磁盘池附加到 Azure VMware 解决方案主机（预览版）
description: 了解如何将通过 iSCSI 目标呈现的磁盘池附加为 Azure VMware 解决方案私有云的 VMware 数据存储。 配置数据存储后，可在其上创建卷，然后将卷附加到 VMware 实例。
ms.topic: how-to
ms.date: 07/13/2021
ms.openlocfilehash: fefb014f221a121259c0b8d6411de362e11a63c0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745650"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>将磁盘池附加到 Azure VMware 解决方案主机（预览版）

[Azure 磁盘池](../virtual-machines/disks-pools.md)为 Azure 磁盘所支持的应用程序和工作负载提供永久性块存储。 可以使用磁盘作为 Azure VMware 解决方案的永久性存储，以实现最佳成本和性能。 例如，如果你要托管存储密集型工作负载，可以使用磁盘池进行纵向扩展，而不是扩展群集。 还可以使用磁盘将数据从本地或主要 VMware 环境复制到辅助站点的磁盘存储。 我们支持将[超级磁盘](../virtual-machines/disks-types.md#ultra-disk)和[高级 SSD](../virtual-machines/disks-types.md#premium-ssd) 呈现为数据存储，以便能够独立于 Azure VMware 解决方案主机缩放存储。  

>[!IMPORTANT]
>Azure VMware 解决方案（预览版）上的 Azure 磁盘池目前以公共预览版提供。
>此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 托管磁盘附加到在 Azure VMware 解决方案资源组下部署的一个 iSCSI 控制器虚拟机。 磁盘作为存储目标部署到磁盘池，每个存储目标在 iSCSI 目标下显示为一个 iSCSI LUN。 可将磁盘池公开为作为数据存储连接到 Azure VMware 解决方案主机的 iSCSI 目标。 一个磁盘池呈现为作为存储目标添加的所有基础磁盘的单个终结点。 每个磁盘池只能有一个 iSCSI 控制器。

以下示意图显示了磁盘池如何与 Azure VMware 解决方案主机配合工作。 每个 iSCSI 控制器使用标准 Azure 协议访问托管磁盘，Azure VMware 解决方案主机可以通过 iSCSI 访问 iSCSI 控制器。


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="该示意图显示磁盘池如何与 Azure VMware 解决方案主机配合工作。" border="false":::


## <a name="supported-regions"></a>支持的区域

只能将磁盘池连接到同一区域中的 Azure VMware 解决方案私有云。 有关受支持的区域列表，请参阅[区域可用性](../virtual-machines/disks-pools.md#regional-availability)。  如果你的私有云部署在不受支持的区域，可将其重新部署到受支持的区域。 将 Azure VMware 解决方案私有云和磁盘池共置在一起可以实现最佳性能和最低网络延迟。


## <a name="prerequisites"></a>先决条件

- 确定工作负载的可伸缩性和性能要求。 有关详细信息，请参阅[规划 Azure 磁盘池](../virtual-machines/disks-pools-planning.md)。

- 部署[配置了虚拟网络](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute)的 [Azure VMware 解决方案私有云](deploy-azure-vmware-solution.md)。 有关详细信息，请参阅[网络规划清单](tutorial-network-checklist.md)和[为 VMware 私有云配置网络](tutorial-configure-networking.md)。 

   - 如果选择了超级磁盘，请对 Azure VMware 解决方案私有云使用“超级性能”，然后[启用 ExpressRoute FastPath](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。

   - 如果选择了高级 SSD，请对 Azure VMware 解决方案私有云使用“标准(1 Gbps)”。  必须使用 Standard\_DS##\_v3 来托管 iSCSI。  对于 Dsv3 系列，如果遇到配额问题，可以请求按 Azure VM 系列提高 [vCPU 配额限制](../azure-portal/supportability/per-vm-quota-requests.md)。

- 作为 iSCSI 目标部署并公开的、用作后备存储的磁盘池（每个磁盘显示为单独的 LUN）。 有关详细信息，请参阅[部署 Azure 磁盘池](../virtual-machines/disks-pools-deploy.md)。

   >[!IMPORTANT]
   > 磁盘池必须部署在 VMware 群集所在的同一订阅中，并且必须附加到 VMware 群集所在的同一 VNET。

## <a name="attach-a-disk-pool-to-your-private-cloud"></a>将磁盘池附加到私有云
将通过 iSCSI 目标呈现的磁盘池附加为 Azure VMware 解决方案私有云的 VMware 数据存储。

>[!IMPORTANT]
>在公共预览版中，只能将磁盘池附加到测试群集或非生产群集。

1. 检查订阅是否已注册到 `Microsoft.AVS`：

   ```azurecli
   az provider show -n "Microsoft.AVS" --query registrationState
   ```

   如果尚未注册，请注册：

   ```azurecli
   az provider register -n "Microsoft.AVS"
   ```

1. 检查订阅是否已注册到 Microsoft.AVS 中的 `CloudSanExperience` AFEC：

   ```azurecli
   az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
   ```

   - 如果尚未注册，请注册：

      ```azurecli
      az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      ```

      注册可能需要大约 15 分钟才能完成，可以检查此操作的当前状态：
      
      ```azurecli
      az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
      ```

      >[!TIP]
      >如果注册停滞在中间状态超过 15 分钟，请取消注册，然后重新注册：
      >
      >```azurecli
      >az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >```

1. 检查是否已安装 `vmware `扩展： 

   ```azurecli
   az extension show --name vmware
   ```

   - 如果已安装该扩展，请检查版本是否为 3.0.0。 如果安装的是旧版本，请更新该扩展：

      ```azurecli
      az extension update --name vmware
      ```

   - 如果尚未安装该扩展，请安装它：

      ```azurecli
      az extension add --name vmware
      ```

3. 使用 `Microsoft.StoragePool` 提供的 iSCSI 目标在 Azure VMware 解决方案私有云群集中创建并附加 iSCSI 数据存储：

   ```bash
   az vmware datastore disk-pool-volume create --name iSCSIDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name lun0
   ```

   >[!TIP]
   >可以显示有关数据存储的帮助：
   >
   >   ```azurecli
   >   az vmware datastore -h
   >   ```
   

4. 显示私有云群集中 iSCSI 数据存储的详细信息：
   
   ```azurecli
   az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
   ```

5. 列出私有云群集中的所有数据存储：

   ```azurecli
   az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="delete-an-iscsi-datastore-from-your-private-cloud"></a>从私有云中删除 iSCSI 数据存储

删除私有云数据存储时，不会删除磁盘池资源。 无需为此操作安排维护时段。

1. 关闭 VM，然后删除与 iSCSI 数据存储关联的所有对象，包括：

   - VM（从清单中删除）

   - 模板

   - 快照

2. 删除私有云数据存储：

   ```azurecli
   az vmware datastore delete --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="next-steps"></a>后续步骤

将磁盘池附加到 Azure VMware 解决方案主机后，接下来可以了解：

- [管理 Azure 磁盘池](../virtual-machines/disks-pools-manage.md )。  部署磁盘池后，可以执行各种管理操作。 可以在磁盘池中添加或删除磁盘，更新 iSCSI LUN 映射，或添加 ACL。

- [删除磁盘池](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool)。 删除磁盘池时，受管理资源组中的所有资源也将随之删除。

- [在磁盘上禁用 iSCSI 支持](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support)。 如果你在磁盘池上禁用 iSCSI 支持，该磁盘池实际上不再可用。

- [将磁盘池移到其他订阅](../virtual-machines/disks-pools-move-resource.md)。 将 Azure 磁盘池移到其他订阅，这涉及到移动该磁盘池本身、包含的磁盘、受管理资源组和所有资源。 

- [磁盘池故障排除](../virtual-machines/disks-pools-troubleshoot.md)。 查看与 Azure 磁盘池（预览版）相关的常见故障代码。 此文还提供了可行的解决方法，并阐述了磁盘池的不同状态。
