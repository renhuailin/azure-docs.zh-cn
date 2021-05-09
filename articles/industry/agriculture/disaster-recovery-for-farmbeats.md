---
title: FarmBeats 的灾难恢复
description: 本文介绍数据恢复如何防止丢失数据。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-ummehabiba
ms.openlocfilehash: 9ece624546cab1b8b6fab8c19f4401bd050f6267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179878"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats 的灾难恢复

数据恢复可防止在 Azure 区域崩溃之类的事件中丢失数据。 在这样的事件中，可以启动故障转移，恢复 FarmBeats 部署中存储的数据。

数据恢复不是 Azure FarmBeats 中的默认功能。 可以通过配置所需 Azure 资源（FarmBeats 用于在 Azure 配对区域中存储数据）来手动配置此功能。 使用“主动 - 被动”方法来启用恢复。

以下部分介绍了如何在 Azure FarmBeats 中配置数据恢复：

- [启用数据冗余](#enable-data-redundancy)
- [从联机备份还原服务](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>启用数据冗余

FarmBeats 将数据存储在三个 Azure 第一方服务中，即 Azure 存储、Cosmos DB 和时序见解  。 请使用以下步骤，针对配对的 Azure 区域为这些服务启用数据冗余：

1.  Azure 存储 - 按照此准则为 FarmBeats 部署中的每个存储帐户启用数据冗余。
2.  Azure Cosmos DB - 按照此准则为 FarmBeats 部署中的 Cosmos DB 帐户启用数据冗余。
3.  Azure 时序见解 (TSI) - TSI 目前不提供数据冗余。 若要恢复时序见解数据，请联系传感器/天气合作伙伴，并再次将数据推送到 FarmBeats 部署。

## <a name="restore-service-from-online-backup"></a>从联机备份还原服务

可以为 FarmBeats 部署的上述每个数据存储所存储的数据启动故障转移和恢复。 在恢复了 Azure 存储和 Cosmos DB 的数据后，请在 Azure 配对区域中创建另一个 FarmBeats 部署，然后通过使用以下步骤将新部署配置为使用已还原的数据存储（即 Azure 存储和 Cosmos DB）中的数据：

1. [配置 Cosmos DB](#configure-cosmos-db)
2. [配置存储帐户](#configure-storage-account)


### <a name="configure-cosmos-db"></a>配置 Cosmos DB

复制已还原的 Cosmos DB 的访问密钥，并对新 FarmBeats Datahub Key Vault 进行更新。


  ![屏幕截图，突出显示了在何处获取访问密钥副本。](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 复制已还原的 Cosmos DB 的 URL，并在新 FarmBeats Datahub 应用服务配置中更新该 URL。 现在可以删除新 FarmBeats 部署中的 Cosmos DB 帐户。

  ![屏幕截图，显示了在何处复制已还原的 Cosmos DB 的 URL。](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>配置存储帐户

复制已还原的存储帐户的访问密钥，并在新 FarmBeats Datahub Key Vault 中更新该密钥。

![屏幕截图，显示了在何处复制已还原的存储帐户的访问密钥。](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 请确保在新的 FarmBeats Batch VM 配置文件中更新存储帐户名称。

![灾难恢复](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同样，如果已为加速器存储帐户启用了数据恢复，请按照步骤 2 在新 FarmBeats 实例中更新加速器存储帐户访问密钥和名称。
