---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93144983"
---
点到站点本机 Azure 证书身份验证连接使用在此练习中配置的以下项目：

* RouteBased VPN 网关。
* 适用于根证书的公钥（.cer 文件），已上传到 Azure。 上传证书以后，该证书将被视为受信任的证书，用于身份验证。
* 从根证书生成的客户端证书。 安装在要连接到 VNet 的每个客户端计算机上的客户端证书。 此证书用于客户端身份验证。
* VPN 客户端配置。 使用 VPN 客户端配置文件配置 VPN 客户端。 这些文件包含客户端连接到 VNet 时所需的信息。 这些文件对操作系统自带的现有 VPN 客户端进行配置。 必须使用配置文件中的设置对进行连接的每个客户端进行配置。
