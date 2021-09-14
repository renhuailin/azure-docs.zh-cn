---
title: 旨在防范勒索软件的 Azure 备份和还原计划 | Microsoft Docs
description: 了解在勒索软件攻击之前和攻击期间，如何保护关键业务系统并确保业务操作快速恢复。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 08/27/2021
ms.openlocfilehash: 6a9b251b9895b3d28bdeba59f121b646b7d8cd96
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477521"
---
# <a name="backup-and-restore-plan-to-protect-against-ransomware"></a>旨在防范勒索软件的备份和还原计划

勒索软件攻击会故意加密或擦除数据和系统，以强制组织向攻击者付款。 这些攻击的目标是数据、备份以及无需向攻击者付款而进行恢复所需的关键文档（以此增加组织付款的几率）。

本文介绍了攻击之前如何保护关键业务系统以及攻击期间如何确保业务操作快速恢复。

> [!NOTE]
> 做好防范勒索软件的准备还可提高在遭受自然灾害和快速攻击（例如 [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) 和 [(Not)Petya](https://attack.mitre.org/software/S0368/)）后的复原能力。 & 

## <a name="what-is-ransomware"></a>什么是勒索软件？

勒索软件是一种通过对文件和文件夹进行加密来阻止用户访问重要数据和系统的敲诈勒索攻击。 攻击者使用勒索软件通过要钱（通常以加密货币形式）向受害者勒索钱财，并以提供解密密钥或不向暗网或公共 Internet 发布敏感数据作为交换条件。

尽管早期勒索软件主要使用恶意软件以网络钓鱼形式或在设备之间传播，但[人为操作的勒索软件](/security/compass/human-operated-ransomware)已经出现。这类软件由人类攻击操作者带动一伙主动攻击者，以组织中的各个系统（而非单个设备或某组设备）为攻击目标。 攻击可以：

- 加密数据
- 外泄数据
- 损坏备份

勒索软件利用攻击者对常见系统和安全配置不当与漏洞的了解来入侵组织、浏览企业网络并在攻击时适应环境及其薄弱点。

勒索软件在某个特定日期实际进行勒索之前，可在最前期、持续几周或几个月分阶段外泄数据。

勒索软件还可以逐渐加密数据，同时将密钥保留于系统。 密钥仍然可用时，你能够使用数据而注意不到勒索软件。 但备份属于加密数据。 当加密完所有数据且最近备份也属于加密数据时，系统将删除密钥，由此将导致无法再读取数据。

攻击在外泄文件的同时，还会在网络留下后门，以便后续开展恶意活动，这通常也是真正的危害之处。无论是否支付赎金，这些风险将持续存在。 这些攻击对业务操作而言可能具有灾难性且难以彻底清除，需要完全逐出攻击者才能防范后续攻击。 早期形式的勒索软件只需修正恶意软件即可，而人为操作的勒索软件则不同，在最初遭到入侵后，它会继续威胁你的业务运营。

### <a name="impact-of-an-attack"></a>攻击的影响

很难准确量化勒索软件攻击对任何组织的影响。 根据攻击范围，影响可能包括：

- 数据丢失
- 业务操作中断
- 财务损失
- 知识产权失窃
- 客户信任或自身信誉受损
- 承担法律费用

## <a name="how-can-you-protect-yourself"></a>如何加强自身防范？

要想防止自己成为勒索软件的受害者，最好能够采取预防措施并拥有相应的工具，确保组织有效防范攻击者在渗透系统时进行的逐步攻击。

可以通过将组织迁移到云服务来减少本地风险。 Microsoft 在本机安全功能方面大力投入，这些功能可让 Microsoft Azure 在面对勒索软件攻击时具有复原能力，并帮助组织有效抵御勒索软件攻击技术。 若要全面了解勒索软件和敲诈勒索，并全面了解如何保护你的组织，请运用[人为操作的勒索软件缓解项目计划](https://download.microsoft.com/download/7/5/1/751682ca-5aae-405b-afa0-e4832138e436/RansomwareRecommendations.pptx) PowerPoint 演示文稿中的信息。

应假设自己在某些时间点会成为勒索软件攻击的受害者。 若要保护数据并避免支付赎金，最重要的步骤之一是为业务关键型信息制定可靠的备份和还原计划。 由于勒索软件攻击者在无效化备份应用程序和操作系统功能（如卷影复制）方面大力投入，因此拥有恶意攻击者无法访问的备份至关重要。

### <a name="azure-backup"></a>Azure 备份

无论是传输中的数据还是静态数据，[Azure 备份](/azure/backup/backup-overview)都可确保备份环境的安全。 使用 Azure 备份[可以备份](/azure/backup/backup-overview#what-can-i-back-up)：

- 本地文件、文件夹和系统状态
- 整个 Windows/Linux VM
- Azure 托管磁盘
- Azure 文件存储共享到存储帐户
- Azure VM 上运行的 SQL Server 数据库

备份数据存储在 Azure 存储中，来宾或攻击者不能直接访问备份存储或其内容。 通过虚拟机备份，Azure 结构完成备份快照创建和存储，期间来宾或攻击者只能暂停工作负载以进行应用程序一致备份而不能参与其他操作。 使用 SQL 和 SAP HANA 时，备份扩展会暂时获得对特定 Blob 的写入访问权限。 这样，即使环境遭到攻击，攻击者也无法篡改或删除现有备份。

Azure 备份提供内置监视和警报功能，用于查看和配置 Azure 备份相关事件的操作。 备份报表充当一站式目标，在其中能够以不同的粒度级别跟踪使用情况、审核备份和还原，以及识别关键趋势。 使用 Azure 备份的监视和报告工具可以在发生任何未经授权的、可疑的或恶意的活动后立即获得警报。

已添加检查，确保只有效用户才可执行各种操作。 这些操作包括添加额外的身份验证层。 为关键操作添加额外的身份验证层时，[修改联机备份](/azure/backup/backup-azure-security-feature#prevent-attacks)之前系统会提示输入安全 PIN。

详细了解内置于 Azure 备份的[安全功能](/azure/backup/security-overview)。

### <a name="validate-backups"></a>验证备份

创建备份时及还原之前，请验证备份是否正常。 我们建议使用[恢复服务保管库](/azure/backup/backup-azure-recovery-services-vault-overview)，它是 Azure 中用于存储数据的存储实体。 数据通常是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库及本地资产存储备份数据。 恢复服务保管库有助于轻松组织备份数据并提供如下功能：

- 增强功能，确保可以保护备份并安全恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](/azure/backup/backup-azure-security-feature)。
- 从中央门户监视混合 IT 环境（Azure IaaS VM 和本地资产）。 [了解详细信息](/azure/backup/backup-azure-monitoring-built-in-monitor)。
- 兼容 Azure 基于角色的访问控制 (Azure RBAC)，其中 Azure RBAC 可限制对定义用户角色集的备份和还原访问权限。 Azure RBAC 提供各种内置角色，而 Azure 备份包含三个用于管理恢复点的内置角色。 [了解详细信息](/azure/backup/backup-rbac-rs-vault)。
- 软删除保护，即使恶意参与者删除备份（或意外删除备份数据）。 备份数据会额外保留 14 天，以便在不丢失数据的情况下恢复备份项。 [了解详细信息](/azure/backup/backup-azure-security-feature-cloud)。
- 通过跨区域还原可还原次要区域中的 Azure VM，该次要区域是 Azure 配对区域。 可随时还原次要区域中复制的数据。 这使你能够在出现中断时还原次要区域数据以实现审核合规性，而无需等待 Azure 声明出现灾难（与保险库的 GRS 设置不同）。 [了解详细信息](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)。

> [!NOTE]
> Azure 备份中有两种保管库类型。 除恢复服务保管库之外，还有[备份保管库](/azure/backup/backup-vault-overview)，用于存储 Azure 备份支持的较新工作负载的数据。

## <a name="what-to-do-before-an-attack"></a>攻击之前要执行哪些工作

如前所述，应假设自己在某些时间点会成为勒索软件攻击的受害者。 在攻击之前识别业务关键型系统并应用最佳做法可让你尽快备份并保持系统运行。 

### <a name="determine-what-is-most-important-to-you"></a>确定最重要的事项

勒索软件可能会在你制定攻击防范计划时进行攻击，因此，首先应识别最重要的业务关键型系统并开始在这些系统上执行定期备份。

根据我们的以往经验，对客户最重要的五个应用程序按此优先级顺序分为以下类别：

- 标识系统 – 用户访问 Active Directory、[Azure AD Connect](/azure/active-directory/hybrid/whatis-azure-ad-connect)、AD 域控制器等任何系统（包括下面所述的所有其他系统）时必需
- 人类生命系统 – 任何支持人类生命或可能使人类生命面临风险的系统，例如医疗或生命支持系统、安全系统（救护车、调度系统、交通灯控制）、大型机器、化学/生物系统、食品或个人产品生产等
- 财务系统 – 处理货币交易并保持业务运行的系统，例如支付系统和相关数据库、季度报告专用财务系统
- 产品或服务支持系统 – 提供客户购买的业务服务或生产/交付物理产品所需的任何系统、工厂控制系统、产品交付/调度系统等类似系统
- 安全（最低）系统 – 还应优先考虑监视攻击和提供最低安全服务所需的安全系统。 应侧重于确保当前攻击（或简单的机会性攻击）无法立即获取（或重新获取）对你的还原系统的访问权限

优先级备份列表也将成为优先级还原列表。 识别关键系统并定期执行备份后，请采取措施以降低风险级别。

### <a name="steps-to-take-before-an-attack"></a>攻击之前要执行的步骤

攻击之前采用这些最佳做法。

| 任务 | 详细信息 |
| --- | --- |
| 首先识别恢复联机状态所需的重要系统（使用上述五大类别）并立即开始执行这些系统的定期备份。 | 若要在攻击后尽快恢复并运行，请立刻确定最重要的事项。 |
| 将组织迁移到云。 <br><br>请考虑购买 Microsoft 统一支持计划或携手 Microsoft 合作伙伴，为你迁移到云提供帮助和支持。 | 通过自动备份和自助服务回滚将数据移动到云服务，从而减少本地风险。 Microsoft Azure 提供了一组强大的工具，可帮助你备份关键业务系统，并加快还原备份。 <br><br>[Microsoft 统一支持](https://www.microsoft.com/en-us/msservices/unified-support-solutions)是一种云服务支持模型，可在需要时为你提供帮助。 统一支持： <br><br>提供指定的全天候服务团队，根据你的需要提供问题解决方案和关键事件升级 <br><br>帮助你监视 IT 环境的运行状况，并主动确保问题在发生之前得到有效防范 |
| 将用户数据迁移到云解决方案（例如 OneDrive 和 SharePoint）以充分利用[版本控制和回收站功能](/compliance/assurance/assurance-malware-and-ransomware-protection#sharepoint-online-and-onedrive-for-business-protection-against-ransomware)。 <br><br>指导用户如何自行恢复文件以减少恢复延迟和成本。   例如，如果某个用户的 OneDrive 文件感染恶意软件，则可以将该用户的整个 OneDrive [还原](https://support.microsoft.com/office/restore-your-onedrive-fa231298-759d-41cf-bcd0-25ac53eb8a15?ui=en-US&rs=en-US&ad=US)到以前的某个时间。 <br><br>让用户还原自己的文件之前，请考虑一种防御策略，如 [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender)。 | 通过内置的安全性和数据管理功能来保护 Microsoft 云中的用户数据。 <br><br>指导用户如何还原自己的文件是一种不错的做法，但需要注意，用户还原的不是用于执行攻击的恶意软件。 你需要： <br><br>确保用户在你确信已逐出攻击者后还原文件 <br><br>应准备缓解措施以防用户恢复某些恶意软件 <br><br>Microsoft 365 Defender 使用 AI 自动操作和 Playbook，将受影响资产修正回安全状态。 Microsoft 365 Defender 利用套件产品的自动修正功能，确保可能时自动修正事件相关的所有受影响资产。 |
| 实现 [Azure 安全基准](/security/benchmark/azure/introduction)。 | Azure 安全基准是 Azure 的自有安全控制框架，以 NIST SP800-53、CIS Controls v7.1 等基于行业的安全控制框架为基础。 该基准为组织提供了有关如何配置 Azure 和 Azure 服务并实现安全控制的指导。 请参阅[备份和恢复](/security/benchmark/azure/security-controls-v2-backup-recovery)。 |
| 定期演练业务连续性/灾难恢复 (BC/DR) 计划。 <br><br>模拟事件响应场景。 对于为防范攻击做相应准备时执行的练习，应按照优先级备份和还原列表进行计划和实施。 <br><br>定期测试“从零恢复”方案，确保 BC/DR 可以使关键业务操作迅速从零功能（所有系统停机）恢复联机。 | 对勒索软件或敲诈勒索攻击与自然灾害同等重视，确保快速恢复业务操作。 <br><br>开展实践练习以验证跨团队流程和技术过程，包括带外员工和客户通信（假定所有电子邮件和聊天均已关闭）。 |
| 请考虑创建风险登记来识别潜在风险，并解决如何通过预防性控制和操作来进行协调的问题。 将勒索软件添加到风险登记，作为高可能性和高影响场景。 | 风险登记有助于根据风险发生的可能性和对业务的严重性来确定风险的优先级。 <br><br>通过[企业风险管理 (ERM)](/compliance/assurance/assurance-risk-management) 评估周期跟踪缓解状态。 |
| 定期自动备份所有关键业务系统（包括备份 Active Directory 等关键依赖项）。 <br><br>创建备份之后验证备份是否正常。 | 可将数据恢复到最新备份。 |
| 保护（或打印）恢复所需的支持文档和系统，例如还原过程文档、CMDB、网络图和 SolarWinds 实例。 | 攻击者会故意攻击这些资源，因为这会影响你的恢复能力。 |
| 请确保已有记录完善的过程，以便充分利用任何第三方支持，尤其是威胁情报提供商、反恶意软件解决方案提供商和恶意软件分析提供商提供的支持。 保护（或打印）这些过程。 | 如果给定勒索软件变种具有已知弱点或提供有解密工具，则第三方联系人可能有用。 |
| 确保备份和恢复策略包括： <br><br>能够将数据备份到特定时间点。 <br><br>备份的多个副本存储于独立脱机（气隙）位置。 <br><br>恢复时间目标，可确定检索备份信息并将这些信息投入生产环境中的速度。 <br><br>将备份快速还原到生产环境/沙盒。 | 组织遭到破坏后，备份对于复原至关重要。 应用 3-2-1 规则以实现最大限度的保护和可用性：即 3 份（1 份原始文件 + 2 份备份）、2 个存储类型和 1 个场外或冷副本。 |
| 保护备份以防蓄意擦除和加密： <br><br>将备份存储在脱机或场外存储及/或不可变存储中。 <br><br>需要采用带外步骤（例如 [MFA](/azure/active-directory/authentication/concept-mfa-howitworks) 或安全 PIN），才能允许修改或清除联机备份。 <br><br>在 Azure 虚拟网络中创建专用终结点，以安全地从恢复服务保管库备份和还原数据。 | 攻击者可访问的备份可显示没法用于业务恢复。 <br><br>脱机存储可确保在不使用任何网络带宽的情况下可靠地传输备份数据。 Azure 备份支持[脱机备份](/azure/backup/offline-backup-overview)，即在不使用网络带宽的情况下脱机传输初始备份数据。 它提供了一种可将备份数据复制到物理存储设备的机制。 然后，这些设备将发送到附近的 Azure 数据中心并上传到[恢复服务保管库](/azure/backup/backup-azure-recovery-services-vault-overview)。 <br><br>联机不可变存储（例如 [Azure Blob](/azure/storage/blobs/storage-blob-immutable-storage)）可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。 <br><br>[多重身份验证 (MFA)](/azure/active-directory/authentication/concept-mfa-howitworks) 对于所有管理员帐户都应为必需，并强烈建议对所有用户使用。 首选方法是尽量使用验证器应用而非短信或语音。 设置 Azure 备份时，可以将恢复服务配置为使用 Azure 门户中生成的安全 PIN 启用 MFA。 这可确保生成安全 PIN 来执行更新或删除恢复点等关键操作。 |
| 指定[受保护的文件夹](/windows/security/threat-protection/microsoft-defender-atp/controlled-folders)。 | 使未经授权的应用程序更难修改这些文件夹中的数据。 |
| 评审你的权限： <br><br>发现对文件共享、SharePoint 和其他解决方案的宽泛写入/删除权限。 “宽泛”的定义是许多用户对业务关键数据都具有写入/删除权限。 <br><br>减少宽泛权限，同时满足业务协作要求。 <br><br>进行审核和监视，确保不会再次出现宽泛权限。 | 降低因宽泛访问权限而诱发勒索软件活动的风险。 |
| 防范网络钓鱼尝试： <br><br>定期开展安全意识培训，帮助用户识别网络钓鱼尝试，并避免单击可能创建初始泄露入口点的内容。 <br><br>将安全筛选控件应用于电子邮件，以检测并最大限度减少成功网络钓鱼尝试的可能性。 | 攻击者渗入组织的最常见方法是通过电子邮件进行网络钓鱼尝试。 [Exchange Online Protection (EOP)](/microsoft-365/security/office-365-security/exchange-online-protection-overview) 是一项基于云的筛选服务，可保护组织免受垃圾邮件、恶意软件和其他电子邮件威胁的攻击。 安装 Exchange Online 邮箱的所有 Microsoft 365 组织中均有 EOP。 <br><br>电子邮件安全筛选控件的一个示例是[安全链接](/microsoft-365/security/office-365-security/safe-links)。 安全链接是 Defender for Office 365 中的一项功能，提供对邮件流中入站电子邮件进行 URL 扫描和重写的功能，以及对电子邮件消息和其他位置中的 URL 和链接的单击时验证功能。 除 EOP 入站电子邮件消息中的常规反垃圾邮件和反恶意软件保护外，还会进行安全链接扫描。 安全链接扫描有助于组织防范网络钓鱼和其他攻击中使用的恶意链接。 <br><br>详细了解[反网络钓鱼保护](/microsoft-365/security/office-365-security/tuning-anti-phishing)。 |

## <a name="what-to-do-during-an-attack"></a>攻击期间要执行的操作

如果遭到攻击，优先级备份列表将成为优先级还原列表。 请在还原之前再次验证备份是否正常。 可以在备份中查找恶意软件。

### <a name="steps-to-take-during-an-attack"></a>攻击期间要执行的步骤

攻击期间采用这些最佳做法。

| 任务 | 详细信息 |
| --- | --- |
| 在攻击初期充分利用第三方支持，特别是威胁情报提供商、反恶意软件解决方案提供商和恶意软件分析提供商提供的支持。 | 如果给定勒索软件变种具有已知弱点或提供有解密工具，则这些联系人可能有用。 <br><br>[Microsoft 检测和响应团队 (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) 可帮助你防范攻击。 DART 与全球各地客户开展合作，帮助客户在攻击发生前加强安全防范，并在攻击发生时予以调查和修正。 <br><br>Microsoft 还提供快速勒索软件恢复服务。 服务由 Microsoft Global [Compromise Recovery Security Practice (CRSP)](https://www.microsoft.com/security/blog/2021/06/09/crsp-the-emergency-team-fighting-cyber-attacks-beside-customers/) 独家提供。 该团队在勒索软件攻击期间的重点是还原身份验证服务，并限制勒索软件的影响。 <br><br>DART 和 CRSP 属于 Microsoft [Industry Solutions Delivery](https://www.microsoft.com/en-us/msservices/security) 安全服务线。 |
| 请联系本地或联邦执法机构。 | 如果你在美国，请使用 [IC3 Complaint Referral Form](https://ransomware.ic3.gov/default.aspx) 联系 FBI 报告勒索软件违规问题。 |
| 采取相应措施将恶意软件或勒索软件有效负载从你的环境中删除并停止传播。 <br><br>在所有可疑计算机和设备上运行完整的当前防病毒扫描，以检测并删除勒索软件相关的有效负载。 <br><br>扫描正在同步数据的设备或映射网络驱动器的目标。 | 可以使用 [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) 或（针对较旧客户端）[Microsoft Security Essentials](https://www.microsoft.com/download/details.aspx?id=5201)。 <br><br>还可使用[恶意软件删除工具 (MSRT)](https://www.microsoft.com/download/details.aspx?id=9905) 来帮助删除勒索软件或恶意软件。 |
| 首先还原业务关键型系统。 请记得在还原之前再次验证备份是否正常。| 此时，无需还原所有内容。 重点关注还原列表中的前五个业务关键型系统。 |
| 如果有脱机备份，可以在从环境中删除勒索软件有效负载（恶意软件）后还原加密数据。 | 若要防止后续受到攻击，请在还原之前确保勒索软件或恶意软件不在脱机备份中。 |
| 识别已知不会受感染的安全时间点备份映像。 <br><br>如果使用恢复服务保管库，请仔细查看事件时间线，以了解还原备份的合适时间点。 | 若要防止后续受到攻击，请在还原之前扫描备份中的勒索软件或恶意软件。 |
| 使用安全扫描程序和其他工具执行完整操作系统还原与数据还原方案。 | [Microsoft 安全扫描程序](/windows/security/threat-protection/intelligence/safety-scanner-download)是一种扫描工具，旨在查找并删除 Windows 计算机中的恶意软件。 只需下载该工具，然后运行扫描，即可查找恶意软件并尝试撤消识别的威胁所做更改。 |
| 确保防病毒或终结点检测和响应 (EDR) 解决方案为最新。 此外，还需要采用最新修补程序。 | 首选 [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) 等 EDR 解决方案。 |
| 业务关键型系统启动并运行后再还原其他系统。 <br><br>系统还原后开始收集遥测数据，以便可以针对还原内容做出形成性决策。 | 遥测数据应有助于识别恶意软件是否仍在系统上。 |

## <a name="post-attack-or-simulation"></a>攻击或模拟后

勒索软件攻击或事件响应模拟后，请执行以下步骤来改进备份和还原计划及安全状况：

1. 明确可从未能正常执行流程的情况中吸取哪些经验教训（简化、加速或以其他方式改进流程的机会）
2. 针对最大难题执行根本原因分析（应足够详细以确保解决方案能够有的放矢 — 考虑人员、流程和技术等因素）
3. 调查并修正原始违规（请联系 [Microsoft 检测和响应团队 (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) 获得帮助）
4. 根据经验教训和改进机会更新备份和还原策略 — 首先要根据最大影响和最快实施步骤来确定优先级

## <a name="next-steps"></a>后续步骤

本文介绍了如何改进备份和还原计划以防范勒索软件。 有关部署勒索软件保护的最佳做法，请参阅[快速防范勒索软件和敲诈勒索](/security/compass/protect-against-ransomware)。

关键行业信息：

- [Microsoft 数字防御报告](https://www.microsoft.com/security/business/security-intelligence-report?rtc=1)（请参阅第 22-24 页）

Microsoft Azure：

- [使用 Microsoft Azure 备份帮助防范勒索软件](https://www.youtube.com/watch?v=VhLOr2_1MCg)（26 分钟的视频）
- [从系统标识泄露中恢复](/azure/security/fundamentals/recover-from-identity-compromise)
- [Azure Sentinel 中的高级多阶段攻击检测](/azure/sentinel/fusion#ransomware)

Microsoft 365：

- [从勒索软件攻击中恢复](/microsoft-365/security/office-365-security/recover-from-ransomware)
- [恶意软件和勒索软件防护](/compliance/assurance/assurance-malware-and-ransomware-protection)
- [保护 Windows 10 电脑，使其免受勒索软件侵害](https://support.microsoft.com/windows/protect-your-pc-from-ransomware-08ed68a7-939f-726c-7e84-a72ba92c01c3)
- [ SharePoint Online 中的勒索软件](/sharepoint/troubleshoot/security/handling-ransomware-in-sharepoint-online)

Microsoft 365 Defender：

- [通过高级搜寻找到勒索软件](/microsoft-365/security/defender/advanced-hunting-find-ransomware)

Microsoft 安全团队博客文章：

- [通过了解网络安全风险实现复原：第 4 部分 - 浏览当前威胁（2021 年 5 月）](https://www.microsoft.com/security/blog/2021/05/26/becoming-resilient-by-understanding-cybersecurity-risks-part-4-navigating-current-threats/)。 请参阅“勒索软件”部分
- [人为操作的勒索软件攻击：可预防的灾难（2020 年 3 月）](https://www.microsoft.com/security/blog/2020/03/05/human-operated-ransomware-attacks-a-preventable-disaster/)。 包括实际人为操作的勒索软件攻击的攻击链分析
- [勒索软件响应 - 是否支付勒索金？（2019 年 12 月）](https://www.microsoft.com/security/blog/2019/12/16/ransomware-response-to-pay-or-not-to-pay/)
- [Norsk Hydro 以透明方式响应勒索软件攻击（2019 年 12 月）](https://www.microsoft.com/security/blog/2019/12/17/norsk-hydro-ransomware-attack-transparency/)
