---
title: '자습서: Azure Machine Learning Service로 이미지 분류 모델 학습시키기'
description: 이 자습서에서는 Azure Machine Learning 서비스를 사용하여 Python Jupyter 노트북의 scikit-learn을 통해 이미지 분류 모델을 학습하는 방법을 보여 줍니다. 이 자습서는 2부로 구성된 시리즈 중 제1부입니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e6e49a03ee76c50cb2fff492bfd50b2820abafe4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343761"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning-service"></a>자습서 1: Azure Machine Learning Service로 이미지 분류 모델 학습시키기

이 자습서에서는 로컬로 및 원격 계산 리소스에 대해 Machine Learning 모델을 학습합니다. Python Jupyter 노트북에서 Azure Machine Learning 서비스(미리 보기)에 대한 학습 및 배포 워크플로를 사용합니다.  그런 다음, 이 노트를 템플릿으로 사용하여 자신의 데이터로 고유한 Machine Learning 모델을 학습할 수 있습니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부**입니다.  

이 자습서에서는 Azure Machine Learning Service와 [MNIST](http://yann.lecun.com/exdb/mnist/) 데이터 집합 및 [scikit-learn](http://scikit-learn.org)을 사용하여 간단한 로지스틱 회귀를 학습합니다.  MNIST는 70,000개의 회색조 이미지로 구성된 인기 있는 데이터 집합입니다. 각 이미지는 0-9의 숫자를 나타내는 28x28픽셀의 필기체 숫자입니다. 목표는 지정된 이미지가 나타내는 숫자를 식별하기 위한 다중 클래스 분류자를 만드는 것입니다. 

방법 배우기:

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 인기 있는 scikit-learn Machine Learning 라이브러리를 사용하여 간단한 로지스틱 회귀를 로컬로 학습합니다. 
> * 원격 클러스터에서 여러 모델 학습
> * 교육 결과 검토 및 최상의 모델 등록

모델을 선택하고 배포하는 방법은 나중에 이 [자습서의 제2부](tutorial-deploy-models-with-aml.md)에서 알아봅니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="get-the-notebook"></a>Notebook 가져오기

사용자의 편의를 위해 이 자습서는 [Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)으로 제공됩니다. Azure Notebooks 또는 자체 Jupyter 노트북 서버에서 `01.train-models.ipynb` 노트북을 실행할 수 있습니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> 이 자습서는 Azure Machine Learning SDK 0.168 버전을 사용하여 테스트했습니다. 

## <a name="set-up-your-development-environment"></a>개발 환경 설정

개발 작업에 대한 모든 설정은 Python Notebook에서 수행할 수 있습니다.  설정에는 다음이 포함됩니다.

* Python 패키지 가져오기
* 작업 영역에 연결하여 로컬 컴퓨터와 원격 리소스 간의 통신 설정
* 모든 실행을 추적하는 실험 만들기
* 학습에 사용할 원격 계산 대상 만들기

### <a name="import-packages"></a>패키지 가져오기

이 세션에 필요한 Python 패키지를 가져옵니다. Azure Machine Learning SDK 버전도 표시합니다.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>작업 영역에 연결

기존 작업 영역에서 작업 영역 개체를 만듭니다. `Workspace.from_config()`는 **config.json** 파일을 읽고, 세부 정보를 `ws`라는 개체에 로드합니다.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>실험 만들기

작업 영역에서 실행을 추적하는 실험을 만듭니다. 작업 영역에는 여러 개의 실험이 있을 수 있습니다. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>원격 계산 대상 만들기

Azure Batch AI는 데이터 과학자가 GPU를 지원하는 VM을 포함하여 Azure Virtual Machines 클러스터에서 기계 학습 모델을 학습시킬 수 있게 해주는 관리 서비스입니다.  이 자습서에서는 학습 환경으로 Azure Batch AI 클러스터를 만듭니다. 사용자의 작업 영역에 클러스터가 아직 없으면 이 코드가 새로 만듭니다. 

 **클러스터를 만드는 데 약 5분이 소요됩니다.** 클러스터가 이미 작업 영역에 있는 경우 이 코드는 해당 클러스터를 사용하고 클러스터 생성 프로세스를 건너뜁니다.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if type(compute_target) is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

이제 클라우드에서 모델을 학습하는 데 필요한 패키지 및 계산 리소스가 준비되었습니다. 

## <a name="explore-data"></a>데이터 탐색

모델을 학습하기 전에 이 모델을 학습하는 데 사용할 데이터를 이해해야 합니다.  클라우드 학습 환경에서 액세스할 수 있도록 데이터를 클라우드에 복사해야 합니다.  이 섹션에서는 다음을 수행하는 방법을 알아봅니다.

* MNIST 데이터 집합 다운로드
* 일부 샘플 이미지 표시
* 클라우드에 데이터 업로드

### <a name="download-the-mnist-dataset"></a>MNIST 데이터 집합 다운로드

MNIST 데이터 집합을 다운로드하고 파일을 `data` 디렉터리에 로컬로 저장합니다.  학습 및 테스트용 이미지 및 레이블이 모두 다운로드됩니다.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>일부 샘플 이미지 표시

압축된 파일을 `numpy` 배열로 로드합니다. 그런 다음, `matplotlib`를 사용하여 데이터 집합에 있는 30개의 무작위 이미지를 그리고, 위에 레이블을 표시합니다. 이 단계를 수행하려면 `util.py` 파일에 포함된 `load_data` 함수가 필요합니다. 이 파일은 샘플 폴더에 포함되어 있습니다. 이 노트북과 동일한 폴더에 배치되어 있는지 확인합니다. `load_data` 함수는 압축 파일을 numpy 배열로 구문 분석합니다.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

이미지의 무작위 샘플이 다음과 같이 표시됩니다.

![이미지의 무작위 샘플](./media/tutorial-train-models-with-aml/digits.png)

이제 이러한 이미지의 모양과 예상되는 예측 결과를 이해할 수 있을 것입니다.

### <a name="upload-data-to-the-cloud"></a>클라우드에 데이터 업로드

이제 데이터를 로컬 머신에서 Azure에 업로드하여 원격 학습을 위해 해당 데이터에 액세스할 수 있도록 합니다. 데이터 저장소는 작업 영역과 연결된 편리한 구조로, 데이터를 업로드/다운로드하고 원격 계산 대상으로부터 데이터와 상호 작용할 수 있는 공간입니다. 이는 Azure Blob 저장소 계정에서 지원됩니다.

MNIST 파일은 데이터 저장소의 루트에 있는 `mnist` 디렉터리로 업로드됩니다.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
이제 모델 학습을 시작하는 데 필요한 모든 준비가 갖추어졌습니다. 

## <a name="train-a-model-locally"></a>로컬로 모델 학습

scikit-learn의 간단한 로지스틱 회귀 모델을 로컬로 학습합니다.

**로컬 학습은 컴퓨터 구성에 따라 1~2분이 소요**될 수 있습니다.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

다음으로, 테스트 집합을 사용하여 예측을 만들고 정확도를 계산합니다. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

로컬 모델 정확도가 다음과 같이 표시됩니다.

`0.9202`

몇 줄의 코드만 사용하면 92%의 정확도가 유지됩니다.

## <a name="train-on-a-remote-cluster"></a>원격 클러스터에서 학습

이제 다른 정규화 비율로 모델을 빌드하여 이 단순 모델을 확장할 수 있습니다. 이번에는 원격 리소스에 대해 모델을 학습합니다.  

이 태스크의 경우 이전에 설정한 원격 학습 클러스터로 작업을 제출하세요.  작업을 제출하려면
* 디렉터리 만들기
* 학습 스크립트 만들기
* 추정기 만들기
* 작업 제출 

### <a name="create-a-directory"></a>디렉터리 만들기

필요한 코드를 컴퓨터에서 원격 리소스로 전달하기 위한 디렉터리를 만듭니다.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>학습 스크립트 만들기

클러스터에 작업을 제출하려면 먼저 학습 스크립트를 만듭니다. 다음 코드를 실행하여 방금 만든 디렉터리에 `train.py`라는 학습 스크립트를 만듭니다. 이 학습 스크립트는 학습 알고리즘에 정규화 비율을 추가하여 로컬 버전과는 약간 다른 모델을 생성합니다.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

이 스크립트가 데이터를 가져오고 모델을 저장하는 방법은 다음과 같습니다.

+ 학습 스크립트는 인수를 읽어 데이터를 포함하는 디렉터리를 찾습니다.  나중에 작업을 제출할 때 이 인수에 대한 데이터 저장소를 가리킵니다(`parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`).

    
+ 학습 스크립트는 모델을 outputs 디렉터리에 저장합니다. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
이 디렉터리에 작성된 모든 내용은 작업 영역으로 자동 업로드됩니다. 자습서 뒷부분에서 이 디렉터리의 모델에 액세스하게 됩니다.

`utils.py` 파일은 데이터 집합을 올바르게 로드하기 위해 학습 스크립트에서 참조됩니다.  이 스크립트를 원격 리소스의 학습 스크립트와 함께 액세스할 수 있도록 스크립트 폴더에 복사하세요.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>추정기 만들기

추정기 개체는 실행을 제출하는 데 사용됩니다.  다음 코드로 추정기를 정의하여 만듭니다.

* 추정기 개체의 이름 `est`
* 스크립트를 포함하는 디렉터리. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다. 
* 계산 대상.  이 경우에는 만든 Batch AI 클러스터를 사용합니다.
* 학습 스크립트 이름, train.py
* 학습 스크립트의 필요한 매개 변수 
* 학습에 필요한 Python 패키지

이 자습서에서 이 대상은 Batch AI 클러스터입니다. 프로젝트 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다. data_folder는 데이터 저장소를 사용하도록 설정되어 있습니다(`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>클러스터에 작업 제출

추정기 개체를 제출하여 실험을 실행합니다.

```python
run = exp.submit(config=est)
run
```

호출은 비동기이므로 작업이 시작되는 즉시 **준비 중** 또는 **실행 중** 상태를 반환합니다.

## <a name="monitor-a-remote-run"></a>원격 실행 모니터링

전체적으로 첫 번째 실행은 **약 10분**이 소요됩니다. 그러나 스크립트 종속성이 변경되지 않는 한, 후속 실행에서도 동일한 이미지가 다시 사용되므로 컨테이너가 시작 시간이 훨씬 더 빠릅니다.

대기 중에 발생하는 작업은 다음과 같습니다.

- **이미지 만들기**: 추정기가 지정한 Python 환경과 일치하는 Docker 이미지가 만들어집니다. 이미지는 작업 영역에 업로드됩니다. 이미지 생성 및 업로드는 **약 5분**이 소요됩니다. 

  이 단계는 후속 실행을 위해 컨테이너가 캐시되므로 각 Python 환경에 대해 한 번만 진행됩니다.  이미지 생성 중에 로그가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 이미지 생성 진행 상태를 모니터링할 수 있습니다.

- **크기 조정 중**: 원격 클러스터에서 현재 사용 가능한 것보다 더 많은 노드를 실행해야 하는 경우 추가 노드가 자동으로 추가됩니다. 크기 조정은 일반적으로 **약 5분**이 소요됩니다.

- **실행 중**: 이 단계에서는 필요한 스크립트와 파일이 계산 대상으로 전송된 다음, 데이터 저장소가 탑재/복사되고, entry_script가 실행됩니다. 작업이 실행 중이지만 stdout 및 ./logs 디렉터리가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 실행 진행 상태를 모니터링할 수 있습니다.

- **Post-Processing**: 실행의 ./outputs 디렉터리가 작업 영역의 실행 기록으로 복사되므로 해당 결과에 액세스할 수 있습니다.


실행 중인 작업의 진행 상태를 여러 가지 방법으로 확인할 수 있습니다. 이 자습서에서는 `wait_for_completion` 메서드와 Jupyter 위젯을 사용합니다. 

### <a name="jupyter-widget"></a>Jupyter 위젯

Jupyter 위젯으로 실행의 진행 상태를 감시합니다.  실행 제출과 마찬가지로, 위젯은 비동기적이며 작업이 완료될 때까지 10~15초 간격으로 라이브 업데이트를 제공합니다.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

다음은 학습이 끝난 후에 표시되는 위젯의 스냅숏입니다.

![노트 위젯](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 발생합니다. 추가 코드를 실행하기 전에 모델이 학습을 완료할 때까지 기다립니다. `wait_for_completion`을 사용하여 모델 학습이 완료되는 시간을 표시합니다. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>실행 결과 표시

이제 모델이 원격 클러스터에서 학습을 마쳤습니다.  다음과 같이 모델의 정확도를 확인합니다.

```python
print(run.get_metrics())
```
출력에는 학습 중에 정규화 비율을 추가했으므로 원격 모델이 로컬 모델보다 약간 더 정확한 것으로 나타납니다.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

배포 자습서에서 이 모델을 좀 더 자세히 알아봅니다.

## <a name="register-model"></a>모델 등록

학습 스크립트의 마지막 단계에서는 작업이 실행되는 클러스터의 VM에 있는 `outputs` 디렉터리에 `outputs/sklearn_mnist_model.pkl` 파일을 작성했습니다. `outputs`는 이 디렉터리의 모든 콘텐츠가 작업 영역에 자동으로 업로드된다는 측면에서 특수한 디렉터리입니다.  이 콘텐츠는 작업 영역의 실험 실행 레코드에 표시됩니다. 따라서 이제 작업 영역에서 모델 파일도 사용할 수 있습니다.

해당 실행과 연결된 파일을 볼 수 있습니다.

```python
print(run.get_file_names())
```

나중에 사용자(또는 기타 공동 작업자)가 모델을 쿼리, 검사 및 배포할 수 있도록 작업 영역에 모델을 등록합니다.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Azure 관리 계산 클러스터만 삭제할 수도 있습니다. 그러나 자동 크기 조정이 켜져 있고 클러스터 최소값이 0이므로 이 특정 리소스는 사용되지 않을 때 추가 계산 비용이 발생하지 않습니다.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>다음 단계

이 Azure Machine Learning Service 자습서에서는 Python을 사용하여 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 인기 있는 scikit-learn Machine Learning 라이브러리를 사용하여 간단한 로지스틱 회귀를 로컬로 학습합니다.
> * 원격 클러스터에서 여러 모델 학습
> * 교육 세부 정보 검토 및 최상의 모델 등록

자습서 시리즈의 다음 부분에 제공되는 지침에 따라 이 등록된 모델을 배포할 준비가 되었습니다.

> [!div class="nextstepaction"]
> [자습서 2 - 모델 배포](tutorial-deploy-models-with-aml.md)
