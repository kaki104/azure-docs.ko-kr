---
title: 컨테이너용 Azure Monitor(미리 보기) 등록 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너의 성능과 어떤 성능 관련 문제가 확인되었는지 이해할 수 있도록 컨테이너용 Azure Monitor를 등록하고 구성하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: 2b7045f74a22732337ceb8dc9136da1c93ee7c2c
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037790"
---
# <a name="how-to-onboard-azure-monitor-for-containers-preview"></a>컨테이너용 Azure Monitor(미리 보기) 등록 방법 
이 문서에서는 컨테이너용 Azure Monitor를 설정하여 Kubernetes 환경에 배포되고 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)에서 호스트되는 워크로드의 성능을 모니터링하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건 
시작하기 전에 다음 항목이 있는지 확인하십시오.

- 새 또는 기존 AKS 클러스터.
- Linux용 컨테이너화된 Log Analytics 에이전트 microsoft/oms:ciprod04202018 이상의 버전. 버전 번호는 *mmddyyyy* 형식의 날짜로 표시됩니다. 이 에이전트는 이 기능을 등록하는 동안 자동으로 설치됩니다. 
- Log Analytics 작업 영역. 새 AKS 클러스터 모니터링을 사용하도록 설정할 때 만들거나 온보드 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들도록 할 수 있습니다. 직접 만들려면 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)를 통해 만들거나 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 만들 수 있습니다.
- 컨테이너 모니터링을 사용하도록 설정하기 위한 Log Analytics 기여자 역할. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../log-analytics/log-analytics-manage-access.md)를 참조하세요.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>구성 요소 

성능을 모니터링하는 기능은 컨테이너화된 Linux용 Log Analytics 에이전트에 의존합니다. 이를 통해 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집합니다. 이 에이전트는 컨테이너 모니터링을 사용하도록 설정한 후에 지정된 Log Analytics 작업 영역에 자동으로 배포되고 등록됩니다. 

>[!NOTE] 
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다. 템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다. 

## <a name="enable-monitoring-for-a-new-cluster"></a>새 클러스터에 대한 모니터링 사용
배포하는 동안 Azure Portal에서 또는 Azure CLI를 사용하여 새 AKS 클러스터의 모니터링을 사용하도록 설정할 수 있습니다. 포털에서 사용하도록 설정하려면 빠른 시작 문서 [AKS(Azure Kubernetes Service) 클러스터 배포](../aks/kubernetes-walkthrough-portal.md)의 단계를 따르세요. **모니터링** 페이지에서 **모니터링 사용** 옵션에 대해 **예**를 선택한 다음, 기존 Log Analytics 작업 영역을 선택하거나 새로 만듭니다. 

Azure CLI로 만든 새로운 AKS 클러스터에 대한 모니터링을 활성화하려면 [AKS 클러스터 만들기](../aks/kubernetes-walkthrough.md#create-aks-cluster) 섹션 아래 빠른 시작 문서의 단계를 수행하세요.  

>[!NOTE]
>Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.43 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 
>

모니터링을 사용하도록 설정하고 모든 구성 작업이 성공적으로 완료되면 다음 두 가지 방법 중 하나로 클러스터의 성능을 모니터링할 수 있습니다.

* 왼쪽 창에서 **상태**를 선택하여 AKS 클러스터에서 직접 모니터링합니다.
* 선택한 클러스터에 대한 AKS 클러스터 페이지에서 **컨테이너 정보 모니터링** 타일을 선택하고 Azure Monitor의 왼쪽 창에서 **상태**를 선택합니다. 

  ![AKS의 컨테이너에 대한 Azure Monitor 선택용 옵션](./media/monitoring-container-insights-onboard/kubernetes-select-monitoring-01.png)

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>관리되는 기존 클러스터에 대해 모니터링 사용
이미 배포된 AKS 클러스터의 모니터링은 Azure CLI를 사용하여 사용하도록 설정하거나 포털에서 사용하도록 설정하거나 제공된 Azure Resource Manager 템플릿으로 PowerShell cmdlet `New-AzureRmResourceGroupDeployment`를 사용하여 사용하도록 설정할 수 있습니다. 

### <a name="enable-monitoring-using-azure-cli"></a>Azure CLI를 사용하여 모니터링을 사용하도록 설정
다음 단계에서는 Azure CLI를 사용하여 AKS 클러스터의 모니터링을 사용하도록 설정합니다. 이 예제에서는 기존 작업 영역을 미리 만들거나 지정할 필요가 없습니다. 이 명령은 해당 지역에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 공간이 아직 없는 경우 기본 작업 공간을 만들어서 프로세스를 간소화합니다.  만들어진 기본 작업 영역은 *DefaultWorkspace-<GUID>-<Region>* 의 형식과 유사합니다.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

출력은 다음과 유사합니다.

```azurecli
provisioningState       : Succeeded
```

기존 작업 영역과 통합하려는 경우 다음 명령을 사용하여 해당 작업 영역을 지정합니다.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

출력은 다음과 유사합니다.

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-from-azure-monitor"></a>Azure Monitor에서 모니터링 사용
Azure Monitor의 Azure Portal에서 AKS 클러스터의 모니터링을 사용하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모니터**를 선택합니다. 
2. 목록에서 **컨테이너(미리 보기)** 를 선택합니다.
3. **모니터 - 컨테이너(미리 보기)** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.
4. 모니터링되지 않는 클러스터 목록에서 이 컨테이너를 찾아 **사용**을 클릭합니다.   
5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너 상태 및 로그에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 AKS 컨테이너가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다. 

    ![AKS 컨테이너 정보 모니터링 사용](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-create-workspace.md)를 참조하세요. AKS 컨테이너가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다. 
 
모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>포털의 AKS 클러스터에서 모니터링 사용
Azure Portal에서 AKS 컨테이너에 대한 모니터링을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 
2. 리소스 목록에서 **컨테이너** 입력을 시작합니다.  
    입력한 내용을 기반으로 목록이 필터링됩니다. 
3. **Kubernetes 서비스**를 선택합니다.  

    ![Kubernetes 서비스 링크](./media/monitoring-container-insights-onboard/portal-search-containers-01.png)

4. 컨테이너 목록에서 컨테이너를 선택합니다.
5. 컨테이너 개요 페이지에서 **컨테이너 상태 모니터링**을 선택합니다.  
6. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너 상태 및 로그에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 AKS 컨테이너가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다. 

    ![AKS 컨테이너 상태 모니터링 사용](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-create-workspace.md)를 참조하세요. AKS 컨테이너가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다. 
 
모니터링을 사용하도록 설정한 후 약 15분 후에 클러스터에 대한 운영 데이터를 볼 수 있습니다. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 모니터링 사용
이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

* AKS 컨테이너 리소스 ID. 
* 클러스터가 배포된 리소스 그룹.
* Log Analytics 작업 영역 및 작업 영역을 만들 지역. 

>[!NOTE]
>템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.
>

Log Analytics 작업 영역은 수동으로 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)나 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 설정할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

#### <a name="create-and-execute-a-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]", 
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. 이 파일을 **existingClusterOnboarding.json**으로 로컬 폴더에 저장합니다.
3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. AKS 클러스터에 대한 **AKS 개요** 페이지에서 찾을 수 있는 값을 사용하여 **aksResourceId** 및 **aksResourceLocation**의 값을 편집합니다. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다. 또한 **workspaceRegion**의 작업 영역 위치를 지정합니다. 
5. 이 파일을 **existingClusterParam.json**으로 로컬 폴더에 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용합니다.

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

        ```powershell
        provisioningState       : Succeeded
        ```

    * Azure CLI를 사용하여 다음 명령을 실행하려면:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

        ```azurecli
        provisioningState       : Succeeded
        ```
모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

## <a name="verify-agent-and-solution-deployment"></a>에이전트 및 솔루션 배포 확인
에이전트 *06072018* 또는 이후 버전을 사용하여 에이전트 및 솔루션이 모두 성공적으로 배포되었는지 확인할 수 있습니다. 이전 버전의 에이전트를 사용하면 에이전트 배포만 확인할 수 있습니다.

### <a name="agent-version-06072018-or-later"></a>에이전트 06072018 또는 이후 버전
다음 명령을 실행하여 에이전트가 성공적으로 배포되었는지 확인합니다. 

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

솔루션의 배포를 확인하려면 다음 명령을 실행합니다.

```
kubectl get deployment omsagent-rs -n=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 이전 에이전트 버전

*06072018* 이전에 릴리스된 Log Analytics 에이전트 버전이 제대로 배포되었는지 확인하려면 다음 명령을 실행합니다.  

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLI로 구성 보기
`aks show` 명령을 사용하면 솔루션을 사용하도록 설정되어 있는지 여부, Log Analytics 작업 영역 resourceID, 클러스터에 대한 요약 정보와 같은 세부 정보를 얻을 수 있습니다.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

몇 분 후 명령이 완료되면 솔루션에 대한 JSON 형식 정보가 반환됩니다.  명령의 결과에는 모니터링 추가 항목 프로필이 표시되며 다음 예제 출력과 유사합니다.

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>다음 단계

* 솔루션을 등록하는 동안 문제가 발생하는 경우 [문제 해결 가이드](monitoring-container-insights-troubleshoot.md)를 검토하세요.

* 모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](monitoring-container-insights-analyze.md)를 참조하세요.