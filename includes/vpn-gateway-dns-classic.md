---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875528"
---
在此配置过程中不需进行 DNS 设置，但如果希望在 VM 之间进行名称解析，则 DNS 是必需的。 指定一个值不会创建新的 DNS 服务器。 指定的 DNS 服务器 IP 地址应该是可以解析所连接的资源名称的 DNS 服务器。

创建虚拟网络后，可以添加 DNS 服务器的 IP 地址来处理名称解析。 打开虚拟网络的设置，选择“DNS 服务器”，并添加要用于名称解析的 DNS 服务器的 IP 地址。

1. 在门户中找到虚拟网络。
2. 在虚拟网络页的“设置”部分，选择“DNS 服务器”。 
3. 添加 DNS 服务器。
4. 若要保存设置，请选择页面顶部的“保存”。