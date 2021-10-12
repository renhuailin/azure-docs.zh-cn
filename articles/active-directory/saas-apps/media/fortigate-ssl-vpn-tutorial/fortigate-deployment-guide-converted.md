---
title: FortiGate 部署指南
description: 设置并使用 Fortinet FortiGate 下一代防火墙产品。
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: b1b3a2dca9e11e12a72ec99685ff71beb279473e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353940"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate Azure 虚拟机部署指南

使用本部署指南，你将了解如何设置和使用部署为 Azure 虚拟机的 Fortinet FortiGate 下一代防火墙产品。 此外，你还将配置 FortiGate SSL VPN Azure AD 库应用，通过 Azure Active Directory 提供 VPN 身份验证。

## <a name="redeem-the-fortigate-license"></a>兑换 FortiGate 许可证

Fortinet FortiGate 下一代防火墙产品在 Azure 基础结构即服务 (IaaS) 中作为虚拟机提供。 此虚拟机有两种许可模式：即用即付和自带许可 (BYOL)。

如果你已购买 Fortinet 提供的可与 BYOL 虚拟机部署选项一起使用的 FortiGate 许可证，请从 Fortinet 的产品激活页面 (https://support.fortinet.com ) 兑换许可证。 生成的许可证文件具有 .lic 文件扩展名。

## <a name="download-firmware"></a>下载固件

编写时，Fortinet FortiGate Azure VM 不附带进行 SAML 身份验证所需的固件版本。 必须从 Fortinet 获取最新版本。

1. 在 https://support.fortinet.com/ 上登录。
2. 转到“下载” > “固件映像”。
3. 在“发行说明”的右侧，选择“下载”。 
4. 选择“v6.00” > “6.4” > “6.4.2”  。
5. 选择同一行上的 HTTPS 链接，下载“FGT_VM64_AZURE-v6-build1723-FORTINET.out” 。
6. 保存该文件备用。

## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 转到 Azure 门户，登录到要在其中部署 FortiGate 虚拟机的订阅。
2. 创建新的资源组，或打开要在其中部署 FortiGate 虚拟机的资源组。
3. 选择“添加”。
4. 在“在市场中搜索”中，输入“Forti”。 选择“Fortinet FortiGate 下一代防火墙”。
5. 选择软件计划（如果你有许可证，则选择“自带许可”；如果没有，则选择“即用即付”）。 选择“创建”。
6. 填充 VM 配置。

    ![“创建虚拟机”的屏幕截图。](virtual-machine.png)

7. 将“身份验证类型”设置为“密码”，并提供 VM 的管理凭据。 
8. 选择“查看 + 创建” > “创建”。
9. 等待 VM 部署完成。


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>设置静态公共 IP 地址并分配完全限定的域名

为获得一致的用户体验，请将分配给 FortiGate VM 的公共 IP 地址设置为静态分配。 此外，请将其映射到完全限定的域名 (FQDN)。

1. 转到 Azure 门户，并打开 FortiGate VM 的设置。
2. 在“概览”屏幕上，选择该公共 IP 地址。

    ![FortiGate SSL VPN 的屏幕截图。](public-ip-address.png)

3. 选择“静态” > “保存”。

如果你拥有要在其中部署 FortiGate VM 的环境的公共可路由域名，请为 VM 创建一个主机 (A) 记录。 此记录映射到上一个静态分配的公共 IP 地址。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>为 TCP 端口 8443 创建新的入站网络安全组规则

1. 转到 Azure 门户，并打开 FortiGate VM 的设置。
2. 在左侧菜单中选择“网络”。 这将列出网络接口并显示入站端口规则。
3. 选择“添加入站端口规则”。
4. 为 TCP 8443 创建新的入站端口规则。

    ![“添加入站安全规则”的屏幕截图。](port-rule.png)

5. 选择“添加”。

## <a name="create-a-second-virtual-nic-for-the-vm"></a>为 VM 创建第二个虚拟 NIC

要使用户能够使用内部资源，必须将第二个虚拟 NIC 添加到 FortiGate VM。 虚拟 NIC 所在的 Azure 虚拟网络必须具有通向这些内部资源的可路由连接。

1. 转到 Azure 门户，并打开 FortiGate VM 的设置。
2. 如果 FortiGate VM 尚未停止，请选择“停止”，等待 VM 关闭。
3. 在左侧菜单中选择“网络”。
4. 选择“附加网络接口”。
5. 选择“创建并附加网络接口”。
6. 配置新网络接口的属性，然后选择“创建”。

    ![创建网络接口的屏幕截图。](new-network-interface.png)

7. 启动 FortiGate VM。


## <a name="configure-the-fortigate-vm"></a>配置 FortiGate VM

以下各部分将引导你完成设置 FortiGate VM 的过程。

### <a name="install-the-license"></a>安装许可证

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 如果部署使用“自带许可证”模型，你会看到要求上传许可证的提示。 选择之前创建的许可证文件，将其上传。 选择“确定”，重启 FortiGate VM。

    ![FortiGate VM 许可证的屏幕截图。](license.png)

5. 重启后，使用管理员凭据再次登录以验证许可证。

### <a name="update-firmware"></a>更新固件

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “固件”。 
5. 在“固件管理”中选择“浏览”，然后选择之前下载的固件文件。 
6. 忽略警告，选择“备份配置并升级”。

    ![“固件管理”的屏幕截图。](backup-configure-upgrade.png)

7. 选择“继续”。 
8. 当系统提示保存 FortiGate 配置（以 .conf 文件的形式）时，请选择“保存”。
9. 等待固件上传并应用。 等待 FortiGate VM 重启。
10. 在 FortiGate VM 重启后，使用管理员凭据再次登录。
11. 当系统提示你设置仪表板时，选择“稍后”。
12. 教程视频开始播放时，选择“确定”。

### <a name="change-the-management-port-to-tcp-8443"></a>将管理端口更改为 TCP 8443

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统”。
5. 在“管理设置”下，将 HTTPS 端口更改为“8443”，然后选择“应用”。  
6. 应用更改后，浏览器会尝试重载管理页面，但会失败。 从现在开始，管理页面地址是 `https://<address>:8443`。

    ![“上传远程证书”的屏幕截图。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>上传 Azure AD SAML 签名证书

1. 转到 `https://<address>:8443`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “证书”。 
5. 选择“导入” > “远程证书” 。
6. 浏览到从 Azure 租户中的 FortiGate 自定义应用部署下载的证书。 将其选中，然后选择“确定”。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上传和配置自定义 SSL 证书

你可能希望使用自己的 SSL 证书（该证书支持你使用的 FQDN）配置 FortiGate VM。 如果你有权访问以 PFX 格式与私钥打包在一起的 SSL 证书，则该证书可用于此目的。

1. 转到 `https://<address>:8443`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “证书”。 
5. 选择“导入” > “本地证书” > “PKCS #12 证书”。  
6. 浏览到包含 SSL 证书和私钥的 .PFX 文件。
7. 为证书提供 .PFX 密码和一个有意义的名称。 然后选择“确定”。 
8. 在左侧菜单中，选择“系统” > “设置”。 
9. 在“管理设置”下，展开“HTTPS 服务器证书”旁的列表，选择前面导入的 SSL 证书。 
10. 选择“应用”。
11. 关闭浏览器窗口并转到 `https://<address>:8443`。
12. 使用 FortiGate 管理员凭据登录。 你现在应会看到正在使用正确的 SSL 证书。

### <a name="configure-authentication-timeout"></a>配置身份验证超时

1. 转到 Azure 门户，并打开 FortiGate VM 的设置。
2. 在左侧菜单中，选择“串行控制台”。
3. 在串行控制台中使用 FortiGate VM 管理员凭据登录。
4. 在串行控制台中运行以下命令：

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>确保网络接口正在获取 IP 地址

1. 转到 `https://<address>:8443`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
3. 在左侧菜单中选择“网络”。
4. 在“网络”下，选择“接口”。
5. 检查 port1（外部接口）和 port2（内部接口），确保它们从正确的 Azure 子网中获取 IP 地址。
    a. 如果任一端口未从子网中获取 IP 地址（通过 DHCP），请右键单击该端口，然后选择“编辑”。
    b. 在“寻址模式”旁边，确保选择“DHCP”。
    c. 选择“确定”。

    ![网络接口寻址的屏幕截图。](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>确保 FortiGate VM 具有到本地公司资源的正确路由

多宿主 Azure VM 的所有网络接口都位于同一虚拟网络（但可能是单独的子网）。 这通常意味着两个网络接口都会与通过 FortiGate 发布的本地公司资源建立连接。 为此，需要创建自定义路由条目，确保在发出本地公司资源请求时，流量从正确的接口流出。

1. 转到 `https://<address>:8443`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
3. 在左侧菜单中选择“网络”。
4. 在“网络”下，选择“静态路由”。
5. 选择“新建”。
6. 选择“目标”旁边的“子网”。
7. 在“子网”下，指定本地公司资源所在的子网的信息（例如 10.1.0.0/255.255.255.0）
8. 在“网关地址”旁边，指定 port2 连接到的 Azure 子网上的网关（例如，此网关通常以 1 结尾，如 10.6.1.1）
9. 选择“接口”旁的内部网络接口 port2
10. 选择“确定”。

    ![配置路由的屏幕截图。](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>配置 FortiGate SSL VPN

执行[教程：Azure Active Directory 单一登录 (SSO) 与 FortiGate SSL VPN 集成](../../fortigate-ssl-vpn-tutorial.md)中概述的步骤
