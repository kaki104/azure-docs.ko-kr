---
title: Azure Active Directory에 대한 Log Analytics 보기를 설치하고 사용하는 방법(미리 보기) | Microsoft Docs
description: Azure Active Directory에 대한 Log Analytics 보기를 설치하고 사용하는 방법(미리 보기)을 알아봅니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b1333eda0fa0bffdf3852f24414511f55acc090e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395581"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Azure Active Directory에 대한 Log Analytics 보기 설치 및 사용

Azure Active Directory Log Analytics 보기를 통해 Azure AD 테넌트의 Azure AD 활동 로그를 분석하고 검색할 수 있습니다. Azure AD 활동 로그에는 포함되는 항목은 다음과 같습니다.

* 감사 로그: [감사 로그 활동 보고서](concept-audit-logs.md)를 통해 테넌트에서 수행된 모든 작업의 기록에 액세스할 수 있습니다.
* 로그인 로그: [로그인 활동 보고서](concept-sign-ins.md)를 통해 감사 로그에 보고된 작업을 수행한 사용자를 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Log Analytics 보기를 사용하려면 다음이 필요합니다.

* Azure 구독의 Log Analytics 작업 영역. [Log Analytics 작업 영역을 만드는 방법](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)을 알아보세요.
* 먼저 [Azure AD 활동 로그를 Log Analytics 작업 영역으로 라우팅](howto-integrate-activity-logs-with-log-analytics.md)하는 단계를 완료합니다.
* [GitHub 리포지토리](https://aka.ms/AADLogAnalyticsviews)의 보기를 로컬 컴퓨터에 다운로드합니다.

## <a name="install-the-log-analytics-views"></a>Log Analytics 보기 설치

1. Log Analytics 작업 영역으로 이동합니다. 이렇게 하려면 먼저 [Azure Portal](https://portal.azure.com)로 이동하고, **모든 서비스**를 선택합니다. 텍스트 상자에서 **Log Analytics**를입력하고, **Log Analytics**를 선택합니다. 활동 로그를 라우팅한 작업 영역을 필수 구성 요소의 일부로 선택합니다.
2. **뷰 디자이너**를 선택하고, **가져오기**를 선택한 다음, **파일 선택**을 선택하여 로컬 컴퓨터에서 보기를 가져옵니다.
3. 필수 조건에서 다운로드한 보기를 선택하고, **저장**을 선택하여 가져오기를 저장합니다. **Azure AD 계정 프로비전 이벤트** 보기, **로그인 이벤트** 보기 및 **사용자 동의 수행** 보기에 대해 이 작업을 수행합니다.

## <a name="use-the-views"></a>보기 사용

1. Log Analytics 작업 영역으로 이동합니다. 이렇게 하려면 먼저 [Azure Portal](https://portal.azure.com)로 이동하고, **모든 서비스**를 선택합니다. 텍스트 상자에서 **Log Analytics**를입력하고, **Log Analytics**를 선택합니다. 활동 로그를 라우팅한 작업 영역을 필수 구성 요소의 일부로 선택합니다.

2. 작업 영역에 있으면 **작업 영역 요약**을 선택합니다. 다음 세 가지 보기가 표시됩니다.

    * **Azure AD 계정 프로비전 이벤트**: 프로비전 활동 감사(예: 새로 프로비전된 사용자 수 및 프로비전 실패 횟수, 업데이트된 사용자 수 및 업데이트 실패 횟수, 프로비전 해제된 사용자 수 및 해당 실패 횟수)와 관련된 보고서가 표시됩니다.    
    * **로그인 이벤트**: 로그인 활동 모니터링(예: 응용 프로그램, 사용자, 장치별 로그인)과 관련된 가장 적절한 보고서와 시간 경과에 따른 로그인 수를 추적하는 요약 보기가 표시됩니다.
    * **사용자 동의 수행**: 사용자 동의(예: 사용자에 의한 동의 허용, 동의한 사용자의 로그인 및 모든 동의 기반 응용 프로그램에 대한 응용 프로그램의 로그인)와 관련된 보고서가 표시됩니다. 

3. 이러한 보기 중 하나를 선택하여 개별 보고서로 이동합니다. 모든 보고서 매개 변수에 대해 경고를 설정할 수도 있습니다. 예를 들어 로그인 오류가 발생할 때마다 알려주는 경고를 설정해 보겠습니다. 이렇게 하려면 먼저 **로그인 이벤트** 보기를 선택하고, **시간 경과별 로그인 오류** 보고서를 선택한 다음, **분석**을 선택하여 보고서를 지지하는 실제 쿼리가 포함된 세부 정보 페이지를 엽니다. 

    ![세부 정보](./media/howto-install-use-log-analytics-views/details.png)


4. **경고 설정**을 선택한 다음, **경고 조건** 섹션 아래에서 **사용자 지정 로그 검색이 &lt;논리 정의 안 됨&gt;일 때마다**를 선택합니다. 로그인 오류가 발생할 때마다 경고하려고 하므로 기본 경고 논리의 **임계값**을 **1**로 설정한 다음, **완료**를 선택합니다. 

    ![신호 논리 구성](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. 경고에 대한 이름과 설명을 입력하고, 심각도를 **경고**로 설정합니다.

    ![규칙 만들기](./media/howto-install-use-log-analytics-views/create-rule.png)

6. 경고할 작업 그룹을 선택합니다. 일반적으로 이 그룹은 이메일 또는 문자 메시지를 통해 알리려는 팀이거나, 웹후크, Runbook, 함수, 논리 앱 또는 외부 ITSM 솔루션을 사용하는 자동화된 작업일 수 있습니다. [Azure Portal에서 작업 그룹을 만들고 관리하는 방법](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)에 대해 알아보세요.

7. **경고 규칙 만들기**를 선택하여 경고를 만듭니다. 이제 로그인할 때마다 경고 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Log Analytics에서 활동 로그를 분석하는 방법](howto-analyze-activity-logs-log-analytics.md)
* [Azure Portal에서 Log Analytics 시작](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)