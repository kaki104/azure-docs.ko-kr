---
title: Azure의 자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기) | Microsoft Docs
description: 이 문서에서는 Azure Portal을 사용하는 Application Gateway의 웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록에 대해 설명합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: victorh
ms.openlocfilehash: 8fb3dce108b59b8df0d330ec642365d2487eae35
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085464"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)

Application Gateway 및 WAF(웹 응용 프로그램 방화벽)는 새 v2 SKU에서 성능을 향상하고 자동 크기 조정, 영역 중복, 정적 VIP 지원 등의 중요한 새 기능을 지원하는 공개 미리 보기로 제공됩니다. 일반 공급 SKU의 기존 기능은 새 v2 SKU에서 계속 지원되며, 알려진 제한 사항 섹션에 몇 가지 예외가 나열되어 있습니다. 새 v2 SKU에는 다음과 같은 향상된 기능이 포함되어 있습니다.

- **자동 크기 조정**: 자동 크기 조정 SKU의 Application Gateway 또는 WAF 배포는 트래픽 부하 패턴의 변화에 따라 강화 또는 축소할 수 있습니다. 또한 자동 크기 조정을 사용하면 프로비전 시 배포 크기 또는 인스턴스 수를 선택할 필요가 없습니다. 따라서 SKU가 진정한 탄력성을 제공합니다. 새 SKU에서, Application Gateway는 자동 크기 조정이 설정된 모드뿐 아니라 고정 용량(자동 크기 조정이 해제됨)에서도 작동할 수 있습니다. 고정 용량 모드는 워크로드가 일관적이고 예측 가능한 시나리오에 유용합니다. 자동 크기 조정 모드는 응용 프로그램 트래픽이 자주 변하는 응용 프로그램에 유용합니다.
   
   > [!NOTE]
   > 현재 WAF SKU에는 자동 크기 조정을 사용할 수 없습니다. 자동 크기 조정 모드 대신 고정 용량 모드를 사용하여 WAF를 구성합니다.
- **영역 중복**: Application Gateway 또는 WAF 배포가 여러 가용성 영역으로 확장될 수 있으므로 Traffic Manager를 사용하여 각 영역에 별도의 Application Gateway 인스턴스를 프로비전하여 작동할 필요가 없습니다. Application Gateway 인스턴스가 배포되는 단일 영역 또는 여러 영역을 선택할 수 있기 때문에 영역 장애 복원력이 보장됩니다. 응용 프로그램에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.
- **성능 향상**: 자동 크기 조정 SKU는 일반적으로 제공되는 SKU보다 최대 5배 높은 SSL 오프로드 성능을 제공합니다.
- **신속한 배포 및 업데이트 시간**: 자동 크기 조정 SKU는 일반적으로 제공되는 SKU보다 빠른 배포 및 업데이트 시간을 제공합니다.
- **정적 VIP**: 응용 프로그램 게이트웨이 VIP가 이제 정적 VIP 유형만 독점적으로 지원합니다. 따라서 재시작 후에도 응용 프로그램 게이트웨이와 연결된 VIP가 변하지 않습니다.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>지원되는 지역
자동 크기 조정 SKU는 미국 동부 2, 미국 중부, 미국 서부 2, 프랑스 중부, 유럽 서부 및 동남 아시아에서 사용할 수 있습니다.

## <a name="pricing"></a>가격
미리 보기 중에는 무료입니다. 응용 프로그램 게이트웨이가 아닌 리소스(예: Key Vault, 가상 머신 등)에 대한 요금은 청구됩니다. 

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

|문제|세부 정보|
|--|--|
|인증 인증서|지원되지 않습니다.<br>자세한 내용은 [Application Gateway의 엔드투엔드 SSL 개요](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)를 참조하세요.|
|동일한 서브넷에서 Standard_v2와 표준 응용 프로그램 게이트웨이 혼합|지원되지 않습니다.<br>또한 자동 크기 조정을 사용하도록 설정하는 경우 하나의 서브넷에 응용 프로그램 게이트웨이 하나만 있을 수 있습니다.|
|Application Gateway 서브넷의 UDR(사용자 정의 경로)|지원되지 않음|
|인바운드 포트 범위에 대한 NSG| - 65200 ~ 65535(Standard_v2 SKU)<br>- 65503 ~ 65534(Standard SKU)<br>자세한 내용은 [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)을 참조하세요.|
|Azure 진단의 성능 로그|지원되지 않습니다.<br>Azure 메트릭을 사용해야 합니다.|
|결제|현재는 요금이 청구되지 않습니다.|
|FIPS 모드, WebSocket|현재는 지원되지 않습니다.|
|ILB 전용 모드|현재는 지원되지 않습니다. 공용 및 ILB 모드가 함께 지원됩니다.|
|웹 응용 프로그램 방화벽 자동 크기 조정|WAF는 자동 크기 조정 모드를 지원하지 않습니다. 고정 용량 모드가 지원됩니다.|

## <a name="next-steps"></a>다음 단계
- [Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 응용 프로그램 게이트웨이 만들기](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)에 대해 자세히 알아보세요.
- [Azure Firewall](../firewall/overview.md)에 대해 자세히 알아보세요. 

