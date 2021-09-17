---
title: Azure 认可的 Linux 分发
description: 了解 Azure 认可的分发中的 Linux，包括 Ubuntu、CentOS、Oracle 和 SUSE 的指南。
services: virtual-machines
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: guybo
ms.openlocfilehash: b9262d512ed27419bab38b3ad26e1bae8cddee0d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696187"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure 认可的 Linux 分发版

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集 

合作伙伴在 Azure 市场中提供了 Linux 映像。 Microsoft 与各大 Linux 社区合作以便在认可的发行版列表中添加更多成员。 对于市场未提供的分发，用户始终可以按照[创建并上传包含 Linux 操作系统的虚拟硬盘](./create-upload-generic.md)中的准则安装自己的 Linux。

## <a name="supported-distributions-and-versions"></a>支持的发行版和版本

下表列出了 Azure 支持的 Linux 分发和版本。 有关详细信息，请参阅 [Microsoft Azure 中对 Linux 映像的支持](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)。

Hyper-V 和 Azure 的 Linux 集成服务 (LIS) 驱动程序是 Microsoft 直接为上游 Linux 内核提供的内核模块。 默认情况下，某些 LIS 驱动程序已内置在发行版的内核中。 基于 Red Hat Enterprise (RHEL)/CentOS 的早期分发在[用于 Hyper-V 和 Azure 的 Linux Integration Services 版本 4.2](https://www.microsoft.com/download/details.aspx?id=55106) 以单独下载的形式提供。 有关详细信息，请参阅 [Linux 内核要求](create-upload-generic.md#linux-kernel-requirements)。

Azure Linux 代理已预安装在 Azure 市场映像中，通常可从分发的包存储库中获得。 源代码可在 [GitHub](https://github.com/azure/walinuxagent)上找到。

| 分发 | 版本 | 驱动程序 | Agent |
| --- | --- | --- | --- |
| 由 Rogue Wave Software 提供的 CentOS（以前称为 OpenLogic） |CentOS 6.x、7.x、8.x |CentOS 6.3：[LIS 下载](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+：在内核中 |包：在“WALinuxAgent”下的[存储库](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)中 <br/>源代码：[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> 从 2020 年 5 月 26 日开始，CoreOS 已处于[生命周期终止](https://coreos.com/os/eol/)状态。 |不再可用 | | |
| 由 credativ 提供的 Debian |8.x、9.x、10.x |在内核中 |包：在“waagent”下的存储库中 <br/>源代码：[GitHub](https://github.com/Azure/WALinuxAgent) |
|由 Kinvolk 提供的 Flatcar Container Linux| Pro、Stable、Beta 版本| 在内核中 | /usr/share/oem/bin/waagent 中已安装 wa-linux-agent |
| 由 Oracle 提供的 Oracle Linux |6.x、7.x、8.x |在内核中 |包：在“WALinuxAgent”下的存储库中 <br/>源代码：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [由 Red Hat 提供的 Red Hat Enterprise Linux](../workloads/redhat/overview.md) |6.x、7.x、8.x |在内核中 |包：在“WALinuxAgent”下的存储库中 <br/>源代码：[GitHub](https://github.com/Azure/WALinuxAgent) |
| 由 SUSE 提供的 SUSE Linux Enterprise |SLES/SLES for SAP 11.x、12.x、15.x <br/> [SUSE 公有云映像生命周期](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |在内核中 |包：<p> 对于 11，在 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 存储库中<br>对于 12，包含在“公有云”模块中的“python-azure-agent”下<br/>源代码：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| 由 SUSE 提供的 openSUSE |openSUSE Leap 15.x |在内核中 |包：在“python-azure-agent”下的 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 存储库中 <br/>源代码：[GitHub](https://github.com/Azure/WALinuxAgent) |
| 由 Canonical 提供的 Ubuntu |Ubuntu Server 和 Pro。 16.x、18.x、20.x<p>对 Ubuntu 12.04 和 14.04 的扩展支持有关的信息可在此处找到：[Ubuntu 扩展安全维护](https://www.ubuntu.com/esm)。 |在内核中 |包：在“walinuxagent”下的存储库中 <br/>源代码：[GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>映像的更新节奏

Azure 要求受认可 Linux 分发版的发布者在每个季度或以更快的节奏，定期使用最新修补程序和安全修复程序，更新其在 Azure 市场中的映像。 在市场中更新的映像将自动以新版映像 SKU 的形式提供给客户使用。 有关如何查找 Linux 映像的详细信息：[在 Azure 市场中查找 Linux VM 映像](./cli-ps-findimage.md)。

## <a name="azure-tuned-kernels"></a>Azure 优化的内核

Azure 与众多认可的 Linux 分发商密切合作，以优化他们在 Azure 市场中发布的映像。 这种协作的一个方面体现在：开发针对 Azure 平台优化的 Linux 内核，并以完全受支持组件的形式交付 Linux 分发版。 Azure 优化的内核整合了新的功能和性能改进，并且与分发版中提供的默认或常规内核相比，其发布节奏更快（通常每季度发布一次）。

在大多数情况下，你会发现这些内核已预装在 Azure 市场中的默认映像内，因此客户可以立即获得这些优化内核的优势。 可在以下链接中找到有关这些 Azure 优化内核的详细信息：

- [CentOS Azure 优化内核 - 通过 CentOS 虚拟化 SIG 提供](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian 云内核 - 适用于 Azure 上的 Debian 10 和 Debian 9“后向移植”映像](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure 优化内核](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure 优化内核](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar Container Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>合作伙伴

### <a name="coreos"></a>CoreOS

CoreOS 计划于 2020 年 5 月 26 日结束之前进入[生命周期结束](https://coreos.com/os/eol/)状态。
Microsoft 为 CoreOS 用户提供了两 (2) 个迁移通道。

- 由 Kinvolk 提供的 Flatcar（请参阅“由 Kinvolk 提供的 Flatcar Container Linux”条目。）
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/)（客户必须上传自己的映像。 以下是[迁移文档](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)）。

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

credativ 是一家独立的咨询和服务公司，致力于通过免费软件开发和实施专业解决方案。 credativ 是获得国际认可的开源领域专业先行者，为许多公司的 IT 部门提供支持。 credativ 正在联合 Microsoft 准备 Debian 映像。 这些映像经过专门的设计，可以在 Azure 上运行并可通过该平台轻松进行管理。 credativ 还会通过其开源支持中心为 Azure 的 Debian 映像的维护和更新提供长期支持。

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk 是开发 Flatcar Container Linux 的公司，延续了最初的 CoreOS 愿景，为容器化应用程序提供最小、不可变且自动更新的基础。 作为最小发行版，Flatcar 只包含部署容器所需的包。 它的不可变文件系统保证了一致性和安全性，同时它的自动更新功能使你能够始终使用最新的安全修补程序。 

Flatcar Container Linux 由 Kinvolk 的全球 Linux 和容器技术专家团队提供支持，他们提供可选的商业支持订阅，包括全天候响应、安全和技术警报以及独家 Azure 优化映像（包括长期支持渠道）。


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是为公有云和私有云提供广泛的解决方案。 让客户面临如何在 Oracle 云以及其他云中部署 Oracle 软件这一问题时拥有更多选择权和灵活性。 通过 Oracle 与 Microsoft 的合作关系，客户可以凭借可信的证书和 Oracle 支持在 Microsoft 公有和私有云中部署 Oracle 软件。  Oracle 对 Oracle 公有和私有云的承诺和投资保持不变。

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

作为世界上领先的开源解决方案提供商，Red Hat 帮助 90% 以上的财富 500 强公司解决业务难题、调整 IT 与业务策略，以及为未来技术做准备。 Red Hat 通过开放式业务模型和价格合理、可预测的订阅模型提供安全的解决方案，从而实现了此目的。

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一个已验证的平台，该平台为云计算提供了高级可靠性和安全性。 SUSE 的通用 Linux 平台可与 Azure 云服务无缝集成，以便交付易于管理的云环境。 借助 1,800 多个独立软件供应商提供的适用于 SUSE Linux Enterprise Server 的 9,200 多个认证应用程序，SUSE 可确保满怀信心地在 Azure 上部署数据中心内支持的运行负载。

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 工程和开放社区监管对 Ubuntu 在客户端、服务器和云计算（包括用户的个人云服务）方面获得成功起到了推动作用。 Canonical 期望使用 Ubuntu 开发一个统一的免费平台（从手机到云），为手机、平板电脑、电视和台式机提供一致的接口系列。 这个愿景使得 Ubuntu 成为各种机构（从公有云提供商到消费类电子产品制造商）的首选以及各个技术专家的最爱。

借助其遍布全球的开发人员和工程中心，Canonical 在与硬件制造商、内容提供商和软件开发人员合作以将 Ubuntu 解决方案推向市场（从电脑到服务器和手持设备）方面拥有独特的优势。
