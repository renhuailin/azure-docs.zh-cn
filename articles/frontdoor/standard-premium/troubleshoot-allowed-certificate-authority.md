---
title: '允许用于 Azure 前门标准/高级 (预览版的证书颁发机构) '
description: 本文列出了创建自己的证书时允许的所有证书颁发机构。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098799"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>允许用于 Azure 前门标准/高级 (预览版的证书颁发机构) 

使用自己的证书为 Azure 前门标准/高级自定义域启用 HTTPS 功能时。 需要 (CA) 的允许证书颁发机构来创建 TLS/SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

创建自己的证书时，以下 CA 获准：

- AddTrust 外部 CA 根
- AlphaSSL 根 CA
- AME 基础结构 CA 01
- AME 基础结构 CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Autopilot 根 CA
- Baltimore CyberTrust 根
- 类 3 公共主要证书颁发机构
- COMODO RSA 证书颁发机构
- COMODO RSA 域验证安全服务器 CA
- D-TRUST 根类 3 CA 2 2009
- DigiCert 云服务 CA-1
- DigiCert 全局根 CA
- DigiCert 全局根 G2
- DigiCert 高保障 CA-3
- DigiCert 高保障 EV 根 CA
- DigiCert SHA2 扩展验证服务器 CA
- DigiCert SHA2 高保障服务器 CA
- DigiCert SHA2 安全服务器 CA
- DST 根 CA X3
- D-TRUST 根类 3 CA 2 2009
- 加密无处不在 DV TLS CA
- 委托根证书颁发机构
- 委托根证书颁发机构 - G2
- Entrust.net 证书颁发机构 (2048)
- GeoTrust 全局 CA
- GeoTrust 主要证书颁发机构
- GeoTrust 主要证书颁发机构 - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign 扩展验证n CA - SHA256 - G2
- GlobalSign 组织验证 CA - G2
- GlobalSign 根 CA
- Go Daddy 根证书颁发机构 - G2
- Go Daddy 安全证书颁发机构 - G2
- 让我们来加密机构 X3
- 毫秒 e-Szigno 根 CA 2009
- QuoVadis 根 CA2 G3
- RapidSSL RSA CA 2018
- 安全通信 RootCA1
- 安全通信 RootCA2
- 安全通信 RootCA3
- Symantec 类 3 EV SSL CA - G3
- Symantec 类 3 安全服务器 CA - G4
- 适用于 Microsoft 的 Symantec 企业移动根
- Thawte 主要根 CA
- Thawte 主要根 CA - G2
- Thawte 主要根 CA - G3
- Thawte RSA CA 2018
- Thawte 时间戳 CA
- TrustAsia TLS RSA CA
- VeriSign 类 3 扩展验证 SSL CA
- VeriSign 类 3 扩展验证 SSL SGC CA
- VeriSign 类 3 公共主要证书颁发机构 - G5
- VeriSign 国际服务器 CA - 类 3
- VeriSign 时间戳服务根
- VeriSign 通用根证书颁发机构
