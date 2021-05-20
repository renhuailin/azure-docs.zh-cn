---
title: 在 Azure IoT Central 中滚动更新 X.509 证书
description: 如何使用 IoT Central 应用程序滚动更新 X.509 证书
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92000062"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>如何在 IoT Central 应用程序中滚动更新 X.509 设备证书

在 IoT 解决方案的生命周期内，需要滚动更新证书。 滚动更新证书的两个主要原因是出现安全违规和证书过期。

如果出现安全违规，滚动更新证书是一种安全最佳做法，可帮助保护系统。 作为[假设违规方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)的一部分，Microsoft 提倡实施反应式安全流程及预防措施。 这些安全流程应该包括设备证书的滚动更新。 滚动更新证书的频率取决于解决方案的安全需求。 其解决方案涉及到高度敏感数据的客户可以每日滚动更新证书，而其他客户则可以每隔数年滚动更新其证书。


## <a name="obtain-new-x509-certificates"></a>获取新的 X.509 证书

可以使用 OpenSSL 之类的工具创建自己的 X.509 证书。 此方法非常适合用于测试 X.509 证书，但在安全性方面不能提供很大的保障。 除非你已准备好充当自己的 CA 提供者，否则请只在测试时使用此方法。

## <a name="enrollment-groups-and-security-breaches"></a>注册组和安全违规

若要为了响应安全违规而更新组注册，应使用以下方法来立即更新当前证书：

1. 在左窗格中导航到“管理”，然后选择“设备连接”。

2. 选择“注册组”，然后在列表中选择组名称。

3. 对于证书更新，选择“管理主要证书”或“管理次要证书”。

4. 在注册组中添加并验证根 X.509 证书。

   针对主要证书和次要证书完成这些步骤（如果这两个证书同时泄露）。

## <a name="enrollment-groups-and-certificate-expiration"></a>注册组和证书过期

如果要滚动证书来处理证书过期问题，请使用以下方法立即更新当前证书：

1. 在左窗格中导航到“管理”，然后选择“设备连接”。

2. 选择“注册组”，然后在列表中选择组名称。

3. 对于证书更新，选择“管理主要证书”。

4. 在注册组中添加并验证根 X.509 证书。

5. 以后当次要证书过期时，请返回此处，并更新该次要证书。

## <a name="individual-enrollments-and-security-breaches"></a>单独注册和安全违规

如果是为了响应安全违规而滚动更新证书，请使用以下方法立即更新当前证书：

1. 选择“设备”，然后选择设备。

2. 选择“连接”，然后将连接方法选为“个人注册”

3. 选择“证书(X.509)”作为机制。

    ![管理单独注册](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 对于证书更新，选择文件夹图标以选择注册条目的待上传新证书。 选择“保存”。

    针对主要证书和次要证书完成这些步骤（如果这两个证书同时泄露）

## <a name="individual-enrollments-and-certificate-expiration"></a>单独注册和证书过期

如果滚动更新证书的目的是处理证书过期问题，则应按如下所述使用辅助证书配置，以减少设备尝试预配时造成的停机时间。

当次要证书即将过期，因此需要滚动更新时，可以换用主要证书配置。 以这种方式在主要证书与辅助证书之间轮换可以减少设备尝试预配时造成的停机时间。

1. 选择“设备”，然后选择设备。

2. 选择“连接”，然后将连接方法选为“个人注册”

3. 选择“证书(X.509)”作为机制。

    ![管理单独注册](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 对于次要证书更新，选择文件夹图标以选择注册条目的待上传新证书。 选择“保存”。

5. 以后当主要证书过期时，请返回此处，并更新该主要证书。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中滚动更新 X.509 证书，即可[连接到 Azure IoT Central](concepts-get-connected.md)。


