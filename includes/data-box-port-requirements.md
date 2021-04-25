---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "89505901"
---
| 端口号。| 入或出 | 端口范围| 必须| 注释 |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|在|LAN|是|此端口用于通过 HTTP 连接到 Data Box Blob 存储 REST API。 如果未连接到 REST API，则此端口会通过 8443 自动重定向到本地 Web UI。 |
| TCP 443 (HTTPS)|在|LAN|是|此端口用于通过 HTTPS 连接到 Data Box Blob 存储 REST API。 如果未连接到 REST API，则此端口会通过 8443 自动重定向到本地 Web UI。 |
| TCP 8443 (HTTPS-Alt)|在|LAN|是|这是 HTTPS 的替代端口，在连接到本地 Web UI 进行设备管理时使用。 |
| TCP 445 (SMB)|出/入|LAN|某些情况下<br>请参阅说明|仅当通过 SMB 连接时，才需要此端口。 |
| TCP 2049 (NFS)|出/入|LAN|某些情况下<br>请参阅说明|仅当通过 NFS 连接时，才需要此端口。 |
| TCP 111 (NFS)|出/入|LAN|某些情况下<br>请参阅说明|此端口用于 rpcbind/端口映射，仅当通过 NFS 连接时才需要。  |
