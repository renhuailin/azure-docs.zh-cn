---
title: 参考 - 适用于 Linux 的 Azure Policy 来宾配置基线
description: Azure 上通过 Azure Policy 来宾配置实现的 Linux 基线的详细信息。
ms.date: 08/03/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 4fe3e374b39c880940fa61b84342d2d8cbf94d12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731348"
---
# <a name="linux-security-baseline"></a>Linux 安全基线

本文详细介绍适用于以下实现中的 Linux 来宾的配置设置：

- **\[预览版\] Linux 计算机应符合 Azure 计算安全基线** Azure Policy 来宾配置定义的要求
- **Azure 安全中心应修复计算机上安全配置中的漏洞**

有关详细信息，请参阅 [Azure Policy 来宾配置](../concepts/guest-configuration.md)和 [Azure 安全基准概述 (V2)](../../../security/benchmarks/overview.md)。

## <a name="general-security-controls"></a>常规安全控件

|名称<br /><sub>(CCEID)</sub> |详细信息 |修正检查 |
|---|---|---|
|确保在 /home 分区上设置 nodev 选项。<br /><sub>(1.1.4)</sub> |说明：攻击者可能在 /home 分区上装入特殊设备（例如，块或字符设备）。 |编辑 /etc/fstab 文件，并将 nodev 添加到 /home 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /tmp 分区上设置 nodev 选项。<br /><sub>(1.1.5)</sub> |说明：攻击者可能在 /tmp 分区上装入特殊设备（例如，块或字符设备）。 |编辑 /etc/fstab 文件，并将 nodev 添加到 /tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /var/tmp 分区上设置 nodev 选项。<br /><sub>(1.1.6)</sub> |说明：攻击者可能在 /var/tmp 分区上装入特殊设备（例如，块或字符设备）。 |编辑 /etc/fstab 文件，并将 nodev 添加到 /var/tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /tmp 分区上设置 nosuid 选项。<br /><sub>(1.1.7)</sub> |说明：由于 /tmp 文件系统仅用于临时文件存储，应设置此选项以确保用户无法在 /var/tmp 中创建 setuid 文件。 |编辑 /etc/fstab 文件，并将 nosuid 添加到 /tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /var/tmp 分区上设置 nosuid 选项。<br /><sub>(1.1.8)</sub> |说明：由于 /var/tmp 文件系统仅用于临时文件存储，应设置此选项以确保用户无法在 /var/tmp 中创建 setuid 文件。 |编辑 /etc/fstab 文件，并将 nosuid 添加到 /var/tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /var/tmp 分区上设置 noexec 选项。<br /><sub>(1.1.9)</sub> |说明：由于 `/var/tmp` 文件系统仅用于临时文件存储，应设置此选项以确保用户无法从 `/var/tmp` 运行可执行的二进制文件。 |编辑 /etc/fstab 文件，并将 noexec 添加到 /var/tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保在 /dev/shm 分区上设置 noexec 选项。<br /><sub>(1.1.16)</sub> |说明：在文件系统上设置此选项可防止用户从共享内存中执行程序。 这可阻止用户在系统上引入可能的恶意软件。 |编辑 /dev/shm 文件，并将 noexec 添加到 /var/tmp 分区的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|禁用自动装载<br /><sub>(1.1.21)</sub> |说明：如果启用了自动装载，任何人都可以通过物理访问方式接入 USB 驱动器或光盘并在系统中使用其内容，即使这些人并不具有装载驱动盘或光盘的权限。 |禁用 autofs 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-autofs" |
|确保已禁用 USB 存储设备的装载<br /><sub>(1.1.21.1)</sub> |说明：删除对 USB 存储设备的支持可减小服务器的本地攻击面。 |在 `/etc/modprobe.d/` 目录中编辑或创建一个以 .conf 结尾的文件并添加 `install usb-storage /bin/true`，然后卸载 usb-storage 模块或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|确保对核心转储进行限制。<br /><sub>(1.5.1)</sub> |说明：对核心转储设置硬性限制可防止用户重写软变量。 如果需要核心转储，请考虑为用户组设置限制（见 `limits.conf(5)`）。 此外，将 `fs.suid_dumpable` 变量设置为 0 可阻止 setuid 程序执行核心转储。 |将 `hard core 0` 添加到 /etc/security/limits.conf 或 limits.d 目录中的某个文件中，并在 sysctl 中设置 `fs.suid_dumpable = 0` 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-core-dumps" |
|确保已禁用预链接。<br /><sub>(1.5.4)</sub> |说明：预链接功能可能会干扰 AIDE 的操作，因为它会更改二进制文件。 如果有恶意用户能够入侵公用库（如 libc），预链接还可能会扩大系统的漏洞。 |使用包管理器卸载 `prelink` 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r remove-prelink" |
|确保配置 /etc/motd 上的权限。<br /><sub>(1.7.1.4)</sub> |说明：如果 `/etc/motd` 文件没有正确的所有权，可能会有未经授权的用户用不正确的或误导性信息修改该文件。 |将 /etc/motd 的所有者和组设置为 root，并将权限设置为 0644 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r file-permissions" |
|确保配置 /etc/issue 上的权限。<br /><sub>(1.7.1.5)</sub> |说明：如果 `/etc/issue` 文件没有正确的所有权，可能会有未经授权的用户用不正确的或误导性信息修改该文件。 |将 /etc/issue 的所有者和组设置为 root，并将权限设置为 0644 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r file-permissions" |
|确保配置 /etc/issue.net 上的权限。<br /><sub>(1.7.1.6)</sub> |说明：如果 `/etc/issue.net` 文件没有正确的所有权，可能会有未经授权的用户用不正确的或误导性信息修改该文件。 |将 /etc/issue.net 的所有者和组设置为 root，并将权限设置为 0644 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r file-permissions" |
|应为所有可移动媒体启用 nodev 选项。<br /><sub>(2.1)</sub> |说明：攻击者可能通过可移动媒体装入特殊设备（例如，块或字符设备） |将 nodev 选项添加到 /etc/fstab 中的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|应为所有可移动媒体启用 noexec 选项。<br /><sub>(2.2)</sub> |说明：攻击者可能通过可移动媒体加载可执行文件 |将 noexec 选项添加到 /etc/fstab 中的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|应为所有可移动媒体启用 nosuid 选项。<br /><sub>(2.3)</sub> |说明：攻击者可能会通过可移动媒体加载通过提升的安全上下文运行的文件 |将 nosuid 选项添加到 /etc/fstab 中的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保未安装 talk 客户端。<br /><sub>(2.3.3)</sub> |说明：该软件存在安全风险，因为它使用未加密的通信协议。 |卸载 `talk` 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r remove-talk" |
|确保配置 /etc/hosts.allow 上的权限。<br /><sub>(3.4.4)</sub> |说明：应务必确保 `/etc/hosts.allow` 文件不会受到未经授权的写入访问，这一点非常重要。 尽管该文件默认受保护，但文件权限有可能发生意外更改或遭受恶意操作篡改。 |将 /etc/hosts.allow 的所有者和组设置为 root，并将权限设置为 0644 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r file-permissions" |
|确保配置 /etc/hosts.deny 上的权限。<br /><sub>(3.4.5)</sub> |说明：应务必确保 `/etc/hosts.deny` 文件不会受到未经授权的写入访问，这一点非常重要。 尽管该文件默认受保护，但文件权限有可能发生意外更改或遭受恶意操作篡改。 |将 /etc/hosts.deny 的所有者和组设置为 root，并将权限设置为 0644 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r file-permissions" |
|确保启用默认的“拒绝防火墙”策略<br /><sub>(3.6.2)</sub> |说明：使用默认的接受策略时，防火墙将接受任何未配置为拒绝的数据包。 使用默认的 DROP 策略可以比使用默认的 ALLOW 策略更轻松地保持防火墙的安全。 |使用防火墙软件，根据具体情况将传入的、传出的和路由的流量的默认策略设置为 `deny` 或 `reject` |
|应为所有 NFS 装载启用 nodev/nosuid 选项。<br /><sub>(5)</sub> |说明：攻击者可能会通过远程文件系统加载通过提升的安全上下文运行的文件或特殊设备 |将 nosuid 和 nodev 选项添加到 /etc/fstab 中的第四个字段（装载选项）。 有关详细信息，请参阅 fstab(5) 手册页。 |
|确保配置了对 /etc/ssh/sshd_config 的权限。<br /><sub>(5.2.1)</sub> |说明：需要防止无权限的用户对 `/etc/ssh/sshd_config` 文件进行未经授权的更改。 |将 /etc/ssh/sshd_config 的所有者和组设置为 root，并将权限设置为 0600 或运行“/opt/microsoft/omsagent/plugin/omsremediate -r sshd-config-file-permissions” |
|确保配置密码创建要求。<br /><sub>(5.3.1)</sub> |说明：强密码可在系统遭受暴力破解方法的攻击时为其提供保护。 |在适用于你的发行版的相应 PAM 中设置以下键/值对：minlen=14，minclass = 4，dcredit = -1，ucredit = -1，ocredit = -1，lcredit = -1，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-password-requirements" |
|确保为失败的密码尝试配置锁定。<br /><sub>(5.3.2)</sub> |说明：在连续 `n` 次不成功的登录尝试后锁定用户 ID 可缓解对系统的暴力破解密码攻击。 |应视情况为 Ubuntu 和 Debian 添加 pam_tally 和 pam_deny 模块。 对于所有其他发行版，请参阅发行版的文档 |
|禁止安装和使用不需要的文件系统 (cramfs)<br /><sub>(6.1)</sub> |说明：攻击者可能会在 cramfs 中使用漏洞来提升权限 |将文件添加到可禁用 cramfs 的 /etc/modprob.d 目录中，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|禁止安装和使用不需要的文件系统 (freevxfs)<br /><sub>(6.2)</sub> |说明：攻击者可能会在 freevxfs 中使用漏洞来提升权限 |将文件添加到可禁用 freevxfs 的 /etc/modprob.d 目录中，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|确保所有用户的主目录都存在<br /><sub>(6.2.7)</sub> |说明：如果用户的主目录不存在或未分配主目录，系统会将用户置于 "/" 中，并且不能写入任何文件或设置本地环境变量。 |如果有用户的主目录不存在，应为这些用户创建主目录，并确保用户拥有其各自的主目录。 应删除未分配有主目录的用户，或视情况为其分配主目录。 |
|确保用户拥有其主目录<br /><sub>(6.2.9)</sub> |说明：由于用户需要对存储在自己主目录中的文件负责，所以用户必须是其主目录的所有者。 |对于有主目录的所有权不由为其指定的用户所有，要将其所有权更改为由为其指定的用户所有。 |
|确保用户的点文件不可由组写入或不可在全局范围内写入。<br /><sub>(6.2.10)</sub> |描述：组可写或全局可写的用户配置文件可能导致恶意用户能够盗取或修改其他用户的数据或获取其他用户的系统权限。 |在不通知用户社区的情况下对用户文件进行全局修改会导致意外中断和用户不满。 因此，建议创建监视策略，用于报告用户点文件权限和根据站点策略确定要执行的操作。 |
|确保无用户具有 .forward 文件<br /><sub>(6.2.11)</sub> |描述：使用 `.forward` 文件会造成安全风险，因为可能会无意中将敏感数据传输到组织之外。 `.forward` 文件还会带来其他风险，因为它可用于执行可导致非预期操作的命令。 |在不通知用户社区的情况下对用户文件进行全局修改会导致意外中断和用户不满。 因此，建议创建监视策略，用于报告用户 `.forward` 文件和根据站点策略确定要执行的操作。 |
|确保无用户具有 .netrc 文件<br /><sub>(6.2.12)</sub> |说明：`.netrc` 文件会造成严重的安全风险，因为它以未加密的形式存储密码。 即使禁用了 FTP，用户帐户也可能从其他系统引入 `.netrc` 文件，这可能会给这些系统带来风险 |在不通知用户社区的情况下对用户文件进行全局修改会导致意外中断和用户不满。 因此，建议创建监视策略，用于报告用户 `.netrc` 文件和根据站点策略确定要执行的操作。 |
|确保无用户具有 .rhosts 文件<br /><sub>(6.2.14)</sub> |说明：仅当 `/etc/pam.conf` 文件中允许使用 `.rhosts` 支持时，此操作才有意义。 即使因 `/etc/pam.conf` 中禁用了支持而导致 `.rhosts` 文件无效，用户也可能会从其他系统中引入这些文件，并可能包含对前述其他系统的攻击者有用的信息。 |在不通知用户社区的情况下对用户文件进行全局修改会导致意外中断和用户不满。 因此，建议创建监视策略，用于报告用户 `.rhosts` 文件和根据站点策略确定要执行的操作。 |
|确保 /etc/passwd 中的所有组也均存在于 /etc/group 中<br /><sub>(6.2.15)</sub> |说明：已在 /etc/passwd 文件中定义但在 /etc/group 文件中不存在的组会对系统安全造成威胁，因为组权限未得到正确管理。 |对于 /etc/passwd 中定义的每个组，请确保在 /etc/group 中有一个相应的组 |
|确保不存在重复的 UID<br /><sub>(6.2.16)</sub> |说明：出于问责目的，必须为用户分配唯一的 UID，这也可确保提供适当的访问保护。 |创建唯一的 UID，并查看共享 UID 所拥有的所有文件，以确定其所属的 UID。 |
|确保不存在重复的 GID<br /><sub>(6.2.17)</sub> |说明：出于问责目的，必须为组分配唯一的 GID，这也可确保提供适当的访问保护。 |创建唯一的 GID，并查看共享 GID 所拥有的所有文件，以确定其所属的 GID。 |
|确保不存在重复的用户名<br /><sub>(6.2.18)</sub> |描述：如果为用户分配了重复的用户名，该用户名会使用 `/etc/passwd` 中该用户名的第一个 UID 创建和访问文件。 例如，如果 "test4" 的 UID 为1000，而后续的 "test4" 条目的 UID 为 2000，则使用 "test4" 登录时将使用 UID 1000。 这会造成 UID 的有效共享，因此存在安全隐患。 |为所有用户创建唯一的用户名。 只要用户具有唯一 UID，文件所有权会自动反映更改。 |
|确保不存在重复的组<br /><sub>(6.2.19)</sub> |描述：如果为组分配了重复的组名，该用户名会使用 `/etc/group` 中该组的第一个 GID 创建和访问文件。 这会造成 GID 的有效共享，因此存在安全隐患。 |为所有用户组创建唯一名称。 只要组具有唯一 GID，文组件所有权会自动反映更改。 |
|确保卷影组为空<br /><sub>(6.2.20)</sub> |说明：分配到卷影组的任何用户都将获得对 /etc/shadow 文件的读取访问权限。 如果攻击者可以获得对 `/etc/shadow` 文件的读取访问权限，他们可以轻松对经过哈希处理的密码运行密码破解程序，从而将密码破解。 `/etc/shadow` 文件中存储的其他安全信息（如过期信息）也可能被攻击者利用来破坏更多用户帐户。 |删除卷影组中的所有用户 |
|禁止安装和使用不需要的文件系统 (hfs)<br /><sub>(6.3)</sub> |说明：攻击者可能会在 hfs 中使用漏洞来提升权限 |将文件添加到可禁用 hfs 的 /etc/modprob.d 目录中，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|禁止安装和使用不需要的文件系统 (hfs)<br /><sub>(6.4)</sub> |说明：攻击者可能会在 hfs 中使用漏洞来提升权限 |将文件添加到可禁用 hfsplus 的 /etc/modprob.d 目录中，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|禁止安装和使用不需要的文件系统 (jffs2)<br /><sub>(6.5)</sub> |说明：攻击者可能会在 jffs2 中使用漏洞来提升权限 |将文件添加到可禁用 jffs2 的 /etc/modprob.d 目录中，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|只能从批准的源中编译内核。<br /><sub>(10)</sub> |说明：未经批准的源中的内核可能包含致使攻击者获得访问权限的漏洞或后门程序。 |安装发行版供应商提供的内核。 |
|/etc/shadow 文件权限应设置为 0400<br /><sub>(11.1)</sub> |说明：如果未正确保护 /etc/shadow，攻击者有可能从中检索到或操作经过哈希处理的密码。 |设置 /etc/shadow* 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-shadow-perms" |
|/etc/shadow- 文件权限应设置为 0400<br /><sub>(11.2)</sub> |说明：如果未正确保护 /etc/shadow-，攻击者有可能从中检索到或操作经过哈希处理的密码。 |设置 /etc/shadow* 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-shadow-perms" |
|/etc/gshadow 文件权限应设置为 0400<br /><sub>(11.3)</sub> |说明：如果未正确保护此文件，攻击者有可能加入安全组 |设置 /etc/gshadow- 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-gshadow-perms" |
|/etc/gshadow- 文件权限应设置为 0400<br /><sub>(11.4)</sub> |说明：如果未正确保护此文件，攻击者有可能加入安全组 |设置 /etc/gshadow 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-gshadow-perms" |
|/etc/passwd 文件权限应设置为 0644<br /><sub>(12.1)</sub> |说明：攻击者有可能修改 userID 和登录 shell |设置 /etc/passwd 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-passwd-perms" |
|/etc/group 文件权限应设置为 0644<br /><sub>(12.2)</sub> |说明：攻击者有可能通过修改组成员资格提升权限 |设置 /etc/group 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-group-perms" |
|/etc/passwd- 文件权限应设置为 0600<br /><sub>(12.3)</sub> |说明：如果未正确保护此文件，攻击者有可能加入安全组 |设置 /etc/passwd- 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-passwd-perms" |
|/etc/group- 文件权限应设置为 0644<br /><sub>(12.4)</sub> |说明：攻击者有可能通过修改组成员资格提升权限 |设置 /etc/group- 的权限和所有权，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-etc-group-perms" |
|应将通过 su 对根帐户的访问限制到 "root" 组<br /><sub>(21)</sub> |说明：如果 su 不限于 root 组中的用户，攻击者就可以通过密码猜测提升权限。 |运行命令 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-su-permissions"。 这会在文件 "/etc/pam.d/su" 中添加一行 "auth required pam_wheel.so use_uid" |
|应存在 "root" 组，且其中包含可通过 su 访问根帐户的所有成员<br /><sub>(22)</sub> |说明：如果 su 不限于 root 组中的用户，攻击者就可以通过密码猜测提升权限。 |通过命令 "groupadd -g 0 root" 创建 root 组 |
|所有帐户都应具有密码<br /><sub>(23.2)</sub> |说明：攻击者有可能登录未设密码的帐户并执行任意命令。 |使用 passwd 命令为所有帐户设置密码 |
|根帐户以外的帐户的唯一 UID 必须大于零 (0)<br /><sub>(24)</sub> |说明：如果根帐户以外的帐户的 uid 为 0，攻击者可能会入侵该帐户并获得 root 权限。 |使用 "usermod -u" 为所有非根帐户分配唯一的非零 uid |
|应启用虚拟内存区域的随机放置<br /><sub>(25)</sub> |说明：攻击者可能会将可执行代码写入内存中的已知区域，导致权限提升 |在文件 "/proc/sys/kernel/randomize_va_space" 中添加值 "1" 或 "2" |
|应该启用对 XD/NX 处理器功能的内核支持<br /><sub>(26)</sub> |说明：攻击者可能会导致系统从内存中的数据区域执行代码，从而获得权限提升。 |确认文件 "/proc/cpuinfo" 包含标志 "nx" |
|"." 不应出现在根的 $PATH 中<br /><sub>(27.1)</sub> |说明：攻击者有可能通过在根 $PATH 中放置恶意文件来提升权限 |修改 /root/.profile 中的 "export PATH =" 行 |
|用户主目录应使用 750 或更严格的模式<br /><sub>(28)</sub> |说明：攻击者有可能从其他用户的主文件夹中检索到敏感信息。 |将主文件夹权限设置为 750 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-home-dir-permissions" |
|应将 login.defs 中所有用户的默认 umask 设置为 077<br /><sub>(29)</sub> |说明：攻击者有可能从其他用户拥有的文件中检索敏到感信息。 |运行命令 "/opt/microsoft/omsagent/plugin/omsremediate -r set-default-user-umask"。 这会在文件 "/etc/login.defs" 中添加一行 "UMASK 077" |
|所有引导加载程序应启用密码保护。<br /><sub>(31)</sub> |说明：具有物理访问权限的攻击者有可能修改引导加载程序选项，从而获得不受限的系统访问权限 |在文件 "/boot/grub/grub.cfg" 中添加引导加载程序密码 |
|确保配置了对引导加载程序配置的权限<br /><sub>(31.1)</sub> |说明：设置对 root 的读取和写入权限只会阻止非根用户查看或更改引导参数。 读取引导参数的非根用户有可能能够发现启动时的安全漏洞并利用它们。 |将引导加载程序的所有者和组设置为 root:root，将权限设置为 0400，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r bootloader-permissions" |
|确保为单用户模式执行身份验证。<br /><sub>(33)</sub> |说明：在单用户模式下要求身份验证，可防止未经授权的用户将系统重启为单一用户模式并在无凭据的情况下获取根权限。 |运行以下命令为根用户设置密码：`passwd root` |
|确保禁用数据包重定向发送。<br /><sub>(38.3)</sub> |描述：攻击者有可能使用被入侵的主机将无效的“ICMP 重定向”发送到其他路由器设备，试图以此破坏路由，让用户访问攻击者设置的系统而不是有效系统。 |在 /etc/sysctl.conf 中设置以下参数："net.ipv4.conf.all.send_redirects = 0" 和 "net.ipv4.conf.default.send_redirects = 0"，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-send-redirects" |
|应对所有接口禁用 ICMP 重定向发送。 (net.ipv4.conf.default.accept_redirects = 0)<br /><sub>(38.4)</sub> |说明：攻击者有可能更改此系统的路由表，将流量重定向到备用目标 |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-accept-redirects"。 |
|应对所有接口禁用 ICMP 重定向发送。 (net.ipv4.conf.default.secure_redirects = 0)<br /><sub>(38.5)</sub> |说明：攻击者有可能更改此系统的路由表，将流量重定向到备用目标 |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-secure-redirects" |
|应对所有接口禁用源路由数据包接收。 (net.ipv4.conf.all.accept_source_route = 0)<br /><sub>(40.1)</sub> |说明：攻击者有可能出于恶意目的对流量进行重定向。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值。 |
|应对所有接口禁用源路由数据包接收。 (net.ipv6.conf.all.accept_source_route = 0)<br /><sub>(40.2)</sub> |说明：攻击者有可能出于恶意目的对流量进行重定向。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值。 |
|应针对网络接口禁用接受源路由的数据包的默认设置。 (net.ipv4.conf.default.accept_source_route = 0)<br /><sub>(42.1)</sub> |说明：攻击者有可能出于恶意目的对流量进行重定向。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值。 |
|应针对网络接口禁用接受源路由的数据包的默认设置。 (net.ipv6.conf.default.accept_source_route = 0)<br /><sub>(42.2)</sub> |说明：攻击者有可能出于恶意目的对流量进行重定向。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值。 |
|应启用对虚假广播 ICMP 响应的忽略。 (net.ipv4.icmp_ignore_bogus_error_responses = 1)<br /><sub>(43)</sub> |说明：攻击者有可能执行 ICMP 攻击，从而导致 DoS |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-icmp-ignore-bogus-error-responses" |
|应启用对发送到广播/多播地址的 ICMP 回显请求 (ping) 的忽略。 (net.ipv4.icmp_echo_ignore_broadcasts = 1)<br /><sub>(44)</sub> |说明：攻击者有可能执行 ICMP 攻击，从而导致 DoS |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-icmp-echo-ignore-broadcasts" |
|应为所有接口启用 martian 数据包（地址不合理的包）的日志记录。 (net.ipv4.conf.all.log_martians = 1)<br /><sub>(45.1)</sub> |说明：攻击者有可能从假冒地址发送流量且不被检测到 |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-log-martians" |
|应为所有接口启用按反向路径执行的源验证。 (net.ipv4.conf.all.rp_filter = 1)<br /><sub>(46.1)</sub> |说明：系统会接受来自不可路由的地址的流量。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-rp-filter" |
|应为所有接口启用按反向路径执行的源验证。 (net.ipv4.conf.default.rp_filter = 1)<br /><sub>(46.2)</sub> |说明：系统会接受来自不可路由的地址的流量。 |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-rp-filter" |
|应启用 TCP SYN Cookie。 (net.ipv4.tcp_syncookies = 1)<br /><sub>(47)</sub> |说明：攻击者有可能通过 TCP 执行 DoS |运行 `sysctl -w key=value` 并将其设置为符合要求的值，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-tcp-syncookies" |
|系统不应充当网络嗅探器。<br /><sub>(48)</sub> |说明：攻击者有可能使用混杂接口来探查网络流量 |混杂模式通过 "/etc/network/interfaces" 或 "/etc/rc.local." 中的 "promisc" 条目启用。 检查这两个文件并删除此条目。 |
|应禁用所有无线接口。<br /><sub>(49)</sub> |说明：攻击者有可能创建虚假 AP 来拦截传输的讯息。 |确认 "/etc/network/interfaces" 中的所有无线接口都已禁用 |
|应启用 IPv6 协议。<br /><sub>(50)</sub> |说明：这对于新式网络上的通信是必需的。 |打开/etc/sysctl.conf 并确认 "net.ipv6.conf.all.disable_ipv6" 和 "net.ipv6.conf.default.disable_ipv6" 设置为 0 |
|确保禁用 DCCP。<br /><sub>(54)</sub> |说明：如果不需要该协议，建议不要安装驱动程序以减小潜在攻击面。 |在 `/etc/modprobe.d/` 目录中编辑或创建一个以 .conf 结尾的文件并添加 `install dccp /bin/true`，然后卸载 dccp 模块或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|确保禁用 SCTP。<br /><sub>(55)</sub> |说明：如果不需要该协议，建议不要安装驱动程序以减小潜在攻击面。 |在 `/etc/modprobe.d/` 目录中编辑或创建一个以 .conf 结尾的文件并添加 `install sctp /bin/true`，然后卸载 sctp 模块或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|禁用对 RDS 的支持。<br /><sub>(56)</sub> |说明：攻击者有可能利用 RDS 中的漏洞来入侵系统 |在 `/etc/modprobe.d/` 目录中编辑或创建一个以 .conf 结尾的文件并添加 `install rds /bin/true`，然后卸载 rds 模块或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|确保禁用 TIPC。<br /><sub>(57)</sub> |说明：如果不需要该协议，建议不要安装驱动程序以减小潜在攻击面。 |在 `/etc/modprobe.d/` 目录中编辑或创建一个以 .conf 结尾的文件并添加 `install tipc /bin/true`，然后卸载 tipc 模块或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-unnecessary-kernel-mods" |
|确保配置日志记录。<br /><sub>(60)</sub> |说明：大量与安全相关的重要信息通过 `rsyslog` 发送（例如，成功和失败的 su 尝试、失败的登录尝试、根登录尝试等）。 |视情况适当配置 syslog、rsyslog 或 syslog-ng |
|应安装 syslog、rsyslog 或 syslog-ng 包。<br /><sub>(61)</sub> |说明：系统不会记录可靠性和安全性问题，这会妨碍得到正确的诊断结果。 |安装 rsyslog 包，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r install-rsyslog" |
|systemd-journald 服务应配置为保留日志消息<br /><sub>(61.1)</sub> |说明：系统不会记录可靠性和安全性问题，这会妨碍得到正确的诊断结果。 |创建 /var/log/journal，并确保 journald.conf 中的存储是自动的或者持久的 |
|确保启用日志记录服务<br /><sub>(62)</sub> |说明：应务必具备记录节点上事件的功能。 |启用 rsyslog 包，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r enable-rsyslog" |
|所有 rsyslog 日志文件的文件权限应设置为 640 或 600。<br /><sub>(63)</sub> |说明：攻击者有可能通过操纵日志来隐藏活动信息 |在文件 "/etc/rsyslog.conf" 中添加一行 "$FileCreateMode 0640" |
|确保对记录器配置文件进行限制。<br /><sub>(63.1)</sub> |说明：应务必确保日志文件存在，同时应具备正确的权限，以便确保敏感 syslog 数据得到存档和保护。 |将记录器的配置文件设置为 0640 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r logger-config-file-permissions" |
|所有 rsyslog 日志文件的所有权应属于 adm 组。<br /><sub>(64)</sub> |说明：攻击者有可能通过操纵日志来隐藏活动信息 |在文件 "/etc/rsyslog.conf" 中添加一行 "$FileGroup adm" |
|所有 rsyslog 日志文件的所有权应属于 syslog 用户。<br /><sub>(65)</sub> |说明：攻击者有可能通过操纵日志来隐藏活动信息 |在文件 "/etc/rsyslog.conf" 中添加一行 "$FileOwner syslog" 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r syslog-owner" |
|Rsyslog 不应接受远程消息。<br /><sub>(67)</sub> |说明：攻击者有可能将消息注入 syslog，从而导致 DoS 或分散对其他活动的关注 |从文件 "/etc/rsyslog.conf" 中删除行 "$ModLoad imudp" 和 "$ModLoad imtcp" |
|应启用 logrotate（syslog 轮换器）服务。<br /><sub>(68)</sub> |说明：日志文件可能会无限制地增大，并占用所有磁盘空间 |安装 logrotate 包，并确认 logrotate cron 条目为活动状态 (chmod 755 /etc/cron.daily/logrotate；chown root:root /etc/cron.daily/logrotate) |
|应禁用 rlogin 服务。<br /><sub>(69)</sub> |说明：攻击者有可能获得访问权限，并避开严格的身份验证要求 |删除 inetd 服务。 |
|除非需要，否则应禁用 inetd。 (inetd)<br /><sub>(70.1)</sub> |说明：攻击者有可能利用 inetd 服务中的漏洞获取访问权限 |卸载 inetd 服务 (apt-get remove inetd) |
|除非需要，否则应禁用 xinetd。 (xinetd)<br /><sub>(70.2)</sub> |说明：攻击者有可能利用 inetd 服务中的漏洞获取访问权限 |卸载 inetd 服务 (apt-get remove xinetd) |
|仅当你的发行版需要且适当的情况下安装 inetd。 根据当前的强化标准进行保护。 （如果需要）<br /><sub>(71.1)</sub> |说明：攻击者有可能利用 inetd 服务中的漏洞获取访问权限 |卸载 inetd 服务 (apt-get remove inetd) |
|仅当你的发行版需要且适当的情况下安装 xinetd。 根据当前的强化标准进行保护。 （如果需要）<br /><sub>(71.2)</sub> |说明：攻击者有可能利用 inetd 服务中的漏洞获取访问权限 |卸载 inetd 服务 (apt-get remove xinetd) |
|应禁用 telnet 服务。<br /><sub>(72)</sub> |说明：攻击者有可能窃听或操纵未加密的 telnet 会话 |删除或注释掉文件 "/etc/inetd.conf" 中的 telnet 条目 |
|应卸载所有 telnetd 包。<br /><sub>(73)</sub> |说明：攻击者有可能窃听或操纵未加密的 telnet 会话 |卸载任何 telnetd 包 |
|应禁用 rcp/rsh 服务。<br /><sub>(74)</sub> |说明：攻击者有可能窃听或操纵未加密的会话 |删除或注释掉文件 "/etc/inetd.conf" 中的 shell 条目 |
|应卸载 rsh-server 包。<br /><sub>(77)</sub> |说明：攻击者有可能窃听或操纵未加密的 rsh 会话 |卸载 rsh-server 包 (apt-get remove rsh-server) |
|应禁用 ypbind 服务。<br /><sub>(78)</sub> |说明：攻击者有可能从 ypbind 服务中检索到敏感信息 |卸载 nis 包 (apt-get remove nis) |
|应卸载 nis 包。<br /><sub>(79)</sub> |说明：攻击者有可能从 NIS 服务中检索到敏感信息 |卸载 nis 包 (apt-get remove nis) |
|应禁用 tftp 服务。<br /><sub>(80)</sub> |说明：攻击者有可能窃听或操纵未加密的会话 |从文件 "/etc/inetd.conf" 中删除 tftp 条目 |
|应卸载 tftpd 包。<br /><sub>(81)</sub> |说明：攻击者有可能窃听或操纵未加密的会话 |卸载 tftpd 包 (apt-get remove tftpd) |
|应卸载 readahead-fedora 包。<br /><sub>(82)</sub> |说明：该包不会产生实质性的暴露风险，也没有带来实质性的优点。 |卸载 readahead-fedora 包 (apt-get remove readahead-fedora) |
|应禁用蓝牙/hidd 服务。<br /><sub>(84)</sub> |说明：攻击者有可能拦截或操纵无线通讯。 |卸载蓝牙包 (apt-get remove bluetooth) |
|应禁用 isdn 服务。<br /><sub>(86)</sub> |说明：攻击者有可能使用调制解调器进行未经授权的访问 |卸载 isdnutils-base 包 (apt-get remove isdnutils-base) |
|应卸载 isdnutils-base 包。<br /><sub>(87)</sub> |说明：攻击者有可能使用调制解调器进行未经授权的访问 |卸载 isdnutils-base 包 (apt-get remove isdnutils-base) |
|应禁用 kdump 服务。<br /><sub>(88)</sub> |说明：攻击者有可能分析之前发生的系统崩溃以检索敏感信息 |卸载 kdump-tools 包 (apt-get remove kdump-tools) |
|应禁用 Zeroconf 网络。<br /><sub>(89)</sub> |说明：攻击者有可能滥用此网络来获取有关网络系统的信息或利用其信任模型的缺陷来欺骗 DNS 请求 |对于 RedHat、CentOS 和 Oracle：在 /etc/sysconfig/网络中添加 `NOZEROCONF=yes or no`。 对于所有其他发行版：删除文件 "/etc/network/interfaces" 中的所有 "ipv4ll" 条目，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-zeroconf" |
|应启用 crond 服务。<br /><sub>(90)</sub> |说明：几乎所有系统在常规维护任务中都需要用到 Cron |安装 cron 包 (apt-get install -y cron) 并确认文件 "/etc/init/cron.conf" 包含行 "start on runlevel [2345]" |
|/etc/anacrontab 的文件权限应设置为 root:root 600。<br /><sub>(91)</sub> |说明：攻击者有可能操纵此文件来阻止计划的任务或执行恶意任务 |设置 /etc/anacrontab 的所有权和权限，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-anacrontab-perms" |
|确保配置 /etc/cron.d 上的权限。<br /><sub>(93)</sub> |说明：向非特权用户授予此目录的写入权限可能会导致他们能够获取未经授权的提升权限。 授予对此目录的读取访问权限，有可能让非特权用户发现如何获取提升的权限或规避审核控制。 |将 /etc/chron.d 的所有者和组设置为 root，并将权限设置为 0700 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-cron-file-perms" |
|确保配置 /etc/cron.daily 上的权限。<br /><sub>(94)</sub> |说明：向非特权用户授予此目录的写入权限可能会导致他们能够获取未经授权的提升权限。 授予对此目录的读取访问权限，有可能让非特权用户发现如何获取提升的权限或规避审核控制。 |将 /etc/chron.daily 的所有者和组设置为 root，并将权限设置为 0700 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-cron-file-perms" |
|确保配置 /etc/cron.hourly 上的权限。<br /><sub>(95)</sub> |说明：向非特权用户授予此目录的写入权限可能会导致他们能够获取未经授权的提升权限。 授予对此目录的读取访问权限，有可能让非特权用户发现如何获取提升的权限或规避审核控制。 |将 /etc/chron.hourly 的所有者和组设置为 root，并将权限设置为 0700 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-cron-file-perms" |
|确保配置 /etc/cron.monthly 上的权限。<br /><sub>(96)</sub> |说明：向非特权用户授予此目录的写入权限可能会导致他们能够获取未经授权的提升权限。 授予对此目录的读取访问权限，有可能让非特权用户发现如何获取提升的权限或规避审核控制。 |将 /etc/chron.monthly 的所有者和组设置为 root，并将权限设置为 0700 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-cron-file-perms" |
|确保配置 /etc/cron.weekly 上的权限。<br /><sub>(97)</sub> |说明：向非特权用户授予此目录的写入权限可能会导致他们能够获取未经授权的提升权限。 授予对此目录的读取访问权限，有可能让非特权用户发现如何获取提升的权限或规避审核控制。 |将 /etc/chron.weekly 的所有者和组设置为 root，并将权限设置为 0700 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r fix-cron-file-perms" |
|确保 at/cron 仅限于授权用户<br /><sub>(98)</sub> |描述：在许多系统上，只有系统管理员有权计划 `cron` 作业。 可使用 `cron.allow` 文件来控制谁可运行 `cron` 作业，从而强制实施此策略。 管理允许列表比管理拒绝列表容易。 使用拒绝列表时，将用户 ID 添加到系统中后，有可能会忘记将其添加到拒绝文件中。 |将 /etc/cron.deny 和 /etc/at.deny 替换为其各自的 `allow` 文件 |
|必须按最佳做法对 SSH 进行配置和管理。 -“/etc/ssh/sshd_config Protocol = 2”<br /><sub>(106.1)</sub> |说明：攻击者可能会利用较早版本的 SSH 协议中的漏洞来获取访问权限 |运行命令“/opt/microsoft/omsagent/plugin/omsremediate -r configure-ssh-protocol”。 这会在“/etc/ssh/sshd_config”文件中设置“Protocol 2” |
|必须按最佳做法对 SSH 进行配置和管理。 -“/etc/ssh/sshd_config IgnoreRhosts = yes”<br /><sub>(106.3)</sub> |说明：攻击者可能会利用 Rhosts 协议中的漏洞来获取访问权限 |运行命令“/usr/local/bin/azsecd remediate (/opt/microsoft/omsagent/plugin/omsremediate) -r enable-ssh-ignore-rhosts”。 这会将行“IgnoreRhosts yes”添加到文件“/etc/ssh/sshd_config” |
|确保将 SSH LogLevel 设置为 INFO<br /><sub>(106.5)</sub> |描述：SSH 提供若干个日志记录级别，其详细程度各不相同。 由于 `DEBUG ` 会导致提供过多数据，以至于难以识别重要的安全信息，因此，除了严格用于调试 SSH 通信之外，不建议使用此级别。 `INFO ` 级别是仅记录 SSH 用户登录活动的基础级别。 在许多情况下（例如，事件响应），确定特定用户在系统上处于活动状态的时间很重要。 注销记录可以消除那些已断开连接的用户，这有助于缩小范围。 |编辑 `/etc/ssh/sshd_config` 文件，按以下所示设置参数：```         LogLevel INFO         ``` |
|确保将 SSH MaxAuthTries 设置为 6 或更小的数字<br /><sub>(106.7)</sub> |说明：将 `MaxAuthTries ` 参数设置为较小值可最大程度减小对 SSH 服务器的暴力攻击成功的风险。 尽管建议设置为 4，但应根据站点策略设置数值。 |确保将 SSH MaxAuthTries 设置为 6 或更低，编辑 `/etc/ssh/sshd_config` 文件，按以下所示设置参数：```         MaxAuthTries 6         ``` |
|确保限制 SSH 访问权限<br /><sub>(106.11)</sub> |说明：限制哪些用户可以通过 SSH 远程访问系统可帮助确保只有经过授权的用户才能访问系统。 |确保 SSH 访问权限受到限制，编辑 `/etc/ssh/sshd_config` 文件，按下图所示设置一个或多个参数：```         AllowUsers          AllowGroups          DenyUsers          DenyGroups          ``` |
|应禁用通过 ssh 服务器实现的 rsh 命令仿真。 -“/etc/ssh/sshd_config RhostsRSAAuthentication = no”<br /><sub>(107)</sub> |说明：攻击者可能会利用 RHosts 协议中的漏洞来获取访问权限 |运行命令“/opt/microsoft/omsagent/plugin/omsremediate -r disable-ssh-rhost-rsa-auth”。 这会将行“RhostsRSAAuthentication no”添加到文件“/etc/ssh/sshd_config” |
|应禁用 SSH 基于主机的身份验证。 -“/etc/ssh/sshd_config HostbasedAuthentication = no”<br /><sub>(108)</sub> |说明：攻击者可能会利用基于主机的身份验证来获取被入侵主机的访问权限 |运行命令“/opt/microsoft/omsagent/plugin/omsremediate -r disable-ssh-host-based-auth”。 这会将行“HostbasedAuthentication no”添加到文件“/etc/ssh/sshd_config” |
|应禁用通过 SSH 进行的根登录。 -“/etc/ssh/sshd_config PermitRootLogin = no”<br /><sub>(109)</sub> |说明：攻击者可能暴力破解根密码或通过直接作为根进行登录来隐藏其命令历史记录 |运行密码“/usr/local/bin/azsecd remediate -r disable-ssh-root-login”。 这会将行“PermitRootLogin no”添加到文件“/etc/ssh/sshd_config” |
|应禁用使用空密码的帐户的远程连接。 -“/etc/ssh/sshd_config PermitEmptyPasswords = no”<br /><sub>(110)</sub> |说明：攻击者可以通过密码猜测获取访问权限 |运行命令“/usr/local/bin/azsecd remediate (/opt/microsoft/omsagent/plugin/omsremediate) -r disable-ssh-empty-passwords”。 这会将行“PermitEmptyPasswords no”添加到文件“/etc/ssh/sshd_config” |
|确保配置了 SSH 空闲超时间隔。<br /><sub>(110.1)</sub> |说明：如果不为连接设置关联的超时值，就可能导致许未经授权的用户能够访问其他用户的 ssh 会话。 设置超时值至少可以降低发生这种情况的风险。 尽管建议设置为 300 秒（5 分钟），但应根据站点策略设置此超时值。 建议将 `ClientAliveCountMax` 设置为 0。 在这种情况下，客户端会话将在空闲 5 分钟后终止，不会发送任何连接状态下的消息。 |编辑 /etc/ssh/sshd_config 文件，以根据策略设置参数 |
|确保将 SSH LoginGraceTime 设置为一分钟或更短的时间。<br /><sub>(110.2)</sub> |说明：将 `LoginGraceTime` 参数设置为较小值可最大程度减小对 SSH 服务器的暴力攻击成功的风险。 它还会限制未经过身份验证的并发连接数，尽管建议设置为 60 秒（1 分钟），但仍应基于站点策略来设置。 |编辑 /etc/ssh/sshd_config 文件，以根据策略设置参数或运行“/opt/microsoft/omsagent/plugin/omsremediate -r configure-login-grace-time” |
|确保只使用批准的 MAC 算法<br /><sub>(110.3)</sub> |描述：MD5 和 96 位 MAC 算法被视为弱算法，且已有证据显示这些算法被用于 SSH 降级攻击的情况有所增加。 随着算力的增加，弱算法因被视为可利用的攻击弱点而持续受到广泛关注。 阻断算法的攻击者可能会利用 MiTM 位置来解密 SSH 隧道并捕获凭据和信息 |编辑 /etc/sshd_config 文件，并添加/修改 MAC 行，在其中包含以逗号分隔的已批准的 MAC 的列表或运行“/opt/microsoft/omsagent/plugin/omsremediate-mac” |
|确保正确配置远程登录警告横幅。<br /><sub>(111)</sub> |说明：警告消息让试图登录到系统的用户了解到他们的有关系统的法律状态，其中必须包含拥有该系统的组织的名称和任何已实施的监视策略。 在登录横幅中显示操作系统和修补程序级别的信息也会产生副作用，即向对某个系统有特定攻击目的的攻击者提供详细的系统信息。 授权用户可以在登录后通过运行 `uname -a` 命令轻松获取这些信息。 |从 /etc/issue.net 文件中删除 \m \r \s 和 \v 的所有实例 |
|确保正确配置本地登录警告横幅。<br /><sub>(111.1)</sub> |说明：警告消息让试图登录到系统的用户了解到他们的有关系统的法律状态，其中必须包含拥有该系统的组织的名称和任何已实施的监视策略。 在登录横幅中显示操作系统和修补程序级别的信息也会产生副作用，即向对某个系统有特定攻击目的的攻击者提供详细的系统信息。 授权用户可以在登录后通过运行 `uname -a` 命令轻松获取这些信息。 |从 /etc/issue 文件中删除 \m \r \s 和 \v 的所有实例 |
|应启用 SSH 警告横幅。 - “/etc/ssh/sshd_config Banner = /etc/issue.net”<br /><sub>(111.2)</sub> |描述：不会向用户发出警告，指示其在系统上的操作受到监视 |运行命令“/usr/local/bin/azsecd remediate -r configure-ssh-banner”。 这会将行“Banner /etc/azsec/banner.txt”添加到文件“/etc/ssh/sshd_config” |
|不允许用户为 SSH 设置环境选项。<br /><sub>(112)</sub> |说明：攻击者可能会绕过 SSH 上的某些访问限制 |从文件“/etc/ssh/sshd_config”中删除行“PermitUserEnvironment yes” |
|应为 SSH 使用合适的密码。 (Ciphers aes128-ctr,aes192-ctr,aes256-ctr)<br /><sub>(113)</sub> |说明：攻击者可能会入侵安全保护较弱的 SSH 连接 |运行命令“/usr/local/bin/azsecd remediate -r configure-ssh-ciphers”。 这会将行“Ciphers aes128-ctr,aes192-ctr,aes256-ctr”添加到文件“/etc/ssh/sshd_config” |
|应禁用 avahi-daemon 服务。<br /><sub>(114)</sub> |说明：攻击者有可能利用 avahi-daemon 中的漏洞来获取访问权限 |禁用 avahi-daemon 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-avahi-daemon" |
|应禁用 cups 服务。<br /><sub>(115)</sub> |说明：攻击者有可能利用 cup 服务中的缺陷来提升权限 |禁用 cups 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-cups" |
|应禁用 isc-dhcpd 服务。<br /><sub>(116)</sub> |说明：攻击者有可能使用 dhcpd 向客户端提供错误信息，从而干扰正常运行。 |删除 isc-dhcp-server 包 (apt-get remove isc-dhcp-server) |
|应卸载 isc-dhcp-server 包。<br /><sub>(117)</sub> |说明：攻击者有可能使用 dhcpd 向客户端提供错误信息，从而干扰正常运行。 |删除 isc-dhcp-server 包 (apt-get remove isc-dhcp-server) |
|应卸载 sendmail 包。<br /><sub>(120)</sub> |说明：攻击者有可能使用此系统向其他用户发送包含恶意内容的电子邮件 |卸载 sendmail 包 (apt-get remove sendmail) |
|应卸载 postfix 包。<br /><sub>(121)</sub> |说明：攻击者有可能使用此系统向其他用户发送包含恶意内容的电子邮件 |卸载 postfix 包 (apt-get remove postfix) 或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r remove-postfix" |
|应视情况禁用 Postfix 网络侦听。<br /><sub>(122)</sub> |说明：攻击者有可能使用此系统向其他用户发送包含恶意内容的电子邮件 |在文件 "/etc/postfix/main.cf" 中添加一行 "inet_interfaces localhost" |
|应禁用 ldap 服务。<br /><sub>(124)</sub> |说明：攻击者有可能操纵此主机上的 LDAP 服务，将假数据分发给 LDAP 客户端 |卸载 slapd 包 (apt-get remove slapd) |
|应禁用 rpcgssd 服务。<br /><sub>(126)</sub> |说明：攻击者有可能利用 rpcgssd/nfs 中的缺陷获取访问权限 |禁用 rpcgssd 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-rpcgssd" |
|应禁用 rpcidmapd 服务。<br /><sub>(127)</sub> |说明：攻击者有可能利用 rpc.idmapd/nfs 中的缺陷获取访问权限 |禁用 rpcidmapd 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-rpcidmapd" |
|应禁用 portmap 服务。<br /><sub>(129)</sub> |说明：攻击者有可能利用 portmap 中的缺陷获取访问权限 |禁用 rpcbind 服务或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-rpcbind" |
|应禁用 rpcsvcgssd 服务。<br /><sub>(130)</sub> |说明：攻击者有可能利用 rpcsvcgssd 中的缺陷获取访问权限 |从文件 "/etc/inetd.conf" 中删除行 "NEED_SVCGSSD = yes" |
|应禁用 named 服务。<br /><sub>(131)</sub> |说明：攻击者有可能利用 DNS 服务将假数据分发给客户端 |卸载 bind9 包 (apt-get remove bind9) |
|应卸载 bind 包。<br /><sub>(132)</sub> |说明：攻击者有可能利用 DNS 服务将假数据分发给客户端 |卸载 bind9 包 (apt-get remove bind9) |
|应禁用 dovecot 服务。<br /><sub>(137)</sub> |说明：系统有可能充当 IMAP/POP3 服务器 |卸载 dovecot-core 包 (apt-get remove dovecot-core) |
|应卸载 dovecot 包。<br /><sub>(138)</sub> |说明：系统有可能充当 IMAP/POP3 服务器 |卸载 dovecot-core 包 (apt-get remove dovecot-core) |
|确保 /etc/passwd 中不存在旧的 `+` 条目<br /><sub>(156.1)</sub> |说明：攻击者有可能使用无密码的用户名 "+" 来获取访问权限 |删除 /etc/passwd 中以 "+:'" 开头的所有条目 |
|确保 /etc/shadow 中不存在旧的 `+` 条目<br /><sub>(156.2)</sub> |说明：攻击者有可能使用无密码的用户名 "+" 来获取访问权限 |删除 /etc/shadow 中以 "+:'" 开头的所有条目 |
|确保 /etc/group 中不存在旧的 `+` 条目<br /><sub>(156.3)</sub> |说明：攻击者有可能使用无密码的用户名 "+" 来获取访问权限 |删除 /etc/group 中以 "+:'" 开头的所有条目 |
|确保密码在 365 天或更短的时间内过期。<br /><sub>(157.1)</sub> |说明：缩短密码的最长期限还会降低攻击者利用泄露的凭据或通过在线暴力攻击成功破解凭据的机会时效。 |将 `/etc/login.defs` 中的 `PASS_MAX_DAYS` 参数设置为不超过 365，或运行 "opt/microsoft/omsagent/plugin/omsremediate -r  configure-password-policy-max-days" |
|确保将密码过期警告天数设为 7 或更多。<br /><sub>(157.2)</sub> |说明：提前发送警告，指示密码将过期，为用户留出想出新安全密码的时间。 用户在时间仓促的情况下有可能选择简单的密码，或将其记录在有泄露风险的位置。 |将 `/etc/login.defs` 中的 `PASS_WARN_AGE` 参数设置为 7，或运行 "opt/microsoft/omsagent/plugin/omsremediate -r configure-password-policy-warn-age" |
|请确保对密码重用进行限制。<br /><sub>(157.5)</sub> |描述：强制让用户无法重复使用过去的 5 个密码，可减小攻击者猜到密码的可能性。 |确保在 /etc/pam.d/common-password 中或同时在 /etc/pam.d/password_auth 和 /etc/pam.d/system_auth 中将 "remember" 选项至少设置为 5，或者运行 "/opt/microsoft/omsagent/plugin/omsremediate -r configure-password-policy-history" |
|确保密码哈希算法为 SHA-512<br /><sub>(157.11)</sub> |说明：SHA-512 算法提供比 MD5 更强的哈希，可通过增加攻击者成功破解密码的难度，为系统提供额外保护。 注意：这些更改仅适用于在本地系统上配置的帐户。 |将密码哈希算法设置为 sha512。 许多分发都提供了用于更新 PAM 配置的工具，可参阅相关文档了解详细信息。 如果未提供任何工具，请编辑相应的 `/etc/pam.d/` 配置文件，在其中添加行 `pam_unix.so`，或进行修改，使其包含 sha512 选项：``` password sufficient pam_unix.so sha512 ``` |
|确保密码更改间隔时间最短为 7 或更多。<br /><sub>(157.12)</sub> |说明：通过限制密码更改的频率，管理员可防止用户为了绕过密码重用控制而重复更改其密码。 |在 `/etc/login.defs` 中将 `PASS_MIN_DAYS` 参数设置为 7：`PASS_MIN_DAYS 7`。 修改所有用户的用户参数，将其密码设置为与 `chage --mindays 7` 匹配，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-pass-min-days" |
|确保所有用户的上次密码更改日期都是过去的日期<br /><sub>(157.14)</sub> |描述：如果某个用户记录的密码更改日期是未来的日期，他们便能绕过设置的任何密码过期。 |确保非活动密码锁定时间为 30 天或更短。可运行以下命令，将默认密码非活动期设置为 30天：``` # useradd -D -f 30 ``` 修改所有用户的用户参数，将其密码设置为与 ``` # chage --inactive 30  ``` 匹配 |
|确保系统帐户不是登录帐户<br /><sub>(157.15)</sub> |说明：应务必避免将非普通用户使用的帐户用于提供交互式 shell。 默认情况下，Ubuntu 将这些帐户的密码字段设置为无效字符串，但也建议将密码文件中的 shell 字段设置为 `/usr/sbin/nologin`。 这可避免将帐户用于运行任何命令。 |将审核脚本返回的任何帐户的 shell 设置为 `/sbin/nologin` |
|确保根帐户的默认组为 GID 0<br /><sub>(157.16)</sub> |说明：将 GID 0 用于 `_root_ ` 帐户有助于防止非特权用户意外获得访问 `_root_` 拥有的文件的权限。 |运行以下命令将 `root` 用户默认组设置为 GID `0`：``` # usermod -g 0 root ``` |
|确保根账号是唯一的 UID 0 帐户<br /><sub>(157.18)</sub> |说明：此访问权限必须限制为仅限默认 `root ` 帐户拥有，并且只能通过系统控制台访问。 必须使用批准的机制通过非特权帐户来进行管理性访问。 |删除 `root` 以外的具有 UID `0` 的任何用户，或为其分配新的 UID（如果适用）。 |
|删除不必要的包<br /><sub>(158)</sub> |说明:  |运行 "/opt/microsoft/omsagent/plugin/omsremediate -r remove-landscape-common" |
|删除不必要的帐户<br /><sub>(159)</sub> |说明：出于符合性目的 |删除不必要的帐户 |
|确保启用 auditd 服务<br /><sub>(162)</sub> |说明：系统事件的捕获为系统管理员提供了信息，使他们能够确定是否发生了对其系统的未经授权的访问。 |安装 audit 包 (systemctl enable auditd) |
|运行 AuditD 服务<br /><sub>(163)</sub> |说明：系统事件的捕获为系统管理员提供了信息，使他们能够确定是否发生了对其系统的未经授权的访问。 |运行 AuditD 服务 (systemctl start auditd) |
|确保启用 SNMP 服务器<br /><sub>(179)</sub> |说明：SNMP 服务器可以使用 SNMP v1 进行通信，此过程以明文传输数据，无需进行身份验证即可执行命令。 除非绝对必要，否则建议不要使用 SNMP 服务。 如果需要使用 SNMP，应将服务器配置为不允许 SNMP v1。 |运行以下命令之一以禁用 `snmpd`：``` # chkconfig snmpd off ```、``` # systemctl disable snmpd ```、``` # update-rc.d snmpd disable ``` |
|确保未启用 rsync 服务<br /><sub>(181)</sub> |说明：`rsyncd` 服务存在安全风险，因为它使用未加密的通信协议。 |运行以下命令之一来禁用 `rsyncd`：`chkconfig rsyncd off`、`systemctl disable rsyncd`、`update-rc.d rsyncd disable`，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r disable-rsysnc" |
|确保未启用 NIS 服务器<br /><sub>(182)</sub> |说明：NIS 服务本质上是一种不安全的系统，该系统很容易受到 DOS 攻击和缓冲区溢出的影响，且对 NIS 映射查询的身份验证的效果也不佳。 通常使用轻型目录访问协议 (LDAP) 这类协议来替代 NIS。 建议禁用该服务，并使用更安全的服务 |运行以下命令之一以禁用 `ypserv`：``` # chkconfig ypserv off ```、``` # systemctl disable ypserv ```、``` # update-rc.d ypserv disable ``` |
|确保未安装 rsh 客户端<br /><sub>(183)</sub> |说明：这些旧客户端存在许多安全漏洞，已替换为更安全的 SSH 包。 删除服务器后，最好确保同时删除这些客户端，以防止用户无意中使用这些命令暴露了其凭据。 请注意，删除 `rsh ` 包会删除 `rsh`、`rcp ` 和 `rlogin` 的客户端。 |使用合适的包管理器或通过手动安装过程卸载 `rsh`：``` yum remove rsh ```、``` apt-get remove rsh ```、``` zypper remove rsh ``` |
|禁止将 SMB V1 用于 Samba<br /><sub>(185)</sub> |说明：SMB v1 具有已知的严重漏洞，且不加密传输中的数据。 如果出于业务原因必须使用它，强烈建议采取其他步骤来缓解此协议固有的风险。 |如果 Samba 未运行，请删除包，否则，在 /etc/samba/smb.conf 的 [global] 部分会有一行：min protocol = SMB2，或运行 "/opt/microsoft/omsagent/plugin/omsremediate -r set-smb-min-version" |

> [!NOTE]
> Azure Policy 来宾配置设置的具体可用情况在 Azure 政府和其他国家云中可能会有所不同。

## <a name="next-steps"></a>后续步骤

有关 Azure Policy 和来宾配置的其他文章：

- [Azure Policy 来宾配置](../concepts/guest-configuration.md)。
- [法规符合性](../concepts/regulatory-compliance.md)概述。
- 在 [Azure Policy 示例](./index.md)中查看其他示例。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
