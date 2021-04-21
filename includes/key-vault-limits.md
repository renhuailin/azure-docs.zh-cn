---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: c2548b1669366564809ed2fde725cb3399922a29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803577"
---
Azure 密钥保管库服务支持两种资源类型：“保管库”和“托管 HSM”。 以下两个部分将分别说明每种类型的服务限制。

### <a name="resource-type-vault"></a>资源类型：保管库

本部分说明 `vaults` 资源类型的服务限制。

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>密钥事务数（每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup>）：

|密钥类型|HSM 密钥<br>CREATE 密钥|HSM 密钥<br>所有其他事务|软件密钥<br>CREATE 密钥|软件密钥<br>所有其他事务|
|:---|---:|---:|---:|---:|
|RSA 2,048 位|5|1,000|10|2,000|
|RSA 3,072 位|5|250|10|500|
|RSA 4,096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|
||||||

> [!NOTE]
> 在上表中，我们看到，对于 RSA 2,048 位软件密钥，每 10 秒允许 2,000 个 GET 事务。 对于 RSA 2,048 位 HSM 密钥，允许每 10 秒 1,000 个 GET 事务。
>
> 限制阈值是加权的，并且是针对其总和施加的。 例如，如上表所示，对 RSA HSM 密钥执行 GET 操作时，使用 4,096 位密钥比使用 2,048 位密钥贵 8 倍。 这是因为 1,000/125 = 8。
>
> 在给定的 10 秒间隔内，Azure Key Vault 客户端在遇到 `429` 限制 HTTP 状态代码之前，只能执行以下操作 *之一*：
> - 2,000 个 RSA 2,048 位软件密钥 GET 事务
> - 1,000 RSA 2,048 位 HSM 密钥 GET 事务
> - 125 RSA 4,096 位 HSM 密钥 GET 事务
> - 124 RSA 4,096 位 HSM 密钥 GET 事务和 8 RSA 2,048 位 HSM 密钥 GET 事务

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>机密、托管存储帐户密钥，以及保管库事务：

| 事务类型 | 每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup> |
| --- | --- |
| 所有事务 |2,000 |

有关超出这些限制时如何处理限制的信息，请参阅 [Azure Key Vault 限制指南](../articles/key-vault/general/overview-throttling.md)。

<sup>1</sup> 所有事务类型的订阅范围限制是每个密钥保管库限制的 5 倍。 例如，每个订阅的 HSM-其他事务数限制为 10 秒内 5,000 个事务。

#### <a name="backup-keys-secrets-certificates"></a>备份密钥、机密和证书

备份密钥保管库对象（如机密、密钥或证书）时，备份操作会将该对象作为加密的 blob 下载。 无法在 Azure 外部解密此 blob。 要从此 blob 获取可用数据，必须将 blob 还原到同一 Azure 订阅和 Azure 地理位置内的密钥保管库中

| 事务类型 | 允许的最大密钥保管库对象版本数 |
| --- | --- |
| 备份单个密钥、机密、证书 |500 |

> [!NOTE]
> 如果尝试备份的密钥、机密或证书对象版本数超出上限，将会导致错误。 无法删除密钥、机密或证书的历史版本。 

#### <a name="azure-private-link-integration"></a>Azure 专用链接集成

> [!NOTE]
> 按订阅启用了专用终结点的密钥保管库数量是可调整的限制。 下面显示的限制是默认限制。 如果你想要为服务请求增加限制，请发送电子邮件到 akv-privatelink@microsoft.com。 我们将根据具体情况审批这些请求。

| 资源 | 限制 |
| -------- | -----:|
| 每个密钥保管库的专用终结点数目 | 64 |
| 每个订阅的包含专用终结点的密钥保管库数目 | 400 |

### <a name="resource-type-managed-hsm-preview"></a>资源类型：托管 HSM（预览）

本部分说明 `managed HSM` 资源类型的服务限制。

#### <a name="object-limits"></a>对象限制

|项|限制|
|----|------:|
每个区域每个订阅的 HSM 实例数|1（在预览期）
每个 HSM 池的密钥数|5000
每个密钥的版本数|100
每个 HSM 的自定义角色定义数|50
HSM 范围的角色分配数|50
每个密钥范围的角色分配数|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>管理操作的事务限制（每个 HSM 实例每秒的操作次数）
|操作 |每秒操作次数|
|----|------:|
所有 RBAC 操作<br/>（包括用于角色定义和角色分配的所有 CRUD 操作）|5
完整 HSM 备份/还原<br/>（每个 HSM 实例仅支持一个并发备份或还原操作）|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>加密操作的事务限制（每个 HSM 实例每秒的操作次数）

- 每个托管 HSM 实例构成 3 个负载均衡的 HSM 分区。 吞吐量限制取决于分配给每个分区的底层硬件容量。 下表显示了至少有一个分区可用的情况下的最大吞吐量。 如果有 3 个分区可用，则实际吞吐量最多可提高 3 倍。
- 所述吞吐量限制是假设使用单个密钥来实现最大吞吐量的前提下给出的。 例如，如果使用单个 RSA-2048 密钥，则最大吞吐量为 1100 次符号运算。 如果使用 1100 个不同的密钥，每个密钥每秒处理 1 个事务，则这些密钥无法实现相同的吞吐量。

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA 密钥操作（每个 HSM 实例每秒的操作次数）

|操作|2048 位|3072 位|4096 位|
|--|--:|--:|--:|
创建键|1| 1| 1
删除密钥（软删除）|10|10|10 
清除密钥|10|10|10 
备份密钥|10|10|10 
还原密钥|10|10|10 
获取密钥信息|1100|1100|1100
加密|10000|10000|6000
解密|1100|360|160
包装|10000|10000|6000
解包|1100|360|160
签名|1100|360|160
验证|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC 密钥操作（每个 HSM 实例每秒的操作次数）

下表说明了针对每个曲线类型每秒可执行的操作次数。

|操作|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
创建键| 1| 1| 1| 1
删除密钥（软删除）|10|10|10|10
清除密钥|10|10|10|10
备份密钥|10|10|10|10
还原密钥|10|10|10|10
获取密钥信息|1100|1100|1100|1100
签名|260|260|165|56
验证|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES 密钥操作（每个 HSM 实例每秒的操作次数）
- 加密和解密操作假设数据包大小为 4KB。
- 加密/解密吞吐量限制适用于 AES-CBC 和 AES GCM 算法。
- 包装/解包吞吐量限制适用于 AES-KW 算法。

|操作|128 位|192 位|256 位|
|--|--:|--:|--:|
创建键|1| 1| 1
删除密钥（软删除）|10|10|10
清除密钥|10|10|10
备份密钥|10|10|10
还原密钥|10|10|10
获取密钥信息|1100|1100|1100
加密|8000|8000 |8000 
解密|8000|8000|8000
包装|9000|9000|9000
解包|9000|9000|9000

