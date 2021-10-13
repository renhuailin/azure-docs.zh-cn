---
title: Azure VMware Solution by CloudSimple - 在私有云上为 vRealize 自动化设置 vCenter
description: 介绍如何在 CloudSimple 私有云上将 VMware vCenter 服务器设置为 VMware vRealize 自动化的终结点
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cd00a319c06131014db2873a7339cdf9127fe58d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611465"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>在私有云上为 VMware vRealize 自动化设置 vCenter

可以在 CloudSimple 私有云上将 VMware vCenter 服务器设置为 VMware vRealize 自动化的终结点。

## <a name="before-you-begin"></a>准备阶段

在配置 vCenter 服务器之前，请先完成以下任务：

* 配置本地环境和私有云之间的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* [配置将本地 DNS 请求通过 DNS 转发](on-premises-dns-setup.md)到私有云的 DNS 服务器。
* 请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，以创建具有下表中列出的权限集的 vRealize 自动化 IaaS 管理员用户。

| 属性值 | 权限 |
------------ | ------------- |  
| 数据存储 |  分配空间 <br> 浏览数据存储 |
| 数据存储群集 | 配置数据存储群集 |
| Folder | 创建文件夹 <br>删除文件夹 |
| 全球 |  管理自定义属性<br>设置自定义属性 |
| 网络 | 分配网络 |
| 权限 | 修改权限 |
| 资源 | 将 VM 分配到资源池<br>迁移已关机的虚拟机<br>迁移已开机的虚拟机 |
| 虚拟机清单 |  从现有创建<br>新建<br>移动<br>删除 | 
| 虚拟机交互 |  配置 CD 媒体<br>控制台交互<br>设备连接<br>关闭电源<br>开机<br>重置<br>挂起<br>工具安装 | 
| 虚拟机配置 |  添加现有磁盘<br>添加新磁盘<br>添加或删除<br>删除磁盘<br>高级<br>更改 CPU 计数<br>更改资源<br>扩展虚拟磁盘<br>磁盘更改跟踪<br>内存<br>修改设备设置<br>重命名<br>设置注释（版本 5.0 和更高版本）<br>设置<br>交换文件位置 |
| 设置 |  自定义<br>克隆模板<br>克隆虚拟机<br>部署模板<br>读取自定义规范 |
| 虚拟机状态 | 创建快照<br>删除快照<br>还原到快照 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>在本地环境中安装 vRealize 自动化

1. 以 CloudSimple 支持团队为你创建的 IaaS 管理员身份登录到 vRealize 自动化 IaaS 服务器设备。
2. 为 vRealize 自动化终结点部署 vSphere 代理。
    1. 转到 https://vra-url:5480/installer，其中 vra-url 是用于访问 vRealize 自动化管理 UI 的 URL 。
    2. 单击“IaaS 安装程序”以下载该安装程序。<br>
    该安装程序文件的命名约定为 setup_vra-url @5480.exe。
    3. 运行安装程序。 在“欢迎”屏幕上，单击 **下一步**。
    4. 接受 EULA，然后单击“下一步”。
    5. 提供登录信息，单击“接受证书”，然后单击“下一步” 。
    ![vRA 凭据](media/configure-vra-endpoint-login.png)
    6. 选择“自定义安装”和“代理机构”，然后单击“下一步”  。
    ![vRA 安装类型](media/configure-vra-endpoint-install-type.png)
    7. 输入 IaaS 服务器登录信息，然后单击“下一步”。 如果使用 Active Directory，请输入“域\用户”格式的用户名。 否则，请使用 user@domain 格式。
    ![vRA 登录信息](media/configure-vra-endpoint-account.png)
    8. 对于代理设置，输入“vSphere”作为“代理类型” 。 输入代理名称。
    9. 在“管理器服务主机”和“模型管理器 Web 服务主机”字段中输入 IaaS 服务器 FQDN 。 单击“测试”以测试每个 FQDN 值的连接。 如果测试失败，请修改 DNS 设置，以便解析 IaaS 服务器主机名。
    10. 为私有云输入 vCenter 服务器终结点的名称。 记下该名称，以供稍后在配置过程中使用。

        ![vRA 安装代理](media/configure-vra-endpoint-proxy.png)

    11. 单击“下一步”。
    12. 单击“安装”  。

## <a name="configure-the-vsphere-agent"></a>配置 vSphere 代理

1. 转到 https://vra-url/vcac 并以 ConfigurationAdmin 身份登录。
2. 选择“基础结构” > “终结点” > “终结点”  。
3. 选择“新建” > “虚拟” > “vSphere”  。
4. 输入在前面的过程中指定的 vSphere 终结点名称。
5. 对于“地址”，请输入 https://vcenter-fqdn/sdk 格式的私有云 vCenter Server URL，其中 vcenter-fqdn 是 vCenter 服务器的名称 。
6. 输入 CloudSimple 支持团队为你创建的 vRealize 自动化 IaaS 管理员用户的凭据。
7. 单击“测试连接”，以验证该用户凭据。 如果测试失败，请验证 URL、帐户信息和[终结点名称](#verify-the-endpoint-name)，然后再次测试。
8. 在测试成功后，单击“确定”以创建 vSphere 终结点。
    ![vRA 终结点配置访问](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>验证终结点名称

若要标识正确的 vCenter 服务器终结点名称，请执行以下操作：

1. 在 IaaS 设备上打开命令提示符。
2. 将目录更改为 C:\Program Files (x86)\VMware\vCAC\Agents\agent-name，其中“agent-name”是分配到 vCenter 服务器终结点的名称。
3. 运行以下命令。

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

输出如下所示。 `managementEndpointName` 字段的值是终结点名称。

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
