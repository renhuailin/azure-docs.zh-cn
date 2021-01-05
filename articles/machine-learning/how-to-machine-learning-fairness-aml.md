---
title: 在 Python 中评估机器学习模型的公平性（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Fairlearn 和 Azure 机器学习 Python SDK 评估和缓解机器学习模型的公平。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 057f965c67c25d486771a4c037e13153a7f2cf66
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901141"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>将 Azure 机器学习与 Fairlearn 开源程序包配合使用来评估机器学习模型的公平性（预览版）

在本操作指南中，你将了解如何将 [Fairlearn](https://fairlearn.github.io/) 开源 Python 程序包与 Azure 机器学习配合使用来执行以下任务：

* 评估模型预测的公平性。 若要详细了解机器学习中的公平性，请参阅[机器学习中的公平性](concept-fairness-ml.md)一文。
* 在 Azure 机器学习工作室中上传、列出和下载公平性评估见解。
* 请查看 Azure 机器学习工作室中的公平性评估仪表板，与模型的公平性见解进行交互。

>[!NOTE]
> 公平性评估并非纯粹的技术练习。 **此程序包可帮助你评估机器学习模型的公平性，但只有你可以配置并做出有关模型执行方式的决策。**  虽然此程序包有助于识别用于评估公平性的量化指标，但机器学习模型的开发人员也必须执行定性分析来评估其自己的模型的公平性。

## <a name="azure-machine-learning-fairness-sdk"></a>Azure 机器学习公平性 SDK 

Azure 机器学习公平性 SDK `azureml-contrib-fairness` 在 Azure 机器学习中集成了开源 Python 程序包 [Fairlearn](http://fairlearn.github.io)。 若要详细了解 Azure 机器学习中的 Fairlearn 的集成，请查看这些[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)。 有关 Fairlearn 的详细信息，请参阅[示例指南](https://fairlearn.github.io/master/auto_examples/)和[示例笔记本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。 

使用以下命令安装 `azureml-contrib-fairness` 和 `fairlearn` 程序包：
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
更高版本的 Fairlearn 还应在下面的示例代码中使用。



## <a name="upload-fairness-insights-for-a-single-model"></a>为单个模型上传公平性见解

下面的示例演示如何使用公平包。 我们会将模型公平见解上载到 Azure 机器学习，并在 Azure 机器学习 studio 中查看公平评估仪表板。

1. 在 Jupyter Notebook 中训练示例模型。 

    对于数据集，我们使用众所周知的成人人口统计数据集，我们将从 OpenML 中提取这些数据集。 我们假设我们有一个贷款决策问题，该标签指示个体是否偿还上一个贷款。 我们将定型一个模型，以预测之前不可见的个人是否会报答贷款。 这种模型可用于做出贷款决定。

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. 登录到 Azure 机器学习并注册你的模型。
   
    公平性仪表板可以与已注册或未注册的模型进行集成。 在 Azure 机器学习中注册模型，步骤如下：
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. 预先计算公平性指标。

    使用 Fairlearn 的 `metrics` 程序包创建一个仪表板字典。 `_create_group_metric_set` 方法的参数与仪表板构造函数类似，只是将敏感特性作为字典传递（以确保名称可用）。 调用此方法时，还必须指定预测类型（在本例中为二元分类）。

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上传预先计算的公平性指标。
    
    现在，导入 `azureml.contrib.fairness` 程序包以执行上传操作：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    创建一个试验，然后创建一个运行并将仪表板上传到其中：
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. 查看 Azure 机器学习工作室中的公平性仪表板

    如果你完成了前面的步骤（将生成的公平性见解上传到 Azure 机器学习），则可查看 [Azure 机器学习工作室](https://ml.azure.com)中的公平性仪表板。 此仪表板与 Fairlearn 中提供的可视化效果仪表板相同，可用于分析敏感特性的子组（例如，男性与女性）之间的差异。
    通过以下途径之一访问 Azure 机器学习工作室中的可视化仪表板：

    * **“试验”窗格（预览版）**
    1. 在左侧窗格中选择“试验”，以查看在 Azure 机器学习中运行的试验列表。
    1. 选择特定的试验可查看该试验中的所有运行。
    1. 选择一个运行，然后选择“公平性”选项卡来查看解释可视化效果仪表板。


    [![“公平性”仪表板](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * “模型”窗格
    1. 如果已通过前面的步骤注册了原始模型，则可在左侧窗格中选择“模型”来查看它。
    1. 选择一个模型，然后选择“公平性”选项卡来查看解释可视化效果仪表板。

    若要了解有关可视化仪表板及其包含内容的详细信息，请查看 Fairlearn 的 [用户指南](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)。

## <a name="upload-fairness-insights-for-multiple-models"></a>为多个模型上传公平性见解

若要比较多个模型并了解其公平评估的不同之处，可以将多个模型传递到可视化仪表板，并比较它们的性能公平权衡。

1. 训练你的模型：
    
    现在，我们创建了一个基于支持向量计算机估计器的第二个分类器，并使用 Fairlearn 的包上传公平仪表板字典 `metrics` 。 假设先前训练的模型仍可用。


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. 注册模型

    接下来，在 Azure 机器学习中注册这两个模型。 为方便起见，请将结果存储在字典中，这会将 `id` 已注册模型的)  (格式的字符串映射 `name:version` 到预测器本身：

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. 在本地加载 Fairlearn 仪表板

    在将公平性见解上传到 Azure 机器学习之前，可以在通过本地方式调用的 Fairlearn 仪表板中检查这些预测。 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 预先计算公平性指标。

    使用 Fairlearn 的 `metrics` 程序包创建一个仪表板字典。

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上传预先计算的公平性指标。
    
    现在，导入 `azureml.contrib.fairness` 程序包以执行上传操作：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    创建一个试验，然后创建一个运行并将仪表板上传到其中：
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    与上一部分类似，你可以在 Azure 机器学习工作室中遵循上述路径之一（使用 **试验** 或 **模型**）来访问可视化结果仪表板，并比较两个模型的公平性和性能。


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>上传未修正和已修正公平性见解

你可以使用 Fairlearn 的[修正算法](https://fairlearn.github.io/master/user_guide/mitigation.html)，将其生成的修正模型与原始的未修正模型进行比较，查看所比较模型在性能/公平性方面的权衡情况。

若要查看演示如何使用 [网格搜索](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) 缓解算法的示例 (这会创建一组具有不同公平和性能折衷的缓解模型) 查看此 [示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)。 

如果在一次运行中上传多个模型的公平见解，则可以比较与公平和性能相关的模型。 您可以单击 "模型比较" 图表中显示的任意模型，以查看特定模型的详细公平见解。


[![模型比较 Fairlearn 仪表板](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>后续步骤

[详细了解模型公平性](concept-fairness-ml.md)

[查看 Azure 机器学习公平性示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
