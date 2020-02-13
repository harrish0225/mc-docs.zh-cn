---
title: 部署故障排除指南
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习规避、解决及故障排除 Azure Kubernetes 服务和 Azure 容器实例的常见 Docker 部署错误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: b5f63b5661e7a31717305fa4656ad9272d9b9d37
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75599468"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure 机器学习 Azure Kubernetes 服务和 Azure 容器实例部署的故障排除

了解如何使用 Azure 机器学习规避或解决 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 的常见 Docker 部署错误。

在 Azure 机器学习中部署模型时，系统将执行大量任务。 部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 生成 Docker 映像，包括：
    1. 从注册表中下载已注册的模型。 
    2. 使用基于在环境 yaml 文件中指定的依赖项的 Python 环境创建 dockerfile。
    3. 添加在 dockerfile 中提供的模型文件和评分脚本。
    4. 使用 dockerfile 生成新的 Docker 映像。
    5. 向与工作区关联的 Azure 容器注册表注册 Docker 映像。

    > [!IMPORTANT]
    > 根据你的代码，无需输入便会自动创建映像。

3. 将 Docker 映像部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

4. 在 ACI 或 AKS 中启动一个（或多个）新容器。 

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](/cli/install-azure-cli?view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

    若要验证 Docker 安装，请使用终端或命令提示符中的命令 `docker run hello-world`。 有关安装 Docker 或排除 Dcoker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

## <a name="before-you-begin"></a>准备阶段

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

如果使用 [WWebservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) API，或 [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) API，建议将部署分成多个任务，因为这两各函数都可作为单个操作执行上述步骤。 通常使用这些 API 会很方便，但在通过将其替换为下述 API 调用进行故障排除时，分解步骤会非常有用。

1. 注册模型。 下面是一些示例代码：

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 生成映像。 下面是一些示例代码：

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 将映像部署为服务。 下面是一些示例代码：

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

将部署过程分解为单独任务后，可以查看部分最常见的错误。

## <a name="image-building-fails"></a>映像生成失败

如果无法生成 Docker 映像，[image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) 或 [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) 调用会失败，并会显示错误信息，以此提供一些线索。 还可以从图像生成日志中找到错误的更多详细信息。 下面是一些显示如何发现映像生成日志 URI 的代码示例。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

此映像日志 URI 是指向 Azure blob 存储中存储的日志文件的 SAS URL. 只需复制 URI 并将其粘贴到浏览器窗口，即可下载和查看日志文件。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

由于 Azure Key Vault 存在访问策略问题，因此映像创建也会失败。 使用 Azure 资源管理器模板创建工作区和关联资源（包括 Azure Key Vault）时，可能会出现这种情况。 例如，在持续集成和部署管道过程中，对同一参数多次使用模板。

大多数通过模板的资源创建操作都是幂等的，但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会中断任何使用该访问的现有工作区对 Key Vault 的访问。 尝试创建新映像时此情况会产生错误。 下面是会收到的错误示例：

__门户__：
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__：
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://chinaeast.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__：
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

若要避免此问题，我们建议运用以下方法之一：

* 请不要对同一个参数多次部署模板。 或是在使用模板重新创建之前删除现有资源。
* 检查 Key Vault 的访问策略，然后使用这些策略设置模板的 `accessPolicies` 属性。
* 检查 Key Vault 资源是否已存在。 如果存在，请勿通过模板重新创建。 例如，添加参数，允许禁用对已存在的 Key Vault 资源的创建。

## <a name="debug-locally"></a>本地调试

如果将模型部署到 ACI 或 AKS 时遇到问题，请尝试将其部署为本地 Web 服务。 使用本地 Web 服务可简化解决问题的过程。 在本地系统上下载并启动包含模型的 Docker 映像。

> [!WARNING]
> 生成方案不支持本地 Web 服务部署。

若要进行本地部署，请修改代码以使用 `LocalWebservice.deploy_configuration()` 创建部署配置。 然后使用 `Model.deploy()` 配置该服务。 以下示例将模型（包含在 `model` 变量中）作为本地 Web 服务进行部署：

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.environment import Environment
from azureml.core.webservice import LocalWebservice

# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

请注意，如果定义你自己的 Conda 规范 YAML，则必须使用大于等于 1.0.45 的版本作为 pip 依赖项列出 azureml 默认值。 此包包含将模型作为 Web 服务托管所需的功能。

此时，你可以正常使用该服务。 例如，以下代码演示了将数据发送到该服务的过程：

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>更新服务

在本地测试中，可能需要更新 `score.py` 文件以添加记录或尝试解决发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，以下代码重新加载服务的脚本，然后向其发送数据。 使用 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关更新部署到其他计算目标的信息，请参阅[部署模型](how-to-deploy-and-where.md#update)更新部分。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 可从服务所使用的 `InferenceConfig` 对象指定的位置重新加载该脚本。

若要更改模型、Conda 依赖项或部署配置，请使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 以下示例更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

要删除服务，请使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a id="dockerlog"></a> 检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 以下示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误  。

使用[检查 Docker 日志](#dockerlog)部分中的信息检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常情况下，在评分脚本的 `init()` 函数中，会调用 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 来查找容器中的模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹，函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

该示例将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为 DEBUG 可能会导致记录其他信息，这可能有助于识别故障。

## <a name="function-fails-runinput_data"></a>函数故障：run(input_data)

如果服务部署成功，但在向评分终结点发布数据时崩溃，可在 `run(input_data)` 函数中添加错误捕获语句，以便转而返回详细的错误消息。 例如：

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，不应在生成环境中按此方法返回错误消息。

## <a name="http-status-code-503"></a>HTTP 状态代码 503

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 但是，自动缩放程序旨在处理负载中的逐步更改  。 如果每秒收到大量请求，客户端可能会收到 HTTP 状态代码 503。

有两种方法可以帮助防止 503 状态代码：

* 更改自动缩放创建新副本的利用率。
    
    默认情况下，自动缩放目标利用率设置为 70%，这意味着服务可以处理高达 30% 的大量每秒请求数 (RPS)。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 该更改不会导致更快创建副本  。 而会以较低的利用率阈值创建副本。 可以在利用率达到 30% 时，通过将值改为 30% 来创建副本，而不是等待该服务的利用率达到 70% 时再创建。
    
    如果 Web 服务已在使用当前最大副本，然后你仍能看见 503 状态代码，则请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本最小数量。 增加最小副本可提供一个更大的池来处理传入峰值。

    若要增加副本的最小数量，请将 `autoscale_min_replicas` 设置为更大的值。 可以使用以下代码计算所需的副本，将值替换为项目指定的值：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果收到请求高峰大于新的最小副本可以处理的数量，则可能会再次收到 503 代码。 例如，服务流量增加时，可能需要增加最小副本数据。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 模块参考。


## <a name="advanced-debugging"></a>高级调试

某些情况下，可能需要以交互方式调试包含在模型部署中的 Python 代码。 例如，如果输入脚本失败，并且无法通过其他记录确定原因。 通过使用 Visual Studio Code 和针对 Visual Studio 的 Python 工具 (PTVSD)，可以附加到在 Docker 容器中运行的代码。

> [!IMPORTANT]
> 使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 进行本地部署模型时，此调试方法不起作用。 相反，必须使用 [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 类创建一个映像。 

要在本地部署 Web 服务，需要在本地系统上安装一个有效的 Docker。 有关使用 Docker 的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地 VS Code 部署环境中安装针对 Visual Studio 的 Python 工具 (PTVSD)，请使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关结合使用 VS Code 和 PTVSD 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code，使其与 Docker 映像进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择“调试”菜单，然后选择“打开配置”   。 打开一个名为 launch.json  的文件。

    1. 在 launch.json  文件中，找到包含 `"configurations": [` 的行，然后在其后插入以下文本：

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果“配置”部分已存在其他项，请在插入的代码后添加一个逗号 (,)。

        本部分使用端口 5678 附加到 Docker 容器。

    1. 保存 launch.json  文件。

### <a name="create-an-image-that-includes-ptvsd"></a>创建包括 PTVSD 的映像

1. 修改部署的 Conda 环境，使其包括 PTVSD。 以下示例演示使用 `pip_packages` 参数添加它的过程：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服务启动时启动 PTVSD 并等待连接，请将以下内容添加到 `score.py` 文件的顶部：

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 在调试过程中，你可能需要对映像中的文件进行更改，而不必重新创建文件。 若要在 Docker 映像中安装文本编辑器 (vim)，请创建一个名为 `Dockerfile.steps` 的新文本文件，并使用以下内容作为该文件的内容：

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    通过文本编辑器，可更改 Docker 映像中的文件以测试更改，而无需创建新映像。

1. 若要创建使用 `Dockerfile.steps` 文件的映像，请在创建映像时使用 `docker_file` 参数。 以下示例演示了操作方法：

    > [!NOTE]
    > 该示例假定 `ws` 指向 Azure 机器学习工作区，且 `model` 是要部署的模型。 `myenv.yml` 文件包含步骤 1 中创建的 Conda 依赖项。

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

映像创建完成之后，即会在注册表中显示映像位置。 位置与以下文本类似：

```text
myregistry.azurecr.io/myimage:1
```

在此文本示例中，注册表名为 `myregistry`，映像名为 `myimage`。 映像版本为 `1`。

### <a name="download-the-image"></a>下载映像

1. 打开命令提示符、终端或其他 shell，并使用以下 [Azure CLI](/cli/?view=azure-cli-latest) 命令对包含 Azure 机器学习工作区的 Azure 订阅进行身份验证：

    ```azurecli
    az login
    ```

1. 若要对包含映像的 Azure 容器注册表 (ACR) 进行身份验证，请使用以下命令。 将 `myregistry` 替换为注册映像时返回的内容：

    ```azurecli
    az acr login --name myregistry
    ```

1. 若要将映像下载到本地 Docker，请使用以下命令。 将 `myimagepath` 替换为注册映像时返回的位置：

    ```bash
    docker pull myimagepath
    ```

    映像路径应与 `myregistry.azurecr.io/myimage:1` 类似。 如果注册表为 `myregistry`，则映像为 `myimage`，映像版本为 `1`。

    > [!TIP]
    > 上一个步骤中的身份验证不会一直持续。 如果在身份验证命令和 pull 命令之间等待时间过长，则会收到身份验证失败。 如果发生该情况，请重新执行身份验证。

    完成下载所用的时间取决于 Internet 连接的速度。 在此过程中会显示下载状态。 下载完成后，可以使用 `docker images` 命令验证是否已下载成功。

1. 如果希望简化映像的操作，可使用以下命令添加标记。 将 `myimagepath` 替换为步骤 2 中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    对于剩下的步骤，可以将本地映像作为 `debug:1` 而不是完整的映像路径值来引用。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果在 `score.py` 文件中为 PTVSD 连接设置超时，则必须在超时到达之前将 VS Code 连接到调试会话。 启动 VS Code，打开 `score.py` 的本地副本，设置一个断点，使其准备就绪，然后再使用本部分中的步骤进行操作。
>
> 有关调试和设置断点的详细信息，请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像启动 Docker 容器，请使用以下命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要将 VS Code 附加到容器中的 PTVSD，请打开 VS Code 并按 F5 或选择“调试”  。 出现提示时，请选择“Azure 机器学习:  Docker 调试”配置。 还可以选择左侧栏中的“调试”图标，“Azure 机器学习:  Docker 调试”项（位于“调试”下拉菜单），然后使用绿色箭头附加调试器。

    ![“调试”图标、“启动调试”按钮和“配置”选择器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此时，VS Code 会连接到 Docker 容器内的 PTVSD，并在之前设置的断点处停止。 现在可以在代码运行时逐句调试代码、查看变量等。

有关使用 VS Code 调试 Python 的详细信息，请参阅[调试 Python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器文件

若要更改映像中的文件，可以附加到正在运行的容器，并执行 bash shell。 你可以在此使用 vim 编辑文件：

1. 若要连接正在运行的容器，并在容器中启动 bash shell，请使用以下命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要查找服务使用的文件，请在容器中使用 bash shell 的以下命令（如果默认目录与 `/var/azureml-app` 不同）：

    ```bash
    cd /var/azureml-app
    ```

    你可以在此使用 vim 编辑 `score.py` 文件。 有关使用 vim 的详细信息，请参阅[使用 Vim 编辑器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 通常不会保留容器的更改。 若要保存所做的任何更改，请在退出前面步骤中（即，另一个 shell 中）启动的 shell 之前，使用以下命令：

    ```bash
    docker commit debug debug:2
    ```

    此命令创建一个名为 `debug:2` 的新映像，其中包含你的编辑内容。

    > [!TIP]
    > 需要停止当前容器并开始使用新版本，更改才会生效。

1. 请确保将对容器中的文件所做的更改与 VS Code 使用的本地文件保持同步。 否则，调试器体验将达不到预期效果。

### <a name="stop-the-container"></a>停止容器

若要停止容器，请使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)