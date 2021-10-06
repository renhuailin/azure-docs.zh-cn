---
title: Azure 中可用的 Red Hat Enterprise Linux 映像
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mamccrea
ms.openlocfilehash: f349e525d8fa16e7c218a248cb8e2c1fb4e21762
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455107"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 中可用的 Red Hat Enterprise Linux (RHEL) 映像

**适用于：** :heavy_check_mark: Linux VM 

Azure 针对不同用例提供了各种 RHEL 映像。

> [!NOTE]
> 所有 RHEL 映像在 Azure 公有云和 Azure 政府云中都可用。 它们在 Azure 中国云中不可用。

## <a name="list-of-rhel-images"></a>RHEL 映像列表
这是 Azure 中可用的 RHEL 映像的列表。 除非另有说明，否则，所有映像都是 LVM 分区映像并附加到常规 RHEL 存储库（不是 EUS 存储库，也不是 E4S 存储库）。 以下映像目前可用于一般用途：

> [!NOTE]
> 不再生成 RAW 映像，取而代之的是 LVM 分区映像。 与旧的 raw（非 LVM）分区方案相比，LVM 有几个优点，包括灵活得多的分区重设大小选项。

产品/服务| SKU | 分区 | 设置 | 备注
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理 | 从 12 月 1 日起提供扩展的生命周期支持。 [此处提供更多详细信息。](redhat-extended-lifecycle-support.md)
|             | 6.8      | RAW    | Linux 代理 | 从 12 月 1 日起提供扩展的生命周期支持。 [此处提供更多详细信息。](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Linux 代理 | 从 12 月 1 日起提供扩展的生命周期支持。 [此处提供更多详细信息。](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Linux 代理 | 从 12 月 1 日起提供扩展的生命周期支持。 [此处提供更多详细信息。](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Linux 代理 | RHEL 7.x 系列映像。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7-LVM    | LVM    | Linux 代理 | RHEL 7.x 系列映像。 <br> 默认情况下附加到常规存储库（不是 EUS）。 如果你想找标准 RHEL 映像进行部署，请使用此组映像和/或其第 2 代对应项。
|             | 7lvm-gen2| LVM    | Linux 代理 | 第 2 代 RHEL 7.x 系列映像。 <br> 默认情况下附加到常规存储库（不是 EUS）。 如果你想找标准 RHEL 映像进行部署，请使用此组映像和/或其第 1 代对应项。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 系列映像。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7.2      | RAW    | Linux 代理 |
|             | 7.3      | RAW    | Linux 代理 |
|             | 7.4      | RAW    | Linux 代理 | 从 2019 年 4 月起默认附加到 EUS 存储库。
|             | 74-gen2  | RAW    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 7.5      | RAW    | Linux 代理 | 从 2019 年 6 月起默认附加到 EUS 存储库。
|             | 75-gen2  | RAW    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 7.6      | RAW    | Linux 代理 | 从 2019 年 5 月起默认附加到 EUS 存储库。
|             | 76-gen2  | RAW    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 7.7      | LVM    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 77-gen2  | LVM    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 7.8      | LVM    | Linux 代理 | 附加到常规存储库（EUS 不适用于 RHEL 7.8）
|             | 78-gen2  | LVM    | Linux 代理 | 附加到常规存储库（EUS 不适用于 RHEL 7.8）
|             | 7.9      | LVM    | Linux 代理 | 附加到常规存储库（EUS 不适用于 RHEL 7.9）
|             | 79-gen2  | LVM    | Linux 代理 | 附加到常规存储库（EUS 不适用于 RHEL 7.9）
|             | 8-LVM    | LVM    | Linux 代理 | RHEL 8.x 系列映像。 附加到常规存储库。
|             | 8-lvm-gen2| LVM    | Linux 代理 | 第 2 代 Hyper-V - RHEL 8.x 系列映像。 附加到常规存储库。
|             | 8        | LVM    | Linux 代理 | RHEL 8.0 映像。
|             | 8-gen2   | LVM    | Linux 代理 | 第 2 代 Hyper-V - RHEL 8.0 映像。
|             | 8.1      | LVM    | Linux 代理 | 默认附加到 EUS 存储库。
|             | 81gen2   | LVM    | Linux 代理 | 第 2 代 Hyper-V - 从 2020 年 11 月起附加到 EUS 存储库。
|             | 8.1-ci   | LVM    | Linux 代理 | 从 2020 年 11 月起附加到 EUS 存储库。
|             | 81-ci-gen2| LVM    | Linux 代理 | 第 2 代 Hyper-V - 从 2020 年 11 月起附加到 EUS 存储库。
|             | 8.2      | LVM    | Linux 代理 | 从 2020 年 11 月起附加到 EUS 存储库。
|             | 82gen2   | LVM    | Linux 代理 | 第 2 代 Hyper-V - 从 2020 年 11 月起附加到 EUS 存储库。
|             | 8.3   | LVM    | Linux 代理 |  附加到常规存储库（EUS 不适用于 RHEL 8.3）
|             | 83-gen2   | LVM    | Linux 代理 |第 2 代 Hyper-V - 附加到常规存储库（EUS 不适用于 RHEL 8.3）
RHEL-SAP      | 7.4      | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.4。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 74sap-gen2| LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.4。 第 2 代映像。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 7.5       | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.5。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 75sap-gen2| LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.5。 第 2 代映像。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 7.6       | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.6。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 76sap-gen2| LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.6。 第 2 代映像。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
|             | 7.7       | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.7。 附加到 E4S 存储库，将对 SAP 和 RHEL 收取额外费用，并收取基础计算费用。
RHEL-SAP-HANA（将于 2020 年 11 月删除） | 6.7       | RAW    | Linux 代理 | 用于 SAP HANA 的 RHEL 6.7。 已过时，取而代之的是 RHEL-SAP 映像。 此映像将于 2020 年 11 月删除。 有关 Red Hat 的 SAP 云产品/服务的更多详细信息，请访问[经过认证的云提供商提供的 SAP 产品/服务](https://access.redhat.com/articles/3751271)。
|             | 7.2       | LVM    | Linux 代理 | 用于 SAP HANA 的 RHEL 7.2。 已过时，取而代之的是 RHEL-SAP 映像。 此映像将于 2020 年 11 月删除。 有关 Red Hat 的 SAP 云产品/服务的更多详细信息，请访问[经过认证的云提供商提供的 SAP 产品/服务](https://access.redhat.com/articles/3751271)。
|             | 7.3       | LVM    | Linux 代理 | 用于 SAP HANA 的 RHEL 7.3。 已过时，取而代之的是 RHEL-SAP 映像。 此映像将于 2020 年 11 月删除。 有关 Red Hat 的 SAP 云产品/服务的更多详细信息，请访问[经过认证的云提供商提供的 SAP 产品/服务](https://access.redhat.com/articles/3751271)。
RHEL-SAP-APPS | 6.8       | RAW    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 6.8。 已过时，取而代之的是 RHEL-SAP 映像。
|             | 7.3       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.3。 已过时，取而代之的是 RHEL-SAP 映像。
|             | 7.4       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.4。
|             | 7.6       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.6。
|             | 7.7       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.7。
|             | 77-gen2       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.7。 第 2 代映像
|             | 8.1       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 8.1。
|             | 81-gen2      | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 8.1。 第 2 代映像。
|             | 8.2       | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 8.2。
|             | 82-gen2      | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 8.2。 第 2 代映像。
RHEL-HA       | 7.4       | LVM    | Linux 代理 | 具有 HA 加载项的 RHEL 7.4。 除了收取基础计算费用之外，还会对 HA 和 RHEL 收取额外费用。 已过时，取而代之的是 RHEL-SAP-HA 映像。
|             | 7.5       | LVM    | Linux 代理 | 具有 HA 加载项的 RHEL 7.5。 除了收取基础计算费用之外，还会对 HA 和 RHEL 收取额外费用。 已过时，取而代之的是 RHEL-SAP-HA 映像。
|             | 7.6       | LVM    | Linux 代理 | 具有 HA 加载项的 RHEL 7.6。 除了收取基础计算费用之外，还会对 HA 和 RHEL 收取额外费用。 已过时，取而代之的是 RHEL-SAP-HA 映像。
RHEL-SAP-HA   | 7.4          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.4，具有 HA 和更新服务。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 74sapha-gen2 | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.4，具有 HA 和更新服务。 第 2 代映像。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 7.5          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.5，具有 HA 和更新服务。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 7.6          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.6，具有 HA 和更新服务。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 76sapha-gen2 | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.6，具有 HA 和更新服务。 第 2 代映像。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 7.7          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.7，具有 HA 和更新服务。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 77sapha-gen2 | LVM    | Linux 代理 | 用于 SAP 的 RHEL 7.7，具有 HA 和更新服务。 第 2 代映像。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 8.1          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 8.1，具有 HA 和更新服务。 附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 81sapha-gen2          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 8.1，具有 HA 和更新服务。 第 2 代映像，附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 8.2          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 8.2，具有 HA 和更新服务。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
|             | 82sapha-gen2          | LVM    | Linux 代理 | 用于 SAP 的 RHEL 8.2，具有 HA 和更新服务。 第 2 代映像，附加到 E4S 存储库。 除了基础计算费用之外，还将针对 SAP 和 HA 存储库以及 RHEL 收费额外费用。
rhel-byos     |rhel-lvm74| LVM    | Linux 代理 | RHEL 7.4 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm75| LVM    | Linux 代理 | RHEL 7.5 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm76| LVM    | Linux 代理 | RHEL 7.6 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm76-gen2| LVM    | Linux 代理 | RHEL 7.6 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm77| LVM    | Linux 代理 | RHEL 7.7 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm77-gen2| LVM    | Linux 代理 | RHEL 7.7 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm78| LVM    | Linux 代理 | RHEL 7.8 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm78-gen2| LVM    | Linux 代理 | RHEL 7.8 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm8 | LVM    | Linux 代理 | RHEL 8.0 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm8-gen2 | LVM    | Linux 代理 | RHEL 8.0 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm81 | LVM    | Linux 代理 | RHEL 8.1 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm81-gen2 | LVM    | Linux 代理 | RHEL 8.1 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm82 | LVM    | Linux 代理 | RHEL 8.2 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。
|             |rhel-lvm82-gen2 | LVM    | Linux 代理 | RHEL 8.2 第 2 代 BYOS 映像，不附加到任何更新源，也不会收取 RHEL 额外费用。

> [!NOTE]
> Red Hat 认为 RHEL-SAP-HANA 产品的生命周期已终止。 现有部署将继续正常运行，但 Red Hat 建议客户从 RHEL-SAP-HANA 映像迁移到 RHEL-SAP-HA 映像，其中包括 SAP HANA 存储库以及 HA 加载项。 有关 Red Hat 的 SAP 云产品/服务的更多详细信息，请访问[经过认证的云提供商提供的 SAP 产品/服务](https://access.redhat.com/articles/3751271)。

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure 中的 Red Hat 映像](./redhat-images.md)。
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)。
* 详细了解 [RHEL BYOS 产品/服务](./byos.md)。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
