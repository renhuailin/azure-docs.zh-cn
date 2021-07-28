---
title: StorSimple 8000 系列 Update 5.1 发行说明
description: 介绍 StorSimple 8000 系列 Update 5.1 的新功能、问题和解决方法。
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: alkohli
ms.openlocfilehash: 735d7c1a25acb767f9d6352f6c8887af4b9464c0
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107895912"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 系列 Update 5.1 发行说明

## <a name="overview"></a>概述

以下发行说明描述 StorSimple 8000 系列 Update 5.1 的新功能，并标识其重要的待解决问题。 其中也包含此版本中随附的 StorSimple 软件更新列表。

Update 5.1 可应用于任何运行 Update 5 的 StorSimple 设备。 如果使用的版本低于 5，请首先应用 Update 5，然后应用 Update 5.1。 与 Update 5.1 相关联的设备版本是 6.3.9600.17885。

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。

> [!IMPORTANT]
>
> * Update 5.1 是必需更新，必须立即安装才能确保设备运行。 Update 5.0 是受支持的最低版本。
> * 安装 Update 5.1 安全更新大约耗时 30 分钟。 有关详细信息，请参阅[如何应用 Update 5.1](storsimple-8000-install-update-51.md)。

## <a name="whats-new-in-update-51"></a>Update 5.1 中的新增功能

Update 5.1 中进行了以下重大改进和 bug 修复：

* TLS 1.2 - 此 StorSimple 更新将在所有客户端上强制执行 TLS 1.2。 TLS 1.2 是所有 StorSimple 8000 系列设备必需的更新。

   如果看到以下警告，则必须更新设备上的软件，然后才能继续：

   一个或多个 StorSimple 设备运行的是较低版本的软件。 TLS 1.2 的最新可用更新是必需更新，应立即安装到这些设备上。 TLS 1.2 用于所有 Azure 门户通信，如果没有此更新，设备将无法与 StorSimple 服务通信。

## <a name="known-issues-in-update-51-from-previous-releases"></a>Update 5.1 中的已知问题（来自以前的版本）

Update 5.1 中没有任何新的已知问题。 有关从之前版本遗留至 Update 5.1 的问题列表，请转到 [Update 3 发行说明](storsimple-update3-release-notes.md#known-issues-in-update-3)。

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Update 5.1 中的 StorSimple 云设备更新

此更新不能应用于 StorSimple 云工具（也称为虚拟设备）。 需要使用 Update 5.1 映像创建全新云设备。 有关如何创建 StorSimple 云设备的信息，请转到[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。

## <a name="next-step"></a>后续步骤

了解如何在 StorSimple 设备上[安装 Update 5.1](storsimple-8000-install-update-51.md)。
