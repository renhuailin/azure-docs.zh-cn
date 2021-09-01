---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 08/17/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 49b5ed53aef4de5282e1be0d02df897e8b9921aa
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322909"
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
    <td class="tg-lboi"><strong><p style="font-size: 16px">10</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 MFA</p></strong>安全中心高度重视多重身份验证 (MFA)。 使用这些建议可保护订阅的用户。 <br>可以通过三种方法来启用 MFA 并符合建议：安全默认值、每用户分配、条件访问策略。 在<a href="/azure/security-center/security-center-identity-access">管理订阅上的 MFA 实施</a>中详细了解这些选项。</td>
    <td class="tg-lboi"; width=55%>- 应在对订阅拥有所有者权限的帐户上启用 MFA<br />- 应在对订阅拥有所有者权限的帐户上启用 MFA<br />- 应在对订阅拥有写入权限的帐户上启用 MFA<br />- 应在对订阅拥有写入权限的帐户上启用 MFA</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">8</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">安全管理端口</p></strong>暴力攻击通常以管理端口为目标。 使用这些建议可通过<a href="/azure/security-center/just-in-time-explained">实时 VM 访问</a>和<a href="/azure/virtual-network/network-security-groups-overview">网络安全组</a>等工具减少暴露。</td>
    <td class="tg-lboi"; width=55%>- 面向 Internet 的虚拟机应使用网络安全组进行保护<br />- 应通过实时网络访问控制来保护虚拟机的管理端口<br />- 应关闭虚拟机上的管理端口</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">6</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用系统更新</p></strong>不应用更新会留下未修补的漏洞，使环境容易受到攻击。 使用这些建议可保持运营效率、减少安全漏洞，并为最终用户提供更稳定的环境。 若要部署系统更新，可以使用<a href="/azure/automation/update-management/overview">更新管理解决方案来管理计算机的修补程序和更新</a>。</td>
    <td class="tg-lboi"; width=55%>- 应在基于 Linux 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- 应在基于 Windows 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装系统更新<br />- 应在计算机上安装系统更新<br />- 应在计算机上安装系统更新（由更新中心提供技术支持）</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">6</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正漏洞</p></strong>安全中心包含多个漏洞评估扫描程序，用于检查计算机、数据库和容器注册表中是否存在威胁参与者可能利用的漏洞。 使用这些建议可启用这些扫描程序并查看其发现结果。<br>详细了解如何扫描<a href="/azure/security-center/deploy-vulnerability-assessment-vm">计算机</a>、<a href="/azure/security-center/defender-for-sql-on-machines-vulnerability-assessment">SQL服务器</a>和<a href="/azure/security-center/defender-for-container-registries-usage">容器注册表</a>。</td>
    <td class="tg-lboi"; width=55%>- 应在虚拟机上启用漏洞评估解决方案<br />- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项<br />- 应只从受信任的注册表中部署容器映像<br />- 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供支持）<br />- 应修正虚拟机中的漏洞</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">加密传输中的数据</p></strong>使用这些建议可保护在组件、位置或程序之间移动的数据。 这类数据更容易遭受中间人攻击、窃听和会话劫持。</td>
    <td class="tg-lboi"; width=55%>- 只能通过 HTTPS 访问 API 应用<br />- 应为 MySQL 数据库服务器启用“强制 SSL 连接”<br />- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”<br />- 应仅在 API 应用中要求使用 FTPS<br />- 应仅在函数应用中要求使用 FTPS<br />- 应仅在 Web 应用中要求使用 FTPS<br />- 只能通过 HTTPS 访问函数应用<br />- 应仅启用与 Redis 缓存的安全连接<br />- 应启用到存储帐户的安全传输<br />- 应将 TLS 更新为 API 应用的最新版本<br />- 应将 TLS 更新为函数应用的最新版本<br />- 应将 TLS 更新为 Web 应用的最新版本<br />- 只能通过 HTTPS 访问 Web 应用程序</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">限制未经授权的网络访问</p></strong>Azure 提供了一套工具，旨在确保整个网络中的访问符合最高安全标准。<br>使用这些建议可管理<a href="/azure/security-center/security-center-adaptive-network-hardening">安全中心的自适应网络强化</a>设置、确保为所有相关 PaaS 服务配置 <a href="/azure/private-link/private-link-overview">Azure 专用链接</a>、在虚拟网络上启用 <a href="/azure/firewall/overview">Azure 防火墙</a>等。</td>
    <td class="tg-lboi"; width=55%>- 应在面向 Internet 的虚拟机上应用自适应网络强化建议<br />- 应在与虚拟机关联的网络安全组上限制所有网络端口<br />- 应用程序配置应使用专用链接<br />- Azure Cache for Redis 应驻留在虚拟网络中<br />- Azure 事件网格域应使用专用链接<br />- Azure 事件网格主题应使用专用链接<br />- Azure 机器学习工作区应使用专用链接<br />- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项<br />- Azure SignalR 服务应使用专用链接<br />- Azure Spring Cloud 应使用网络注入<br />- 容器注册表不得允许无限制的网络访问<br />- 容器注册表应使用专用链接<br />- 容器应只侦听允许的端口<br />- CORS 不应允许所有资源都能访问 API 应用<br />- CORS 不应允许所有资源都能访问函数应用<br />- CORS 不应允许所有资源都能访问你的 Web 应用程序<br />- 应在 Key Vault 上启用防火墙<br />- 面向 Internet 的虚拟机应使用网络安全组进行保护<br />- 应禁用虚拟机上的 IP 转发<br />- Kubernetes 服务管理 API 服务器应配置为受限访问权限<br />- 应为 Key Vault 配置专用终结点<br />- 应为 MariaDB 服务器启用专用终结点<br />- 应为 MySQL 服务器启用专用终结点<br />- 应为 PostgreSQL 服务器启用专用终结点<br />- 应为 MariaDB 服务器禁用公用网络访问<br />- 应为 MySQL 服务器禁用公用网络访问<br />- 应为 PostgreSQL 服务器禁用公用网络访问<br />- 服务应只侦听允许的端口<br />- 存储帐户应使用专用链接连接<br />- 存储帐户应使用虚拟网络规则来限制网络访问<br />- 应限制对主机网络和端口的使用<br />- 虚拟网络应受 Azure 防火墙保护<br />- VM 映像生成器模板应使用专用链接</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用静态加密</p></strong>使用这些建议可确保减少存储数据保护方面的错误配置。</td>
    <td class="tg-lboi"; width=55%>- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign<br />- 应对 SQL 数据库启用透明数据加密<br />- 虚拟机应该加密计算和存储资源之间的临时磁盘、缓存和数据流</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">管理访问和权限</p></strong>安全程序的核心是确保用户具有完成其工作（但仅限于此）所需的访问权限：最小特权访问模型。 使用这些建议可管理标识和访问要求。</td>
    <td class="tg-lboi"; width=55%>-对 Linux 虚拟机进行身份验证需要 SSH 密钥<br />- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项<br />- 应避免使用特权提升的容器<br />- 应避免使用共享敏感主机命名空间的容器<br />- 应从订阅中删除弃用的帐户<br />- 应从订阅中删除弃用的帐户<br />- 应从订阅中删除拥有所有者权限的已弃用帐户<br />- 应从订阅中删除拥有所有者权限的已弃用帐户<br />- 应从订阅中删除拥有所有者权限的外部帐户<br />- 应从订阅中删除拥有所有者权限的外部帐户<br />- 应从订阅中删除具有写入权限的外部帐户<br />- 应从订阅中删除具有写入权限的外部帐户<br />- 确保函数应用已启用“客户端证书(传入客户端证书)”<br />- 应在计算机上安装来宾配置扩展<br />- 应强制对容器使用不可变（只读）根文件系统<br />- 应强制对容器使用最低权限 Linux 功能<br />- 应在 API 应用中使用托管标识<br />- 应在函数应用中使用托管标识<br />- 应在 Web 应用中使用托管标识<br />- 应避免特权容器<br />- 应在 Kubernetes 服务上使用基于角色的访问控制<br />- 应避免以根用户身份运行容器<br />- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证<br />- 应使用服务主体（而不是管理证书）来保护你的订阅<br />- 应禁止存储帐户公共访问<br />- 应限制为只有已知列表才能使用 Pod HostPath 卷装载，以限制来自遭入侵容器的节点访问<br />- 应使用系统分配的托管标识来部署虚拟机的来宾配置扩展</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">4</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正安全配置</p></strong>配置错误的 IT 资产受到攻击的风险更高。 使用这些建议可强化基础结构中识别的错误配置。</td>
    <td class="tg-lboi"; width=55%>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项<br />- 应在计算机上解决 Log Analytics 代理运行状况问题<br />- 应在基于 Linux 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- 应在基于 Windows 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应限制替代或禁用容器 AppArmor 配置文件<br />- 应在 Kubernetes 服务上定义 Pod 安全策略（已弃用）<br />- SQL 数据库应已解决漏洞发现<br />- 计算机上的 SQL 服务器应已解决漏洞发现<br />- 应修正容器安全配置中的漏洞<br />- 应修正 Linux 虚拟机上的安全配置漏洞（由来宾配置提供支持）<br />- 应修正计算机上安全配置中的漏洞<br />- 应修正虚拟机规模集上安全配置中的漏洞<br />- 应修正 Windows 虚拟机上的安全配置漏洞（由来宾配置提供支持）<br />- 应对 SQL 托管实例启用漏洞评估<br />- 应对 SQL Server 启用漏洞评估</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">3</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用自适应应用程序控制</p></strong>自适应应用程序控制是一种智能的、自动化的端到端解决方案，可用于控制哪些应用程序可以在计算机上运行。 它还有助于强化计算机免受恶意软件的侵害。</td>
    <td class="tg-lboi"; width=55%>-应在计算机中启用自适应应用程序控制以定义安全应用程序<br />- 应更新自适应应用程序控制策略中的允许列表规则<br />- 应在计算机上解决 Log Analytics 代理运行状况问题<br />- 应在基于 Linux 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在基于 Windows 的 Azure Arc 计算机上安装 Log Analytics 代理</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">2</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">通过 Azure 高级网络解决方案保护应用程序</p></strong>Azure 的高级网络解决方案包括 <a href="/azure/ddos-protection/ddos-protection-overview"></a>Azure DDoS 保护</a>、<a href="/azure/web-application-firewall/overview"></a>Azure Web 应用程序防火墙</a>和<a href="/azure/governance/policy/concepts/policy-for-kubernetes"></a>适用于 Kubernetes 的 Azure Policy 加载项</a>。 使用这些建议可确保通过这些工具和其他工具来保护应用程序。</td>
    <td class="tg-lboi"; width=55%>- 应启用 Azure DDoS 防护标准<br />- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项<br />- 应强制执行容器 CPU 和内存限制<br />- 应为应用程序网关启用 Web 应用程序防火墙 (WAF)<br />- 应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">2</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用终结点保护</p></strong>为确保终结点免受恶意软件的侵害，行为传感器会从终结点的操作系统收集数据并加以处理，然后将此数据发送到私有云进行分析。 安全分析利用大数据、机器学习和其他来源针对威胁提出响应建议。 例如，<a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> 使用威胁情报来识别攻击方法并生成安全警报。<br>安全中心支持以下终结点保护解决方案：Windows Defender、System Center Endpoint Protection、Trend Micro、Symantec v12.1.1.1100、适用于 Windows 的 McAfee v10、适用于 Linux 的 McAfee v10 和适用于 Linux 的 Sophos v9。 如果安全中心检测到以上任一解决方案，则不再显示安装 Endpoint Protection 的建议。</td>
    <td class="tg-lboi"; width=55%>- 应在虚拟机规模集上修正 Endpoint Protection 运行状况故障<br />- 应在计算机上解决 Endpoint Protection 运行状况问题<br />- 应在计算机上解决 Endpoint Protection 运行状况问题<br />- 应在计算机上安装 Endpoint Protection<br />- 应在虚拟机规模集上安装 Endpoint Protection 解决方案<br />- 应在服务器上启用文件完整性监视<br />- 在虚拟机上安装 Endpoint Protection 解决方案<br />- 在计算机上安装 Endpoint Protection 解决方案<br />- 应在计算机上解决 Log Analytics 代理运行状况问题<br />- 应在基于 Linux 的 Azure Arc 计算机上安装 Log Analytics 代理<br />- 应在虚拟机上安装 Log Analytics 代理<br />- 应在虚拟机规模集上安装 Log Analytics 代理<br />- 应在基于 Windows 的 Azure Arc 计算机上安装 Log Analytics 代理</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">1</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用审核和日志记录</p></strong>日志记录数据可让你深入了解过去的问题，防止潜在的问题，可以提高应用程序的性能，并允许自动执行原本手动执行的操作。<br>- <strong>控制和管理日志</strong>提供有关 <a href="/azure/azure-resource-manager/management/overview">Azure 资源管理器</a>操作的信息。<br>- <strong>数据平面日志</strong>提供作为 Azure 资源使用情况的一部分引发的事件的相关信息。<br>- <strong>已处理的事件</strong>提供已处理的分析事件/警报的相关信息。</td>
    <td class="tg-lboi"; width=55%>- 应对 SQL Server 启用审核<br />- 应启用 Azure Data Lake Store 的诊断日志<br />- 应启用 Azure 流分析的诊断日志<br />- 应启用 Batch 帐户的诊断日志<br />- 应启用 Data Lake Analytics 的诊断日志<br />- 应启用事件中心的诊断日志<br />- 应启用密钥保管库的诊断日志<br />- 应启用搜索服务的诊断日志<br />- 应启用服务总线的诊断日志<br />- 应启用虚拟机规模集的诊断日志<br />-应在逻辑应用中启用诊断日志<br />- 应在应用服务中启用诊断日志</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">实现安全最佳做法</p></strong>新式安全做法“假定突破”网络边界。 因此，此控制中的许多最佳做法都集中在标识管理上。<br>丢失密钥和凭据是一个常见问题。 <a href="/azure/key-vault/key-vault-overview">Azure 密钥保管库</a>通过加密密钥、.pfx 文件和密码来保护密钥和机密。<br>虚拟专用网 (VPN) 是访问虚拟机的一种安全方法。 如果 VPN 不可用，请使用复杂的密码和双重身份验证（例如 <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD 多重身份验证</a>）。 双重身份验证避开了固有的仅依赖用户名和密码的弱点。<br>使用强身份验证和授权平台是另一种最佳做法。 组织可以使用联合标识来委派授权标识的管理。 当员工离职，需要撤销其访问权限时，这一点也很重要。</td>
    <td class="tg-lboi"; width=55%>-  删除/批准不受信任的引导组件<br />- [必要时启用] Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据<br />- [必要时启用] Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密<br />- [必要时启用] 认知服务帐户应启用使用客户管理的密钥 (CMK) 的数据加密<br />- [必要时启用] 应使用客户管理的密钥 (CMK) 来加密容器寄存器<br />- [必要时启用] MySQL 服务器应使用客户管理的密钥进行静态数据加密<br />- [必要时启用] PostgreSQL 服务器应使用客户管理的密钥进行静态数据加密<br />- [必要时启用] SQL 托管实例应使用客户管理的密钥对数据进行静态加密<br />- [必要时启用] SQL 服务器应使用客户管理的密钥进行静态数据加密<br />- [必要时启用] 存储帐户应使用客户管理的密钥 (CMK) 进行加密<br />- 最多只能为订阅指定 3 个所有者<br />- 应限制对具有防火墙和虚拟网络配置的存储帐户的访问<br />- 应在 SQL 托管实例的高级数据安全设置中启用所有高级威胁防护类型<br />- 应在 SQL Server 的高级数据安全设置中启用所有高级威胁防护类型<br />- 应为 SQL Server 预配 Azure Active Directory 管理员<br />-API 管理服务应使用虚拟网络<br />- 应将 SQL Server 的审核保留设置为至少 90 天<br />- 应对订阅启用 Log Analytics 代理自动预配<br />- 应加密自动化帐户变量<br />- 应为虚拟机启用 Azure 备份<br />-Azure Cosmos DB 帐户应有防火墙规则<br />- 应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL<br />- 应为未受保护的 SQL 托管实例启用 Azure Defender for SQL<br />-认知服务帐户应启用数据加密<br />-认知服务帐户应限制网络访问<br />-认知服务帐户应使用客户自有存储或启用数据加密<br />- 默认 IP 筛选策略应为“拒绝”<br />- 应启用 IoT 中心的诊断日志<br />- 应启用高严重性警报的电子邮件通知<br />- 应启用向订阅所有者发送高严重性警报的电子邮件通知<br />-确保 API 应用的“客户端证书(传入客户端证书)”设置为“打开”<br />- 应从订阅中删除拥有读取权限的外部帐户<br />- 应从订阅中删除拥有读取权限的外部帐户<br />- 应为 Azure Database for MariaDB 启用异地冗余备份<br />- 应为 Azure Database for MySQL 启用异地冗余备份<br />- 应为 Azure Database for PostgreSQL 启用异地冗余备份<br />- 应在受支持的 Linux 虚拟机规模集上安装来宾证明扩展名<br />- 应在受支持的 Linux 虚拟机上安装来宾证明扩展名<br />- 应在受支持的 Windows 虚拟机规模集上安装来宾证明扩展名<br />- 应在受支持的 Windows 虚拟机上安装来宾证明扩展名<br />- 应在计算机上安装来宾配置扩展<br />- 完全相同的身份验证凭据<br />- IoT 设备 - 代理正在发送未充分利用的消息<br />- IoT 设备 - 经审核的进程已停止发送事件<br />- IoT 设备 - 打开设备上的端口<br />- IoT 设备 - 操作系统基线验证失败<br />- IoT 设备 - 在其中一个链中找到了宽容防火墙策略<br />- IoT 设备 - 在输入链中找到了宽容防火墙规则<br />- IoT 设备 - 在输出链中找到了宽容防火墙规则<br />- IoT 设备 - 需要进行 TLS 加密套件升级<br />- IP 筛选器规则的 IP 范围大<br />- 应将 Java 更新为 API 应用的最新版本<br />- 应将 Java 更新为函数应用的最新版本<br />- 应将 Java 更新为 Web 应用的最新版本<br />- Key Vault 密钥应具有到期日期<br />- Key Vault 机密应具有到期日期<br />- 密钥保管库应启用清除保护<br />- 密钥保管库应启用软删除<br />-Kubernetes 群集应只可通过 HTTPS 进行访问<br />- Kubernetes 群集应禁用自动装载 API 凭据<br />- Kubernetes 群集不应授予 CAPSYSADMIN 安全功能<br />- Kubernetes 群集不应使用默认命名空间<br />- Linux 虚拟机应强制实施内核模块签名验证<br />- Linux 虚拟机应使用安全启动<br />- 应重启计算机以应用安全配置更新<br />- 应在对订阅拥有读取权限的帐户上启用 MFA<br />- 应在对订阅拥有读取权限的帐户上启用 MFA<br />- 应在 Linux 虚拟机上安装网络流量数据收集代理<br />- 应在 Windows 虚拟机上安装网络流量数据收集代理<br />-应启用网络观察程序<br />- 应使用网络安全组来保护非面向 Internet 的虚拟机<br />- 应将 PHP 更新为 API 应用的最新版本<br />- 应将 PHP 更新为 Web 应用的最新版本<br />-应启用 Azure SQL 数据库上的专用终结点连接<br />-应禁用 Azure SQL 数据库上的公用网络访问<br />-应为认知服务帐户禁用公用网络访问<br />- 应将 Python 更新为 API 应用的最新版本<br />- 应将 Python 更新为函数应用的最新版本<br />- 应将 Python 更新为 Web 应用的最新版本<br />- 应为 API 应用禁用远程调试<br />- 应为函数应用禁用远程调试<br />- 应禁用 Web 应用程序的远程调试<br />- 应在受支持的 Windows 虚拟机上启用安全启动<br />- 应将存储帐户迁移到新的 Azure 资源管理器资源<br />- 子网应与网络安全组关联<br />- 订阅应有一个联系人电子邮件地址，用于接收安全问题通知<br />- 应向订阅分配多个所有者<br />- 存储在 Azure Key Vault 中的证书的有效期不得超过 12 个月<br />- 虚拟机来宾证明状态应为正常<br />- 应将虚拟机迁移到新的 Azure 资源管理器资源<br />- 应使用系统分配的托管标识来部署虚拟机的来宾配置扩展<br />- 应在受支持的虚拟机上启用 vTPM<br />- Web 应用应该请求一个用于所有传入请求的 SSL 证书<br />- 应在计算机上启用 Windows Defender 攻击防护<br />-应将 Windows Web 服务器配置为使用安全通信协议</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用数据分类</p></strong>通过按敏感度和业务影响对组织数据进行分类，可以为数据确定并分配值，为治理提供策略和基础。<br><a href="/azure/information-protection/what-is-information-protection">Azure 信息保护</a>可帮助进行数据分类。 它使用加密、标识和授权策略来保护数据并限制数据访问。 Microsoft 使用的一些分类包括非业务、公共、常规、机密和高度机密。</td>
    <td class="tg-lboi"; width=55%>-应对 SQL 数据库中的敏感数据进行分类</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">0</p></strong></td>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 Azure Defender</p></strong>使用这些建议可启用任何 <a href="/azure/security-center/azure-defender">Azure Defender</a> 计划。</td>
    <td class="tg-lboi"; width=55%>- 已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 的扩展<br />- 应启用适用于应用服务的 Azure Defender<br />- 应启用适用于 Azure SQL 数据库服务器的 Azure Defender<br />- 应启用适用于容器注册表的 Azure Defender<br />- 应启用 Azure Defender for DNS<br />- 应启用适用于 Key Vault 的 Azure Defender<br />- 应启用 Azure Defender for Kubernetes<br />- 应启用 Azure Defender for Resource Manager<br />- 应启用适用于服务器的 Azure Defender<br />- 应启用适用于计算机上的 SQL 服务器的 Azure Defender<br />- 应启用适用于存储的 Azure Defender</td>
  </tr>
</tbody>
</table>
