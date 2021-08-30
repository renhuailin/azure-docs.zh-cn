---
title: 交换 X12 消息以实现 B2B 集成
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 生成 B2B 企业集成解决方案时，发送、接收和处理 X12 消息。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, divswa, azla
ms.topic: how-to
ms.date: 07/16/2021
ms.openlocfilehash: 5328fad1530ee8dd7b4a2c79581d443488c44b28
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453834"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-using-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 交换 X12 消息以实现 B2B 企业集成

在 Azure 逻辑应用中，可创建使用 X12 操作处理 X12 消息的工作流。 这些操作包括可在工作流中用于处理 X12 通信的触发器和操作。 你可以使用与在工作流中添加任何其他触发器和操作相同的方式添加 X12 触发器和操作，但需要满足额外的先决条件才能使用 X12 操作。

本文介绍在工作流中使用 X12 触发器和操作的要求和设置。 如果要改为查找 EDIFACT 消息，请查看[交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](logic-apps-overview.md)和[快速入门：使用多租户 Azure 逻辑应用和 Azure 门户创建集成工作流](quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要在其中使用 X12 触发器或操作的逻辑应用资源和工作流。 若要使用 X12 触发器，需要一个空白工作流。 若要使用 X12 操作，需要一个具有现有触发器的工作流。

* 一个已链接到你的逻辑应用资源的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 逻辑应用和集成帐户必须使用相同的 Azure 订阅，并且存在于同一 Azure 区域或位置。

  集成帐户还需要包含以下 B2B 项目：

  * 至少两个使用 X12 标识限定符的[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)。

  * 贸易合作伙伴之间定义的 X12 [协议](logic-apps-enterprise-integration-agreements.md)。 有关接收和发送消息时要使用的设置的信息，请查看[接收设置](#receive-settings)和[发送设置](#send-settings)。

    > [!IMPORTANT]
    > 如果使用健康保险流通与责任法案 (Health Insurance Portability and Accountability Act, HIPAA) 架构，则需要在协议中添加 `schemaReferences` 部分。 有关详细信息，请参阅 [HIPAA 架构和消息类型](#hipaa-schemas)。

  * 用于 XML 验证的[架构](logic-apps-enterprise-integration-schemas.md)。

    > [!IMPORTANT]
    > 如果使用健康保险流通与责任法案 (HIPAA) 架构，请确保查看 [HIPAA 架构和消息类型](#hipaa-schemas)。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/x12/)。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本使用 [ISE 的 B2B 消息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

<a name="receive-settings"></a>

## <a name="receive-settings"></a>接收设置

在贸易合作伙伴协议中设置协议属性后，可以配置此协议如何识别和处理从合作伙伴接收的入站消息。

1. 在“添加”下面，选择“接收设置”。 

1. 根据要与其交换消息的合作伙伴达成的协议，请在“接收设置”窗格中设置属性，其中包含以下部分：

   * [标识符](#inbound-identifiers)
   * [确认](#inbound-acknowledgement)
   * [架构](#inbound-schemas)
   * [包络线](#inbound-envelopes)
   * [控制编号](#inbound-control-numbers)
   * [验证](#inbound-validations)
   * [内部设置](#inbound-internal-settings)

1. 完成后，请务必选择“确定”保存设置。

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>接收设置 - 标识符

![入站消息的标识符属性](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 属性 | 说明 |
|----------|-------------|
| ISA1（授权限定符） | 要使用的授权限定符值。 默认值为“00 - 不存在任何授权信息”。 <p>**注意**：如果选择其他值，请指定 ISA2 属性的值。 |
| ISA2 | 当 ISA1 属性不是“00 - 不存在任何授权信息”时要使用的授权信息值。 此属性值必须最少包含一个字母数字字符，最多包含 10 个。 |
| ISA3（安全限定符） | 要使用的安全限定符值。 默认值为“00 - 不存在任何安全信息”。 <p>**注意**：如果选择其他值，请指定 ISA4 属性的值。 |
| ISA4 | 当 ISA3 属性不是“00 - 不存在任何安全信息”时要使用的安全信息值。 此属性值必须最少包含一个字母数字字符，最多包含 10 个。 |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>接收设置 - 确认

![确认入站消息](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 属性 | 说明 |
|----------|-------------|
| 预期的 TA1 | 向交换发送方返回技术确认 (TA1)。 |
| 预期的 FA | 向交换发送方返回功能确认 (FA)。 <p>对于“FA 版本”属性，根据架构版本，选择 997 或 999 确认。 <p>若要在功能确认中为接受的事务集生成 AK2 循环，请选择“包括 AK2/IK2 循环”。 |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>接收设置 - 架构

![入站消息的架构](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

对于此部分，请从每个事务类型 (ST01) 和发送方应用程序 (GS02) 的[集成帐户](./logic-apps-enterprise-integration-create-integration-account.md)中选择一个[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 EDI 接收管道会通过将在此部分中设置的值和架构与传入消息中的 ST01 和 GS02 值进行匹配以及与传入消息的架构进行匹配，来分解传入消息。 完成每行后，会自动显示新的空行。

| 属性 | 说明 |
|----------|-------------|
| **版本** | 架构的 X12 版本 |
| 事务类型 (ST01) | 事务类型 |
| 发送方应用程序 (GS02) | 发送方应用程序 |
| **架构** | 要使用的架构文件 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>接收设置 - 包络线

![在入站消息的事务集中使用的分隔符](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 属性 | 说明 |
|----------|-------------|
| ISA11 用法 | 要在事务集中使用的分隔符： <p>- 标准标识符：在 EDI 接收管道中使用句点 (.) 十进制表示法，而不是使用传入文档的十进制表示法。 <p>- 重复分隔符：为重复出现的简单数据元素或重复数据结构指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>接收设置 - 控制编号

![处理入站消息的控制编号重复项](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 属性 | 说明 |
|----------|-------------|
| 不允许交换控制编号重复项 | 阻止重复交换。 检查交换控制编号 (ISA13) 是否匹配接收的交换控制编号。 如果检测到匹配项，则 EDI 接收管道不处理交换。 <p><p>若要指定执行检查的间隔天数，请为“每 x 天检查重复的 ISA13”属性输入适当的值。 |
| 不允许组控制编号重复项 | 阻止具有重复组控制编号的交换。 |
| 不允许事务集控制编号重复项 | 阻止具有重复事务集控制编号的交换。 |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>接收设置 - 验证

![入站消息验证](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

“默认”行显示用于 EDI 消息类型的验证规则。 如果要定义不同的规则，请选择要将规则设置为 true 的每个框。 完成每行后，会自动显示新的空行。

| 属性 | 说明 |
|----------|-------------|
| 消息类型 | EDI 消息类型 |
| EDI 验证 | 根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| 扩展验证 | 如果数据类型不是 EDI，则验证会基于数据元素要求，且允许重复、枚举和数据元素长度验证（最小/最大）。 |
| 允许前导零/尾随零 | 保留所有额外的前导或尾随零和空格字符。 不要删除这些字符。 |
| 剪裁前导零/尾随零 | 删除任何前导或尾随零和空格字符。 |
| 尾随分隔符策略 | 生成尾部分隔符。 <p>- 不允许：禁止在入站交换中包含尾随分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>- 可选：接受包含或不包含尾随分隔符的交换。 <p>- 必需：入站交换必须包含尾随分隔符。 |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>接收设置 - 内部设置

![入站消息的内部设置](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 属性 | 说明 |
|----------|-------------|
| 将隐式小数格式“Nn”转换为十进制数值 | 将使用格式“Nn”指定的 EDI 数字转换为十进制数值。 |
| 如果允许尾随分隔符，请创建空的 XML 标记 | 使交换发送方包含用于尾随分隔符的空 XML 标记。 |
| 将交换拆分为事务集 - 出错时暂停事务集 | 通过将相应包络线应用于事务集，将交换中的每个事务集分析为单独 XML 文档。 仅暂停未通过验证的事务。 |
| 将交换拆分为事务集 - 出错时暂停交换 | 通过应用相应包络线，将交换中的每个事务集分析为单独 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 |
| 保留交换 - 出错时暂停事务集 | 保留交换不变，并为整个批处理交换创建 XML 文档。 仅暂停未能通过验证的事务集，但继续处理所有其他事务集。 |
| 保留交换 - 出错时暂停交换 |保留交换不变，为整个批处理交换创建 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则暂停整个交换。 |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>发送设置

设置协议属性后，可以配置此协议识别和处理出站消息的方式，该出站消息通过此协议发送到合作伙伴。

1. 在“添加”下面，选择“发送设置”。 

1. 根据要与其交换消息的合作伙伴达成的协议来配置这些属性。 有关属性说明，请参阅本部分中的表格。

   “发送设置”划分为以下部分：

   * [标识符](#outbound-identifiers)
   * [确认](#outbound-acknowledgement)
   * [架构](#outbound-schemas)
   * [包络线](#outbound-envelopes)
   * [控制版本号](#outbound-control-version-number)
   * [控制编号](#outbound-control-numbers)
   * [字符集和分隔符](#outbound-character-sets-separators)
   * [验证](#outbound-validation)

1. 完成后，请务必选择“确定”保存设置。

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>发送设置 - 标识符

![出站消息的标识符属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 属性 | 说明 |
|----------|-------------|
| ISA1（授权限定符） | 要使用的授权限定符值。 默认值为“00 - 不存在任何授权信息”。 <p>**注意**：如果选择其他值，请指定 ISA2 属性的值。 |
| ISA2 | 当 ISA1 属性不是“00 - 不存在任何授权信息”时要使用的授权信息值。 此属性值必须最少包含一个字母数字字符，最多包含 10 个。 |
| ISA3（安全限定符） | 要使用的安全限定符值。 默认值为“00 - 不存在任何安全信息”。 <p>**注意**：如果选择其他值，请指定 ISA4 属性的值。 |
| ISA4 | 当 ISA3 属性不是“00 - 不存在任何安全信息”时要使用的安全信息值。 此属性值必须最少包含一个字母数字字符，最多包含 10 个。 |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>发送设置 - 确认

![出站消息的确认属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 属性 | 说明 |
|----------|-------------|
| 预期的 TA1 | 向交换发送方返回技术确认 (TA1)。 <p>此设置指定发送消息的管理方请求协议中的托管方的确认。 管理方基于协议的接收设置需要这些确认。 |
| 预期的 FA | 向交换发送方返回功能确认 (FA)。 对于“FA 版本”属性，根据架构版本，选择 997 或 999 确认。 <p>此设置指定发送消息的主机合作伙伴请求协议中来宾合作伙伴的确认。 管理方基于协议的接收设置需要这些确认。 |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>发送设置 - 架构

![出站消息的架构](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

对于此部分，请从每个事务类型 (ST01) 的[集成帐户](./logic-apps-enterprise-integration-create-integration-account.md)中选择一个[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 完成每行后，会自动显示新的空行。

| 属性 | 说明 |
|----------|-------------|
| **版本** | 架构的 X12 版本 |
| 事务类型 (ST01) | 架构的事务类型 |
| **架构** | 要使用的架构文件。 如果先选择架构，将自动设置版本和事务类型。 |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>发送设置 - 包络线

![在出站消息的事务集中使用的分隔符](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 属性 | 说明 |
|----------|-------------|
| ISA11 用法 | 要在事务集中使用的分隔符： <p>- 标准标识符：在 EDI 发送管道中使用句点 (.) 十进制表示法，而不是使用出站文档的十进制表示法。 <p>- 重复分隔符：为重复出现的简单数据元素或重复数据结构指定分隔符。 例如，脱字符 (^) 通常用作重复分隔符。 对于 HIPAA 架构，只能使用脱字符。 |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>发送设置 - 控制版本号

![出站消息的控制版本号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

对于此部分，请从每个交换的[集成帐户](./logic-apps-enterprise-integration-create-integration-account.md)中选择一个[架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 完成每行后，会自动显示新的空行。

| 属性 | 说明 |
|----------|-------------|
| 控制版本号 (ISA12) | X12 标准的版本 |
| 用法指示符 (ISA15) | 交换的上下文，为测试数据、信息数据或生产数据 |
| **架构** | 用于为发送到 EDI 发送管道的 X12 编码交换生成 GS 和 ST 段的架构。 |
| GS1 | 可选：选择功能代码。 |
| GS2 | 可选：指定应用程序发送方。 |
| GS3 | 可选：指定应用程序接收器。 |
| GS4 | 可选：选择 CCYYMMDD 或 YYMMDD 。 |
| GS5 | 可选，选择 HHMM、HHMMSS 或 HHMMSSdd  。 |
| GS7 | 可选：选择负责代理的值。 |
| GS8 | 可选：指定架构文档版本。 |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>发送设置 - 控制编号

![出站消息的控制编号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 属性 | 说明 |
|----------|-------------|
| 交换控制编号 (ISA13) | 交换控制编号的值范围，最小值为 1，最大值为 999999999 |
| 组控制编号 (GS06) | 组控制编号的值范围，最小值为 1，最大值为 999999999 |
| 事务集控制编号 (ST02) | 事务集控制编号的值范围，最小值为 1，最大值为 999999999 <p>- 前缀：可选，字母数字值 <br>- 后缀：可选，字母数字值 |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>发送设置 - 字符集和分隔符

![出站消息的消息类型的分隔符](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

“默认”行显示用作消息架构分隔符的字符集。 如果不想使用“默认”字符集，可以为每种消息类型输入不同分隔符集。 完成每行后，会自动显示新的空行。

> [!TIP]
> 若要提供特殊字符值，请编辑作为 JSON 的协议并提供特殊字符的 ASCII 值。

| 属性 | 说明 |
|----------|-------------|
| 要使用的字符集 | X12 字符集，为“基本”、“扩展”或“UTF8”。 |
| **架构** | 要使用的架构。 选择架构后，请根据以下分隔符说明选择要使用的字符集。 |
| 输入类型 | 字符集的输入类型 |
| 组件分隔符 | 分隔复合数据元素的单个字符 |
| 数据元素分隔符 | 分隔复合数据中的简单数据元素的单个字符 |
| 替换字符分隔符 | 用于在生成出站 X12 消息时替换有效负载数据中的所有分隔符的替换字符 |
| 段终止符 | 指示 EDI 段的结尾的单个字符 |
| **后缀** | 与段标识符一起使用的字符。 如果指定了后缀，则段终止符数据元素可以为空。 如果段终止符留空，则必须指定后缀。 |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>发送设置 - 验证

![出站消息的验证属性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

“默认”行显示用于 EDI 消息类型的验证规则。 如果要定义不同的规则，请选择要将规则设置为 true 的每个框。 完成每行后，会自动显示新的空行。

| 属性 | 说明 |
|----------|-------------|
| 消息类型 | EDI 消息类型 |
| EDI 验证 | 根据架构的 EDI 属性、长度限制、空数据元素和尾部分隔符的定义，对数据类型执行 EDI 验证。 |
| 扩展验证 | 如果数据类型不是 EDI，则验证会基于数据元素要求，且允许重复、枚举和数据元素长度验证（最小/最大）。 |
| 允许前导零/尾随零 | 保留所有额外的前导或尾随零和空格字符。 不要删除这些字符。 |
| 剪裁前导零/尾随零 | 删除任何前导或尾随零和空格字符。 |
| 尾随分隔符策略 | 生成尾部分隔符。 <p>- 不允许：禁止在出站交换中包含尾随分隔符。 如果交换包含尾部分隔符，会将它声明为无效。 <p>- 可选：发送包含或不包含尾随分隔符的交换。 <p>- 必需：出站交换必须包含尾随分隔符。 |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 架构和消息类型

使用 HIPAA 架构和 277 或 837 消息类型时，需要执行一些额外的步骤。 这些消息类型的[文档版本号 (GS8)](#outbound-control-version-number) 具有 9 个以上的字符，例如“005010X222A1”。 此外，某些文档版本号映射到变体消息类型。 如果在架构和协议中未引用正确的消息类型，则会收到以下错误消息：

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

此表列出了受影响的消息类型、所有变体以及映射到这些消息类型的文档版本号：

| 消息类型或变体 |  说明 | 文档版本号 (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | 医疗保健信息状态通知 | 005010X212 |
| 837_I | 医疗保健索赔：机构 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 医疗保健索赔：牙科 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 医疗保健索赔：职业 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

使用这些文档版本号时，还需要禁用 EDI 验证，因为它们会导致字符长度无效的错误。

若要指定这些文档版本号和消息类型，请执行以下步骤：

1. 在 HIPAA 架构中，将当前消息类型替换为要使用的文档版本号的变体消息类型。

   例如，假设你要将文档版本号 `005010X222A1` 用于 `837` 消息类型。 在架构中，将每个 `"X12_00501_837"` 值替换为 `"X12_00501_837_P"` 值。

   若要更新架构，请执行以下步骤：

   1. 在 Azure 门户中，转到集成帐户。 查看和下载你的架构。 替换消息类型并重命名架构文件，然后将修改后的架构上载到集成帐户。 有关详细信息，请参阅[编辑架构](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)。

   1. 在协议的消息设置中，选择修改后的架构。

1. 在协议的 `schemaReferences` 对象中，添加另一个条目来指定与文档版本号匹配的变体消息类型。

   例如，假设要将文档版本号 `005010X222A1` 用于 `837` 消息类型。 协议有一个 `schemaReferences` 部分，其中包含以下属性和值：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   在此 `schemaReferences` 部分中，添加另一个具有以下值的项：

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   完成后，`schemaReferences` 部分如下所示：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 在协议的消息设置中，如果使用“默认”值，请通过清除每个消息类型或所有消息类型的“EDI 验证”复选框来禁用 EDI 验证。

   ![为所有消息类型或每个消息类型禁用验证](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="next-steps"></a>后续步骤

* [X12 TA1 技术确认和错误代码](logic-apps-enterprise-integration-x12-ta1-acknowledgment.md)
* [X12 997 功能确认和错误代码](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [关于 Azure 逻辑应用中的连接器](../connectors/apis-list.md)
