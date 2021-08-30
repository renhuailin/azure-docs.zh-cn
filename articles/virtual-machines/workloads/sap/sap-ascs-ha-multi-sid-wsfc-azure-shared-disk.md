---
title: 使用 WSFC 和 Azure 共享磁盘实现 SAP ASCS/SCS 多 SID HA | Microsoft Docs
description: 使用 WSFC 和 Azure 共享磁盘实现 SAP ASCS/SCS 实例的多 SID 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86546995e0b5481daeff32f2fcdae61a4a69524b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284772"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>使用 Windows Server 故障转移群集和 Azure 共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性

> ![Windows OS][Logo_Windows] Windows
>

本文重点介绍如何通过使用 Azure 共享磁盘在现有 Windows Server 故障转移群集 (WSFC) 群集中安装附加的 SAP ASCS/SCS 群集实例，从单一 ASCS/SCS 安装转移到 SAP 多 SID 配置。 完成此过程后，即已配置 SAP 多 SID 群集。

## <a name="prerequisites-and-limitations"></a>先决条件和限制

目前，可以使用 Azure 高级 SSD 盘作为 SAP ASCS/SCS 实例的 Azure 共享磁盘。 存在以下限制：

-  对于 SAP 工作负载，不支持将 [Azure 超级磁盘](../../disks-types.md#ultra-disk)作为 Azure 共享磁盘。 目前，不能在可用性集中使用 Azure 超级磁盘来放置 Azure VM
-  只有可用性集中的 VM 支持带有高级 SSD 盘的 [Azure 共享磁盘](../../disks-shared.md)。 它在可用性区域部署中不受支持。 
-  Azure 共享磁盘值 [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) 确定可以使用共享磁盘的群集节点数。 通常，对于 SAP ASCS/SCS 实例，你将在 Windows 故障转移群集中配置两个节点，因此 `maxShares` 的值必须设置为“2”。
-  所有 SAP ASCS/SCS 群集 VM 都必须部署在同一 [Azure 邻近位置组](../../windows/proximity-placement-groups.md)中。   
   尽管可以在没有 PPG 的情况下通过 Azure 共享磁盘在可用性集中部署 Windows 群集 VM，但 PPG 可以确保 Azure 共享磁盘与群集 VM 在物理上接近，从而降低 VM 与存储层之间的延迟。    

有关 Azure 共享磁盘限制的更多详细信息，请仔细阅读 Azure 共享磁盘文档的[限制](../../disks-shared.md#limitations)部分。  

> [!IMPORTANT]
> 使用 Azure 共享磁盘部署 SAP ASCS/SCS Windows 故障转移群集时，请注意，部署将在一个存储群集中使用单个共享磁盘运行。 如果部署 Azure 共享磁盘的存储群集出现问题，SAP ASCS/SCS 实例将受到影响。  

> [!IMPORTANT]
> 该设置必须满足以下条件：
> * 每个数据库管理系统 (DBMS) SID 都必须有自己专用的 WSFC 群集。  
> * 属于一个 SAP 系统 SID 的 SAP 应用程序服务器必须有自身的专用 VM。  
> * 不支持在同一个群集中混合使用排队复制服务器 1 和排队复制服务器 2。  


## <a name="supported-os-versions"></a>支持的操作系统版本

支持 Windows Server 2016 和 Windows Server 2019（使用最新的数据中心映像）。

强烈建议使用 Windows Server 2019 Datacenter，原因如下：
- Windows 2019 故障转移群集服务可以感知 Azure
- 增加了 Azure 主机维护的集成和感知，并通过监视 Azure 计划事件改进了体验。
- 可以使用分布式网络名称（它是默认选项）。 因此，群集网络名称无需使用专用 IP 地址。 此外，无需在 Azure 内部负载均衡器上配置此 IP 地址。 

## <a name="architecture"></a>体系结构

排队复制服务器 1 (ERS1) 和排队复制服务器 2 (ERS2) 在多 SID 配置中受支持。  不支持在同一群集中混合使用 ERS1 和 ERS2。 

1. 第一个示例展示两个 SAP SID，两者都采用 ERS1 体系结构，其中：

   - SAP SID1 使用 ERS1 部署在共享磁盘上。 ERS 实例安装在本地主机和本地驱动器上。
     SAP SID1 有自己的（虚拟）IP 地址 (SID1 (A)SCS IP1)，这是在 Azure 内部负载均衡器上配置的。

   - SAP SID2 使用 ERS1 部署在共享磁盘上。 ERS 实例安装在本地主机和本地驱动器上。
     SAP SID2 有自己的（虚拟）IP 地址 (SID2 (A)SCS IP2)，这也是在 Azure 内部负载均衡器上配置的。

![高可用性 SAP ASCS/SCS 实例 - 具有 ERS1 配置的两个实例][sap-ha-guide-figure-6007]

2. 第二个示例展示两个 SAP SID，两者都采用 ERS2 体系结构，其中： 

   - 采用 ERS2 的 SAP SID1 也是群集实例，部署在本地驱动器上。  
     SAP SID1 有自己的（虚拟）IP 地址 (SID1 (A)SCS IP1)，这是在 Azure 内部负载均衡器上配置的。
     SAP SID1 系统使用的 SAP ERS2 有自己的（虚拟）IP 地址 (SID1 ERS2 IP2)，这是在 Azure 内部负载均衡器上配置的。

   - 采用 ERS2 的 SAP SID2 也是群集实例，部署在本地驱动器上。  
     SAP SID2 有自己的（虚拟）IP 地址 (SID2 (A)SCS IP3)，这是在 Azure 内部负载均衡器上配置的。
     SAP SID2 系统使用的 SAP ERS2 有自己的（虚拟）IP 地址 (SID2 ERS2 IP4)，这是在 Azure 内部负载均衡器上配置的。

   这里总共有四个虚拟 IP 地址：  
   - SID1 (A)SCS IP1
   - SID2 ERS2   IP2
   - SID2 (A)SCS IP3
   - SID2 ERS2   IP4

![高可用性 SAP ASCS/SCS 实例 - 具有 ERS1 和 ERS2 配置的两个实例][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>基础结构准备

除了现有群集 SAP PR1 ASCS/SCS 实例外，我们还将安装新的 SAP SID PR2。  

### <a name="host-names-and-ip-addresses"></a>主机名和 IP 地址

| 主机名角色 | 主机名 | 静态 IP 地址 | 可用性集 | 邻近放置组 |
| --- | --- | --- |---| ---|
| 第 1 个群集节点 ASCS/SCS 群集 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 第 2 个群集节点 ASCS/SCS 群集 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 群集网络名称 | pr1clust |10.0.0.42（仅适用于 Win 2016 群集） | 不适用 | 不适用 |
| SID1 ASCS 群集网络名称 | pr1-ascscl |10.0.0.43 | 不适用 | 不适用 |
| SID1 ERS 群集网络名称（仅 适用于 ERS2） | pr1-erscl |10.0.0.44 | 不适用 | 不适用 |
| SID2 ASCS 群集网络名称 | pr2-ascscl |10.0.0.45 | 不适用 | 不适用 |
| SID2 ERS 群集网络名称（仅 适用于 ERS2） | pr1-erscl |10.0.0.46 | 不适用 | 不适用 |

### <a name="create-azure-internal-load-balancer"></a>创建 Azure 内部负载均衡器

SAP ASCS、SAP SCS 和新的 SAP ERS2 均使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要[负载均衡器](../../../load-balancer/load-balancer-overview.md)才能使用虚拟 IP 地址。 强烈建议使用[标准负载均衡器](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。 

对于第二个 SAP SID ASCS/SCS/ERS 实例 PR2，需要将配置添加到现有负载均衡器。 第一个 SAP SID PR1 的配置应已准备就绪。  

**(A)SCS PR2 [实例编号 02]**
- 前端配置
    - 静态 ASCS/SCS IP 地址 10.0.0.45
- 后端配置  
    已就绪 - 在配置 SAP SID PR1 时，已将 VM 添加到后端池
- 探测端口
    - 端口 620nr [62002] 保留协议 (TCP)、间隔 (5)、不正常阈值 (2) 的默认选项
- 负载均衡算法
    - 如果使用“标准负载均衡器”，请选择“HA 端口”
    - 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
        - 32nr TCP [3202]
        - 36nr TCP [3602]
        - 39nr TCP [3902]
        - 81nr TCP [8102]
        - 5nr13 TCP [50213]
        - 5nr14 TCP [50214]
        - 5nr16 TCP [50216]
        - 与 PR2 ASCS 前端 IP、运行状况探测和现有后端池关联。  

    - 请确保将空闲超时时间（分钟）设置为最大值 30，并启用浮动 IP（直接服务器返回）。

**ERS2 PR2 [实例编号 12]** 

由于排队复制服务器 2 (ERS2) 也是群集实例，因此，除以上 SAP ASCS/SCS IP 外，还必须在 Azure ILB 上配置 ERS2 虚拟 IP 地址。 本部分仅适用于对 PR2 使用排队复制服务器 2 体系结构的情况。  
- 新前端配置
    - 静态 SAP ERS2 IP 地址 10.0.0.46

- 后端配置  
  已将 VM 添加到 ILB 后端池。  

- 新探测端口
    - 端口 621nr  [62112] 保留协议 (TCP)、间隔 (5)、不正常阈值 (2) 的默认选项

- 新负载均衡规则
    - 如果使用“标准负载均衡器”，请选择“HA 端口”
    - 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
        - 32nr TCP [3212]
        - 33nr TCP [3312]
        - 5nr13 TCP [51212]
        - 5nr14 TCP [51212]
        - 5nr16 TCP [51212]
        - 与 PR2 ERS2 前端 IP、运行状况探测和现有后端池关联。  

    - 请确保将空闲超时时间（分钟）设置为最大值（例如 30），并启用浮动 IP（直接服务器返回）。


### <a name="create-and-attach-second-azure-shared-disk"></a>创建并附加第二个 Azure 共享磁盘

在其中一个群集节点上运行以下命令。 需要调整资源组、Azure 区域、SAPSID 等的值。  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>用 PowerShell 格式化共享磁盘
1. 获取磁盘编号。 在其中一个群集节点上运行 PowerShell 命令：

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 格式化磁盘。 本例中的磁盘编号为 3。 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 验证磁盘现在是否作为群集磁盘显示。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. 在群集中注册磁盘。  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>为群集 SAP ASCS/SCS 实例创建虚拟主机名

1. 在 Windows DNS 管理器中，为新 SAP ASCS/SCS 实例的虚拟主机名创建 DNS 条目。  
   分配给 DNS 中虚拟主机名的 IP 地址必须与分配给 Azure 负载均衡器的 IP 地址相同。  

   ![_定义 SAP ASCS/SCS 群集虚拟名称和 IP 地址的 DNS 条目][sap-ha-guide-figure-6009]
 
   定义 SAP ASCS/SCS 群集虚拟名称和 IP 地址的 DNS 条目

2. 如果使用的是 SAP 排队复制服务器 2（也是群集实例），则还需要在 DNS 中为 ERS2 保留虚拟主机名。 
   分配给 DNS 中 ERS2 虚拟主机名的 IP 地址必须与分配给 Azure 负载均衡器的 IP 地址相同。  

   ![_定义 SAP ERS2 群集虚拟名称和 IP 地址的 DNS 条目][sap-ha-guide-figure-6010]
 
   定义 SAP ERS2 群集虚拟名称和 IP 地址的 DNS 条目

3. 若要定义分配给虚拟主机名的 IP 地址，请选择“DNS 管理器” > “域” 。

   ![SAP ASCS/SCS 和 ERS2 群集配置的新虚拟名称和 IP 地址][sap-ha-guide-figure-6011]

   SAP ASCS/SCS 和 ERS2 群集配置的新虚拟名称和 TCP/IP 地址

## <a name="sap-installation"></a>SAP 安装 

### <a name="install-the-sap-first-cluster-node"></a>安装 SAP 的第一个群集节点

按照 SAP 说明的安装过程操作。 请确保在开始安装选项“第一个群集节点”中，选择“群集共享磁盘”作为配置选项。  
选择新创建的共享磁盘。  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>修改 ASCS/SCS 实例的 SAP 配置文件

如果你运行的是排队复制服务器 1，请添加 SAP 配置文件参数 `enque/encni/set_so_keepalive`，如下所述。 配置文件参数可避免 SAP 工作进程与排队服务器之间的连接在空闲时间太长时关闭。 ERS2 不需要 SAP 参数。 

1. 如果使用的是 ERS1，则将此配置文件参数添加到 SAP ASCS/SCS 实例配置文件中。

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   对于 ERS1 和 ERS2，请确保按 SAP 说明 [1410736](https://launchpad.support.sap.com/#/notes/1410736) 中所述设置 `keepalive` OS 参数。   

2. 若要应用 SAP 配置文件参数更改，请重启 SAP ASCS/SCS 实例。

### <a name="configure-probe-port-on-the-cluster-resource"></a>在群集资源上配置探测端口

使用内部负载均衡器探测功能，让整个群集配置使用 Azure Load Balancer。 Azure 内部负载均衡器通常在参与的虚拟机之间平均分配传入的工作负荷。

但是，这在某些群集配置中由于只有一个实例处于活动状态而不起作用。 其他实例处于被动状态，并且无法接受任何工作负荷。 当 Azure 内部负载均衡器检测处于活动状态的实例，并且仅将活动实例作为目标时，探测功能会很有帮助。  

> [!IMPORTANT]
> 在此示例配置中，“ProbePort” 设置为“620Nr”。 对于编号为 02 的 SAP ASCS 实例，则为“62002”。
> 你将需要调整配置，使其与 SAP 实例编号和 SAP SID 一致。

若要添加探测端口，请在其中一个群集 VM 上运行以下 PowerShell 模块：

- 如果 SAP ASC/SCS 实例的实例编号为 02 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- 如果使用实例编号为 12 的 ERS2（群集实例）。 不需要为 ERS1 配置探测端口，因为它不是群集实例。  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 函数 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` 的代码如下：
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>继续安装 SAP

1. 按照 SAP 安装指南中描述的流程安装数据库实例。  
2. 按照 SAP 安装指南中描述的下列步骤，在第二个群集节点上安装 SAP。  
3. 在已指定为托管 PAS 的虚拟机上安装 SAP 主应用程序服务器 (PAS) 实例。   
   按照 SAP 安装指南中所述的流程操作。 Azure 上没有依赖项。
4. 在虚拟机上安装额外的 SAP 应用程序服务器，它们被指定为托管 SAP 应用程序服务器实例。  
   按照 SAP 安装指南中所述的流程操作。 Azure 上没有依赖项。 

## <a name="test-the-sap-ascsscs-instance-failover"></a>测试 SAP ASCS/SCS 实例故障转移
对于概述的故障转移测试，假设 SAP ASCS 在节点 A 上处于活动状态。  

1. 验证 SAP 系统是否可以成功从节点 A 故障转移到节点 B。在本例中，针对 SAPSID PR2 执行此测试。  
   请确保每个 SAPSID 都可以成功转移到另一个群集节点。   
   选择以下选项之一，启动 SAP \<SID\> 群集组从群集节点 A 到群集节点 B 的故障转移：
    - 故障转移群集管理器  
    - 故障转移群集 PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. 重启 Windows 来宾操作系统中的群集节点 A。 这会启动 SAP \<SID\> 群集组从节点 A 到节点 B 的自动故障转移。  
3. 重启 Azure 门户中的群集节点 A。 这会启动 SAP \<SID\> 群集组从节点 A 到节点 B 的自动故障转移。  
4. 使用 Azure PowerShell 重启群集节点 A。 这会启动 SAP \<SID\> 群集组从节点 A 到节点 B 的自动故障转移。

## <a name="next-steps"></a>后续步骤

* [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构][sap-high-availability-infrastructure-wsfc-shared-disk]
* [对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘安装 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../availability.md