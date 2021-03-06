---
title: Azure Stack의 상태 및 경고 모니터링 | Microsoft Docs
description: Azure Stack에서 상태 및 경고를 모니터링하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2018
ms.author: mabrigg
ms.openlocfilehash: 69ed08e8f6c820790c432bfa25988e038fd0efbd
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379692"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Azure Stack의 상태 및 경고 모니터링

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 인프라 모니터링 상태 및 Azure stack에 대 한 경고를 볼 수 있도록 하는 기능을 포함 합니다. 합니다 **하위 지역 관리** 기본 공급자 구독에 대 한 관리자 포털에서 기본적으로 고정 된 타일 Azure Stack의 배포 된 모든 영역을 나열 합니다. 타일 각 영역에 대 한 활성 위험 및 경고 경고 수가 표시 되며 상태 및 경고 기능의 Azure Stack에 진입점.

 ![지역 관리 타일](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Azure Stack의 상태 이해

 상태 및 경고 상태 리소스 공급자에 의해 관리 됩니다. Azure Stack 구성 요소는 Azure Stack이 배포 및 구성되는 동안 상태 리소스 공급자에 등록합니다. 이 등록 상태 및 각 구성 요소에 대 한 경고의 표시할 수 있습니다. Azure Stack의 상태는 간단한 개념입니다. 등록된 구성 요소 인스턴스가 있으면 해당 구성 요소의 상태에 최악의 활성 경고 심각도인 경고 또는 위험이 반영됩니다.

## <a name="alert-severity-definition"></a>경고 심각도 정의

Azure Stack의 심각도 두 개의 경고 발생: **경고** 하 고 **중요 한**합니다.

- **Warning**  
  예약 된 방식으로 운영자는 경고를 해결할 수 있습니다. 경고는 일반적으로 영향을 주지 않습니다 사용자 워크 로드.

- **중요**  
  운영자 긴급도 사용 하 여 중요 한 경고를 해결 해야 합니다. 이러한 사항은 현재 영향을 줄 또는 Azure Stack 사용자를 곧 영향을 줍니다. 

 
 ## <a name="view-and-manage-component-health-state"></a>보기 및 구성 요소 상태 관리
 
 Azure Stack 운영자, 관리자 포털 및 REST API 및 PowerShell을 통해 구성 요소의 상태를 볼 수 있습니다.
 
포털에서 상태를 보려는 원하는 영역을 클릭 합니다 **하위 지역 관리** 바둑판식으로 배열 합니다. 리소스 공급자 및 인프라 역할의 상태를 볼 수 있습니다.

![인프라 역할 목록](media/azure-stack-monitor-health/image2.png)

자세한 정보를 보려는 리소스 공급자 또는 인프라 역할을 클릭 수 있습니다.

> [!WARNING]  
> 인프라 역할을 클릭 하 고 역할 인스턴스를 클릭 하는 경우에 시작, 다시 시작 하는 옵션 또는 종료 가지 있습니다. 통합된 시스템에 업데이트를 적용 하는 경우에 이러한 작업을 사용 하지 마세요. 또한 수행 **되지** Azure Stack 개발 키트 환경에서 이러한 옵션을 사용 합니다. 이러한 옵션은 통합된 시스템 환경에 대해서만 설계 된 인프라 역할에 따라 둘 이상의 역할 인스턴스가 있는 합니다. 개발 키트에서 (특히 AzS Xrp01) 역할 인스턴스를 다시 시작 하면 시스템이 불안정 합니다. 지원 문제를 해결 하려면 문제를 게시 합니다 [Azure Stack 포럼](https://aka.ms/azurestackforum)합니다.
>
 
## <a name="view-alerts"></a>경고 보기

각 Azure stack에 대 한 활성 경고 목록은에서 직접 사용할 수는 **하위 지역 관리** 블레이드입니다. 기본 구성에서 첫 번째 타일은는 **경고** 타일에 지역에 대 한 경고 및 중요 한의 요약을 표시 합니다. 대시보드에 빠른 액세스를 위해이 블레이드의 다른 타일과 마찬가지로 경고 타일을 고정할 수 있습니다.   

![경고를 표시하는 경고 타일](media/azure-stack-monitor-health/image3.png)

상단 부분을 선택 하 여 합니다 **경고** 타일을 이동 하면 지역에 대 한 모든 활성 경고의 목록입니다. 타일 내에서 **위험** 또는 **경고** 줄 항목을 선택하면 필터링된 경고 목록(위험 또는 경고)으로 이동됩니다. 

![필터링된 경고 알림](media/azure-stack-monitor-health/image4.png)
  
합니다 **경고** 블레이드에서 상태 (활성 또는 닫힘) 및 심각도 (위험 또는 경고)를 기준으로 필터링 하는 기능을 지원 합니다. 기본 보기에는 모든 활성 경고가 표시 됩니다. 모든 종결된 경고는 7일 후 시스템에서 제거됩니다.

![위험 또는 경고 상태로 필터링하는 필터 창](media/azure-stack-monitor-health/image5.png)

합니다 **보기 API** 작업 목록 보기를 생성 하는 데 사용 된 REST API를 표시 합니다. 이 작업에는 쿼리 경고에 사용할 수 있는 REST API 구문을 사용 하 여 자세히 알아보는 빠른 방법을 제공 합니다. 기존 데이터 센터 모니터링, 보고 및 티켓팅 솔루션을 사용 하 여 통합 또는 automation에서이 API를 사용할 수 있습니다. 

![REST API를 보여주는 보기 API 옵션](media/azure-stack-monitor-health/image6.png)

경고 세부 정보를 보려면 특정 경고를 클릭할 수 있습니다. 경고 세부 정보는 경고와 관련 되 고 영향을 받는 구성 요소 및 경고의 원본 빠른 탐색을 사용 하도록 설정 된 모든 필드를 보여 줍니다. 예를 들어, 다음 경고에는 인프라 역할 인스턴스 중 하나가 오프 라인 상태가 되거나 액세스할 수 없는 경우 발생 합니다.  

![경고 세부 정보 블레이드](media/azure-stack-monitor-health/image7.png)

인프라 역할 인스턴스가 다시 온라인 상태가 되 면이 경고가 자동으로 닫힙니다. 아니지만 많은 일부 경고는 문제가 해결 되 면 자동으로 닫힙니다. 선택 하는 것이 좋습니다 **경고 닫기** 수정 단계를 수행 합니다. 문제가 지속 되 면 Azure Stack에 새 경고를 생성 합니다. 이 문제를 해결 하는 경우 경고 닫힌 상태를 유지 하 고 추가 조치가 필요 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack에서 업데이트 관리](azure-stack-updates.md)

[Azure Stack의 지역 관리](azure-stack-region-management.md)
