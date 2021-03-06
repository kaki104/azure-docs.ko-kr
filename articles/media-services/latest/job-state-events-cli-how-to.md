---
title: CLI를 사용하여 Event Grid에서 Azure Media Services 이벤트 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Media Services 이벤트를 모니터링하기 위해 Event Grid를 구독하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 8145b4eb3c39511eb9cd0ed052c36b8338191d4f
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389499"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Azure CLI를 사용하여 Event Grid에서 Media Services 이벤트 만들기 및 모니터링

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure CLI를 사용하여 Azure Media Services 계정에 대한 이벤트를 구독합니다. 그런 다음, 이벤트를 트리거하여 결과를 봅니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 이 문서에서는 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

## <a name="prerequisites"></a>필수 조건

- 활성 Azure 구독
- [Media Services 계정 만들기](create-account-cli-how-to.md)

    리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

Media Services 계정에 대한 이벤트를 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 문서에서는 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. **Azure에 배포**를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

"Azure Event Grid 뷰어" 사이트로 전환하더라도 이벤트가 아직 표시되지 않습니다.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

다음 단계와 같이[Azure Portal](http://portal.azure.com)에 로그인하고 **CloudShell**을 시작하여 CLI명령을 실행합니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="set-the-azure-subscription"></a>Azure 구독 설정

다음 명령에서 Media Services 계정에 사용할 Azure 구독 ID를 제공합니다. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동하면 액세스 권한이 있는 구독 목록을 볼 수 있습니다.

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

## <a name="subscribe-to-media-services-events"></a>Media Services 이벤트 구독

추적하려는 이벤트를 Event Grid에 알리도록 문서를 구독합니다. 다음 예제에서는 사용자가 만든 Media Services 계정을 구독하고 이벤트 알림에 대한 엔드포인트로 만든 웹 사이트의 URL을 전달합니다. 

`<event_subscription_name>`을 이벤트 구독의 고유 이름으로 바꿉니다. `<resource_group_name>` 및 `<ams_account_name>`에는 Media Services 계정을 만들 때 사용한 값을 사용합니다. `<endpoint_URL>`의 경우 웹앱의 URL을 제공하고, 홈 페이지 URL에 `api/updates`를 추가합니다. 구독할 때 엔드포인트를 지정하면 Event Grid에서 해당 엔드포인트로의 이벤트 라우팅을 처리합니다. 

1. 리소스 ID를 가져옵니다.

    ```azurecli-interactive
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    예: 

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. 이벤트를 구독합니다.

    ```azurecli-interactive
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    예: 

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > 유효성 검사 핸드셰이크 경고가 발생할 수 있습니다. 잠시 시간을 내어 핸드셰이크의 유효성을 검사해야 합니다.

이제 이벤트를 트리거하여 Event Grid에서 메시지를 사용자 엔드포인트에 배포하는 방법을 살펴보겠습니다.

## <a name="send-an-event-to-your-endpoint"></a>엔드포인트에 이벤트 보내기

인코딩 작업을 실행하여 Media Services 계정에 대한 이벤트를 트리거할 수 있습니다. [이 빠른 시작](stream-files-dotnet-quickstart.md)에 따라 파일을 인코딩하고 이벤트 보내기를 시작할 수 있습니다. 

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 엔드포인트에서 `validationResponse`를 `validationCode`로 설정해야 합니다. 자세한 내용은 [Event Grid 보안 및 인증](../../event-grid/security-authentication.md)을 참조하세요. 웹앱 코드를 보고 구독의 유효성을 검사하는 방법을 확인할 수 있습니다.

> [!TIP]
> 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. 모든 이벤트를 보려면 페이지를 새로 고치지 마세요.

![구독 이벤트 보기](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>다음 단계

[업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

