---
title: 使用 STONITH 为 Azure 上的 SAP HANA（大型实例）进行高可用性设置 | Microsoft 文档
description: 了解如何使用 STONITH 设备在 SUSE 中为 Azure 上的 SAP HANA（大型实例）建立高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710831"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>使用 STONITH 设备在 SUSE 中进行高可用性设置

本文将针对如何使用 STONITH 设备在 SUSE 操作系统上的 HANA 大型实例中设置高可用性 (HA) 进行详细的分步说明。

> [!NOTE]
> 本指南是成功测试 Microsoft HANA 大型实例环境中的设置的结果。 适用于 HANA 大型实例的 Microsoft 服务管理团队不支持操作系统。 有关操作系统层的故障排除或说明，请联系 SUSE。 
>
> Microsoft 服务管理团队确实设置并完全支持 STONITH 设备。 它可以帮助排查 STONITH 设备问题。

## <a name="prerequisites"></a>先决条件

要使用 SUSE 集群设置高可用性，需要执行以下操作：

- 预配 HANA 大型实例。
- 使用最新补丁安装和注册操作系统。
- 将 HANA 大型实例服务器连接到 SMT 服务器以获取补丁和包。
- 设置网络时间协议（NTP 时间服务器）。
- 阅读并了解有关 HA 设置的最新 SUSE 文档。

## <a name="setup-details"></a>设置详细信息

本指南使用以下设置：

- 操作系统：适用于 SAP 的 SLES 12 SP1
- HANA 大型实例：2xS192（4 个套接字，2 TB）
- HANA 版本：HANA 2.0 SP1
- 服务器名：sapprdhdb95 (node1) 和 sapprdhdb96 (node2)
- STONITH 设备：基于 iSCSI
- HANA 大型实例节点之一上的 NTP

在使用 HANA 系统复制设置 HANA 大型实例时，可请求 Microsoft 服务管理团队设置 STONITH 设备。 在预配时执行此操作。 

如果你是已预配 HANA 大型实例的现有客户，则仍可以设置 STONITH 设备。 以服务申请表 (SRF) 的形式向 Microsoft 服务管理团队提供以下信息。 可通过技术部客户经理或 HANA 大型实例载入的 Microsoft 联系人来获取 SRF。

- 服务器名和服务器 IP 地址（例如，myhanaserver1 和 10.35.0.1）
- 位置（例如，美国东部）
- 客户名称（例如，Microsoft）
- HANA 系统标识符 (SID)（例如，H11）

配置 STONITH 设备后，Microsoft 服务管理团队会提供 STONITH 块设备 (SBD) 名和 iSCSI 存储的 IP 地址。 可以使用此信息来配置 STONITH 设置。 

按照以下部分中的步骤使用 STONITH 设置 HA。

## <a name="identify-the-sbd-device"></a>标识 SBD 设备

> [!NOTE]
> 本部分仅适用于现有客户。 如果你是新客户，Microsoft 服务管理团队会向你提供 SBD 设备名称，因此可以跳过此部分。

1. 将 /etc/iscsi/initiatorname.isci 修改为： 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft 服务管理会提供此字符串。 在这两个节点上修改文件。 但每个节点上的节点编号不同。
    
    ![屏幕截图：显示包含节点的 InitiatorName 值的 initiatorname 文件。](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. 通过设置 `node.session.timeo.replacement_timeout=5` 和 `node.startup = automatic` 来修改 /etc/iscsi/iscsid.conf。 在这两个节点上修改文件。

3. 在这两个节点上运行以下发现命令。

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    结果显示四个会话。
    
    ![屏幕截图：显示包含发现命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. 在这两个节点上运行以下命令以登录到 iSCSI 设备。 

    ```
    iscsiadm -m node -l
    ```
    
    结果显示四个会话。
    
    ![屏幕截图：显示包含节点命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. 使用以下命令运行 rescan-scsi-bus.sh 重新扫描脚本。 此脚本显示为你创建的新磁盘。  在这两个节点上运行该命令。

    ```
    rescan-scsi-bus.sh
    ```
    
    结果应显示一个大于零的 LUN 编号（例如，1、2 等）。
     
    ![屏幕截图：显示包含脚本结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. 要获取设备名，请在这两个节点上运行以下命令。 

    ```
      fdisk –l
    ```
    
    在结果中，选择大小为 178 MiB 的设备。
    
    ![屏幕截图：显示包含 fdisk 结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>初始化 SBD 设备

1. 在这两个节点上使用以下命令初始化 SBD 设备。

    ```
    sbd -d <SBD Device Name> create
    ```
    ![屏幕截图：显示包含 sbd 创建命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. 在这两个节点上使用以下命令检查已写入设备的信息。

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>配置 SUSE HA 群集

1. 在这两个节点上使用以下命令检查是否安装了 ha_sles 和 SAPHanaSR-doc 模式。 如果未安装，请先进行安装。

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![屏幕截图：显示包含模式命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![屏幕截图：显示包含 SAPHanaSR-doc 命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. 使用 `ha-cluster-init` 命令或 yast2 向导设置群集。 本示例中使用的是 yast2 向导。 仅在主节点上执行此步骤。

    1. 依次转到“yast2” > “高可用性” > “群集”  。
    
        ![屏幕截图：显示选择了“高可用性”和“群集”的 YaST 控制中心。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. 在显示的有关 hawk 包安装的对话框中，选择“取消”，因为已安装 halk2 包。
    
        ![屏幕截图：显示包含“安装”和“取消”选项的对话框。](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. 在显示的有关继续的对话框中，选择“继续”。
    
        ![屏幕截图：显示有关在不安装所需包的情况下继续操作的消息。](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. 预期值为已部署的节点数（在本例中为 2）。 选择“**下一页**”。  

     
        
    1. 添加节点名，然后选择“添加建议的文件”。

        ![屏幕截图：显示包含“同步主机”和“同步文件”列表的“群集配置”窗口。](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. 选择“开启 csync2”。
    
    1. 选择“生成预共享密钥”。 
    
    1. 在显示的弹出消息中，选择“确定”。
    
        ![屏幕截图：显示指出已生成密钥的消息。](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. 使用 Csync2 中的 IP 地址和预共享密钥执行身份验证。 使用 `csync2 -k /etc/csync2/key_hagroup` 生成密钥文件。 
    
        在创建文件 key_hagroup 后，将其手动复制到群集的所有成员。 确保将文件从 node1 复制到 node2。 然后，选择“下一步”。
        
        ![屏幕截图：显示包含将密钥复制到群集的所有成员所需的选项的“群集配置”对话框。](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. 在默认选项中，“启动”为“关闭” 。 将其更改为“打开”，以便在启动时启动 pacemaker 服务。 可以基于设置需求做出选择。

        ![屏幕截图：显示“启动”已打开的“群集服务”窗口。](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. 选择“下一步”，完成群集配置。

## <a name="set-up-the-softdog-watchdog"></a>设置 softdog 监视程序

1. 在这两个节点上将以下行添加到 /etc/init.d/boot.local 。
    
    ```
    modprobe softdog
    ```
    ![屏幕截图：显示添加了 softdog 行的启动文件。](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. 在这两个节点上使用以下命令更新文件 /etc/sysconfig/sbd 。
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![屏幕截图：显示添加了 SBD_DEVICE 值的 sbd 文件。](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. 通过运行以下命令在这两个节点上加载内核模块。
    
    ```
    modprobe softdog
    ```
    ![屏幕截图：显示包含命令 modprobe softdog 的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. 使用以下命令确保 softdog 在这两个节点上运行。
    
    ```
    lsmod | grep dog
    ```
    ![屏幕截图：显示包含运行 lsmod 命令结果的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. 在这两个节点上使用以下命令启动 SBD 设备。

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![屏幕截图：显示包含启动命令的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. 在这两个节点上使用以下命令测试 SBD 守护程序。 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    在这两个节点上配置后，结果显示两个条目。    
    
    ![屏幕截图：显示正在显示两个条目的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. 向其中一个节点发送以下测试消息。

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. 在第二个节点 (node2) 上，使用以下命令检查消息状态。
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![屏幕截图：显示其中一位成员显示另一位成员测试值的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. 要采用 SBD 配置，请在这两个节点上更新文件 /etc/sysconfig/sbd，如下所示 。

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. 在主节点 (node1) 上使用以下命令启动 pacemaker 服务。

    ```
    systemctl start pacemaker
    ```
    ![屏幕截图：显示正在显示启动 pacemaker 后的状态的控制台窗口。](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    如果 pacemaker 服务失败，请参阅本文稍后的[场景 5：Pacemaker 服务失败](#scenario-5-pacemaker-service-fails)部分。

## <a name="join-the-node-to-the-cluster"></a>将节点加入到群集

在 node2 上运行以下命令，让该节点加入群集。

```
ha-cluster-join
```

如果在加入群集期间收到错误，请参阅本文稍后的[场景 6：Node2 无法加入群集](#scenario-6-node2-cant-join-the-cluster)部分。

## <a name="validate-the-cluster"></a>验证群集

1. 在这两个节点上使用以下命令检查并选择首次启动群集。
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![屏幕截图：显示包含 pacemaker 状态的控制台窗口。](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. 运行以下命令，确保这两个节点都处于联机状态。 可以在该群集的任意节点上运行该命令。
    
    ```
    crm_mon
    ```
    ![屏幕截图：显示包含 crm_mon 命令结果的控制台窗口。](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    还可登录到 hawk 来检查群集状态：`https://\<node IP>:7630`。 默认用户为 hacluster，密码为 linux 。 如果需要，可使用 `passwd` 命令更改密码。
    
## <a name="configure-cluster-properties-and-resources"></a>配置群集属性和资源

本部分将介绍配置群集资源的步骤。
在本示例中设置以下资源。 如果需要，可引用 SUSE HA 指南来配置其余部分。

- 群集启动
- STONITH 设备
- 虚拟 IP 地址

仅在主节点上执行配置。

1. 创建群集启动文件，并添加以下文本对其进行配置。
    
    ```
    sapprdhdb95:~ # vi crm-bs.txt
    # enter the following to crm-bs.txt
    property $id="cib-bootstrap-options" \
    no-quorum-policy="ignore" \
    stonith-enabled="true" \
    stonith-action="reboot" \
    stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
    resource-stickiness="1000" \
    migration-threshold="5000"
    op_defaults $id="op-options" \
    timeout="600"
    ```

2. 使用以下命令将配置添加到群集。

    ```
    crm configure load update crm-bs.txt
    ```
    ![屏幕截图：显示正在运行 crm 命令的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. 通过添加资源、创建文件和添加文本来配置 STONITH 设备，如下所示。

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    使用以下命令将配置添加到群集。
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. 通过创建文件并添加以下文本来添加资源的虚拟 IP 地址。

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    使用以下命令将配置添加到群集。
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. 使用 `crm_mon` 命令验证资源。 

    结果显示两个资源。

    ![屏幕截图：显示包含两个资源的控制台窗口。](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    此外，可以在 https://\<node IP address>:7630/cib/live/state 上检查状态。
    
    ![屏幕截图：显示两个资源的状态。](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>测试故障转移过程

1. 要测试故障转移过程，请使用以下命令停止 node1 上的 pacemaker 服务。

    ```
    Service pacemaker stop
    ```
    
    资源故障转移到 node2。

2. 停止 node2 上的 pacemaker 服务，资源故障转移到 node1。

    以下是故障转移前的状态：  
    ![屏幕截图：显示故障转移前两个资源的状态。](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    以下是故障转移后的状态：  
    ![屏幕截图：显示故障转移后两个资源的状态。](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![屏幕截图：显示包含故障转移后资源的状态的控制台窗口。](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>疑难解答

本部分介绍在设置过程中可能会遇到的失败场景。

### <a name="scenario-1-cluster-node-not-online"></a>情景 1：群集节点未联机

如果在群集管理器中没有任何节点显示为联机，则可以尝试此过程使其联机。

1. 使用以下命令启动 iSCSI 服务。

    ```
    service iscsid start
    ```
    
2. 使用以下命令登录到该 iSCSI 节点。

    ```
    iscsiadm -m node -l
    ```
    
    预期输出如下所示：

    ```
    sapprdhdb45:~ # iscsiadm -m node -l
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
    ```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>场景 2：Yast2 不显示图形视图

在本文中，使用 yast2 图形屏幕来设置高可用性群集。 如果 yast2 不随如下所示的图形窗口一起打开，并引发 Qt 错误，请执行以下步骤安装所需的包。 如果正常打开，则可以跳过这些步骤。

以下是 Qt 错误的示例：

![屏幕截图：显示包含一条错误消息的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

以下是预期输出的示例：

![屏幕截图：显示突出显示了“高可用性”和“群集”的 YaST 控制中心。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. 必须以用户“root”身份登录，并设置 SMT 以下载并安装包。

2. 转到“yast” > “软件” > “软件管理” > “依赖项”，然后选择“安装建议的包”    。 

    >[!NOTE]
    >在这两个节点上执行这些步骤，以便可以从两个节点访问 yast2 图形视图。
    
    以下屏幕截图显示了预期的屏幕。
    
    ![屏幕截图：显示正在显示 YaST 控制中心的控制台窗口。](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. 在“依赖项”下，选择“安装建议的包” 。

    ![屏幕截图：显示选择了“安装建议的包”的控制台窗口。](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. 查看所做的更改，然后选择“确定”。

    ![屏幕截图：显示包含已选择用于安装的包列表的控制台窗口。](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    包安装继续进行。

    ![屏幕截图：显示正在显示安装进度的控制台窗口。](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. 选择“**下一页**”。    

6. 当显示“安装成功完成”屏幕时，选择“完成” 。

    ![屏幕截图：显示包含一条成功消息的控制台窗口。](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. 使用以下命令安装 libqt4 和 libyui-qt 包。
    
    ```
    zypper -n install libqt4
    ```
    ![屏幕截图：显示正在安装第一个包的控制台窗口。](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![屏幕截图：显示正在安装第二个包的控制台窗口。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![屏幕截图：显示正在安装第二个包的控制台窗口（续）。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    Yast2 现在可以打开图形视图。

    ![屏幕截图：显示选择了“软件”和“联机更新”的 YaST 控制中心。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>场景 3：Yast2 不显示高可用性选项

要在 yast2 控制中心上显示高可用性选项，需要安装其他包。

1. 依次转到“Yast2” > “软件” > “软件管理”  。 然后选择“软件” > “联机更新” 。  

    ![屏幕截图：显示选择了“软件”和“联机更新”的 YaST 控制中心。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. 选择以下项的模式。 然后选择“接受”。

    - SAP HANA 服务器基
    - C/C++ 编译器和工具
    - 高可用性
    - SAP 应用程序服务器基

    ![屏幕截图：显示在编译器和工具的项中选择第一种模式。](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![屏幕截图：显示在编译器和工具的项中选择第二种模式。](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. 在已更改的用于解析依赖项的包列表中，选择“继续”。

    ![屏幕截图：显示包含已更改为可用于解析依赖项的包的“已更改的包”对话框。](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. 在“正在执行安装”状态页上，选择“下一步” 。

    ![屏幕截图：显示“正在执行安装”状态页。](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. 安装完成后，将显示安装报告。 选择“完成”  。

    ![屏幕截图：显示安装报告。](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>情景 4：HANA 安装失败，显示 gcc 程序集错误

如果 HANA 安装失败，可能会收到以下错误。

![屏幕截图：显示指出操作系统未准备好执行 gcc5 程序集的错误消息。](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

要修复此问题，请安装 libgcc_sl 和 libstdc++6 库，如以下屏幕截图所示。

![屏幕截图：显示正在安装所需库的控制台窗口。](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>情景 5：Pacemaker 服务失败

如果 pacemaker 服务无法启动，则会显示以下信息。

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

要修复此问题，请从文件 /usr/lib/systemd/system/fstrim.timer 中删除以下行：

```
Persistent=true
```
    
![屏幕截图：显示包含要删除的 Persistent=true 值的 fstrim 文件。](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>场景 6：Node2 无法加入群集

如果通过 ha-cluster-join 命令将 node2 加入到现有群集出现问题，则会显示以下错误。

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![屏幕截图：显示包含指出无法从特定 IP 地址检索 SSH 密钥的错误消息的控制台窗口。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

要修复此问题，请执行以下操作：

1. 在这两个节点上运行以下命令。

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![屏幕截图：显示正在第一个节点上运行命令的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![屏幕截图：显示正在第二个节点上运行命令的控制台窗口的一部分。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. 确认 node2 已添加到群集。

    ![屏幕截图：显示包含成功的加入命令的控制台窗口。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>后续步骤

可以在以下文章中找到有关 SUSE HA 设置的详细信息： 

- [SAP HANA SR 性能优化方案](https://www.suse.com/support/kb/doc/?id=000019450)（SUSE 网站）
- [隔离和 STONITH](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html)（SUSE 网站）
- [准备好为 SAP HANA 使用 Pacemaker 群集 – 第 1 部分：基本信息](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)（SAP 博客）
- [准备好为 SAP HANA 使用 Pacemaker 群集 – 第 2 部分：两个节点的故障](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)（SAP 博客）

了解如何为操作系统执行文件级备份和还原：

> [!div class="nextstepaction"]
> [OS 备份和还原](large-instance-os-backup.md)
