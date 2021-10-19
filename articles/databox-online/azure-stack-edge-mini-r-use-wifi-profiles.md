---
title: 将 Wi-Fi 配置文件与 Azure Stack Edge Mini R 设备配合使用
description: 本文介绍如何为高安全性企业网络和个人网络上的 Azure Stack Edge Mini R 设备创建 Wi-Fi 配置文件。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 34fe33f3c1d5be747e96ae71567424026c7c7a2a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669200"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>将 Wi-Fi 配置文件与 Azure Stack Edge Mini R 设备配合使用

本文介绍如何将无线网络 (Wi-Fi) 配置文件与 Azure Stack Edge Mini R 设备配合使用。

准备 Wi-Fi 配置文件的方式取决于无线网络的类型：

- 如果是 Wi-Fi 保护访问 2 (WPA2) - 个人网络（如家庭网络或 Wi-Fi 开放热点），可以使用与其他设备相同的密码，下载并利用现有的无线配置文件。

- 在高安全性企业环境中，可通过 WPA2 - 企业网络访问自己的设备。 在此类网络上，每台客户端计算机都具有不同的 Wi-Fi 配置文件，并且将通过证书进行身份验证。 你需要与网络管理员一起确定所需的配置。

稍后我们将进一步讨论这两种类型网络的配置文件要求。

在这两种情况下，在测试或将配置文件与设备一起使用之前，务必确保配置文件满足所在组织的安全要求。

## <a name="about-wi-fi-profiles"></a>关于 Wi-Fi 配置文件

Wi-Fi 配置文件包含 SSID（服务集标识符或网络名称）、密码密钥和将 Azure Stack Edge Mini R 设备连接到无线网络所需的安全信息。

如下代码示例演示了用于典型无线网络的配置文件的基本设置：

* `SSID` 是网络名称。

* `name` 是方便用户记忆的 Wi-Fi 连接名称。 用户在浏览设备上的可用连接时，将看到该名称。

* 将配置文件配置为当计算机位于网络范围内时自动连接到无线网络（`connectionMode` = `auto`）。

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

有关 Wi-Fi 配置文件设置的详细信息，请参阅[为 Windows 10 和更高版本的设备添加 Wi-Fi 设置](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)中的企业配置文件，另请参阅[配置 Cisco Wi-Fi 配置文件](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile)。

若要在 Azure Stack Edge Mini R 设备上启用无线连接，请在设备上配置 Wi-Fi 端口，然后将 Wi-Fi 配置文件添加到设备中。 在企业网络上，还需将证书上传到设备。 然后，可以从设备的本地 Web UI 连接到无线网络。 有关详细信息，请参阅[管理 Azure Stack Edge Mini R 上的无线连接](./azure-stack-edge-mini-r-manage-wifi.md)。

## <a name="profile-for-wpa2---personal-network"></a>WPA2 - 个人网络配置文件

在 Wi-Fi 保护访问 2 (WPA2) - 个人网络（如家庭网络或 Wi-Fi 开放热点）上，多个设备可能使用相同的配置文件和密码。 在家庭网络上，移动电话和便携式计算机使用相同的无线配置文件和密码连接到网络。

例如，Windows 10 客户端可以生成运行时配置文件。 登录无线网络时，系统会提示你输入 Wi-Fi 密码，一旦提供该密码，便会与系统建立连接。 此环境中无需证书。

在此类网络上，可以从便携式计算机中导出 Wi-Fi 配置文件，然后将其添加到 Azure Stack Edge Mini R 设备中。 有关说明，请参阅下文[导出 Wi-Fi 配置文件](#export-a-wi-fi-profile)。

> [!IMPORTANT]
> 在为 Azure Stack Edge Mini R 设备创建 Wi-Fi 配置文件之前，请与网络管理员联系，以了解所在组织对无线网络的安全要求。 除非已确认无线网络符合要求，否则不应在设备上测试或使用任何 Wi-Fi 配置文件。

## <a name="profiles-for-wpa2---enterprise-network"></a>WPA2 - 企业网络配置文件

在无线保护访问 2 (WPA2) - 企业网络上，需要与网络管理员合作以获取所需的 Wi-Fi 配置文件和证书，从而将 Azure Stack Edge Mini R 设备连接到网络。

对于高度安全的网络，Azure 设备可以使用带有可扩展身份验证协议-传输层安全性 (EAP-TLS) 的受保护的可扩展身份验证协议 (PEAP)。 带有 EAP-TLS 的 PEAP 使用计算机身份验证：客户端和服务器使用证书来相互验证身份。

> [!NOTE]
> * Azure Stack Edge Mini R 设备不支持使用 PEAP Microsoft 质询握手身份验证协议版本 2 (PEAP MSCHAPv2) 进行用户身份验证。
> * 若要访问 Azure Stack Edge Mini R 功能，需要使用 EAP-TLS 身份验证。 使用 Active Directory 设置的无线连接将不起作用。

网络管理员将为每台计算机生成唯一的 Wi-Fi 配置文件和客户端证书。 网络管理员决定对每个设备使用单独的证书还是共享证书。

如果你在工作区中的多个物理位置工作，则网络管理员可能需要为你的无线连接提供针对多个站点的 Wi-Fi 配置文件和证书。

在企业网络上，建议请勿更改网络管理员提供的 Wi-Fi 配置文件中的设置。 你可能仅希望调整自动连接设置。 有关详细信息，请参阅适用于 Windows 10 及更高版本设备的 Wi-Fi 设置中的[基本配置文件](/mem/intune/configuration/wi-fi-settings-windows#basic-profile)。

在高安全性企业环境中，可以使用现有的无线网络配置文件作为模板：

* 可以从工作计算机下载企业无线网络配置文件。 有关说明，请参阅下文[导出 Wi-Fi 配置文件](#export-a-wi-fi-profile)。

* 如果组织中的其他人已通过无线网络连接到其 Azure Stack Edge Mini R 设备，则他们可以从其设备下载 Wi-Fi 配置文件。 有关说明，请参阅[下载 Wi-Fi 配置文件](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile)。

## <a name="export-a-wi-fi-profile"></a>导出 Wi-Fi 配置文件

若要在计算机上导出 Wi-Fi 接口的配置文件，请执行以下步骤：

1. 确保用于导出无线配置文件的计算机可以连接到设备将使用的 Wi-Fi 网络。

1. 若要查看计算机上的无线配置文件，请在“开始”菜单上，打开“命令提示符”(cmd.exe)，然后输入以下命令：

   `netsh wlan show profiles`

   输出如下所示：

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

1. 若要导出配置文件，请输入以下命令：

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>" key=clear`

   例如，以下命令将 XML 格式的 ContosoFTINET 配置文件保存到名为 `gusp` 的用户的“下载”文件夹中。

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads key=clear

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>将证书、Wi-Fi 配置文件添加到设备

拥有所需的 Wi-Fi 配置文件和证书后，请执行以下步骤来配置 Azure Stack Edge Mini R 设备，以进行无线连接：

1. 对于 WPA2 - 企业网络，请按照[上传证书](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)中的指导，将所需证书上传到设备。

1. 将 Wi-Fi 配置文件上传到 Mini R 设备，然后按照[添加、连接到 Wi-Fi 配置文件](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)中的指导进行连接。

## <a name="next-steps"></a>后续步骤

- 了解如何[配置 Azure Stack Edge Mini R 的网络](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)。
- 了解如何[在 Azure Stack Edge Mini R 上管理 Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md)。
