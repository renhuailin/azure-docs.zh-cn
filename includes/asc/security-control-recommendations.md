---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/28/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 963dcc047f697d851677263d7dba6fd4988b9323
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099686"
---
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>安全评分</b></th>
    <th class="tg-cly1"><b>安全控制和说明</b></th>
    <th class="tg-cly1"><b>建议</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">万</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 MFA</p></strong>如果只使用密码对用户进行身份验证，则会开放攻击途径。 如果密码较弱或者已在其他位置公开，那么如何确定是该用户在使用用户名和密码登录？<br>启用 <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> 后，帐户将更安全，并且用户仍然可以通过单一登录 (SSO) 向几乎所有应用程序验证身份。</td>
    <td class="tg-lboi"; width=55%>- 应在对订阅拥有所有者权限的帐户上启用 MFA<br />- 应在对订阅具有写入权限的帐户上启用 MFA</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">8</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">安全管理端口</p></strong>暴力攻击以管理端口为目标来获取对 VM 的访问权限。 由于这些端口并不总是需要打开，因此，一个缓解策略是使用实时网络访问控制、网络安全组和虚拟机端口管理来降低对端口的暴露。<br>由于许多 IT 组织不会阻止从其网络出站的 SSH 通信，因此攻击者可以创建加密隧道，允许受感染系统上的 RDP 端口与攻击者命令通信，以便控制服务器。 攻击者可以使用 Windows 远程管理子系统在你的环境中横向移动，并使用盗用的凭据访问网络上的其他资源。</td>
    <td class="tg-lboi"; width=55%>- 面向 Internet 的虚拟机应受网络安全组保护<br />- 应通过实时网络访问控制来保护虚拟机的管理端口<br />- 应关闭虚拟机上的管理端口</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">共</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用系统更新</p></strong>系统更新使组织能够保持运营效率、减少安全漏洞，并为最终用户提供更稳定的环境。 不应用更新会留下未修补的漏洞，使环境容易受到攻击。 这些漏洞可能会被人利用，导致数据丢失、数据泄露、勒索软件和资源滥用。 若要部署系统更新，可以使用<a href="/azure/automation/update-management/overview">更新管理解决方案来管理虚拟机的修补程序和更新</a>。 更新管理是指控制软件版本的部署和维护的过程。</td>
    <td class="tg-lboi"; width=55%>- Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本<br />- 应在你的计算机上解决 Log Analytics 代理运行状况问题<br />- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上<br />- 应在计算机上安装监视代理<br />- 应为云服务角色更新 OS 版本<br />- 应在虚拟机规模集上安装系统更新<br />- 应在计算机上安装系统更新<br />- 系统更新应安装在你的计算机上 (更新中心提供支持) <br />- 应重启计算机来应用系统更新</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">共</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正漏洞</p></strong>漏洞是攻击者可用来破坏资源机密性、可用性或完整性的薄弱环节。 <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">管理漏洞</a>可以减少组织暴露、强化终结点外围应用、提高组织复原能力以及减少资源的受攻击面。 威胁和漏洞管理可显示错误的软件和安全配置，并提供缓解建议。</td>
    <td class="tg-lboi"; width=55%>- 应在虚拟机上启用漏洞评估解决方案<br />- 应在托管实例上启用适用于 SQL 的 Azure Defender<br />- 应在 SQL server 上启用适用于 SQL 的 Azure Defender<br />- 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序<br />- 只应从受信任的注册表部署容器映像<br />- 在虚拟机上启用内置漏洞评估解决方案<br />- Azure 容器注册表映像中的漏洞应对 (由 Qualys 提供支持) <br />- 应修正虚拟机中的漏洞<br />- 应通过漏洞评估解决方案修正漏洞<br />- 应修正 SQL 数据库上的漏洞评估结果<br />- 应修正计算机上的 SQL server 上的漏洞评估结果<br />- 应对 SQL 托管实例启用漏洞评估<br />- 应对 SQL Server 启用漏洞评估<br />- 应在虚拟机上安装漏洞评估解决方案</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">加密传输中的数据</p></strong>数据在组件、位置或程序之间传输时为 "传输中"。 无法保护传输中的数据的组织更容易遭受中间人攻击、窃听和会话劫持。 应使用 SSL/TLS 协议交换数据，并建议使用 VPN。 通过 Internet 在 Azure 虚拟机和本地位置之间发送加密数据时，可以使用虚拟网络网关（例如 <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN 网关</a>）发送加密流量。</td>
    <td class="tg-lboi"; width=55%>- 只能通过 HTTPS 访问 API 应用<br />- 应为 MySQL 数据库服务器启用“强制 SSL 连接”<br />- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”<br />- 应仅在 API 应用中要求使用 FTPS<br />- 应仅在函数应用中要求使用 FTPS<br />- 应仅在 Web 应用中要求使用 FTPS<br />- 只能通过 HTTPS 访问函数应用<br />- IoT 设备-需要 TLS 密码套件升级<br />- 应仅启用与 Redis 缓存的安全连接<br />- 应启用到存储帐户的安全传输<br />- 应将 TLS 更新为 API 应用的最新版本<br />- 应将 TLS 更新为函数应用的最新版本<br />- 应将 TLS 更新为 Web 应用的最新版本<br />- 只能通过 HTTPS 访问 Web 应用程序</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">限制未经授权的网络访问</p></strong>组织内的终结点提供从虚拟网络到受支持的 Azure 服务的直接连接。 子网中的虚拟机可以与所有资源通信。 若要限制与子网内资源的通信，请创建一个网络安全组并将其关联到子网。 组织可以通过创建入站和出站规则来限制和防范未经授权的流量。</td>
    <td class="tg-lboi"; width=55%>- 应在面向 Internet 的虚拟机上应用自适应网络强化建议<br />- 所有网络端口应在与虚拟机关联的网络安全组上受到限制<br />- 应用配置应使用专用链接<br />- 适用于 Redis 的 Azure 缓存应驻留在虚拟网络中<br />- Azure 事件网格域应使用专用链接<br />- Azure 事件网格主题应使用专用链接<br />- Azure 机器学习工作区应使用专用链接<br />- 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序<br />- Azure SignalR 服务应使用专用链接<br />- Azure 春季 Cloud 应使用网络注入<br />- 容器注册表不允许无限制的网络访问<br />- 容器注册表应使用专用链接<br />- 容器只应侦听允许的端口<br />- CORS 不应允许所有资源都能访问 API 应用<br />- CORS 不应允许所有资源都能访问函数应用<br />- CORS 不应允许每个资源访问你的 Web 应用程序<br />- 默认 IP 筛选器策略应为 Deny<br />- 应在 Key Vault 上启用防火墙<br />- 面向 Internet 的虚拟机应受网络安全组保护<br />- IoT 设备-打开设备上的端口<br />- IoT 设备-找到其中一个链中的许可防火墙策略<br />- IoT 设备-找到输入链中的 "许可防火墙" 规则<br />- IoT 设备-找到输出链中的 "许可防火墙" 规则<br />- IP 筛选器规则大型 IP 范围<br />- 应禁用虚拟机上的 IP 转发<br />- 应将 Kubernetes Services 管理 API 服务器配置为具有受限访问权限<br />- 专用终结点应配置为 Key Vault<br />- 应为 MariaDB 服务器启用专用终结点<br />- 应为 MySQL 服务器启用专用终结点<br />- 应为 PostgreSQL 服务器启用专用终结点<br />- 应为 MariaDB 服务器禁用公共网络访问<br />- 应为 MySQL 服务器禁用公共网络访问<br />- 应为 PostgreSQL 服务器禁用公共网络访问<br />- 服务只应侦听允许的端口<br />- 存储帐户应使用专用链接连接<br />- 存储帐户应使用虚拟网络规则限制网络访问<br />- 应限制主机网络和端口的使用<br />- 虚拟网络应受 Azure 防火墙保护<br />- VM 映像生成器模板应使用专用链接</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用静态加密</p></strong><a href="/azure/security/fundamentals/encryption-atrest">静态加密</a>为已存储的数据提供数据保护。 对静态数据进行的攻击包括试图获得对存储数据的硬件的物理访问权限。 Azure 使用对称加密来加密和解密大量静态数据。 将使用对称加密密钥在将数据写入到存储时对数据进行加密。 该加密密钥还用于解密准备在内存中使用的数据。 必须将密钥存储在实施了基于标识的访问控制和审核策略的安全位置。 Azure 密钥保管库就是这样的安全位置。 如果攻击者获取了加密数据但未获取加密密钥，则攻击者必须破解加密才能访问数据。</td>
    <td class="tg-lboi"; width=55%>- 应加密自动化帐户变量<br />- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据<br />- 应使用客户管理的密钥 (CMK 来加密 Azure 机器学习工作区) <br />- 应为 MySQL 服务器启用自带密钥数据保护<br />- 应为 PostgreSQL 服务器启用自带密钥数据保护<br />- 认知服务帐户应使用客户管理的密钥来启用数据加密 (CMK) <br />- 容器注册表应使用客户托管的密钥进行加密 (CMK) <br />- 应对虚拟机应用磁盘加密<br />- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign<br />- SQL 托管实例应使用客户管理的密钥来加密静态数据<br />- SQL server 应使用客户管理的密钥来加密静态数据<br />- 存储帐户应使用客户管理的密钥 (CMK) 进行加密<br />- 应对 SQL 数据库启用透明数据加密</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">管理访问权限和权限</p></strong>安全程序的核心是确保用户具有完成其工作（但仅限于此）所需的访问权限：<a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">最小特权访问模型</a>。<br>通过使用 <a href="/azure/role-based-access-control/overview">AZURE RBAC)  (azure 基于角色的访问控制 </a>创建角色分配来控制对资源的访问权限。 角色分配由三个元素组成：<br>- <strong>安全主体</strong>：用户请求访问的对象<br>- <strong>角色定义</strong>：他们的权限<br>- <strong>作用域</strong>：权限适用于的资源集</td>
    <td class="tg-lboi"; width=55%>- 对 Linux 计算机进行身份验证时，应要求 SSH 密钥<br />- 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序<br />- 应避免具有特权升级的容器<br />- 应避免共享敏感主机命名空间的容器<br />- 应从订阅中删除弃用的帐户<br />- 应从订阅中删除不推荐使用的具有所有者权限的帐户<br />- 应从订阅中删除具有所有者权限的外部帐户<br />- 应从订阅中删除具有写入权限的外部帐户<br />- 函数应用应具有客户端证书 (传入的客户端证书) 启用<br />- 相同的身份验证凭据<br />- 应为容器强制实施 (只读) 根文件系统<br />- 应为容器强制实施最低权限的 Linux 功能<br />- 应在 API 应用中使用托管标识<br />- 托管标识应在函数应用中使用<br />- 应在 web 应用中使用托管标识<br />- 应避免特权容器<br />- 应在 Kubernetes Services 上使用 Role-Based 访问控制<br />- 应避免以 root 用户身份运行容器<br />- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证<br />- 应使用服务主体（而不是管理证书）来保护你的订阅<br />- 应禁止存储帐户公共访问<br />- 应向订阅分配多个所有者<br />- 应将 pod HostPath 卷装载的使用限制为已知列表，以限制节点对受攻击的容器的访问</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正安全配置</p></strong>配置错误的 IT 资产受到攻击的风险更高。 当部署资产并且必须在截止日期之前完成时，通常会忘记基本的强化措施。 错误的安全配置可能出现在基础结构中的任何级别：从操作系统和网络设备到云资源。<br>Azure 安全中心会不断将资源的配置与行业标准、法规和基准中的要求进行比较。 配置了对组织而言很重要的相关“合规性包”（标准和基线）后，任何差距都会产生安全建议，其中包括 CCEID 以及对潜在安全影响的说明。<br>常用包是 <a href="/azure/security/benchmarks/introduction">Azure 安全性基准</a> 和 <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure 基础基准版本 1.1.0</a>。</td>
    <td class="tg-lboi"; width=55%>- 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序<br />- IoT 设备-审核进程停止发送事件<br />- IoT 设备-操作系统基准验证失败<br />- 应在你的计算机上解决 Log Analytics 代理运行状况问题<br />- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上<br />- 应在计算机上安装监视代理<br />- 重写或禁用容器 AppArmor 应限制配置文件<br />- Pod 安全策略应在 Kubernetes Services 上定义 (弃用) <br />- 应在 Linux 虚拟机上启用安全启动<br />- 虚拟机应为启动完整性运行状况证明<br />- 虚拟机的来宾配置扩展应与系统分配的托管标识一起部署<br />- 应修正容器安全配置中的漏洞<br />- 应修正计算机上安全配置中的漏洞<br />- 应修正虚拟机规模集上安全配置中的漏洞</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">三维空间</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用自适应应用程序控制</p></strong>自适应应用程序控制 (AAC) 是一种智能的、自动化的端到端解决方案，可用于控制哪些应用程序可以在 Azure 计算机和非 Azure 计算机上运行。 它还有助于强化计算机免受恶意软件的侵害。<br>安全中心使用机器学习为一组计算机创建一个已知安全应用程序列表。<br>这种将已批准的应用程序列入列表的创新方法在不增加管理复杂性的情况下提供了安全优势。<br>AAC 尤其适用于需要运行一组特定应用程序的专用服务器。</td>
    <td class="tg-lboi"; width=55%>- 应在你的计算机上启用用于定义安全应用程序的自适应应用程序控件<br />- 应更新自适应应用程序控制策略中的允许列表规则<br />- 应在你的计算机上解决 Log Analytics 代理运行状况问题<br />- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上<br />- 应在虚拟机上安装 Log Analytics 代理<br />- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上<br />- 应在计算机上安装监视代理</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">pps-2</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">通过 Azure 高级网络解决方案保护应用程序</p></strong></td>
    <td class="tg-lboi"; width=55%>- 应启用 Azure DDoS 保护标准<br />- 应在群集上安装和启用适用于 Kubernetes 的 Azure 策略外接程序<br />- 应强制实施容器 CPU 和内存限制<br />- 应为应用程序网关启用 Web 应用程序防火墙 (WAF) <br />- 应为 Azure 前门服务服务启用 Web 应用程序防火墙 (WAF) </td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">pps-2</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 endpoint protection</p></strong>为确保终结点免受恶意软件的侵害，行为传感器会从终结点的操作系统收集数据并加以处理，然后将此数据发送到私有云进行分析。 安全分析利用大数据、机器学习和其他来源针对威胁提出响应建议。 例如，<a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> 使用威胁情报来识别攻击方法并生成安全警报。<br>安全中心支持以下终结点保护解决方案：Windows Defender、System Center Endpoint Protection、Trend Micro、Symantec v12.1.1.1100、适用于 Windows 的 McAfee v10、适用于 Linux 的 McAfee v10 和适用于 Linux 的 Sophos v9。 如果安全中心检测到以上任一解决方案，则不再显示安装 Endpoint Protection 的建议。</td>
    <td class="tg-lboi"; width=55%>- 应在虚拟机规模集上修正 Endpoint Protection 运行状况故障<br />- 应在计算机上解决 Endpoint Protection 运行状况问题<br />- 应在计算机上解决 Endpoint Protection 运行状况问题<br />- 应在你的计算机上安装 Endpoint protection<br />- 应在虚拟机规模集上安装 Endpoint Protection 解决方案<br />- 应在服务器上启用文件完整性监视<br />- 在虚拟机上安装 Endpoint Protection 解决方案<br />- 在计算机上安装 Endpoint Protection 解决方案<br />- 应在你的计算机上解决 Log Analytics 代理运行状况问题<br />- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上<br />- 应在计算机上安装监视代理</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">2</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用审核和日志记录</p></strong>日志记录数据可让你深入了解过去的问题，防止潜在的问题，可以提高应用程序的性能，并允许自动执行原本手动执行的操作。<br>- <strong>控制和管理日志</strong>提供有关 <a href="/azure/azure-resource-manager/management/overview">Azure 资源管理器</a>操作的信息。<br>- <strong>数据平面日志</strong>提供作为 Azure 资源使用情况的一部分引发的事件的相关信息。<br>- <strong>已处理的事件</strong>提供已处理的分析事件/警报的相关信息。</td>
    <td class="tg-lboi"; width=55%>- 应对 SQL Server 启用审核<br />- 应启用 Azure Data Lake Store 的诊断日志<br />- 应启用 Azure 流分析的诊断日志<br />- 应启用 Batch 帐户的诊断日志<br />- 应启用 Data Lake Analytics 的诊断日志<br />- 应启用事件中心的诊断日志<br />- 应启用 IoT 中心的诊断日志<br />- 应启用密钥保管库的诊断日志<br />- 应启用搜索服务中的诊断日志<br />- 应启用服务总线的诊断日志<br />- 应启用虚拟机规模集的诊断日志<br />- 应在逻辑应用中启用诊断日志<br />- 应在应用服务中启用诊断日志</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">实现安全最佳做法</p></strong>最新的安全做法 "假定违反" 网络外围网络。 因此，此控制中的许多最佳做法都集中在标识管理上。<br>丢失密钥和凭据是一个常见问题。 <a href="/azure/key-vault/key-vault-overview">Azure 密钥保管库</a>通过加密密钥、.pfx 文件和密码来保护密钥和机密。<br>虚拟专用网 (VPN) 是访问虚拟机的一种安全方法。 如果 Vpn 不可用，则使用复杂的密码和双因素身份验证，例如 <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD 多重身份验证</a>。 双重身份验证避开了固有的仅依赖用户名和密码的弱点。<br>使用强身份验证和授权平台是另一种最佳做法。 组织可以使用联合标识来委派授权标识的管理。 当员工离职，需要撤销其访问权限时，这一点也很重要。</td>
    <td class="tg-lboi"; width=55%>- 最多只能为订阅指定 3 个所有者<br />- 应限制对具有防火墙和虚拟网络配置的存储帐户的访问<br />- 应在面向内部的虚拟机上应用自适应网络强化建议<br />- 应在 SQL 托管实例的高级数据安全设置中启用所有高级威胁防护类型<br />- 应在 SQL Server 的高级数据安全设置中启用所有高级威胁防护类型<br />- 应为 SQL Server 预配 Azure Active Directory 管理员<br />- API 管理服务应使用虚拟网络<br />- 应将 SQL Server 的审核保留设置为至少 90 天<br />- 应在订阅上启用 Log Analytics 代理的自动预配<br />- 应为虚拟机启用 Azure 备份<br />- Azure Cosmos DB 帐户应有防火墙规则<br />- 认知服务帐户应启用数据加密<br />- 认知服务帐户应限制网络访问<br />- 认知服务帐户应使用客户拥有的存储或启用数据加密<br />- 应启用高严重性警报的电子邮件通知<br />- 应启用高严重性警报的电子邮件通知订阅所有者<br />- 确保 API 应用已将客户端证书传入客户端证书设置为 On<br />- 应从订阅中删除拥有读取权限的外部帐户<br />- 应为 Azure Database for MariaDB 启用异地冗余备份<br />- 应为 Azure Database for MySQL 启用异地冗余备份<br />- 应为 Azure Database for PostgreSQL 启用异地冗余备份<br />- 应在你的计算机上安装来宾配置扩展<br />- IoT 设备-发送不充分利用消息的代理<br />- 应将 Java 更新为 API 应用的最新版本<br />- 应将 Java 更新为函数应用的最新版本<br />- 应将 Java 更新为 Web 应用的最新版本<br />- Key Vault 密钥应具有到期日期<br />- Key Vault 机密应具有到期日期<br />- Key vault 应启用清除保护<br />- Key vault 应启用软删除<br />- Kubernetes 群集只能通过 HTTPS 访问<br />- 应在对订阅拥有读取权限的帐户上启用 MFA<br />- 网络流量数据收集代理应安装在 Linux 虚拟机上<br />- 应在 Windows 虚拟机上安装网络流量数据收集代理<br />- 应启用网络观察程序<br />- 应使用网络安全组来保护非面向 Internet 的虚拟机<br />- 应将 PHP 更新为 API 应用的最新版本<br />- 应将 PHP 更新为 Web 应用的最新版本<br />- 应启用 Azure SQL 数据库上的专用终结点连接<br />- 应禁用对 Azure SQL 数据库的公共网络访问<br />- 应为认知服务帐户禁用公共网络访问<br />- 应将 Python 更新为 API 应用的最新版本<br />- 应将 Python 更新为函数应用的最新版本<br />- 应将 Python 更新为 Web 应用的最新版本<br />- 应为 API 应用禁用远程调试<br />- 应为函数应用禁用远程调试<br />- 对于 Web 应用程序，应关闭远程调试<br />- 应将存储帐户迁移到新的 Azure 资源管理器资源<br />- 子网应与网络安全组相关联<br />- 订阅应具有安全问题的联系人电子邮件地址<br />- 存储在 Azure Key Vault 中的证书的有效期不应超过12个月<br />- 应将虚拟机迁移到新的 Azure 资源管理器资源<br />- Web 应用应该请求一个用于所有传入请求的 SSL 证书<br />- 应在你的计算机上启用 Windows Defender 攻击防护<br />- Windows web 服务器应配置为使用安全通信协议</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用数据分类</p></strong>通过按敏感度和业务影响对组织数据进行分类，可以为数据确定并分配值，为治理提供策略和基础。<br><a href="/azure/information-protection/what-is-information-protection">Azure 信息保护</a>可帮助进行数据分类。 它使用加密、标识和授权策略来保护数据并限制数据访问。 Microsoft 使用的一些分类包括非业务、公共、常规、机密和高度机密。</td>
    <td class="tg-lboi"; width=55%>- 应该对 SQL 数据库中的敏感数据进行分类</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用高级威胁防护</p></strong>Azure 安全中心的可选 Azure Defender 威胁防护计划为你的环境提供了全面的防御。 当安全中心检测到环境中的任何区域遭到威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。<br>每个 Azure Defender 计划都是单独的可选产品/服务，可以使用此安全控件中的相关建议启用该产品/服务。<br><a href="/azure/security-center/threat-protection">详细了解安全中心的威胁防护</a>。</td>
    <td class="tg-lboi"; width=55%>- 应启用适用于应用服务的 Azure Defender<br />- 应为 Azure SQL Database 服务器启用 azure Defender<br />- 应启用容器注册表的 Azure Defender<br />- 应启用适用于 Key Vault 的 Azure Defender<br />- 应启用适用于 Kubernetes 的 Azure Defender<br />- 应启用服务器的 Azure Defender<br />- 应为计算机上的 SQL server 启用 Azure Defender<br />- 应启用 Azure Defender 存储</td>
  </tr>
</tbody>
</table>
