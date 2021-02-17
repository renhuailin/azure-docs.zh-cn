---
title: 将自己的证书用于 Azure Data Box/Azure Data Box Heavy 设备
description: 如何使用自己的证书来访问 Data Box 或 Data Box Heavy 设备上的本地 web UI 和博客存储。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545167"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>在 Data Box 和 Data Box Heavy 设备上使用你自己的证书

在订单处理过程中，会生成自签名证书，用于访问 Data Box 或 Data Box Heavy 设备的本地 web UI 和 Blob 存储。 如果希望通过受信任的通道与设备通信，可以使用自己的证书。

本文介绍了如何安装自己的证书，以及如何在将设备返回到数据中心之前恢复为默认证书。 它还提供证书要求摘要。

## <a name="about-certificates"></a>关于证书

证书在 **公钥** 和实体之间提供链接 (例如域名) ，由受信任的第三方（如 **证书颁发机构** **） ()** 验证。  证书提供了一种方便的方法来分发受信任的公共加密密钥。 通过这种方式，证书可确保信任你的通信，并将加密信息发送到正确的服务器。

最初配置 Data Box 设备时，将自动生成自签名证书。 您也可以选择携带自己的证书。 如果你打算携带自己的证书，则需要遵循一些准则。

在 Data Box 或 Data Box Heavy 设备上，将使用两种类型的终结点证书：

- Blob 存储证书
- 本地 UI 证书

### <a name="certificate-requirements"></a>证书要求

证书必须满足以下要求：

- 终结点证书需要 `.pfx` 具有可导出的私钥。
- 可以为每个终结点使用单个证书，为多个终结点使用多域证书，或使用通配符终结点证书。
- 终结点证书的属性类似于典型 SSL 证书的属性。
- 需要在客户端计算机上使用 DER 格式的相应证书 (`.cer` 文件扩展名) 。
- 上载本地 UI 证书后，需要重新启动浏览器并清除缓存。 请参阅浏览器的特定说明。
- 如果设备名称或 DNS 域名发生更改，则必须更改证书。
- 创建终结点证书时使用下表：

  |类型 |使用者名称 (SN)   |使用者可选名称 (SAN)   |使用者名称示例 |
  |---------|---------|---------|---------|
  |本地 UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob 存储|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |多 SAN 单一证书|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

有关详细信息，请参阅 [证书要求](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md)。

## <a name="add-certificates-to-device"></a>将证书添加到设备

你可以使用自己的证书来访问本地 web UI 和访问 Blob 存储。

> [!IMPORTANT]
> 如果更改了设备名称或 DNS 域，则必须创建新证书。 然后，应将客户端证书和设备证书更新为新的设备名称和 DNS 域。

若要将自己的证书添加到设备，请执行以下步骤：

1. 中转到 "**管理**  >  **证书**"。

   **名称** 显示设备名称。 **Dns 域** 显示 dns 服务器的域名。

   屏幕底部显示当前正在使用的证书。 对于新设备，你将看到在订单处理过程中生成的自签名证书。

   ![Data Box 设备的 "证书" 页](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. 如果需要更改设备名 (设备名称) 或 **dns 域** (dns 服务器的域) 的 **名称**，请在添加证书之前立即执行此操作。 然后，选择“应用”。

   如果设备名称或 DNS 域名发生更改，则必须更改证书。

   ![为 Data Box 应用新的设备名称和 DNS 域](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. 若要添加证书，请选择 " **添加证书** " 以打开 " **添加证书** " 面板。 然后选择 **证书类型** -" **Blob 存储** " 或 " **本地 web UI**"。

   ![Data Box 设备的 "证书" 页上的 "添加证书" 面板](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. 选择) 格式 (的证书文件 `.pfx` ，并输入在导出证书时设置的密码。 然后选择 " **验证 & 添加**"。

   ![将 Blob 终结点证书添加到 Data Box 的设置](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   成功添加证书后，" **证书** " 屏幕会显示新证书的指纹。 证书的状态为 " **有效**"。

   ![已成功添加有效的新证书](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. 若要查看证书详细信息，请选择并单击证书名称。 证书将在一年后过期。

   ![查看 Data Box 设备的证书详细信息](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. 如果更改了本地 Web UI 的证书，则需要重新启动浏览器，然后重新启动本地 web UI。 若要避免任何 SSL 缓存问题，需要执行此步骤。

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. 在用于访问本地 web UI 的客户端计算机上安装新证书。 有关说明，请参阅下面的 [将证书导入到客户端](#import-certificates-to-client)。


## <a name="import-certificates-to-client"></a>将证书导入到客户端

将证书添加到 Data Box 设备后，需要将证书导入到用来访问设备的客户端计算机。 将证书导入到本地计算机的受信任根证书颁发机构存储区。

若要在 Windows 客户端上导入证书，请执行以下步骤：

1. 在 "文件资源管理器" 中，右键单击) 格式 (的证书文件 `.cer` ，然后选择 " **安装证书**"。 该操作会启动证书导入向导。

    ![导入证书 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. 对于 " **存储位置**"，选择 " **本地计算机**"，然后选择 " **下一步**"。

    ![选择 "本地计算机" 作为证书导入向导中的存储位置](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. 选择 **"将所有证书放入下列存储**"，选择 " **受信任的根证书颁发机构**"，然后选择 " **下一步**"

   ![选择 "证书导入向导" 中的 "受信任的根证书颁发机构" 存储](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. 检查设置，然后选择 " **完成**"。 将显示一条消息，告知您导入已成功。

   ![查看证书设置，并完成证书导入向导](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>恢复为默认证书

在将设备返回到 Azure 数据中心之前，应恢复为在订单处理期间生成的原始证书。

若要还原到订单处理期间生成的证书，请执行以下步骤：

1. 转到 "**管理**  >  **证书**"，然后选择 "**还原证书**"。

   恢复证书将使用在订单处理期间生成的自签名证书返回。 将从设备中删除你自己的证书。

   ![管理 Data Box 设备的证书中的 "还原证书" 选项](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. 证书重新确定成功完成后，请单击 " **关闭" 或 "重新启动**"，然后选择 " **重新启动**"。 若要避免任何 SSL 缓存问题，需要执行此步骤。

   ![在 Data Box 设备上恢复证书后关闭或重新启动本地 web UI](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   等待几分钟，然后再次登录到本地 web UI。
