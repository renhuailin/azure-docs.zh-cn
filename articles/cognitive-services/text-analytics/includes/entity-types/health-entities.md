---
title: 医疗保健的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: 1477e81d3809a0712913c95f54d4c073f08bb362
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550729"
---
[健康状况文本分析](../../how-tos/text-analytics-for-health.md)处理非结构化医疗数据并从中提取见解。 此服务会检测医疗概念并让这些概念浮现出来，为概念分配断言，推断概念之间的语义关系，并将这些概念链接到常用的医疗本体论。

健康状况文本分析检测到以下类别的医疗概念。 目前仅支持英语文本。

| 类别  | 说明  |
|---------|---------|
| [ANATOMY](#anatomy) | 这些概念描述有关身体和解剖系统、部位、位置或区域的信息。 |
 | [DEMOGRAPHICS](#demographics) | 描述有关性别和年龄信息的概念。 |
 | [EXAMINATION](#examinations) | 描述有关诊断过程和测试信息的概念。 |
 | [EXTERNAL INFLUENCE](#external-influence) | 与医学相关的外部因素（例如过敏原）有关的概念。|
 | [GENERAL ATTRIBUTES](#general-attributes) | 这些概念提供有关以上类别中其他概念的详细信息。 |
 | [GENOMICS](#genomics) | 描述有关基因和变体信息的概念。 |
 | [HEALTHCARE](#healthcare) | 这些概念描述有关管理事件、护理环境和医疗保健职业的信息。 |
 | [MEDICAL CONDITION](#medical-condition) | 这些概念描述有关诊断、症状或体征的信息。 |
 | [MEDICATION](#medication) | 这些概念描述有关药物的信息，包括药物名称、分类、剂量和施用途径。 |
 | [SOCIAL](#social) | 描述有关医学相关的社会领域信息的概念，例如家庭关系。 |
 | [TREATMENT](#treatment) | 描述有关治疗过程的信息的概念。 |

请参阅下面的更多信息和示例。

## <a name="anatomy"></a>结构

### <a name="entities"></a>实体

**BODY_STRUCTURE** - 身体系统、解剖位置或区域，以及身体部位。 例如，手臂、膝盖、腹部、鼻子、肝脏、头部、呼吸系统、淋巴细胞。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="身体结构实体的示例。" lightbox="../../media/ta-for-health/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>人口统计数据

### <a name="entities"></a>实体

AGE - 所有年龄相关的术语和短语，包括适用于患者、家属等人员的术语和短语。 例如，40 岁、51 岁、3 个月、成人、幼儿、老年人、年轻人、未成年人、中年人。

**GENDER** - 揭示主体性别的术语。 例如，男性、女性、妇女、先生、女士。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="年龄实体的示例。" lightbox="../../media/ta-for-health/age-entity.png":::

## <a name="examinations"></a>检查

### <a name="entities"></a>实体

EXAMINATION_NAME - 诊断过程和化验，包括生命体征和身体方面的测量。 例如，MRI、ECG、HIV 检测、血红蛋白、血小板计数、比例尺系统（如布里斯托尔大便量表）。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="检查实体的示例。" lightbox="../../media/ta-for-health/exam-name-entities.png":::

## <a name="external-influence"></a>外部影响

### <a name="entities"></a>实体

ALLERGEN - 一种引发过敏反应的抗原。 例如，猫、花生。

:::image type="content" source="../../media/ta-for-health/external-influence-allergen.png" alt-text="外部影响实体的示例。" lightbox="../../media/ta-for-health/external-influence-allergen.png":::


## <a name="general-attributes"></a>常规属性

### <a name="entities"></a>实体

COURSE - 对另一个实体随时间变化的描述，例如病情进展（例如，改善、恶化、消退、缓解）、治疗或用药过程（例如，增加用药剂量）。 

:::image type="content" source="../../media/ta-for-health/course-entity.png" alt-text="过程实体的示例。" lightbox="../../media/ta-for-health/course-entity.png":::

DATE - 与身体状况、检查、治疗、药物或管理事件有关的完整日期。

:::image type="content" source="../../media/ta-for-health/date-entity.png" alt-text="日期实体的示例。" lightbox="../../media/ta-for-health/date-entity.png":::

DIRECTION - 可能与身体结构、身体状况、检查或处置相关的方向性术语，例如，左方、侧方、上方、后方。

:::image type="content" source="../../media/ta-for-health/direction-entity.png" alt-text="方向实体的示例。" lightbox="../../media/ta-for-health/direction-entity.png":::

FREQUENCY - 描述身体状况、检查、处置或药物治疗在过去发生、现在发生或将来发生的频率。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="药物频率属性的示例。" lightbox="../../media/ta-for-health/medication-form.png":::


TIME - 与身体状况、检查、处置、药物治疗或管理事件的开始时间和/或时长（持续时间）相关的时态方面的术语。 

MEASUREMENT_UNIT - 与检查或身体状况测量相关的测量单位。

MEASUREMENT_VALUE - 与检查或身体状况测量相关的值。

:::image type="content" source="../../media/ta-for-health/measurement-value-entity.png" alt-text="测量值实体的示例。" lightbox="../../media/ta-for-health/measurement-value-entity.png":::

RELATIONAL_OPERATOR - 表达实体与某些其他信息之间定量关系的短语。

:::image type="content" source="../../media/ta-for-health/measurement-unit.png" alt-text="测量单位实体的示例。" lightbox="../../media/ta-for-health/measurement-unit.png"::: 

## <a name="genomics"></a>基因组学

### <a name="entities"></a>实体

VARIANT - 所有提到的基因变异和突变。 例如，`c.524C>T`、`(MTRR):r.1462_1557del96`
  
GENE_OR_PROTEIN - 所有提到的人类基因以及染色体、染色体组成部分和蛋白质的名称和符号。 例如，MTRR、F2。

MUTATION_TYPE - 对突变的描述，包括其类型、影响和位置。 例如，三体、种系突变、机能丧失。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="基因实体的示例。" lightbox="../../media/ta-for-health/genomics-entities.png":::

EXPRESSION - 基因表达水平。 例如，阳性、阴性、过表达、检测到高/低水平、升高。

:::image type="content" source="../../media/ta-for-health/expression.png" alt-text="基因表达实体的示例。" lightbox="../../media/ta-for-health/expression.png":::


## <a name="healthcare"></a>医疗保健

### <a name="entities"></a>实体
  
ADMINISTRATIVE_EVENT - 与医疗保健系统相关但具有管理/半管理性质的事件。 例如，登记、入院、试验、研究项目、转院、出院、住院。 

CARE_ENVIRONMENT - 为患者提供护理的环境或位置。 例如，急诊室、医师办公室、心脏科、临终关怀中心、医院。

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="医疗保健事件实体的示例。" lightbox="../../media/ta-for-health/healthcare-event-entity.png" :::

HEALTHCARE_PROFESSION - 执业医生或非执业医疗从业者。 例如，牙医、病理学家、神经学家、放射科医生、药剂师、营养师、理疗师、脊椎指压师。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="医疗保健环境实体的另一个示例。" lightbox="../../media/ta-for-health/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>身体状况

### <a name="entities"></a>实体

DIAGNOSIS - 疾病、综合征、中毒。 例如，乳腺癌、阿尔茨海默症、HTN、CHF、脊髓损伤。

SYMPTOM_OR_SIGN - 疾病或其他诊断的主观或客观证据。 例如，胸痛、头痛、头晕、皮疹、SOB、腹部发软、肠鸣良好、营养良好。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="身体状况实体的示例。" lightbox="../../media/ta-for-health/medical-condition-entity.png":::

CONDITION_QUALIFIER - 用于描述身体状况的定量术语。 以下所有子类别均被视为限定词：

1.  与时间相关的表达：这些是定性描述时间维度的术语，例如突然的、急性的、慢性的、长期的。 
2.  性质程度：这些是描述身体状况的“性质”的术语，如强烈的、急剧的。
3.  严重程度：严重、轻微、有点、不受控制。
4.  扩展性表达：局部、病灶、扩散。

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="此屏幕截图显示了包含诊断实体的病情限定符属性的另一个示例。" lightbox="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" :::

CONDITION_SCALE - 用一个尺度来表征病情的定性术语，这是一个有限有序的值列表。

:::image type="content" source="../../media/ta-for-health/condition-scale-example.png" alt-text="病情限定符属性和诊断实体的另一个示例。" lightbox="../../media/ta-for-health/condition-scale-example.png":::

## <a name="medication"></a>药物

### <a name="entities"></a>实体

MEDICATION_CLASS - 具有相似作用机制、相关作用方式、相似化学结构和/或用于治疗相同疾病的一组药物。 例如，ACE 抑制剂、阿片类、抗生素、止痛药。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="药物类实体的示例。" lightbox="../../media/ta-for-health/medication-entities-class.png":::

MEDICATION_NAME - 提及药物，包括受版权保护的品牌名称和非品牌名称。 例如“布洛芬”。

**DOSAGE** - 安排的药物量。 例如，注射氯化钠溶液 1000 mL。

**MEDICATION_FORM** - 药物的形式。 例如，溶液、药丸、胶囊、片剂、贴片、凝胶、糊剂、泡沫、喷雾、滴剂、乳霜、糖浆。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="药物剂量属性的示例。" lightbox="../../media/ta-for-health/medication-dosage.png":::

**MEDICATION_ROUTE** - 药物的给药方法。 例如口服、局部、吸入。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="药物形式属性的示例。" lightbox="../../media/ta-for-health/medication-form.png"::: 

## <a name="social"></a>社交

### <a name="entities"></a>实体

FAMILY_RELATION - 提及主体的家庭关系。 例如父亲、女儿、兄弟姐妹、父母。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="家庭关系实体的示例。" lightbox="../../media/ta-for-health/family-relation.png":::

## <a name="treatment"></a>处理方式

### <a name="entities"></a>实体

TREATMENT_NAME - 治疗过程。 例如，膝关节置换手术、骨髓移植、TAVI、饮食。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="治疗名称实体的示例。" lightbox="../../media/ta-for-health/treatment-entities-name.png":::


## <a name="supported-assertions"></a>支持的断言

断言修饰符分为三个类别，每个类别侧重于不同的方面。
每个类别都包含一组互斥的值。 每个类别仅为每个实体指定一个值。 每个类别最常见的值是默认值。 服务的输出响应仅包含不同于默认值的断言修饰符。

### <a name="certainty"></a>确定性  

提供有关概念存在的信息（存在与不存在），以及文本与其存在（明确与可能）有关的信息。

Positive（默认值）：概念存在或已发生。

**Negative**：概念目前尚不存在或者从未发生过。

:::image type="content" source="../../media/ta-for-health/negative-entity.png" alt-text="阴性实体的示例。" lightbox="../../media/ta-for-health/negative-entity.png":::

**Positive_Possible**：概念可能存在，但存在一些不确定性。

:::image type="content" source="../../media/ta-for-health/positive-possible-entity.png" alt-text="可能阳性的实体的示例。" lightbox="../../media/ta-for-health/positive-possible-entity.png" :::

**Negative_Possible**：概念可能不存在，但存在一些不确定性。

:::image type="content" source="../../media/ta-for-health/negative-possible-entity.png" alt-text="可能阴性的实体的示例。" lightbox="../../media/ta-for-health/negative-possible-entity.png" :::

**Neutral_Possible**：概念可能存在，也可能不存在，没有偏向任何一方的倾向。

:::image type="content" source="../../media/ta-for-health/neutral-possible-entity.png" alt-text="可能中性的实体的示例。" lightbox="../../media/ta-for-health/neutral-possible-entity.png":::

### <a name="conditionality"></a>条件性

提供有关概念的存在是否依赖于特定条件的信息。 

None（默认值）：概念是事实，而不是假设，并且不依赖于特定情况。

**Hypothetica**：概念可能正在形成，或者会在将来发生。

:::image type="content" source="../../media/ta-for-health/hypothetical-entity.png" alt-text="假设实体的示例。" lightbox="../../media/ta-for-health/hypothetical-entity.png":::

**Conditional**：概念存在或仅在某些条件下出现。

:::image type="content" source="../../media/ta-for-health/conditional-entity.png" alt-text="条件实体的示例。" lightbox="../../media/ta-for-health/conditional-entity.png":::

### <a name="association"></a>关联

描述概念是否与文本的主体或其他人相关联。

Subject（默认值）：概念与文本的主体（通常为患者）相关联。

**Someone_Else**：概念与不是文本主体的人员关联。

:::image type="content" source="../../media/ta-for-health/association-entity.png" alt-text="关联实体的示例。" lightbox="../../media/ta-for-health/association-entity.png":::

