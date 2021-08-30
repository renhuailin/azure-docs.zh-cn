---
title: Red Hat 更新基础结构 | Microsoft Docs
description: 了解用于 Microsoft Azure 中按需 Red Hat Enterprise Linux 实例的 Red Hat 更新基础结构
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 02ea434b74e92e2bdd126a98d33e9e68adb811d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468974"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构
 [Red Hat 更新基础结构](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) 允许云提供程序（如 Azure）镜像 Red Hat 托管的存储库内容，创建包含 Azure 特定内容的自定义存储库，并将其提供给最终用户 VM 使用。

已预先配置 Red Hat Enterprise Linux (RHEL) 即用即付 (PAYG) 映像来访问 Azure RHUI。 不需要任何其他配置。 要获取最新更新，请在 RHEL 实例准备好后运行 `sudo yum update`。 RHEL PAYG 软件费涵盖了此服务。

若要详细了解 Azure 中的 RHEL 映像（包括发布和保留策略），请参阅 [Azure 中的 Red Hat Enterprise Linux 映像概述](./redhat-images.md)。

可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。

> [!IMPORTANT]
> RHUI 仅适用于即用即付 (PAYG) 映像。 对于自定义映像和黄金映像（也称为自带订阅 [BYOS]），系统需要附加到 RHSM 或卫星才能接收更新。 有关更多详细信息，请参阅 [Red Hat 文章](https://access.redhat.com/solutions/253273)。


## <a name="important-information-about-azure-rhui"></a>有关 Azure RHUI 的重要信息

* Azure RHUI 是更新基础结构，它支持在 Azure 中创建的所有 RHEL PAYG VM。 这并不会妨碍你向订阅管理器、卫星或其他更新源注册 PAYG RHEL VM，但使用 PAYG VM 进行注册将导致间接双重计费。 有关详细信息，请参阅以下要点。
* RHEL PAYG 映像价格涵盖了 Azure 托管 RHUI 的访问权限。 从 Azure 托管的 RHUI 注销 PAYG RHEL VM 不会将虚拟机转换为自带许可 (BYOL) 类型的 VM。 如果向另一更新源注册同一 VM，可能会产生间接双倍费用。 第一次你需要支付 Azure RHEL 软件费。 第二次你需要支付之前已购买的 Red Hat 订阅费。 如果始终需要使用 Azure 托管的 RHUI 以外的更新基础结构，请考虑注册使用 [RHEL BYOS 映像](./byos.md)。

* Azure 中的 RHEL SAP PAYG 映像（RHEL for SAP、RHEL for SAP HANA 以及 RHEL for SAP Business Application）已根据 SAP 认证需要，连接到保留在特定 RHEL 次要版本上的专用 RHUI 通道。

* 对 Azure 托管的 RHUI 的访问限制为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。 若要通过本地网络基础结构代理所有 VM 流量，可能需要为 RHEL PAYG VM 设置用户定义的路由才能访问 Azure RHUI。 如果是这种情况，则需要为所有 RHUI IP 地址添加用户定义的路由。


## <a name="image-update-behavior"></a>映像更新行为

自 2019 年 4 月起，Azure 提供默认已连接到扩展更新支持 (EUS) 存储库的 RHEL 映像以及默认已连接到常规（非 EUS）存储库的 RHEL 映像。 有关 RHEL EUS 的更多详细信息，请参阅 Red Hat 的[版本生命周期文档](https://access.redhat.com/support/policy/updates/errata)和 [EUS 文档](https://access.redhat.com/articles/rhel-eus)。 `sudo yum update` 的默认行为会因从其中进行预配的 RHEL 映像而异，因为不同的映像连接到不同的存储库。

有关完整映像列表，请使用 Azure CLI 运行 `az vm image list --publisher redhat --all`。

### <a name="images-connected-to-non-eus-repositories"></a>连接到非 EUS 存储库的映像

如果从连接到非 EUS 存储库的 RHEL 映像预配 VM，则在运行 `sudo yum update` 时，将升级到最新的 RHEL 次要版本。 例如，如果从 RHEL 7.4 PAYG 映像预配 VM 并运行 `sudo yum update`，最终会获得 RHEL 7.8 VM（RHEL7 系列中的最新次要版本）。

连接到非 EUS 存储库的映像不会在 SKU 中包含次要版本号。 SKU 是 URN（映像的完整名称）中的第三个元素。 例如，以下所有映像都附加到非 EUS 存储库：

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

请注意，SKU 为 7-LVM 或 7-RAW。 次要版本在这些映像的版本（URN 中的第四个元素）中指示。

### <a name="images-connected-to-eus-repositories"></a>连接到 EUS 存储库的映像

如果从连接到 EUS 存储库的 RHEL 映像预配 VM，则在运行 `sudo yum update` 时，不会升级到最新的 RHEL 次要版本。 这是因为连接到 EUS 存储库的映像也被版本锁定到其特定的次要版本。

连接到 EUS 存储库的映像将在 SKU 中包含次要版本号。 例如，以下所有映像都附加到 EUS 存储库：

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS 和版本锁定 RHEL VM

对于可能会希望在预配 VM 后将其 RHEL VM 锁定到特定 RHEL 次要版本的客户，扩展更新支持 (EUS) 存储库可供使用。 可以通过对存储库进行更新以指向扩展的更新支持存储库来将 RHEL VM 的版本锁定到特定的次版本。 也可撤消 EUS 版本锁定操作。

>[!NOTE]
> RHEL Extras 不支持 EUS。 这意味着，如果要安装通常可从 RHEL Extras 渠道获得的包，则在 EUS 上将无法这样做。 [Red Hat Enterprise Linux Extras 产品生命周期 - Red Hat 客户门户](https://access.redhat.com/support/policy/updates/extras/)页面上详细说明了 Red Hat Extras 产品生命周期。

在撰写本文时，对 RHEL 7.4 及更低版本的 EUS 支持已终止。 有关更多详细信息，请参阅 [Red Hat 文档](https://access.redhat.com/support/policy/updates/errata/#Long_Support)中的“Red Hat Enterprise Linux 扩展维护”部分。
* RHEL 7.4 EUS 支持于 2019 年 8 月 31 日终止
* RHEL 7.5 EUS 支持于 2020 年 4 月 30 日终止
* RHEL 7.6 EUS 支持于 2021 年 5 月 31 日终止
* RHEL 7.7 EUS 支持于 2021 年 8 月 30 日终止

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>将 RHEL VM 7.x 切换到 EUS（版本锁定到特定次要版本）
使用以下指令将 RHEL 7.x VM 锁定到特定次要版本（以 root 身份运行）：

>[!NOTE]
> 这仅适用于 EUS 可用的 RHEL 7.x 版本。 在撰写本文时，这包括 RHEL 7.2-7.7。 有关更多详细信息，请访问 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)页。

1. 禁用非 EUS 存储库：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. 添加 EUS 存储库：
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. 锁定 `releasever` 变量（以 root 身份运行）：
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 以上指令会将 RHEL 次版本锁定到当前次版本。 如果希望进行升级并锁定到不是最新版本的较高次版本，请输入具体的次版本。 例如，`echo 7.5 > /etc/yum/vars/releasever` 会将 RHEL 版本锁定到 RHEL 7.5。

1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>将 RHEL VM 8.x 切换到 EUS（版本锁定到特定次要版本）
使用以下指令将 RHEL 8.x VM 锁定到特定次要版本（以 root 身份运行）：

>[!NOTE]
> 这仅适用于 EUS 可用的 RHEL 8.x 版本。 在撰写本文时，这包括 RHEL 8.1-8.2。 有关更多详细信息，请访问 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)页。

1. 禁用非 EUS 存储库：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. 获取 EUS 存储库配置文件：
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. 添加 EUS 存储库：
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. 锁定 `releasever` 变量（以 root 身份运行）：
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 以上指令会将 RHEL 次版本锁定到当前次版本。 如果希望进行升级并锁定到不是最新版本的较高次版本，请输入具体的次版本。 例如，`echo 8.1 > /etc/yum/vars/releasever` 会将 RHEL 版本锁定到 RHEL 8.1。

    >[!NOTE]
    > 如果存在访问 releasever 的权限问题，可使用“nano /etc/yum/vars/releaseve”编辑该文件并添加映像版本详细信息，然后进行保存（按“Ctrl+O”再按 Enter，然后按“Ctrl+X”）。  

1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>将 RHEL 7.x VM 切换回非 EUS（删除版本锁）
以 root 身份运行以下命令：
1. 删除 `releasever` 文件：
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 禁用 EUS 存储库：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. 配置 RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>将 RHEL 8.x VM 切换回非 EUS（删除版本锁）
以 root 身份运行以下命令：
1. 删除 `releasever` 文件：
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 禁用 EUS 存储库：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. 获取常规的存储库配置文件：
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. 添加 EUS 存储库：
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 内容传送服务器的 IP

在 RHEL 按需映像可用的所有区域中都提供了 RHUI。 目前包括 [Azure 状态仪表板](https://azure.microsoft.com/status/)页上列出的所有公共区域、Azure 美国政府区域和 Microsoft Azure 德国区域。

如果使用网络配置进一步限制来自 RHEL PAYG VM 的访问，根据所处环境，请确保允许使用以下 IP 以便 `yum update` 能够正常工作：


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>从 2020 年 1 月起，新的 Azure 美国政府版映像将使用上面 Azure 全球标头中提到的公共 IP。

>[!NOTE]
>另请注意，已弃用 Azure 德国，取而代之的是公共德国地区。 建议 Azure 德国客户使用 [Red Hat 更新基础结构](#manual-update-procedure-to-use-the-azure-rhui-servers)页面上的步骤开始指向公共 RHUI。

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 基础结构


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>更新 VM 上已过期的 RHUI 客户端证书

如果使用较旧的 RHEL VM 映像（例如，RHEL 7.4 [映像 URN：`RedHat:RHEL:7.4:7.4.2018010506`]），则会遇到因 TLS/SSL 客户端证书现已过期而出现的 RHUI 连接问题。 你看到的错误可能类似于“SSL 对等机已拒绝你的证书，因为已过期”或“错误：无法为存储库检索存储库元数据(repom .xml): ...请验证其路径并重试”。 若要解决此问题，请使用以下命令更新 VM 上的 RHUI 客户端程序包：

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

另外，运行 `sudo yum update` 也可能会更新客户端证书包（具体取决于 RHEL 版本），尽管你将看到其他存储库的“SSL 证书已过期”错误。 如果此更新成功，则应当还原与其他 RHUI 存储库的正常连接，以便能够成功运行 `sudo yum update`。

如果在运行 `yum update` 时遇到 404 错误，请尝试运行以下命令来刷新 yum 缓存：
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>排查 Azure RHUI 连接问题
如果从 Azure RHEL PAYG VM 连接到 Azure RHUI 时遇到问题，请按照下列步骤操作：

1. 检查 Azure RHUI 终结点的 VM 配置：

    1. 检查 `/etc/yum.repos.d/rh-cloud.repo` 文件 `[rhui-microsoft-azure-rhel*]` 部分的 `baseurl` 是否包含对 `rhui-[1-3].microsoft.com` 的引用。 如果是，则使用的是新 Azure RHUI。

    1. 如果它指向 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位置，则需要更新配置。 你使用的是旧 VM 快照，需要将其更新为指向新的 Azure RHUI。

1. Azure 托管的 RHUI 仅限 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM 进行访问。

1. 如果使用新配置并已确认 VM 从 Azure IP 范围建立了连接，但仍无法连接到 Azure RHUI，请向 Microsoft 或 Red Hat 提出支持案例。

### <a name="infrastructure-update"></a>基础结构更新

2016 年 9 月，我们部署了更新的 Azure RHUI。 2017 年 4 月，我们关闭了旧版 Azure RHUI。 如果一直在使用 2016 年 9 月或之后的 RHEL PAYG 映像（或其快照），则会自动连接到新的 Azure RHUI。 但是，如果在 VM 上使用旧版本的快照，则需手动更新其配置以访问 Azure RHUI，如以下部分所述。

新的 Azure RHUI 服务器通过 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)进行配置。 在流量管理器中，任何 VM 都可使用单一终结点 (rhui-1.microsoft.com)，而无需考虑区域。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>执行手动更新过程以使用 Azure RHUI 服务器
此过程仅供参考。 RHEL PAYG 映像已包含用于连接 Azure RHUI 的正确配置。 要手动更新配置以使用 Azure RHUI 服务器，请完成以下步骤：

- 适用于 RHEL 6：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- 适用于 RHEL 7：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- 适用于 RHEL 8：
    1. 创建配置文件：
        ```bash
        vi rhel8.config
        ```
    1. 将以下内容添加到配置文件中：
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. 保存文件并运行以下命令：
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. 更新 VM
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>后续步骤
* 要通过 Azure 市场 PAYG 映像创建 Red Hat Enterprise Linux VM 并利用 Azure 托管的 RHUI，请转到 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6)。
* 若要详细了解 Azure 中的 Red Hat 映像，请转到此[文档页面](./redhat-images.md)。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
