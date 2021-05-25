---
title: StorSimple 8000 系列 Update 5.1 发行说明
description: 介绍 StorSimple 8000 系列 Update 5.1 的新功能、问题和解决方法。
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657563"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 系列 Update 5.1 发行说明

## <a name="overview"></a>概述

以下发行说明描述 StorSimple 8000 系列 Update 5.1 的新功能，并标识其重要的待解决问题。 其中也包含此版本中随附的 StorSimple 软件更新列表。

Update 5.1 可应用于任何运行 Update 5 的 StorSimple 设备。 如果你使用的版本低于 5，请首先应用 Update 5，然后应用 5.1。 与 Update 5.1 相关联的设备版本是 6.3.9600.17885。

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。

> [!IMPORTANT]
>
> * Update 5.1 是必需的更新，必须立即安装。 有关详细信息，请参阅[如何应用 Update 5.1](storsimple-8000-install-update-51.md)。
> * Update 5.1 只包含安全更新。 安装此更新大约需要 30 分钟。 强烈建议你应用 Update 5.1 以确保设备运行正常。
> * 对于新版本，由于我们分阶段推出更新，可能不能立即看到更新。 请等待几天，再次扫描更新，因为很快就会提供这些更新。

## <a name="whats-new-in-update-51"></a>Update 5.1 中的新增功能

Update 5.1 中进行了以下重大改进和 bug 修复：

* TLS 1.2 - 此 StorSimple 更新将在所有客户端上强制执行 TLS 1.2。 这是所有 StorSimple 8000 系列设备必需的更新。

   如果看到以下警告，则必须在设备上更新软件，然后才能继续：

   一个或多个 StorSimple 设备运行的是较低版本的软件。 TLS 1.2 的最新可用更新是必需更新，应在这些设备上立即安装。 TLS 1.2 用于所有 Azure 门户通信，如果没有此更新，设备将无法与 StorSimple 服务通信。

## <a name="known-issues-in-update-51-from-previous-releases"></a>Update 5.1 中的已知问题（来自以前的版本）

Update 5.1 中没有任何新的已知问题。 有关从之前版本遗留至 Update 5.1 的问题列表，请转到 [Update 3 发行说明](storsimple-update3-release-notes.md#known-issues-in-update-3)。

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Update 5.1 中的 StorSimple 云设备更新

此更新不能应用于 StorSimple 云工具（也称为虚拟设备）。 需要使用 Update 5.1 映像创建新的云设备。 有关如何创建 StorSimple 云设备的信息，请转到[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。

## <a name="next-step"></a>后续步骤

了解如何在 StorSimple 设备上[安装 Update 5.1](storsimple-8000-install-update-51.md)。
