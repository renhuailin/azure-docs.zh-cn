---
title: Azure 上 Red Hat Enterprise Linux 映像的就地升级
description: 了解如何执行从 Red Hat Enterprise 7.x 映像到最新 8.x 版的就地升级。
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mamccrea
ms.openlocfilehash: e127ec753a6db0556c901aff737656da47883c7a
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454860"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux 就地升级

适用于：:heavy_check_mark: Linux VM 

本文提供了有关如何执行从 Red Hat Enterprise Linux (RHEL) 7 到 Red Hat Enterprise Linux 8 的就地升级的说明。 本说明使用 Azure 中的 `leapp` 工具。 在就地升级过程中，现有 RHEL 7 操作系统被替换为 RHEL 8 版本。

>[!Note] 
> Red Hat Enterprise Linux 上的 SQL Server 产品/服务不支持在 Azure 上就地升级。

## <a name="what-to-expect-during-the-upgrade"></a>升级过程中的预期情况
在升级过程中，系统会重启几次。 最后一次重启将 VM 升级到 RHEL 8 最新次要版本。 

升级过程可能需要 20 分钟到 2 小时不等。 总时间取决于多种因素，例如 VM 大小和系统上安装的包数。

## <a name="preparations"></a>准备工作
Red Hat 和 Azure 建议使用就地升级将系统转换到下一个主要版本。 

在开始升级前，请注意以下事项。 

>[!Important] 
> 在开始升级前，生成映像的快照。

* 确保使用最新的 RHEL 7 版本。 目前，最新版本为 RHEL 7.9。 如果使用锁定的版本，且无法升级到 RHEL 7.9，请按照[这些步骤切换到非 EUS（扩展更新支持）存储库](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)。

* 运行以下命令检查升级，查看它是否会成功完成。 该命令应生成 /var/log/leapp/leapp-report.txt 文件。 此文件解释了该过程、发生的情况以及升级是否可能。

    >[!NOTE]
    > 使用根帐户运行本文中的命令。 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* 确保串行控制台正常运行。 你将使用此控制台在升级过程中进行监视。

* 在 /etc/ssh/sshd_config 中启用 SSH 根访问：
    1. 打开 /etc/ssh/sshd_config 文件。
    1. 搜索 `#PermitRootLogin yes`。
    1. 删除数字符号 (`#`) 以取消注释字符串。

## <a name="upgrade-steps"></a>升级步骤

仔细执行这些步骤。 建议先在测试计算机上尝试升级，然后再在生产实例上尝试升级。

1. 执行 `yum` 更新以提取最新的客户端包。
    ```bash
    yum update -y
    ```

1. 安装 `leapp-client-package`。
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. 在 [Red Hat 门户](https://access.redhat.com/articles/3664871)中，获取 leapp-data.tar.gz 文件，其中包含 repomap.csv 和 pes-events.json  。 提取 leapp-data.tar.gz 文件。
    1. 下载 leapp-data.tar.gz 文件。
    1. 提取内容并删除文件。 请使用以下命令：
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. 为 `leapp` 添加一个 `answers` 文件。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 开始升级。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  在 `leapp upgrade` 命令成功完成后，手动重启系统以完成该过程。 系统不可用，因为它会重启几次。 使用串行控制台监视进程。

1.  验证更新已成功完成。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 升级完成后，删除根 SSH 访问：
    1. 打开 /etc/ssh/sshd_config 文件。
    1. 搜索 `#PermitRootLogin yes`。
    1. 添加数字符号 (`#`) 以注释字符串。

1. 重启 SSHD 服务以应用更改。
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>常见问题

在 `leapp preupgrade` 进程或 `leapp upgrade` 进程失败时，通常会发生以下错误：

* 错误：找不到以下已禁用的插件模式的匹配项。

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    解决方案：禁用 subscription-manager 插件。 通过编辑 /etc/yum/pluginconf.d/subscription-manager.conf 文件并将 `enabled` 更改为 `enabled=0` 以禁用它。

    当启用的 subscription-manager `yum` 插件没有用于 `PAYG` VM 时，会发生此错误。

* 错误：使用根进行远程登录时可能存在问题。

    当 `leapp preupgrade` 失败时，你可能会看到此错误：

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
    解决方案：在 /etc/sshd_config 中启用根访问。

    若未在 /etc/sshd_config 中启用根 SSH 访问，则会发生此错误。 有关详细信息，请参阅本文中的[准备工作](#preparations)部分。 


## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure 中的 Red Hat 映像](./redhat-images.md)。
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)。
* 详细了解 [RHEL BYOS 产品/服务](./byos.md)。
* 若要详细了解 Red Hat 就地升级过程，请参阅 Red Hat 文档中的[从 RHEL 7 升级到 RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)。
* 若要详细了解所有 RHEL 版本的 Red Hat 支持策略，请参阅 Red Hat 文档中的 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。