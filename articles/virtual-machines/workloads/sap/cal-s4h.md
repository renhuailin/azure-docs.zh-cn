---
title: 在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA | Microsoft Docs
description: 在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 71dde25230c2a474a42872f7b2830c60439738c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630767"
---
# <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library

借助 [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8)，你可以使用完全预配置的 SAP 系统快速创建演示环境。 单击几下鼠标，即可启动并运行 SAP 系统。 以下链接重点介绍了可在 Azure 上快速部署的几个解决方案。 只需选择“创建实例”链接即可。 

你需要使用 S-User 或 P-User 进行身份验证。 可以通过 [SAP 社区](https://community.sap.com/)免费创建 P-User。  请查看下面概述的更多详细信息。

| 解决方案 | 链接 |
| -------------- | :--------- | 
| SAP S/4HANA 2020 FPS02，已完全激活的设备  2021 年 7 月 27 日 | [创建实例](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|此设备包含 SAP S/4HANA 2020 (FPS02)（其中带有预激活的 SAP Best Practices for SAP S/4HANA 核心功能），以及适用于 Service、Master Data Governance (MDG)、Transportation Mgmt. (TM)、Portfolio Mgmt. (PPM)、Human Capital Management (HCM)、Analytics、Migration Cockpit 等工具的进一步方案。 用户访问通过 SAP Fiori、SAP GUI、SAP HANA Studio、Windows 远程桌面或后端操作系统进行，以实现完全的管理访问。 |  [详细信息]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) |
| SAP S/4HANA 2020 FPS01，完全激活的设备  2021 年 4 月 20 日 | [创建实例](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|此设备包含 SAP S/4HANA 2020 (FPS01)（其中带有预激活的 SAP Best Practices for SAP S/4HANA 核心功能），以及适用于 Service、Master Data Governance (MDG)、Transportation Mgmt. (TM)、Portfolio Mgmt. (PPM)、Human Capital Management (HCM)、Analytics、Migration Cockpit 等工具的进一步方案。 用户访问通过 SAP Fiori、SAP GUI、SAP HANA Studio、Windows 远程桌面或后端操作系统进行，以实现完全的管理访问。 |  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| SAP S/4HANA 2020 FPS02  2021 年 6 月 10 日 | [创建实例](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| 此解决方案作为标准 S/4HANA 系统安装提供，包括一个用于轻松地进行前端访问的远程桌面。 它在客户端 100 中包含了已预配置且激活的 SAP S/4HANA Fiori UI，并且根据 SAP 说明 3045635“在 SAP S/4HANA 2020 FPS02 中为 SAP Fiori 进行快速激活”激活了必备组件。 请参阅“详细信息”链接。 | [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| SAP BW/4HANA 2.0 SP07，包括 BW/4HANA Content 2.0 SP06  2020 年 2 月 24 日 | [创建实例](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|此解决方案提供对 SAP BW/4HANA 的见解。 SAP BW/4HANA 是针对 HANA 进行了优化的下一代数据仓库。 除了基本的 BW/4HANA 选项外，该解决方案还提供了一系列 HANA 优化 W/4HANA 内容，以及 SAP 数据仓库云混合方案的下一步骤。 因为系统已预先配置，所以你可以直接开始实现你的方案。| [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| SAP Business One 10.0 PL02，适用于 SAP HANA 的版本 2020 年 8 月 4 日  | [创建实例](https://cal.sap.com/registration?sguid=371edc8c-56c6-4d21-acb4-2d734722c712&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Business One 得到了 170 多个国家/地区中超过 70,000 家中小型企业的信任，是一种灵活、经济并且可缩放的 ERP 解决方案，具备 SAP HANA 的功能。 该解决方案预配置为使用 31 天试用版授权，并且预先安装了你选择的某个演示数据库。 请参阅入门指南，以了解该解决方案的范围以及如何轻松添加新的演示数据库。 |  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/371edc8c-56c6-4d21-acb4-2d734722c712) |
| SAP Data Custodian v2106 的信息检测程序 2021 年 8 月 30 日  | [创建实例](https://cal.sap.com/registration?sguid=db44680c-8a2a-405d-8963-838db38fa7dd&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Data Custodian 的信息检测程序可用于自动对云资源进行数据标记。 信息检测程序会在基础结构资源中进行搜索，并确定这些资源是否包含某些类型的信息。 |  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/db44680c-8a2a-405d-8963-838db38fa7dd) |
| SAP Yard Logistics 2009 for SAP S/4HANA  2021 年 7 月 28 日 | [创建实例](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|使用 SAP Yard Logistics 应用程序运行更高效且能实现更高盈利的供应链物流。 使用一系列可视化和报告工具，让你最大程度地了解所有场流程并预览计划内工作负荷，因此你可以优化资源使用情况，并通过单个系统支持规划、执行和计费。|  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| SAP S/4HANA 2020 FPS02，完全激活的设备  2021 年 7 月 27 日 | [创建实例](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|此解决方案作为标准 S/4HANA 系统安装提供，包括一个用于轻松地进行前端访问的远程桌面。 它在客户端 100 中包含了已预配置且激活的 SAP S/4HANA Fiori UI，并且根据 SAP 说明 3045635“在 SAP S/4HANA 2020 FPS02 中为 SAP Fiori 进行快速激活”激活了必备组件。 请参阅“详细信息”链接。| [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| SAP Focused Run 3.0 FP01，未配置  2021 年 7 月 21 日 | [创建实例](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run 专为需要进行大量系统和应用程序监视、预警和分析的企业而设计。 这是一个功能强大的解决方案，适用于希望将所有客户托管在一个可缩放、安全且自动化的中央环境中的服务提供商。 它还满足了客户在系统管理、用户监视、集成监视以及配置和安全分析方面的高级需求。|  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| SAP S/4HANA 2020 FPS01 Utilities 试用版  2021 年 7 月 21 日 | [创建实例](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|通过此解决方案，你可以创建自己的 SAP S/4HANA 2020 Utilities 系统，并获得亲自实践的体验，包括全区域完全管理访问。 精选的引导式教程将帮助你了解计量数据的优化处理、通过基于角色的 FIORI 用户界面进行的简化计费过程，以及“客户参与”中特定于行业的客户服务行为。|  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| SAP Product Lifecycle Costing 4.0 SP3 Hotfix 2  2021 年 8 月 1 日 | [创建实例](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing 解决方案用于在产品生命周期的早期阶段为新产品或产品相关报价计算成本和其他维度，快速确定成本动因，并轻松模拟和比较备选方案。|  [详细信息](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)| 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>设置并开始使用 SAP Cloud Appliance Library

> [!NOTE]
> 有关 SAP CAL 的详细信息，请转到 [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) 网站。 SAP 还提供了有关 [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) 的博客。

> [!NOTE]
> 自 2017 年 5 月 29 日起，除了优先级较低的经典部署模型外，还可使用 Azure 资源管理器部署模型来部署 SAP CAL。 建议使用新的 Resource Manager 部署模型，并忽略经典部署模型。

## <a name="create-an-account-in-the-sap-cal"></a>在 SAP CAL 中创建帐户
1. 首次登录 SAP CAL，请使用 SAP S-User 或 SAP 中注册的其他用户。 然后定义 SAP CAL 用于在 Azure 上部署设备的 SAP CAL 帐户。 在帐户定义中，需要：

    a. 选择 Azure 上的部署模型（Resource Manager 或经典）。

    b. 输入 Azure 订阅。 一个 SAP CAL 帐户仅可分配到一个订阅。 如果需要多个订阅，则需要另外创建 SAP CAL 帐户。

    c. 授予 SAP CAL 权限，以便部署到 Azure 订阅中。

    > [!NOTE]
    > 后续步骤演示如何创建 Resource Manager 部署的 SAP CAL 帐户。 如果已有链接到经典部署模型的 SAP CAL 帐户，则需要按照这些步骤创建新的 SAP CAL 帐户。 需要在 Resource Manager 模型中部署新的 SAP CAL 帐户。

2. 创建一个新的 SAP CAL 帐户。 “帐户”页显示 Azure 的三个选项： 

    a. Microsoft Azure（经典）是经典部署模型，已不再是首选项。

    b. Microsoft Azure 是新的 Resource Manager 部署模型。

    c. 21Vianet 运营的 Windows Azure 是在中国使用经典部署模型的一个选项。

    若要部署在 Resource Manager 模型中，请选择“Microsoft Azure”。

    ![SAP CAL 帐户详细信息](./media/cal-s4h/s4h-pic-2a.png)

3. 输入可在 Azure 门户中找到的 Azure 订阅 ID。

   ![SAP CAL 帐户](./media/cal-s4h/s4h-pic3c.png)

4. 若要授权将 SAP CAL 部署到定义的 Azure 订阅，请单击“授权”。 浏览器选项卡中将显示以下页面：

   ![Internet Explorer 云服务登录](./media/cal-s4h/s4h-pic4c.png)

5. 如果列出多个用户，则选择链接到所选择的 Azure 订阅共同管理者的 Microsoft 帐户。 浏览器选项卡中将显示以下页面：

   ![Internet Explorer 云服务确认](./media/cal-s4h/s4h-pic5a.png)

6. 单击“接受”。 如果授权成功，则将再次显示 SAP CAL 帐户定义。 稍后，用户将收到一条消息，确认授权过程成功。

7. 若要将新创建的 SAP CAL 帐户分配到你的用户，请在右侧的文本框中输入你的用户 ID 并单击“添加”。

   ![帐户到用户的关联](./media/cal-s4h/s4h-pic8a.png)

8. 若要将你的帐户与你用于登录到 SAP CAL 的用户相关联，请单击“评审”。 
 
9. 若要创建用户和新建的 SAP CAL 帐户之间的关联，请单击“创建”。

   ![用户到 SAP CAL 帐户的关联](./media/cal-s4h/s4h-pic9b.png)

已成功创建 SAP CAL 帐户，该帐户能够：

- 使用 Resource Manager 部署模型。
- 将 SAP 系统部署到 Azure 订阅。

现在可以开始将 S/4HANA 部署到 Azure 中的用户订阅。

> [!NOTE]
> 在继续之前，请确定你是否具有所需的 Azure 核心配额。 SAP CAL 中的一些解决方案使用 Azure M 系列 VM 来部署某些基于 SAP HANA 的解决方案。 你的 Azure 订阅可能没有任何 M 系列核心配额。 如果是这样，你可能需要联系 Azure 支持来获取所需的配额。

> [!NOTE]
> 在 SAP CAL 部署 Azure 上的解决方案时，可能只能选择一个 Azure 区域。 若要部署到除 SAP CAL 建议的区域之外的其他 Azure 区域，则需要从 SAP 购买 CAL 订阅。 你可能还需要通过 SAP 打开一条消息，使你的 CAL 帐户能够发送至最初建议的区域之外的其他 Azure 区域。

## <a name="deploy-a-solution"></a>部署解决方案

从 SAP CAL 的“解决方案”页部署解决方案。 SAP CAL 有两个序列要部署：

- 用一页定义要部署的系统的基本序列
- 在 VM 大小上可供选择的高级序列 

我们在此演示部署的基本路径。

1. 在“帐户详细信息”页上，需要执行以下操作：

    a. 选择 SAP CAL 帐户。 （使用通过 Resource Manager 部署模型部署时关联的帐户。）

    b. 输入实例“名称”。

    c. 选择 Azure“区域”。 SAP CAL 建议一个区域。 如果需要另一个 Azure 区域并且没有 SAP CAL 订阅，则需要通过 SAP 订购 CAL 订阅。

    d. 输入一个用于解决方案的八个或九个字符的主密码。 密码用于不同组件的管理员。

   ![SAP CAL 基本模式：创建实例](./media/cal-s4h/s4h-pic10a.png)

2. 单击“创建”，然后在出现的消息框中单击“确定”。

   ![SAP CAL 支持的 VM 大小](./media/cal-s4h/s4h-pic10b.png)

3. 在“私钥”对话框中，单击“存储”将私钥存储在 SAP CAL 中。 若要使用私钥密码保护，请单击“下载”。 

   ![SAP CAL 私钥](./media/cal-s4h/s4h-pic10c.png)

4. 阅读 SAP CAL“警告”消息，并单击“确定”。

   ![SAP CAL 警告](./media/cal-s4h/s4h-pic10d.png)

    现在正在进行部署。 一段时间后，根据解决方案的大小和复杂性（SAP CAL 提供的估算值），状态将显示为活动并可供使用。

5. 若要查找一个资源组中使用其他相关资源收集的虚拟机，请转到 Azure 门户： 

   ![新门户中部署的 SAP CAL 对象](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. 在 SAP CAL 门户上，状态将显示为“活动”。 若要连接到解决方案，请单击“连接”。 在此解决方案内部署需连接到不同组件的不同选项。

   ![SAP CAL 实例](./media/cal-s4h/active_solution.png)

7. 在能够使用其中一个方法连接到已部署的系统之前，请单击“入门指南”。 

   ![连接到实例](./media/cal-s4h/connect_to_solution.png)

    此文档将为用户的每个连接方法命名。 用户密码设置为部署过程开始时定义的主密码。 文档中列出了可用于登录到已部署系统的其他更多功能用户和其密码。 

    例如，如果使用 Windows 远程桌面计算机上已预装的 SAP GUI，S/4 系统可能如下所示：

   ![预安装的 SAP GUI 中的 SM50](./media/cal-s4h/gui_sm50.png)

    或者，如果使用 DBACockpit，实例可能如下所示：
 

   ![在 DBACockpit SAP GUI 中的 SM50](./media/cal-s4h/dbacockpit.png)

几个小时内即可在 Azure 中部署一个完善的 SAP S/4 设备。

如果购买了 SAP CAL 订阅，则 SAP 完全支持通过 Azure 上的 SAP CAL 进行部署。 支持队列是 BC-VCM-CAL。







