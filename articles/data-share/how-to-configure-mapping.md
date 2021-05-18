---
title: 在 Azure Data Share 中配置数据集映射
description: 了解如何使用 Azure Data Share 为接收的共享配置数据集映射。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257836"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何在 Azure Data Share 中为接收的共享配置数据集映射

本文介绍如何使用 Azure Data Share 为接收的共享配置数据集映射。 可能需要配置数据集映射以指定要接收数据的目标数据存储，或者如果需要更改目标数据存储，可能需要配置数据集映射。

## <a name="navigate-to-a-received-data-share"></a>导航到已接收的数据共享

在 Azure Data Share 服务中，导航到接收到的共享，然后选择“数据集”选项卡。 

![数据集映射](./media/dataset-mapping.png "数据集映射") 

选中要为其分配目标位置的数据集旁边的复选框。 选择“+ 映射到目标”以选择新目标存储。 如果数据集已映射，而你想要更改目标数据存储，请先选择“取消映射”。

![映射到目标](./media/dataset-map-target.png "映射到目标") 

## <a name="select-a-target-store"></a>选择目标存储

选择以哪种目标数据存储类型保存数据。 对于基于快照的共享，任何以前映射的存储帐户中已存在的任何数据都不会自动移动到新的目标存储。 对于就地共享，请选择指定“位置”中的数据存储。 “位置”是数据提供程序的源数据存储所在的 Azure 数据中心。

![目标存储帐户](./media/dataset-map-target-sql.png "目标存储") 

## <a name="select-a-file-format-sql-sources-only"></a>选择文件格式（仅 SQL 源）

如果源数据来自基于 SQL 的源，并且你想要以文件形式接收它，可以选择其接收格式。 

![选择格式](./media/sql-file-formats.png "SQL 文件格式")

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。



