---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041578"
---
获取根证书的 .cer 文件。 你可以使用通过企业解决方案生成的根证书（推荐），或者生成自签名证书。 创建根证书后，将公共证书数据（不是私钥）作为 Base64 编码的 X.509 .cer 文件导出。 稍后，请将此文件上传到 Azure。

* **企业证书：** 如果使用的是企业级解决方案，可以使用现有的证书链。 获取要使用的根证书的 .cer 文件。
* **自签名根证书：** 如果使用的不是企业证书解决方案，请创建自签名根证书。 否则，创建的证书将不兼容 P2S 连接，客户端在尝试连接时会收到连接错误。 可以使用 Azure PowerShell、MakeCert 或 OpenSSL。 以下文章中的步骤介绍了如何生成兼容的自签名根证书：

  * [Windows 10 PowerShell 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)：这些指令需要 Windows 10 和 PowerShell 才能生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [MakeCert 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：使用 MakeCert 的前提是，无法接触用于生成证书的 Windows 10 计算机。 虽然 MakeCert 已弃用，但仍可使用它来生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [Linux 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)。