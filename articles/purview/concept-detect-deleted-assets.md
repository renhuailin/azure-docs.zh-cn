---
title: 扫描如何检测已删除的资产
description: 本文介绍 Azure Purview 帐户如何在扫描过程中检测已删除的资产。
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552355"
---
# <a name="how-scans-detect-deleted-assets"></a>扫描如何检测已删除的资产

本文介绍 Azure Purview 如何使用扫描结果来检测已删除的资产。

## <a name="background-info"></a>背景信息

Azure Purview 目录只有在扫描数据存储后才会知道数据存储的状态。 为了解文件、表或容器是否已删除，它会将上次扫描输出与当前扫描输出进行比较。 例如，假设上次扫描 Azure Data Lake Storage Gen2 帐户时，包含一个名为 folder1 的文件夹。 再次扫描同一帐户时，folder1 缺失。 因此，目录假定该文件夹已被删除。

## <a name="detecting-deleted-files"></a>检测已删除的文件

用于检测缺失文件的逻辑适用于同一个用户以及不同用户的多次日志扫描。 例如，假设某个用户对 Data Lake Storage Gen2 数据存储上的文件夹 A、B 和 C 运行一次扫描。稍后，同一帐户中的不同用户对同一数据存储的 C、D 和 E 文件夹运行一次不同的一次性扫描。 由于文件夹 C 被扫描了两次，因此目录会检查它是否可能被删除。 然而，文件夹 A、B、D 和 E 只扫描一次，并且目录不会将其检查已删除的资产。

为了将已删除的文件从目录中移除，请务必定期运行扫描。 扫描间隔很重要，因为在运行另一次扫描之前，目录无法检测到已删除的资产。 因此，如果你每月对特定存储运行一次扫描，则在运行下一次扫描之前，目录无法检测该存储中的任何已删除的数据资产。

枚举大数据存储（如 Data Lake Storage Gen2）时，有多种方法（包括枚举错误和丢弃的事件）丢失信息。 特定扫描可能会错过已创建或已删除的文件。 因此，除非目录确定某个文件已被删除，否则不会从目录中删除这个文件。 这一策略的意义是，如果某个文件在扫描的数据存储中不存在，但仍存在于目录中，则可能会出现错误。 在某些情况下，可能需要对数据存储进行两次或三次扫描才能捕获某些已删除的资产。

## <a name="next-steps"></a>后续步骤

要开始使用 Azure Purview 目录，请参阅[快速入门：创建 Purview 帐户](create-catalog-portal.md)。
