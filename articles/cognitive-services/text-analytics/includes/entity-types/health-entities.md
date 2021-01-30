---
title: 医疗保健的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 614d0fe69cee88791559758d5e08dda66672669b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097249"
---
[健康状况文本分析](../../how-tos/text-analytics-for-health.md)检测到以下类别的医疗概念。  （请注意，此容器预览版仅支持英文文本，且每个容器映像中只提供一个模型版本。）


| Category  | 说明  |
|---------|---------|
| [ANATOMY](#anatomy) | 描述有关身体和解剖系统、地点、位置或区域信息的概念。 |
 | [DEMOGRAPHICS](#demographics) | 描述有关性别和年龄信息的概念。 |
 | [EXAMINATION](#examinations) | 描述有关诊断过程和测试信息的概念。 |
 | [GENOMICS](#genomics) | 描述有关基因和变体信息的概念。 |
 | [HEALTHCARE](#healthcare) | 描述有关管理事件、护理环境和医疗保健职业信息的概念。 |
 | [MEDICAL CONDITION](#medical-condition) | 描述有关诊断、症状或体征信息的概念。 |
 | [MEDICATION](#medication) | 描述有关药物信息的概念，包括药物名称、类别、剂量和给药途径。 |
 | [SOCIAL](#social) | 描述有关医学相关的社会领域信息的概念，例如家庭关系。 |
 | [TREATMENT](#treatment) | 描述有关治疗过程的信息的概念。 |
  
每个类别可能包括两个概念组：

* **实体** - 描述医疗概念（如诊断结果、药物名称或治疗名称）的术语。  例如，“支气管炎”是诊断结果，“阿司匹林”是药物名称 。  该组中的提及可以链接到 UMLS 概念 ID。
* **属性** - 提供有关检测到的实体的详细信息的短语，例如，“严重”是“支气管炎”的条件限定词，或“81 mg”是“阿司匹林”的剂量   。  此类别中的提及不会链接到 UMLS 概念 ID。

此外，该服务会识别不同概念之间的关系，包括属性和实体之间的关系，例如“方向”到“身体结构”或“剂量”到“药物名称”之间的关系，以及实体之间的关系，例如在缩写检测中   。

## <a name="anatomy"></a>结构

### <a name="entities"></a>实体

**BODY_STRUCTURE** - 身体系统、解剖位置或区域，以及身体部位。 例如，手臂、膝盖、腹部、鼻子、肝脏、头部、呼吸系统、淋巴细胞。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="身体结构实体的示例。":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="身体结构实体的扩展示例。":::

### <a name="attributes"></a>特性

**DIRECTION** - 表征身体结构的方向性术语，例如：左、侧、上、后。

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="方向属性的示例。":::

### <a name="supported-relations"></a>支持的关系

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>人口统计数据

### <a name="entities"></a>实体

**AGE** - 所有年龄相关术语和短语，包括患者、家庭成员和其他人的。 例如，40 岁、51 岁、3 个月、成人、幼儿、老年人、年轻人、未成年人、中年人。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="年龄实体的示例。":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="年龄实体的另一个示例。":::


**GENDER** - 揭示主体性别的术语。 例如，男性、女性、妇女、先生、女士。

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="性别实体的示例。":::

### <a name="attributes"></a>特性

**RELATIONAL_OPERATOR** - 表示人口统计信息实体与其他信息之间的关系的短语。

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="关系运算符的示例。":::

## <a name="examinations"></a>检查

### <a name="entities"></a>实体

**EXAMINATION_NAME** –诊断过程和测试。 例如，MRI、ECG、HIV 检测、血红蛋白、血小板计数、比例尺系统（如布里斯托尔大便量表）。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="检查实体的示例。":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="检查名称实体的另一个示例。":::

### <a name="attributes"></a>特性

描述检查特征的 **方向** 性术语。

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="带有检查名称实体的方向属性的示例。":::

**MEASUREMENT_UNIT** –检查的单位。 例如，在“血红蛋白 > 9.5 g/dL”中，术语“g/dL”是“血红蛋白”检测的单位  。

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="带有检查名称实体的测量单位属性的示例。":::

**MEASUREMENT_VALUE** –检查的值。 例如，在“血红蛋白 > 9.5 g/dL”中，术语“9.5”是“血红蛋白”检测的值  。

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="带有检查名称实体的测量值属性的示例。":::

**RELATIONAL_OPERATOR** –表示检查和其他信息之间的关系的短语。 例如，目标检查所需的测量值。

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="带有检查名称实体的关系运算符的示例。":::

**TIME** –与检查的开始和/或长度 (持续时间) 相关的临时术语。 例如，检测的开始时间。

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="带有检查名称实体的时间属性的示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>基因组学

### <a name="entities"></a>实体

**基因** –基因的所有提及。 例如，MTRR、F2。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="基因实体的示例。":::

**VARIANT** –基因变体的全部提及。 例如，c.524C>T、(MTRR):r.1462_1557del96
  
## <a name="healthcare"></a>医疗保健

### <a name="entities"></a>实体
  
**ADMINISTRATIVE_EVENT** –与医疗保健系统相关但具有管理/半管理性质的事件。 例如，登记、入院、试验、研究项目、转院、出院、住院。 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="医疗保健事件实体的示例。":::

**CARE_ENVIRONMENT** –提供患者的环境或位置。 例如，急诊房间、医师的办公室、cardio 单位、hospice、医院。

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="此屏幕截图显示了医疗保健环境实体的示例。":::

**HEALTHCARE_PROFESSION** –医疗保健实践者许可或非许可。 例如，牙医、病理学家、神经学家、放射科医生、药剂师、营养师、理疗师、脊椎指压师。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="此屏幕截图显示了医疗保健环境实体的另一个示例。":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="医疗保健环境实体的另一个示例。":::

## <a name="medical-condition"></a>身体状况

### <a name="entities"></a>实体

**诊断** -疾病、症状、中毒。 例如，乳腺癌症，Alzheimer，HTN，CHF，痛苦缆线。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="身体状况实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="身体状况实体的另一个示例。":::

**SYMPTOM_OR_SIGN** -疾病或其他诊断的主观或目标证据。 例如，胸痛、头痛、头晕、皮疹、SOB、腹部发软、肠鸣良好、营养良好。

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="身体状况征兆或症状实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="身体状况征兆或症状实体的另一个示例。":::

### <a name="attributes"></a>特性

**CONDITION_QUALIFIER** - 用于描述身体状况的性质术语。 以下所有子类别均视为限定词：

1.  与时间相关的表达：这些是定性描述时间维度的术语，例如突然的、急性的、慢性的、长期的。 
2.  质量表达式：这些是描述医疗条件的 "性质" 的术语，如烧、清晰。
3.  严重程度：严重、轻微、有点、不受控制。
4.  扩展性表达：局部、病灶、扩散。
5.  辐射表达：放射、辐射。
6.  病情程度：在某些情况下，病情的特征是一个程度，这一程度是值的有限有序列表。 例如，III 期胰腺癌患者。
7.  病情量表：与病情的进程或进展有关的术语，例如改善、恶化、解决、缓解。 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="病情限定符属性和诊断实体的示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="病情限定符属性和诊断实体的另一个示例。":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="带有症状和药物实体的病情限定符属性的示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="此屏幕截图显示了包含诊断实体的病情限定符属性的另一个示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="此屏幕截图显示了包含诊断实体的病情限定符属性的另一个示例。":::

**DIRECTION** - 表征身体医疗状况的方向术语。

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="带有身体状况实体的方向属性的示例。":::

**FREQUENCY** - 身体状况发生或应该发生的频率。

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="带有身体状况实体的频率属性的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="带有症状或征兆实体的方向属性的另一个示例。":::

**MEASUREMENT_UNIT** - 表征身体状况的单位。 例如，在“1.5x2x1 cm 肿瘤”中，术语“cm”是“肿瘤”的测量单位  。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="带有身体状况实体的测量单位属性的示例。":::

**MEASUREMENT_VALUE** - 表征身体状况的值。 例如，在“1.5x2x1 cm 肿瘤”中，术语“1.5x2x1”是“肿瘤”的值  。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="此屏幕截图显示了包含症状或征兆实体的方向属性的示例。":::

**RELATIONAL_OPERATOR** - 表示身体状况附加信息之间关系的短语。 例如，时间或测量值。 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="此屏幕截图显示了包含症状或征兆实体的方向属性的另一个示例。":::

**TIME** - 与身体状况的开始和/或长度（持续时间）有关的时间术语。 例如，症状开始（发作）或疾病发生的时间。

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="此屏幕截图显示了包含症状或征兆实体的方向属性的另一个示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>药物

### <a name="entities"></a>实体

**MEDICATION_CLASS** –一组具有类似操作机制、一种相关操作模式、一个类似化学结构和/或用于处理相同疾病的药物。 例如，ACE 抑制剂、阿片类、抗生素、止痛药。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="药物类实体的示例。":::

**MEDICATION_NAME** –药物提及，其中包括版权品牌名称和非品牌名称。 例如，Advil、布洛芬。

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="药物名称实体的示例。":::

### <a name="attributes"></a>特性

**DOSAGE** - 安排的药物量。 例如，注射氯化钠溶液 1000 mL。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="药物剂量属性的示例。":::

**FREQUENCY** - 服用药物的频率。

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="药物频率属性的示例。":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="药物频率属性的另一个示例。":::

**MEDICATION_FORM** - 药物的形式。 例如，溶液、药丸、胶囊、片剂、贴片、凝胶、糊剂、泡沫、喷雾、滴剂、乳霜、糖浆。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="药物形式属性的示例。":::

**MEDICATION_ROUTE** - 药物的给药方法。 例如口服、阴道、静脉注射、硬膜外、局部外用、吸入。

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="用药路径属性的示例。":::

**RELATIONAL_OPERATOR** - 表示药物与其他信息之间的关系的短语。 例如，所需的测量值。

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="此屏幕截图显示了包含药物实体的关系运算符属性的示例。":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="此屏幕截图显示了包含药物实体的关系运算符属性的另一个示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>社交

### <a name="entities"></a>实体

**FAMILY_RELATION** -主题的家庭亲属提及。 例如父亲、女儿、兄弟姐妹、父母。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="此屏幕截图显示了治疗时间属性的另一个示例。":::

## <a name="treatment"></a>处理方式

### <a name="entities"></a>实体

**TREATMENT_NAME** – Therapeutic 的过程。 例如，膝关节置换手术、骨髓移植、TAVI、饮食。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="治疗名称实体的示例。":::

### <a name="attributes"></a>特性

**DIRECTION** - 表征治疗的方向术语。

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="此屏幕截图显示了治疗方向属性的示例。":::

**FREQUENCY** - 治疗发生或应该发生的频率。

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="此屏幕截图显示了治疗方向属性的另一个示例。":::
 
**RELATIONAL_OPERATOR** - 表示治疗与其他信息之间的关系的短语。  例如，距离上一个过程经过了多少时间。

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="治疗关系运算符属性的示例。":::

**TIME** - 与治疗的开始和/或长度（持续时间）有关的时间术语。 例如治疗的日期。

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="此屏幕截图显示了治疗时间属性的示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
