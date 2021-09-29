---
title: 将 CentOS VM 加入到 Azure AD 域服务 | Microsoft Docs
description: 了解如何配置 CentOS Linux 虚拟机并将其加入到 Azure Active Directory 域服务托管域。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/17/2021
ms.author: justinha
ms.openlocfilehash: dc82c2c2793495009b9200ac0976c796ef2ccc9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603745"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>将 CentOS Linux 虚拟机加入到 Azure Active Directory 域服务托管域

若要让用户使用一组凭据登录到 Azure 中的虚拟机 (VM)，可以将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 托管域。 将 VM 加入到 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据来登录和管理服务器。 托管域中的组成员身份也应用于控制对 VM 上的文件或服务的访问。

本文介绍如何将 CentOS Linux VM 加入到托管域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 不属于托管域的用户帐户。
* 独一无二的 Linux VM 名称，最大长度为 15 个字符，可避免名称被截断，名称被截断可能会在 Active Directory 中导致冲突。

## <a name="create-and-connect-to-a-centos-linux-vm"></a>创建并连接到 CentOS Linux VM

如果 Azure 中有现有的 CentOS Linux VM，请使用 SSH 连接到该 VM，然后继续执行下一步，[开始配置 VM](#configure-the-hosts-file)。

如果需要创建 CentOS Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，确保 VM 可以与托管域通信：

* 将该 VM 部署到已启用 Azure AD 域服务的虚拟网络或与其对等互连的虚拟网络。
* 将 VM 部署到与托管域不同的子网中。

部署 VM 后，请遵循使用 SSH 连接到 VM 的步骤。

## <a name="configure-the-hosts-file"></a>配置主机文件

若要确保为托管域正确配置了 VM 主机名，请编辑“/etc/hosts”文件，并设置主机名：

```console
sudo vi /etc/hosts
```

在 hosts 文件中，更新 localhost 地址 。 在以下示例中：

* aaddscontoso.com 是托管域的 DNS 域名。
* centos 是你要加入到托管域的 CentOS VM 的主机名。

将以下名称更新为你自己的值：

```console
127.0.0.1 centos.aaddscontoso.com centos
```

完成后，使用编辑器的 `:wq` 命令保存并退出 hosts 文件。

## <a name="install-required-packages"></a>安装所需程序包

VM 需要其他一些包才能将 VM 加入托管域。 若要安装和配置这些包，请使用 `yum` 更新和安装域加入工具：

```console
sudo yum install adcli realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>将 VM 加入托管域

在 VM 上安装所需的包之后，请将 VM 加入托管域。

1. 使用 `realm discover` 命令发现托管域。 以下示例发现领域 AADDSCONTOSO.COM。 以全部大写的形式指定你自己的托管域名：

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   如果 `realm discover` 命令找不到托管域，请查看以下故障排除步骤：

    * 确保可从该 VM 中访问域。 尝试使用 `ping aaddscontoso.com` 查看是否返回肯定答复。
    * 检查 VM 是否已部署到提供托管域的虚拟网络或与其对等互连的虚拟网络。
    * 确认已将虚拟网络的 DNS 服务器设置更新为指向托管域的域控制器。

1. 现在使用 `kinit` 命令初始化 Kerberos。 指定属于托管域的用户。 如有必要，[将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须以全部大写的形式输入托管域名。 在以下示例中，名为 `contosoadmin@aaddscontoso.com` 的帐户用于初始化 Kerberos。 输入你自己的属于托管域的用户帐户：

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 最后，使用 `realm join` 命令将 VM 加入托管域。 使用属于在前面的 `kinit` 命令中指定的托管域的相同用户帐户，例如 `contosoadmin@AADDSCONTOSO.COM`：

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --membership-software=adcli
    ```

将 VM 加入托管域需要一点时间。 以下示例输出显示 VM 已成功加入托管域：

```output
Successfully enrolled machine in realm
```

如果 VM 无法成功完成域加入过程，请确保 VM 的网络安全组允许将 TCP + UDP 端口 464 上的出站 Kerberos 流量发送到托管域的虚拟网络子网。

## <a name="allow-password-authentication-for-ssh"></a>允许对 SSH 进行密码身份验证

默认情况下，用户只能使用基于 SSH 公钥的身份验证登录到 VM。 基于密码的身份验证失败。 将 VM 加入托管域时，这些域帐户需要使用基于密码的身份验证。 更新 SSH 配置，以允许基于密码的身份验证，如下所示。

1. 使用编辑器打开 sshd_conf 文件：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 将 PasswordAuthentication 的行更新为 yes ：

    ```console
    PasswordAuthentication yes
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出 sshd_conf 文件。

1. 若要应用更改并让用户使用密码登录，请重新启动 SSH 服务：

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>为“AAD DC 管理员”组授予 sudo 特权

若要授予“AAD DC Administrators”组成员对 CentOS VM 的管理特权，请向“/etc/sudoers”添加一个条目 。 添加后，“AAD DC 管理员”组的成员即可使用 CentOS VM 上的 `sudo` 命令。

1. 打开 sudoers 文件进行编辑：

    ```console
    sudo visudo
    ```

1. 将以下条目添加到“/etc/sudoers”文件的末尾。 “AAD DC 管理员”组的名称中包含空格，因此请在组名称中包含反斜杠转义符。 添加自己的域名，例如 aaddscontoso.com：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成后，使用编辑器的 `:wq` 命令进行保存并退出编辑器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

若要验证 VM 是否已成功加入托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并且已应用域的组成员身份。

1. 从控制台创建新的 SSH 连接。 通过 `ssh -l` 命令使用属于托管域的域帐户（如 `contosoadmin@aaddscontoso.com`），然后输入 VM 的地址，例如 centos.aaddscontoso.com。 如果使用 Azure Cloud Shell，请使用 VM 的公共 IP 地址，而不是使用内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. 成功连接到 VM 后，验证是否已正确初始化主目录：

    ```console
    pwd
    ```

    你应位于“/home”目录中，并具有你自己的与用户帐户相匹配的目录。

1. 现在请检查是否已正确解析组成员身份：

    ```console
    id
    ```

    应会看到托管域中的组成员身份。

1. 如果以“AAD DC 管理员”组成员的身份登录到 VM，请检查是否可以正确使用 `sudo` 命令：

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到托管域或使用域帐户登录时遇到问题，请参阅[域加入问题故障排除](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
