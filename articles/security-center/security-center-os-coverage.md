---
title: Azure Security Center에서 지원하는 기능 및 플랫폼 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지원하는 기능 및 플랫폼 목록을 제공합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319985"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center에서 지원하는 기능 및 플랫폼

클래식 및 리소스 관리자 배포 모델을 둘 다 사용하여 생성된 VM(가상 머신) 및 컴퓨터에 대해 보안 상태 모니터링과 권장 사항이 제공됩니다.

> [!NOTE]
> Azure 리소스의 [클래식 및 리소스 관리자 배포 모델](../azure-classic-rm.md) 에 대해 자세히 알아봅니다.
>
>

## <a name="supported-platforms"></a>지원되는 플랫폼 

이 섹션에서는 Azure Security Center 에이전트를 실행하고 데이터를 수집할 수 있는 플랫폼을 나열합니다.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 컴퓨터 및 VM에 대해 지원되는 플랫폼
지원되는 Windows 운영 체제:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 컴퓨터 및 VM에 대해 지원되는 플랫폼
지원되는 Linux 운영 체제:

* Ubuntu 버전 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian 버전 6, 7, 8, 9
* CentOS 버전 5, 6, 7
* RHEL(Red Hat Enterprise Linux) 버전 5, 6, 7
* SLES(SUSE Linux Enterprise Server) 버전 11, 12
* Oracle Linux 버전 5, 6, 7
* Amazon Linux 2012.09~2017
* Openssl 1.1.0은 x86_64 플랫폼(64 비트)에서만 지원

> [!NOTE]
> Linux 운영 체제에서는 아직 가상 컴퓨터 동작 분석을 사용할 수 없습니다.
>
>

## <a name="vms-and-cloud-services"></a>VM 및 Cloud Services
클라우드 서비스에서 실행 중인 VM도 지원됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.


## <a name="supported-iaas-features"></a>지원되는 IaaS 기능

> [!div class="mx-tableFixed"]
> 

|서버|Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|비 Azure|Azure|비 Azure|
|VMBA 위협 검색 경고|✔|✔|✔(지원되는 버전에서만)|✔|
|네트워크 기반 위협 검색 경고|✔|X|✔|X|
|Windows Defender ATP 통합*|✔(지원되는 버전에서만)|✔|X|X|
|누락된 패치|✔|✔|✔|✔|
|보안 구성|✔|✔|✔|✔|
|맬웨어 방지|✔|✔|X|X|
|JIT VM 액세스|✔|X|✔|X|
|적응 응용 프로그램 컨트롤|✔(Azure만)|X|X|X|
|FIM|✔|✔|✔|✔|
|디스크 암호화|✔|X|✔|X|
|타사 배포|✔|X|✔|X|
|NSG|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|네트워크 맵|✔|X|✔|X|
|적응형 네트워크 강화|✔|X|✔|X|

* 이러한 기능은 현재 공개 미리 보기로 지원됩니다.


## <a name="supported-paas-features"></a>지원되는 PaaS 기능


|서비스|권장 사항|위협 감지|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Blob 저장소 계정*|✔| ✔|
|App Services|✔| ✔|
|클라우드 서비스|✔| X|
|Redis Cache|✔| X|
|Service Fabric|✔| X|
|Azure Automation|✔| X|
|데이터 레이크 |✔| X|
|주요 자격 증명 모음|✔| X|
|Service Bus|✔| X|
|Stream Analytics|✔| X|
|Batch|✔| X|
|논리 앱|✔| X|
|Vnets|✔| 해당 없음|
|서브넷|✔| 해당 없음|
|NIC|✔| ✔|
|NSG|✔| 해당 없음|
|구독|✔| ✔|

* 이러한 기능은 현재 공개 미리 보기로 지원됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center의 계획 및 운영 가이드](security-center-planning-and-operations-guide.md) — 디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택하는 방법을 알아봅니다.
- [Azure Security Center에서 유형별 보안 경고](security-center-alerts-type.md#virtual-machine-behavioral-analysis) - Security Center에서 가상 컴퓨터 동작 분석 및 크래시 덤프 메모리 분석에 대해 자세히 알아봅니다.
- [Azure Security Center FAQ](security-center-faq.md) — 서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) — Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
