---
title: 将 CoreOS VM 加入到 Azure AD 域服务 | Microsoft Docs
description: 了解如何配置 CoreOS Linux 虚拟机并将其加入到 Azure AD 域服务托管域。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 4de85e8cf62ed2b8e5726d2c569396bcba1cb968
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590093"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>将 CoreOS 虚拟机加入 Azure Active Directory 域服务托管域

若要让用户使用一组凭据登录到 Azure 中的虚拟机 (VM)，可以将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 托管域。 将 VM 加入到 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据来登录和管理服务器。 托管域中的组成员身份也应用于控制对 VM 上的文件或服务的访问。

本文介绍如何将 CoreOS VM 加入到托管域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 属于托管域的用户帐户。
* 独一无二的 Linux VM 名称，最大长度为 15 个字符，可避免名称被截断，名称被截断可能会在 Active Directory 中导致冲突。

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>创建并连接到 CoreOS Linux VM

如果 Azure 中有现有的 CoreOS Linux VM，请使用 SSH 连接到该 VM，然后继续执行下一步，[开始配置 VM](#configure-the-hosts-file)。

如果需要创建 CoreOS Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，确保 VM 可以与托管域通信：

* 将该 VM 部署到已启用 Azure AD 域服务的虚拟网络或与其对等互连的虚拟网络。
* 将 VM 部署到与 Azure AD 域服务托管域不同的子网中。

部署 VM 后，请遵循使用 SSH 连接到 VM 的步骤。

## <a name="configure-the-hosts-file"></a>配置主机文件

若要确保为托管域正确配置了 VM 主机名，请编辑“/etc/hosts”文件，并设置主机名：

```console
sudo vi /etc/hosts
```

在 hosts 文件中，更新 localhost 地址 。 在以下示例中：

* aaddscontoso.com 是托管域的 DNS 域名。
* coreos 是你要加入到托管域的 CoreOS VM 的主机名。

将以下名称更新为你自己的值：

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

完成后，使用编辑器的 `:wq` 命令保存并退出 hosts 文件。

## <a name="configure-the-sssd-service"></a>配置 SSSD 服务

更新 /etc/sssd/sssd.conf SSSD 配置。

```console
sudo vi /etc/sssd/sssd.conf
```

为以下参数指定你自己的托管域名：

* 全部大写的域
* [域/AADDSCONTOSO] 其中 AADDSCONTOSO 全部大写
* ldap_uri
* ldap_search_base
* krb5_server
* 全部大写的“krb5_realm”

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>将 VM 加入托管域

更新 SSSD 配置文件后，现在将虚拟机加入到托管域。

1. 首先，使用 `adcli info` 命令验证你是否可以查看有关托管域的信息。 下面的示例获取域“AADDSCONTOSO.COM”的信息。 以全部大写的形式指定你自己的托管域名：

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   如果 `adcli info` 命令找不到托管域，请查看以下故障排除步骤：

    * 确保可从该 VM 中访问域。 尝试使用 `ping aaddscontoso.com` 查看是否返回肯定答复。
    * 检查 VM 是否已部署到提供托管域的虚拟网络或与其对等互连的虚拟网络。
    * 确认已将虚拟网络的 DNS 服务器设置更新为指向托管域的域控制器。

1. 现在，使用 `adcli join` 命令将 VM 加入到托管域。 指定属于托管域的用户。 如有必要，[将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须以全部大写的形式输入托管域名。 在以下示例中，名为 `contosoadmin@aaddscontoso.com` 的帐户用于初始化 Kerberos。 输入你自己的属于托管域的用户帐户。

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    VM 成功加入托管域后，`adcli join` 命令不会返回任何信息。

1. 若要应用域加入配置，请启动 SSSD 服务：
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

若要验证 VM 是否已成功加入托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并且已应用域的组成员身份。

1. 从控制台创建新的 SSH 连接。 通过 `ssh -l` 命令使用属于托管域的域帐户（如 `contosoadmin@aaddscontoso.com`），然后输入 VM 的地址，例如 coreos.aaddscontoso.com。 如果使用 Azure Cloud Shell，请使用 VM 的公共 IP 地址，而不是使用内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. 现在请检查是否已正确解析组成员身份：

    ```console
    id
    ```

    应会看到托管域中的组成员身份。

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到托管域或使用域帐户登录时遇到问题，请参阅[域加入问题故障排除](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
