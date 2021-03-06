---
title: 本地和远程运行的模型可解释性
titleSuffix: Azure Machine Learning
description: 了解使用 Azure 机器学习 SDK 时如何获取解释，以了解机器学习模型如何确定特征重要性并做出预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c0ee9bc9ed1e84d51d0c566725fe16dd45aab4b
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598207"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>本地和远程运行的模型可解释性

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure 机器学习 Python SDK 的可解释性包来了解模型做出其预测的原因。 你将学习如何执行以下操作：

* 解释在本地和远程计算资源上训练的机器学习模型。
* 在 Azure 运行历史记录中存储本地和全局解释。
* 在 [Azure 机器学习工作室](https://ml.azure.com)中查看可解释性可视化效果。
* 使用模型部署评分解释器。

有关详细信息，请参阅 [Azure 机器学习中的模型可解释性](how-to-machine-learning-interpretability.md)。

## <a name="local-interpretability"></a>本地可解释性

以下示例演示如何在不联系 Azure 服务的情况下在本地使用可解释性包。

1. 如果需要，请使用 `pip install azureml-interpret` 获取可解释性包。

1. 在本地 Jupyter 笔记本中训练示例模型。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. 在本地调用解释器。
   * 若要初始化解释器对象，请将模型和一些训练数据传递给该解释器的构造函数。
   * 若要使解释和可视化效果更具参考性，可以选择传入特征名称和输出类名称（如果执行分类）。

   以下代码块演示如何在本地使用 `TabularExplainer`、`MimicExplainer` 和 `PFIExplainer` 实例化解释器对象。
   * `TabularExplainer` 调用下面的三个 SHAP 解释器之一（`TreeExplainer`、`DeepExplainer` 或 `KernelExplainer`）。
   * `TabularExplainer` 为用例自动选择最适合的解释器，但你可以直接调用三个基础解释器中的每一个。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    或

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>总体的全局特征重要性值

请参阅以下示例来帮助获取全局特征重要性值。

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>实例级的本地特征重要性值

通过针对单个实例或一组实例调用解释来获取本地特征重要性值。
> [!NOTE]
> `PFIExplainer` 不支持本地解释。

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>远程运行的可解释性

以下示例演示如何使用 `ExplanationClient` 类为远程运行启用模型可解释性。 它在概念上类似于本地过程，不过需要：

* 在远程运行中使用 `ExplanationClient` 来上传可解释性上下文。
* 稍后在本地环境中下载该上下文。

1. 可根据需要使用 `pip install azureml-contrib-interpret` 获取所需的包。

1. 在本地 Jupyter 笔记本中创建训练脚本。 例如，`train_explain.py`。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. 将 Azure 机器学习计算设置为计算目标，并提交训练运行。 有关说明，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md#amlcompute)。 [示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)也可能很有帮助。

1. 下载本地 Jupyter 笔记本中的解释。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>原始特征转换

可以选择获取原始的未经转换的特征中的解释，而不是工程特征中的解释。 对于此选项，请将特征转换管道传递到 `train_explain.py` 中的解释器。 否则，解释器会根据工程特征提供解释。

支持的转换格式与 [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas) 中所述的格式相同。 一般情况下，只要转换针对单个列运行，并且很明确地可以判断它们执行一对多的转换，则就会支持这些转换。

使用 `sklearn.compose.ColumnTransformer` 或拟合的转换器元组列表获取原始特征的解释。 以下示例使用 `sklearn.compose.ColumnTransformer`。

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

如果你想要使用拟合的转换器元组列表运行示例，请使用以下代码：

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>可视化效果

下载本地 Jupyter 笔记本中的解释后，可以使用可视化仪表板来了解和解释模型。

### <a name="global-visualizations"></a>全局可视化效果

下图提供了已训练模型的全局视图及其预测和解释。

|绘图|说明|
|----|-----------|
|数据浏览| 显示数据集的概述以及预测值。|
|全局重要性|全局显示排名靠前的 K（可配置的 K）个重要特征。 帮助了解基础模型的全局行为。|
|解释探索|演示某个特征如何影响模型预测值的变化或预测值的概率。 显示特征交互的影响。|
|摘要重要性|在所有数据点中使用本地特征重要性值来显示每个特征对预测值的影响分布。|

[![全局可视化仪表板](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本地可视化效果

可以通过在绘图中选择单个数据点，来加载任何数据点的本地特征重要性绘图。

|绘图|说明|
|----|-----------|
|本地重要性|全局显示排名靠前的 K（可配置的 K）个重要特征。 帮助演示基础模型对特定数据点的本地行为。|
|扰动探索|允许更改所选数据点的特征值，并观察对预测值所做的最终更改。|
|个体条件预期 (ICE)| 允许特征值从最小值更改为最大值。 帮助演示在特征发生更改时数据点的预测如何更改。|

[![本地特征重要性可视化仪表板](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![特征扰动可视化仪表板](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![ICE 绘图可视化仪表板](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> 在 Jupyter 内核启动之前，请确保为可视化仪表板启用小组件扩展。

* Jupyter 笔记本

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

若要加载可视化仪表板，请使用以下代码：

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure 机器学习工作室中的可视化效果

如果已完成[远程可解释性](#interpretability-for-remote-runs)步骤，可以在 [Azure 机器学习工作室](https://ml.azure.com)中查看可视化仪表板。 此仪表板是前面所述的可视化仪表板的简化版本。 它仅支持两个选项卡：

|绘图|说明|
|----|-----------|
|全局重要性|全局显示排名靠前的 K（可配置的 K）个重要特征。 帮助了解基础模型的全局行为。|
|摘要重要性|在所有数据点中使用本地特征重要性值来显示每个特征对预测值的影响分布。|

如果全局和本地解释均可用，数据将填充在这两个选项卡中。 如果只有全局解释可用，将会禁用“摘要重要性”选项卡。

通过以下途径之一访问 Azure 机器学习工作室中的可视化仪表板：

* “试验”窗格（预览） 
  1. 在左侧窗格中选择“试验”，以查看在 Azure 机器学习中运行的试验列表。 
  1. 选择特定的试验可查看该试验中的所有运行。
  1. 选择一个运行，然后选择“解释”选项卡来查看解释可视化仪表板。 

   [![本地特征重要性可视化仪表板](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* “模型”窗格 
  1. 如果已遵循[使用 Azure 机器学习部署模型](/machine-learning/service/how-to-deploy-and-where)中的步骤注册了原始模型，则可以在左侧窗格中选择“模型”来查看它。 
  1. 选择一个模型，然后选择“解释”选项卡来查看解释可视化仪表板。 

## <a name="interpretability-at-inference-time"></a>推理时的可解释性

可将解释器与原始模型一起部署，并在推断时使用该解释器来提供本地解释信息。 我们还提供了更轻量的评分解释器来改善推断时的解释性能。 部署轻量评分解释器的过程类似于部署模型，包括以下步骤：

1. 创建解释对象。 例如，可以使用 `TabularExplainer`：

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用解释对象创建评分解释器。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 配置并注册使用评分解释器模型的映像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. （可选步骤）可以从云检索评分解释器，并测试解释。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 遵循以下步骤将映像部署到计算目标：

   1. 如果需要，请遵循[使用 Azure 机器学习部署模型](/machine-learning/service/how-to-deploy-and-where)中的步骤注册原始预测模型。

   1. 创建评分文件。

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. 定义部署配置。

         此配置取决于模型的要求。 以下示例定义一种使用 1 个 CPU 核心和 1 GB 内存的配置。

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 创建包含环境依赖项的文件。

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. 创建装有 g++ 的自定义 Dockerfile。

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 部署创建的映像。
   
         此过程大约需要 5 分钟。

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. 测试部署。

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 清理。

   若要删除已部署的 Web 服务，请使用 `service.delete()`。

## <a name="next-steps"></a>后续步骤

[详细了解模型可解释性](how-to-machine-learning-interpretability.md)