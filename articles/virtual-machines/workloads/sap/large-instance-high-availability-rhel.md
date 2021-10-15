---
title: SAP on RHEL 的 Azure 大型实例高可用性
description: 了解如何在 Red Hat Enterprise Linux 中使用 Pacemaker 群集自动执行 SAP HANA 数据库故障转移。
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 199d38a4c6ddca96c745342bcef0b07dc78b48bd
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401287"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>SAP on RHEL 的 Azure 大型实例高可用性

> [!NOTE]
> 本文包含对术语“黑名单”和“从”的引用，Microsoft 不再使用这些术语。 在从软件中删除该术语后，我们会将其从本文中删除。

在本文中，你将学习如何在 RHEL 7 中配置 Pacemaker 群集，来自动执行 SAP HANA 数据库故障转移。 若要完成本指南中的步骤，需要充分了解 Linux、SAP HANA 和 Pacemaker。

下表包含本文中使用的主机名。 本文中的代码块显示了需要运行的命令以及这些命令的输出。 请特别注意每个命令中引用的节点。

| 类型 | 主机名 | 节点|
|-------|-------------|------|
|主要主机|`sollabdsm35`|节点 1|
|辅助主机|`sollabdsm36`|节点 2|

## <a name="configure-your-pacemaker-cluster"></a>配置 Pacemaker 群集


在开始配置群集之前，请设置 SSH 密钥交换，以在节点之间建立信任。

1. 使用以下命令在两个节点上创建相同的 `/etc/hosts`。

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  创建并交换 SSH 密钥。
    1. 生成 SSH 密钥。

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. 将密钥复制到其他主机以实现无密码 SSH 通信。
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  在两个节点上禁用 selinux。
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. 重新启动服务器，然后使用以下命令验证 selinux 的状态。
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. 配置 NTP（网络时间协议）。 两个群集节点的时间和时区必须匹配。 使用以下命令打开 `chrony.conf` 并验证该文件的内容。
    1. 应将以下内容添加到配置文件。 根据你的环境更改实际值。
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. 启用 chrony 服务。 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. 更新系统
    1. 在开始安装 SBD 设备之前，请先在系统上安装最新的更新。
    1. 客户必须确保至少安装了 4.1.1-12.el7_6.26 版本的 resource-agents-sap-hana 包，如 [RHEL 高可用性群集的支持策略 - 管理群集中的 SAP HANA](https://access.redhat.com/articles/3397471) 中所述
    1. 如果你不想要对系统进行全面更新（即使建议这样做），请至少更新以下包。
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. 安装 SAP HANA 和 RHEL-HA 存储库。

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. 在所有节点上安装 Pacemaker、SBD、OpenIPMI、ipmitool 和 fencing_sbd 工具。

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>配置监视器

本部分介绍如何配置监视器。 本部分使用本文开头所提到的两个主机：`sollabdsm35` 和 `sollabdsm36`。

1. 请确保监视器守护程序未在任何系统上运行。
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. 在安装过程中安装的默认 Linux 监视器是 UCS 和 HPE SDFlex 系统所不支持的 iTCO 监视器。 因此必须禁用此监视器。
    1. 系统上安装并加载了错误的监视器：
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. 从环境中卸载错误的驱动程序：
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. 为确保在下一次启动系统期间不会加载该驱动程序，必须将该驱动程序加入阻止列表。 若要将 iTCO 模块加入阻止列表，请将以下内容添加到 `50-blacklist.conf` 文件的末尾：
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. 将该文件复制到辅助主机。
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. 测试 ipmi 服务是否已启动。 IPMI 计时器必须未运行。 将从 SBD pacemaker 服务执行计时器管理。
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. 默认不会创建所需的设备 /dev/watchdog。

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. 配置 IPMI 监视器。

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. 将监视器配置文件复制到辅助节点。
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  启用并启动 ipmi 服务。
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     现已启动 IPMI 服务并已创建设备 /dev/watchdog – 但计时器仍处于停止状态。 稍后 SBD 将管理监视器重置并启用 IPMI 计时器。
7.  检查 /dev/watchdog 是否存在但未使用。
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD 配置
本部分介绍如何配置 SBD。 本部分使用本文开头所提到的两个主机：`sollabdsm35` 和 `sollabdsm36`。

1.  请确保 iSCSI 或 FC 磁盘在两个节点上均可见。 此示例使用基于 FC 的 SBD 设备。 有关 SBD 隔离的详细信息，请参阅 [RHEL 高可用性群集的设计指南 - SBD 注意事项](https://access.redhat.com/articles/2941601)和 [RHEL 高可用性群集的支持策略 - sbd 和 fence_sbd](https://access.redhat.com/articles/2800691)
2.  LUN-ID 在所有节点上必须相同。
  
3.  检查 sbd 设备的多路径状态。
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  创建 SBD 磁盘并设置群集基元隔离。 必须在第一个节点上执行此步骤。
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  将 SBD 配置复制到节点 2。
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  检查 SBD 磁盘是否在两个节点中可见。
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  在 SBD 配置文件中添加 SBD 设备。

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>群集初始化
在本部分，你将初始化群集。 本部分使用本文开头所提到的两个主机：`sollabdsm35` 和 `sollabdsm36`。

1.  设置群集用户密码（在所有节点上）。
    ```
    passwd hacluster
    ```
2.  在所有系统上启动 PCS。
    ```
    systemctl enable pcsd
    ```
  

3.  停止并禁用防火墙（在所有节点上）。
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  启动 pcsd 服务。
    ```
    systemctl start pcsd
    ```

5.  仅从节点 1 运行群集身份验证。

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  创建群集。
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  检查群集状态。

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. 如果某个节点未加入群集，请检查防火墙是否仍在运行。

9. 创建并启用 SBD 设备
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. 停止群集并重启群集服务（在所有节点上）。

    ```
    pcs cluster stop --all
    ```

11. 重启群集服务（在所有节点上）。

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync 必须启动 SBD 服务。

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. 重启群集（如果未从 pcsd 自动启动）。

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. 启用 Stonith 设置。
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. 检查新群集状态现在是否显示为包含一个资源。
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. 现在，IPMI 计时器必须已运行，并且 /dev/watchdog 设备必须已由 sbd 打开。

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. 检查 SBD 状态。

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. 通过使内核崩溃来测试 SBD 隔离。

    * 触发内核崩溃。

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * 要运行的第二项测试是使用 PCS 命令来隔离节点。

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. 对于其余的 SAP HANA 群集，可通过以下设置禁用 STONITH：

   * pcs property set `stonith-enabled=false`
   * 在设置群集过程中，使 STONITH 保持停用状态有时可以带来更大的方便，因为这样可以避免系统意外重新启动。
   * 必须将此参数设置为 true，以提高使用效率。 如果此参数未设置为 true，则不支持群集。
   * pcs property set `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>将 HANA 集成到群集中

在本部分，你要将 HANA 集成到群集中。 本部分使用本文开头所提到的两个主机：`sollabdsm35` 和 `sollabdsm36`。

默认支持的是创建性能优化方案，可在其中直接切换数据库。 在此，本文档仅介绍此方案。 在这种情况下，我们建议为 QAS 系统安装一个群集，为 PRD 系统安装一个单独的群集。 只有在这种情况下，才能在所有组件投入生产之前对其进行测试。


* 此过程是根据以下页面上的 RHEL 说明建立的：

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>配置 HSR 所要遵循的步骤

 | **日志复制模式**            | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **内存中同步（默认）** | 内存中同步 (mode=syncmem) 表示当日志条目已写入主实例的日志卷，且辅助实例在复制到内存之后已确认发送了日志时，日志写入被视为成功。 当与辅助系统的连接断开时，主系统会继续处理事务，并仅将更改写入本地磁盘。 只要连接了辅助系统，当主系统和辅助系统同时故障，或者执行接管时辅助系统断开，就会发生数据丢失。 此选项提供更好的性能，因为无需等待辅助实例中的磁盘 I/O，但这更容易导致数据丢失。                                                                                                                                                                                                                                                                                                                     |
| **同步**                     | 同步 (mode=sync) 表示当日志条目已写入主实例和辅助实例的日志卷时，日志写入被视为成功。 当与辅助系统的连接断开时，主系统会继续处理事务，并仅将更改写入本地磁盘。 在这种情况下，只要辅助系统保持连接，就不会丢失数据。 当在执行接管时辅助系统断开，可能会发生数据丢失。 此外，这种复制模式可以使用完全同步选项运行。 这意味着当日志缓冲区已写入主实例和辅助实例的日志文件时，日志写入成功。 另外，当辅助系统断开连接时（例如由于网络故障），主系统会暂停处理事务，直到重新建立与辅助系统的连接。 在这种情况下，不会发生数据丢失。 只能使用参数 \[system\_replication\]/enable\_full\_sync) 设置系统复制的完全同步选项。 若要详细了解如何启用完全同步选项，请参阅“为系统复制启用完全同步选项”。                                                                                                                                                                                                                                                                                                              |
| **异步**                    | 异步 (mode=async) 表示主系统以异步方式将重做日志缓冲区发送到辅助系统。 当事务已写入主系统的日志文件，并通过网络发送到辅助系统时，主系统会提交该事务。 它不会等待辅助系统进行确认。 此选项提供更好的性能，因为无需等待辅助系统中的日志 I/O。 辅助系统上所有服务的数据库一致性得到保障。 但是，这样更容易丢失数据。 接管时可能会丢失数据更改。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

1.  下面是需要在节点 1（主要节点）上执行的操作。
    1. 确保数据库日志模式设置为“常规”。

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. 只有在执行初始备份之后，才会进行 SAP HANA 系统复制。 以下命令在 `/tmp/` 目录中创建初始备份。 为数据库选择适当的备份文件系统。 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. 备份此数据库的所有数据库容器。
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. 在源系统上启用 HSR 过程。
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. 检查主要系统的状态。
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. 下面是需要在节点 2（辅助节点）上执行的操作。
     1. 停止数据库。
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. （仅适用于 SAP HANA 2.0）将 SAP HANA 系统 `PKI SSFS_HR2.KEY` 和 `SSFS_HR2.DAT` 文件从主要节点复制到辅助节点。
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. 将辅助节点启用为复制站点。
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. 启动数据库。
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. 检查数据库状态。
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. 还可以获取有关复制状态的详细信息：
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>日志复制模式说明

有关日志复制模式的详细信息，请参阅[官方的 SAP 文档](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html)。
  

#### <a name="network-setup-for-hana-system-replication"></a>HANA 系统复制的网络设置


为确保复制流量使用正确的 VLAN 进行复制，必须在 `global.ini` 中正确配置 VLAN。 如果跳过此步骤，则 HANA 将使用“访问 VLAN”进行复制，这可能不符合你的期望。


以下示例显示了到辅助站点的系统复制的主机名解析配置。 可以标识三个不同的网络：

* 使用 10.0.1.* 范围内的地址的公用网络

* 用于在每个站点上的主机之间进行内部 SAP HANA 通信的网络：192.168.1.*

* 用于系统复制的专用网络：10.5.1.*

在第一个示例中，`[system_replication_communication]listeninterface` 参数已设置为 `.global`，并且仅指定了相邻复制站点的主机。

在后一个示例中，`[system_replication_communication]listeninterface` 参数已设置为 `.internal`，并且指定了两个站点的所有主机。

  

有关详细信息，请参阅 [SAP HANA 系统复制的网络配置](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)。

  

对于系统复制，不必要编辑 `/etc/hosts` 文件，内部（“虚拟”）主机名必须映射到 `global.ini` 文件中的 IP 地址，以创建专用网络用于系统复制。 此配置的语法如下：

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>在 Pacemaker 群集中配置 SAP HANA
本部分介绍如何在 Pacemaker 群集中配置 SAP HANA。 本部分使用本文开头所提到的两个主机：`sollabdsm35` 和 `sollabdsm36`。

请确保满足以下先决条件：  

* Pacemaker 群集是根据文档配置的，具有适当且可正常运行的隔离

* 所有群集节点上已禁用 SAP HANA 引导时启动，因为启动和停止将由群集管理

* 在群集节点之间可以正常使用 SAP 提供的工具进行 SAP HANA 系统复制和接管

* SAP HANA 包含可供两个群集节点中的群集使用的监视帐户

* 这两个节点均已订阅“高可用性”和“RHEL for SAP HANA”（RHEL 6、RHEL 7）通道

  

* 一般而言，请仅从一个节点执行所有 pcs 命令，因为 CIB 将自动从 pcs shell 更新。

* [有关仲裁策略的详细信息](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>配置步骤 
1. 配置 pcs。
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  配置 corosync。
    有关详细信息，请参阅[如何使用 pacemaker 和 corosync 配置 RHEL 7 高可用性群集](https://access.redhat.com/solutions/1293523)。
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  创建克隆的 SAPHanaTopology 资源。
    SAPHanaTopology 资源正在收集每个节点上 SAP HANA 系统复制的状态和配置。 SAPHanaTopology 要求配置以下属性。
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 op start timeout=600 \
       op stop timeout=300 \
       op monitor interval=10 timeout=600 \
       clone clone-max=2 clone-node-max=1 interleave=true

       ```

    | 属性名称 | 说明  |
    |---|---|
    | SID | SAP HANA 安装的 SAP 系统标识符 (SID)。 此标识符对于所有节点必须相同。 |
    | InstanceNumber | 2 位数 SAP 实例标识符。|

    * 资源状态
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       Clone: SAPHanaTopology_HR2_00-clone
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat type=SAPHanaTopology)
         Attributes: InstanceNumber=00 SID=HR2
         Operations: monitor interval=60 timeout=60 (SAPHanaTopology_HR2_00-monitor-interval-60)
                     start interval=0s timeout=180 (SAPHanaTopology_HR2_00-start-interval-0s)
                     stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
       
         
       ```

4.  创建主要/辅助 SAPHana 资源。
    * SAPHana 资源负责启动、停止和重新定位 SAP HANA 数据库。 此资源必须作为主要/辅助群集资源运行。 该资源具有以下属性。

| 属性名称            | 必需？ | 默认值 | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | 是       | 无          | SAP HANA 安装的 SAP 系统标识符 (SID)。 此标识符对于所有节点必须相同。                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber            | 是       | 无          | 2 位数 SAP 实例标识符。                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | 否        | 是           | 群集是否应该优先切换到辅助实例而不是在本地重启主要实例？ （"no"：优先在本地重启；"yes"：优先接管到远程站点）                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | 否        | FALSE         | 在接管并发生 DUPLICATE_PRIMARY_TIMEOUT 后，是否应将以前的 SAP HANA 主要实例注册为辅助实例？ （"false"：否，需要手动干预；"true"：是，由资源代理将以前的主要实例注册为辅助实例）                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | 否        | 7200          | 存在两个主要实例时，主要实例时间戳之间所需的时间差（秒）。 如果该时间差小于时隙，则群集会将其中一个或两个实例保持“WAITING”状态。 这样，管理员便有机会对故障转移做出反应。 该时间差过后，将会注册以前的有故障主要实例。 在新的主要实例上进行这种注册后，系统复制将覆盖所有数据。 |

5.  创建 HANA 资源。
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=true op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=61 role="Slave" timeout=700 \
    op monitor interval=59 role="Master" timeout=700 \
    op promote timeout=3600 \
    op demote timeout=3600 \
    master meta notify=true clone-max=2 clone-node-max=1 interleave=true


    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary
     Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true
     Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)
      Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200 InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2
      Operations: demote interval=0s timeout=320 (SAPHana_HR2_00-demote-interval-0s)
                  monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)
                  monitor interval=121 role=Secondary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-121)
                  monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-119)
                  promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)
                  start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)
                  stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)
   

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  创建虚拟 IP 地址资源。
    群集将包含虚拟 IP 地址，以访问 SAP HANA 的主要实例。 下面是使用 IP 10.7.0.84/24 创建 IPaddr2 资源的示例命令。
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  创建约束。
    * 为了正常操作，我们需要确保在启动 SAPHana 资源之前已启动 SAPHanaTopology 资源，并确保虚拟 IP 地址存在于运行 SAPHana 主要资源的节点上。 若要实现此目的，需要创建以下 2 个约束。
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>测试手动将 SAPHana 资源移到另一个节点

#### <a name="sap-hana-takeover-by-cluster"></a>（群集接管 SAP HANA）


若要测试将 SAPHana 资源从一个节点移到另一个节点，请使用以下命令。 请注意，由于 SAPHana 资源在内部的工作方式，运行以下命令时，不应使用选项 `--primary`。

`pcs resource move SAPHana_HR2_00-primary`

对每个 pcs 资源调用 move 命令后，群集将创建位置约束以实现资源移动。 要使以后能够自动故障转移，必须删除这些约束。
若要删除这些约束，可使用以下命令。

```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* 登录到 HANA 进行验证。

  * 已降级的主机：

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * 已提升的主机：

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

如果使用选项 `AUTOMATED_REGISTER=false`，则无法来回切换。

如果此选项设置为 false，则必须重新注册节点：
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

原本是主要主机的节点 2 现在成了辅助主机。

请考虑将此选项设置为 true，以自动注册已降级的主机。
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

是否优先使用自动注册取决于客户场景。 在接管后自动重新注册节点对于操作团队而言会更方便。 但是，你可能希望手动注册节点，以便能够先运行额外的测试来确保一切符合预期。

##  <a name="references"></a>参考

1. [pacemaker 群集中纵向扩展的自动 SAP HANA 系统复制](https://access.redhat.com/articles/3397471)
2. [RHEL 高可用性群集的支持策略 - 管理群集中的 SAP HANA](https://access.redhat.com/articles/3397471)
3. [在 Azure 中的 RHEL 上设置 Pacemaker - Azure 虚拟机](high-availability-guide-rhel-pacemaker.md)
4. [通过 Azure 门户控制 Azure HANA 大型实例 - Azure 虚拟机](hana-li-portal.md)
