---
title: Azure Percept DK 设置体验疑难解答
description: 获取在安装体验过程中发现的一些较常见问题的故障排除技巧
author: SillyKeith
ms.author: amiyouss
ms.service: azure-percept
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 40b7289b3c6fd841a0af9b9a61f66906e0dfa028
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226839"
---
# <a name="troubleshoot-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 设置体验疑难解答

请参阅下表，了解 [Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)过程中发现的常见问题的暂时解决方法。 如果问题仍然存在，请联系 Azure 客户支持。

|问题|Reason|解决方法|
|:-----|:------|:----------|
|当连接到 Azure 帐户注册页或 Azure 门户时，可能会使用缓存的帐户自动登录。 如果你没有使用正确帐户登录，则可能会导致与文档不一致的体验。|浏览器设置为“记住”你以前使用过的帐户的结果。|在 Azure 页上，选择右上角的帐户名称，然后选择“注销”。接下来，你就能用正确的帐户登录。|
|Azure Percept DK Wi-Fi 接入点（apd-xxxx）未显示在可用 Wi-Fi 网络列表中。|这通常是一个在 15 分钟内就能解决的暂时性问题。|等待网络显示。 如果超过 15 分钟仍未显示，请重启设备。|
|与 Azure Percept DK Wi-Fi 接入点的连接频繁断开。|通常是因为设备与主计算机之间的连接质量差。 也可能是因为主机上其他 Wi-Fi 连接的干扰。|请确保将天线正确附加到开发工具包。 如果开发工具包离主机很远，请尝试把它移近一些。 如果主计算机中正在运行任何其他 Internet 连接（例如 LTE/5G），请关闭这些连接。|
|主机显示与 Azure Percept DK 接入点连接有关的安全警告。|这是已知问题，将在之后的更新中修复它。|继续执行安装过程是安全的。|
|Azure Percept DK Wi-Fi 接入点（scz-xxxx 或 apd-xxxx）显示在网络列表中，但未能连接。|这可能是因为开发工具包的 Wi-Fi 接入点发生临时损坏。|重启开发工具包，然后重试。|
|安装过程中无法连接到 Wi-Fi 网络。|Wi-Fi 网络当前必须具有 Internet 连接，以便与 Azure 通信。 目前不支持 EAP[PEAP/MSCHAP]、强制网络门户和企业 EAP-TLS 连接。|确保 Wi-Fi 网络类型受支持并且具有 Internet 连接。|
|设备代码错误 <br><br> 如果在设备代码页上收到以下错误： <br><br>在安装体验 UI 中 - 无法获取设备代码。 确保设备已连接到 Internet； <br><br> 在浏览器的 Web 开发人员模式下 - 未能加载资源：服务器的响应状态为 503（服务不可用） <br><br>或 <br><br>证书尚无效。 | Wi-Fi 网络出现问题，导致设备无法完成 DNS 查询或联系 NTP 时间服务器。 | 请尝试将以太网电缆插入开发工具包或连接到其他 Wi-Fi 网络，然后重试。 <br><br> 不太常见的原因可能是主计算机的日期/时间不正确。  |
|使用设备代码时遇到的问题<br><br> 使用设备代码并登录到 Azure 后，会看到有关策略权限或合规性问题的 Azure 错误消息。 你将无法继续执行安装过程。<br><br> 下面是你可能会看到的一些错误：<br><br>BlockedByConditionalAccessOnSecurityPolicy：租户管理员已配置阻止该请求的安全策略。 检查租户级别上定义的安全策略，以确定请求是否符合策略要求。 <br><br>DevicePolicyError：用户尝试从条件访问策略目前不支持的平台登录到设备。<br><br>DeviceNotCompliant：条件访问策略需要合规设备，该设备不合规。 用户必须使用已批准的 MDM 提供程序（例如 Intune）注册其设备<br><br>BlockedByConditionalAccess：条件访问策略已阻止访问。 访问策略不允许令牌颁发。<br><br>目前无法访问 - 登录成功，但不符合访问此资源的条件   |作为一种安全预防措施，某些 Azure 租户可能会阻止使用“设备代码”来操作 Azure 资源。 这通常是组织的条件访问 IT 策略的结果。 因此，Azure Percept 安装体验无法为你创建任何 Azure 资源。 <br><br>条件访问策略要求你连接到公司网络或 VPN 才能继续。    |与你的组织合作，了解他们的条件访问 IT 策略。  |