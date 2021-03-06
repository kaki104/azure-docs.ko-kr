---
title: Azure Active Directory 보고 FAQ | Microsoft Docs
description: Azure Active Directory 보고 FAQ입니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c2e131776fb9f2c5e87c363d0d57e655bc34448f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294223"
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory 보고 FAQ

이 문서에는 Azure AD(Azure Active Directory) 보고에 대한 질문과 대답이 포함되어 있습니다. 자세한 내용은 [Azure Active Directory 보고](overview-reports.md)를 참조하세요. 

## <a name="getting-started"></a>시작 

**Q: https://graph.windows.net/&lt;tenant-name&gt;/reports/ 엔드포인트 API를 사용하여 Azure AD 감사 및 통합 응용 프로그램 사용 보고서를 프로그래밍 방식으로 보고 시스템에 끌어오고 있습니다. 어떤 방식으로 전환해야 하나요?**

**A:** [API 참조 설명서](https://developer.microsoft.com/graph/)에서 새로운 API를 사용하여 [작업 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)에 액세스하는 방법을 알아보세요. 이 엔드포인트에는 이전 API 엔드포인트에서 가져온 모든 데이터를 제공하는 두 개의 보고서(감사 및 로그인)가 있습니다. 이 새 엔드포인트에는 앱 사용, 장치 사용 및 사용자 로그인 정보를 가져오는 데 사용할 수 있는 Azure AD Premium 라이선스가 있는 로그인 보고서도 있습니다.

--- 

**Q: https://graph.windows.net/&lt;tenant-name&gt;/reports/ 엔드포인트 API를 사용하여 Azure AD 보안 보고서(누출된 자격 증명 또는 익명 IP 주소의 로그인 등, 특정 유형의 검색)를 프로그래밍 방식으로 보고 시스템에 끌어오고 있습니다. 어떤 방식으로 전환해야 하나요?**

**A:** [ID 보호 위험 이벤트 API](../identity-protection/graph-get-started.md)를 사용하여 Microsoft Graph를 통해 보안 검색에 액세스할 수 있습니다. 이 새로운 형식은 고급 필터링, 필드 선택 등을 사용하여 데이터를 보다 유연하게 쿼리할 수 있도록 하고, 위험 이벤트를 SIEM 및 기타 데이터 수집 도구에 보다 쉽게 통합하기 위해 한 가지 형식으로 표준화합니다. 데이터가 다른 형식으로 되어 있으므로 이전 쿼리를 새 쿼리로 대체할 수 없습니다. 그러나 [새로운 API는 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)를 사용합니다. 이것은 O365 또는 Azure AD와 같은 API에 대한 Microsoft 표준입니다. 따라서 필요한 작업이 현재 MS Graph 투자를 확장하거나 이러한 새 표준 플랫폼으로의 전환을 시작하도록 지원할 수 있습니다.

--- 

**Q: Premium 라이선스는 어떻게 받을 수 있나요?**

**A:** 이 질문에 대한 답변은 [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하세요.

---

**Q: Premium 라이선스를 받은 후 활동 데이터를 보는 데 얼마나 걸리나요?**

**A:** 무료 라이선스로서 활동 데이터가 이미 있는 경우 동일한 데이터를 볼 수 있습니다. 모든 데이터가 없으면 1일이나 2일 정도 걸립니다.

---

**Q: Azure AD Premium 라이선스를 받은 후 지난 달의 데이터를 볼 수 있습니까?**

**A:**: 최근에 Premium 버전(평가판 버전을 포함)으로 전환한 경우 최대 7일간 데이터를 볼 수 있습니다. 데이터가 누적된 경우 최대 30일간 볼 수 있습니다.

---

**Q: Azure Portal에서 해당 활동 로그인을 보거나 API를 통해 데이터를 가져오기 위해 전역 관리자 권한이 필요한가요?**

**A:** 아니요. Azure Portal에서 또는 API를 통해 보고 데이터를 가져오려면 **보안 읽기 권한자**, **보안 관리자** 또는 **전역 관리자**여야 합니다.

---


## <a name="activity-logs"></a>활동 로그


**Q: Azure Portal에서 활동 로그 (감사 및 로그인)의 데이터 보존은 어떻게 됩니까?** 

**A:** 이 질문에 대한 답변은 [수집된 데이터 저장 기간은 얼마입니까?](reference-reports-data-retention.md#q-for-how-long-is-the-collected-data-stored)를 참조하세요.

--- 

**Q: 내 작업을 완료한 후 얼마나 지나야 활동 데이터를 볼 수 있습니까?**

**A:** 감사 활동 로그에는 15분에서 1시간까지의 대기 시간이 있습니다. 로그인 활동 로그는 레코드에 따라 15분에서 2시간까지 걸릴 수 있습니다.

---


**Q: Azure Portal을 통해 Office 365 활동 로그 정보를 얻을 수 있습니까?**

**A:** Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유하더라도 Office 365 활동 로그를 전체 보기로 보려는 경우, Office 365 관리 센터로 이동하여 Office 365 활동 로그 정보를 얻을 수 있습니다.

---


**Q: Office 365 활동 로그에 대한 정보를 얻기 위해 어떤 API를 사용해야 합니까?**

**A:** Office 365 관리 API를 사용하여 [API를 통해 Office 365 활동 로그](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview)에 액세스합니다.

---

**Q: Azure Portal에서 몇 개의 레코드를 다운로드할 수 있습니까?**

**A:** Azure Portal에서 최대 5,000개의 레코드를 다운로드할 수 있습니다. 레코드는 *가장 최근* 순으로 정렬되며, 기본적으로 가장 최근의 5,000개 레코드를 가져옵니다.

---

## <a name="risky-sign-ins"></a>위험한 로그인

**Q: ID 보호에 위험 이벤트가 있지만 모든 로그인에서 해당 로그인이 표시되는 것은 아닙니다. 이것은 예상된 동작인가요?**

**A:** 예. ID 보호는 대화형인지 여부에 관계없이 모든 인증 흐름의 위험을 평가합니다. 그러나 모든 로그인은 대화형 로그인만 표시합니다.

---

**Q: Azure Portal에서 "위험 플래그가 지정된 사용자" 보고서를 다운로드하려면 어떻게 해야 하나요?**

**A:** *위험 플래그가 지정된 사용자* 보고서를 다운로드하는 옵션이 곧 추가될 예정입니다.

---

**Q: Azure Portal에서 로그인 또는 사용자에게 위험 플래그가 지정된 이유를 어떻게 알 수 있나요?**

**A:** Premium Edition 고객은 "위험 플래그가 지정된 사용자"에서 사용자를 클릭하거나 "위험한 로그인"을 클릭하여 기본 위험 이벤트에 대해 자세히 알아볼 수 있습니다. Free 및 Basic Edition 고객은 기본 위험 이벤트 정보 없이도 위험에 처한 사용자 및 로그인을 볼 수 있습니다.

---

**Q: IP 주소는 로그인 및 위험한 로그인 보고서에서 어떻게 계산되나요?**

**A:** IP 주소는 IP 주소와 해당 주소가 실제로 연결된 컴퓨터 간에 확실한 연결이 없는 경우와 같은 방법으로 발급됩니다. 모바일 공급자 및 클라이언트 장치가 실제로 사용되는 위치에서 종종 매우 먼 중앙 풀에서 IP 주소를 발급하는 VPN과 같은 요인에 의해 복잡합니다. 위의 설명을 고려하면 IP 주소를 실제 위치로 변환하는 것은 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보에 기반한 최상의 노력입니다. 

---

**Q: "추가 위험이 있는 로그인이 감지됨" 위험 이벤트는 무엇을 의미하나요?**

**A:** 환경에서 모든 위험한 로그인을 파악할 수 있도록 하기 위해 Azure AD Identity Protection 구독자만 감지하기 위한 로그인에 대해 "추가 위험이 있는 로그인이 감지됨" 함수를 자리 표시자로 표시합니다.

---

**Q: "추가 위험이 있는 로그인이 감지됨" 위험 이벤트는 무엇을 의미하나요?**

**A:** 환경에서 모든 위험한 로그인을 파악할 수 있도록 하기 위해 Azure AD Identity Protection 구독자만 감지하기 위한 로그인에 대해 "추가 위험이 있는 로그인이 감지됨" 함수를 자리 표시자로 표시합니다.

---

## <a name="conditional-access"></a>조건부 액세스

**Q: 이 기능의 새로운 점**

**A:** 이제 고객은 모든 로그인 보고서를 통해 조건부 액세스 정책의 문제를 해결할 수 있습니다. 고객은 조건부 액세스 상태를 검토하고 로그인에 적용된 정책의 세부 정보와 각 정책의 결과를 자세히 알아볼 수 있습니다. 

**Q: 어떻게 시작하나요?**

**A:** 시작하기:
    * [Azure Portal](https://portal.azure.com)에서 로그인으로 이동합니다. 
    * 문제를 해결할 로그인을 클릭합니다.
    * **조건부 액세스** 탭으로 이동합니다. 여기서는 로그인에 영향을 미친 정책과 각 정책의 결과를 볼 수 있습니다. 
    
**Q: 조건부 액세스 상태에 가능한 모든 값**

**A:** 조건부 액세스 상태는 다음 값을 가질 수 있습니다.
    * **적용되지 않음**: 범위 내 사용자 및 앱에 CA 정책이 없음을 의미합니다. 
    * **성공**: 범위의 사용자 및 앱에 CA 정책이 있으며 CA 정책이 충족되었음을 의미합니다. 
    * **실패**: 범위의 사용자 및 앱에 CA 정책이 있으며 CA 정책이 충족되지 않았음을 의미합니다. 
    
**Q: 조건부 액세스 정책 결과에 가능한 모든 값**

**A:** 조건부 액세스 정책은 다음 결과를 가질 수 있습니다.
    * **성공**: 정책이 충족되었습니다.
    * **오류**: 정책이 충족되지 않았습니다.
    * **적용되지 않음**: 정책 조건이 충족되지 않았기 때문일 수 있습니다.
    * **사용 안 함**: 정책이 사용 안 함 상태이기 때문입니다. 
    
**Q: 모든 로그인 보고서의 정책 이름이 CA 정책 이름과 일치하지 않습니다. 그 이유는 무엇일까요?**

**A:** 모든 로그인 보고서의 정책 이름은 로그인 시점의 CA 정책 이름을 기준으로 합니다. 나중, 즉 로그인 후에 정책 이름을 업데이트했다면 이 값이 CA의 정책 이름과 일관되지 않을 수 있습니다.

**Q: 내 로그인이 조건부 액세스 정책으로 인해 차단되었으나 로그인 활동 보고서에는 로그인이 성공했다고 나옵니다. 그 이유는 무엇일까요?**

**A:** 현재 로그인 보고서는 조건부 액세스가 적용될 때 Exchange ActiveSync 시나리오에 대한 정확한 결과를 표시하지 못할 수도 있습니다. 보고서의 로그인 결과가 성공적인 로그인으로 표시되나, 조건부 액세스 정책으로 인해 실제로는 로그인에 실패한 경우가 있을 수도 있습니다. 
