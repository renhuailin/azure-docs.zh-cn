---
title: Azure 上的 Red Hat Enterprise Linux 映像的就地升级
description: 了解如何从 Red Hat Enterprise 7、windows 映像就地升级到最新的2.x 版。
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 9b35a3cbe23af91f7f0b8aceecfb8ba3c9720461
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484407"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux 就地升级

本文提供了有关如何从 Red Hat Enterprise Linux (RHEL) 7 到 Red Hat Enterprise Linux 8 执行就地升级的说明。 说明使用 `leapp` Azure 中的工具。 在就地升级过程中，现有 RHEL 7 操作系统将替换为 RHEL 8 版本。

>[!Note] 
> Red Hat Enterprise Linux 上的 SQL Server 产品/服务不支持在 Azure 上就地升级。

## <a name="what-to-expect-during-the-upgrade"></a>升级过程中的预期情况
在升级过程中，系统会重新启动几次。 最后一次重新启动将 VM 升级到 RHEL 8 最新次要版本。 

升级过程可能需要20分钟到2小时。 总时间取决于多种因素，例如 VM 大小和系统上安装的包数。

## <a name="preparations"></a>准备
Red Hat 和 Azure 建议使用就地升级将系统转换为下一个主要版本。 

在开始升级之前，请记住以下注意事项。 

>[!Important] 
> 在开始升级之前，拍摄映像的快照。

* 请确保使用的是最新的 RHEL 7 版本。 当前，最新版本为 RHEL 7.9。 如果使用锁定的版本，但无法升级到 RHEL 7.9，请按照 [以下步骤切换到) 存储库 (扩展更新支持](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)。

* 运行以下命令以检查升级，并查看其是否会成功完成。 该命令应生成 */var/log/leapp/leapp-report.txt* 文件。 此文件说明了该过程、发生的情况以及升级是否可行。

    >[!NOTE]
    > 使用根帐户运行本文中的命令。 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* 确保串行控制台正常运行。 在升级过程中，你将使用此控制台进行监视。

* 在/etc/ssh/中启用 SSH 根访问 *sshd_config*：
    1. 打开文件 " */etc/ssh/sshd_config*。
    1. 搜索 `#PermitRootLogin yes`。
    1. 删除数字符号 (`#`) 取消注释字符串。

## <a name="upgrade-steps"></a>升级步骤

请仔细执行这些步骤。 建议在测试计算机上尝试升级，然后再在生产实例上尝试。

1. 执行 `yum` 更新以提取最新的客户端包。
    ```bash
    yum update -y
    ```

1. 安装 `leapp-client-package`。
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. 在 [Red Hat 门户](https://access.redhat.com/articles/3664871)中，获取 *leapp-data* 文件，其中包含 *repomap.csv* 和 *pes-events.js*。 提取 *leapp-data* 文件。
    1. 下载 *leapp-data* 文件。
    1. 提取内容并删除该文件。 请使用以下命令：
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. 添加的 `answers` 文件 `leapp` 。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 开始升级。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`命令成功完成后，手动重新启动系统以完成该过程。 系统无法使用，因为它会重新启动几次。 使用串行控制台监视进程。

1.  验证升级是否成功完成。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 升级完成后，请删除根 SSH 访问权限：
    1. 打开文件 " */etc/ssh/sshd_config*。
    1. 搜索 `#PermitRootLogin yes`。
    1. 添加数字符号 (`#`) 注释字符串。

1. 重新启动 SSHD 服务以应用所做的更改。
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>常见问题

如果 `leapp preupgrade` 进程失败或进程失败，通常会发生以下错误 `leapp upgrade` ：

* **错误**：找不到以下已禁用的插件模式的匹配项。

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **解决方案**：禁用订阅管理器插件。 通过编辑 */etc/yum/pluginconf.d/subscription-manager.conf* 文件并将其更改 `enabled` 为来禁用它 `enabled=0` 。

    当启用的订阅管理器 `yum` 插件未用于 vm 时，将发生此错误 `PAYG` 。

* **错误**：使用 root 远程登录时可能存在问题。

    当失败时，可能会看到此错误 `leapp preupgrade` ：

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **解决方案**：在 */Etc/sshd_config* 中启用根访问。

    如果 */etc/sshd_config* 中未启用根 SSH 访问，则会发生此错误。 有关详细信息，请参阅本文中的 [准备](#preparations) 部分。 


## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure 中的 Red Hat 映像](./redhat-images.md)。
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)。
* 了解有关 [RHEL BYOS 产品/服务](./byos.md)的详细信息。
* 若要了解有关 Red Hat 就地升级过程的详细信息，请参阅 Red Hat 文档中的 [从 RHEL 7 升级到 rhel 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) 。
* 若要了解有关 RHEL 所有版本的 Red Hat 支持策略的详细信息，请参阅 Red Hat 文档中的 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata) 。