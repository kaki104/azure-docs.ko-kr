---
title: Azure Digital Twins 공개 미리 보기 서비스 제한 | Microsoft Docs
description: Azure Digital Twins 공개 미리 보기 서비스 제한 이해
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212270"
---
# <a name="public-preview-service-limits"></a>공개 미리 보기 서비스 제한

**공개 미리 보기** 중에 Azure Digital Twins에는 아래에 설명된 임시 구독, 인스턴스 및 요금 제한이 있습니다.

이러한 제약 조건은 새 서비스 및 서비스의 다양한 기능에 대한 학습을 단순화하는 데 도움이 되도록 존재합니다.

> [!NOTE]
> 이러한 제한은 **GA**(**일반 공급**)와 함께 많아지거나 제거됩니다.

## <a name="per-subscription-limits"></a>구독당 제한

**공개 미리 보기** 중에 각 Azure 구독에서는 한 번에 정확히 하나의 Azure Digital Twins 인스턴스를 만들거나 실행할 수 있습니다.

> [!TIP]
> 인스턴스를 삭제하면 새 인스턴스를 만들 수 있습니다.

## <a name="per-instance-limits"></a>인스턴스당 제한

차례로 각 Azure Digital Twins 인스턴스에는 다음이 포함될 수 있습니다.

- 하나의 **IoTHub** 리소스
- 이벤트 유형 **DeviceMessage**에 대한 하나의 **EventHub** 엔드포인트
- 이벤트 유형 **SensorChange**, **SpaceChange**, **TopologyOperation** 또는 **UdfCustom**의 최대 3개의 **EventHub**, **ServiceBus** 또는 **EventGrid** 엔드포인트

## <a name="management-api-limits"></a>관리 API 제한

관리 API의 요청 빈도 제한은 다음과 같습니다.

- 관리 API에 대한 초당 100개의 요청
- 단일 관리 API 쿼리는 최대 1,000개의 개체를 반환할 수 있음

> [!IMPORTANT]
> 1000개 개체 제한을 초과하면 오류가 수신되며 쿼리를 단순화해야 합니다.

## <a name="udf-rate-limits"></a>UDF 빈도 제한

다음 제한은 Azure Digital Twins 인스턴스에 요청한 모든 사용자 정의 함수 호출의 총 수를 설정합니다.

- 초당 400개의 클라이언트 라이브러리 호출
- 초당 100개의 **SendNotification** 호출

> [!NOTE]
> 다음 작업을 수행하면 임시로 추가 빈도 제한이 적용될 수 있습니다.
> - 토폴로지 개체 메타데이터 편집
> - UDF 정의 업데이트
> - 장치에서 처음으로 원격 분석 전송

## <a name="device-telemetry-limits"></a>장치 원격 분석 제한

아래 제한은 장치가 Azure Digital Twins 인스턴스에 보낼 수 있는 모든 메시지의 총 수를 제한합니다.

- 초당 100개의 메시지

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 샘플을 사용하려면 [사용 가능한 회의실을 찾는 빠른 시작](./quickstart-view-occupancy-dotnet.md)으로 이동하세요.