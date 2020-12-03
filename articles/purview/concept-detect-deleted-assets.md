---
title: 扫描如何检测已删除的资产
description: 本文介绍 Azure 监控范围帐户如何在扫描过程中检测已删除的资产。
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552355"
---
# <a name="how-scans-detect-deleted-assets"></a>扫描如何检测已删除的资产

本文介绍 Azure 监控范围如何使用扫描结果来检测已删除的资产。

## <a name="background-info"></a>背景信息

Azure 监控范围目录仅在其扫描时知道数据存储的状态。 为了了解文件、表或容器是否已删除，它会将上次扫描输出与当前扫描输出进行比较。 例如，假设上次扫描 Azure Data Lake Storage Gen2 帐户时，它包含一个名为 *folder1* 的文件夹。 再次扫描同一帐户时， *folder1* 缺失。 因此，目录假定已删除该文件夹。

## <a name="detecting-deleted-files"></a>检测已删除的文件

用于检测缺少的文件的逻辑适用于同一个用户以及不同用户的多个扫描。 例如，假设用户在文件夹 A、B 和 C 上的 Data Lake Storage Gen2 数据存储上运行一次扫描。稍后，同一帐户中的不同用户在同一数据存储区的 C、D 和 E 文件夹上运行一次不同的一次性扫描。 由于文件夹 C 被扫描了两次，因此目录会检查它是否可能删除。 然而，文件夹 A、B、D 和 E 只扫描一次，并且目录不会将其检查已删除的资产。

若要将已删除的文件保存在目录中，请务必运行常规扫描。 扫描间隔很重要，因为在运行另一个扫描之前，目录无法检测已删除的资产。 因此，如果你每月在特定商店运行扫描一次，则在你稍后运行下一个扫描之前，该目录无法检测该存储中的任何已删除的数据资产。

枚举大数据存储（如 Data Lake Storage Gen2）时，有多种方法 (包括枚举错误和丢弃的事件) 丢失信息。 特定扫描可能会丢失创建或删除的文件。 因此，除非已删除目录，否则不会将其从目录中删除。 此策略意味着在扫描的数据存储中不存在的文件仍然存在于目录中时，可能会出现错误。 在某些情况下，可能需要对数据存储进行两次或三次扫描，才能捕获某些已删除的资产。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 监控范围目录，请参阅 [快速入门：创建监控范围帐户](create-catalog-portal.md)。
