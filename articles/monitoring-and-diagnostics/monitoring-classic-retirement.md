---
title: 클래식 경고 및 모니터링을 Azure Monitor 통합 경고 및 모니터링으로 대체
description: 이전에 Azure Portal의 경고(클래식) 아래에 표시된 클래식 모니터링 서비스 및 기능의 사용 중지에 대한 개요입니다. 클래식 경고 및 모니터링에는 Azure 리소스에 대한 클래식 메트릭 경고, Application Insights에 대한 클래식 메트릭 경고, Application Insights에 대한 클래식 웹 테스트 경고, Application Insights에 대한 클래식 사용자 지정 메트릭 기반 경고 및 Application Insights SmartDetection v1에 대한 클래식 경고가 포함되어 있습니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f7efafe5e3080de15781496032b688bc5fa71df2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418427"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>클래식 경고 및 모니터링을 Azure Monitor 통합 경고 및 모니터링으로 대체

Azure Monitor는 이제 리소스 전체에서 '하나의 메트릭' 및 '하나의 경고'를 지원하는 통합된 전체 스택 모니터링 서비스가 되었습니다. 자세한 내용은 [새 Azure Monitor에 대한 블로그 게시물](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)을 참조하세요. 새 Azure 모니터링 및 경고 플랫폼은 성장하고 있는 클라우드 컴퓨팅에 보조를 맞추고 Microsoft 지능형 클라우드 원칙에 맞게 더 빠르고, 더 스마트하게, 확장 가능하도록 설계되었습니다. 

새 Azure 모니터링 및 경고 플랫폼을 사용함에 따라 Azure 경고의 *클래식 경고 보기* 섹션 내에서 호스팅되는 "클래식" 모니터링 및 경고 플랫폼은 2019년 6월 이후로 사용 중지됩니다.

 ![Azure Portal의 클래식 경고](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

새 플랫폼에서 경고를 시작하고 다시 만드는 것이 좋습니다. 많은 수의 경고가 있는 고객을 위해 중단 또는 추가 비용 없이 기존 클래식 경고를 새 경고 시스템으로 자동으로 전환할 수 있는 방법을 제공하기 위해 노력하고 있습니다.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights의 통합 메트릭 및 경고

Azure Monitor의 새 메트릭 플랫폼은 이제 Application Insights에서 제공하는 모니터링을 강화합니다. 이러한 이동은 Application Insights가 작업 그룹에 연결되어 이전의 이메일 및 웹후크 호출보다 훨씬 더 많은 옵션을 허용합니다. 경고는 이제 음성 통화, Azure Functions, Logic Apps, SMS 및 ServiceNow 및 ITSM 도구(예: Automation Runbook)를 트리거할 수 있습니다. 거의 실시간 모니터링 및 경고를 제공하는 새 플랫폼을 통해 Application Insights 사용자는 동일한 기술을 활용하여 다른 Azure 리소스 모니터링을 강화하고 Microsoft 제품 모니터링을 지원할 수 있습니다.

Application Insights에 대한 새로운 통합 모니터링 및 경고에 포함된 항목은 다음과 같습니다.

- **Application Insights 플랫폼 메트릭** – Application Insights 제품에서 인기 있는 미리 작성된 메트릭을 제공합니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 플랫폼 메트릭 사용](../application-insights/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) 문서를 참조하세요.
- **Application Insights 가용성 및 웹 테스트** - 웹 응용 프로그램 또는 서버의 응답성과 가용성을 평가할 수 있는 기능을 제공합니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 가용성 테스트 및 알림 사용](../application-insights/app-insights-monitor-web-app-availability.md) 문서를 참조하세요.
- **Application Insights 사용자 지정 메트릭** – 모니터링 및 경고에 대한 자체의 메트릭을 정의하고 내보낼 수 있습니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 사용자 지정 메트릭 사용](../application-insights/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 문서를 참조하세요.
- **Application Insights 오류 이상(스마트 검색의 일부)** – 웹 응용 프로그램이 실패한 HTTP 요청 또는 종속성 호출의 속도가 비정상적으로 증가하는 경우 거의 실시간으로 자동으로 알려줍니다. Application Insights 오류 이상(스마트 검색의 일부)은 새 Azure Monitor의 일부로 곧 사용할 수 있으며, 앞으로 몇 개월 이내에 롤아웃될 때 이 문서를 다음 반복에 대한 링크로 업데이트할 예정입니다.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>다른 Azure 리소스에 대한 통합 메트릭 및 경고

2018년 3월 이후 Azure 리소스에 대한 차세대 경고 및 다차원 모니터링이 출시되었습니다. 이제 새 메트릭 플랫폼 및 경고는 거의 실시간에 가까운 기능을 통해 더욱 빨라졌습니다. 무엇보다도 최신 플랫폼에 특정 값 조합, 조건 또는 작업을 조각화 및 필터링할 수 있는 차원 옵션이 포함되어 있어 최신 메트릭 플랫폼 경고에서 더 자세한 세분성을 제공합니다. 새 Azure Monitor의 모든 경고와 마찬가지로, ActionGroups를 사용함으로써 최신 메트릭 경고가 확장되어 이메일 또는 웹후크를 넘어 SMS, 음성, Azure Function, Automation Runbook 등으로 알림을 확장할 수 있습니다.
Azure 리소스에 대해 사용할 수 있는 최신 메트릭은 다음과 같습니다.

- **Azure Monitor 표준 플랫폼 메트릭** – 다양한 Azure 서비스와 제품에서 인기 있는 미리 채워진 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) 및 [Azure Monitor에서 메트릭 경고 지원](alert-metric-overview.md#supported-resource-types-for-metric-alerts) 문서를 참조하세요.
- **Azure Monitor 사용자 지정 메트릭** – Azure 진단 에이전트가 포함된 사용자 구동 원본의 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor의 사용자 지정 메트릭](metrics-custom-overview.md) 문서를 참조하세요. 사용자 지정 메트릭을 사용하면 [Windows Azure 진단 에이전트](metrics-store-custom-guestos-resource-manager-vm.md) 및 [InfluxData Telegraf 에이전트](metrics-store-custom-linux-telegraf.md)에서 수집한 메트릭을 게시할 수도 있습니다.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>클래식 모니터링 및 경고 플랫폼의 사용 중지

앞에서 설명한 대로, 현재 Azure Portal의 [경고(클래식) 섹션](monitoring-overview-alerts-classic.md)에서 사용할 수 있는 클래식 모니터링 및 경고 플랫폼은 새 시스템으로 교체됨에 따라 앞으로 몇 개월 내에 사용 중지됩니다.
관련 API, Azure Portal 인터페이스 및 서비스의 종료를 포함하여 이전의 클래식 모니터링 및 경고는 2019년 6월 30일에 사용 중지됩니다. 사용 중지되는 기능은 구체적으로 다음과 같습니다.

- 현재 Azure Portal의 [경고(클래식) 섹션](monitoring-overview-alerts-classic.md)을 통해 사용할 수 있는 Azure 리소스에 대한 이전(클래식) 메트릭 및 경고이며, [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 리소스로 액세스할 수 있습니다.
- 현재 Azure Portal의 [경고(클래식) 섹션](monitoring-overview-alerts-classic.md)을 통해 사용할 수 있는 Application Insights에 대한 이전(클래식) 플랫폼과 사용자 지정 메트릭 및 관련 경고이며, [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 리소스로 액세스할 수 있습니다.
- 현재 Azure Porta에서 [Application Insights 내 스마트 검색](../application-insights/app-insights-proactive-diagnostics.md)으로 사용할 수 있는 이전(클래식) 오류 이상 경고이며, Azure Portal의 [경고(클래식) 섹션](monitoring-overview-alerts-classic.md)에 표시된 경고 구성도 포함됩니다.

해당 [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](insights-alerts-powershell.md), [CLI](insights-alerts-command-line-interface.md), Azure Portal 페이지 및 [리소스 템플릿](monitoring-enable-alerts-using-template.md)이 포함된 모든 클래식 모니터링 및 경고 시스템은 2019년 6월까지 사용할 수 있습니다. 이 날짜 이후에는 클래식 모니터링 및 경고 서비스가 사용 중지되어 더 이상 사용할 수 없게 됩니다. 2019년 6월 이후 경고(클래식)에 계속 존재하는 모든 경고 규칙은 계속 실행되지만 수정할 수는 없습니다.

2019년 6월 이후 클래식 모니터링 및 경고 플랫폼에 남아 있는 모든 경고는 2019년 7월에 Microsoft에서 새 Azure 모니터 플랫폼의 해당 경고로 자동으로 마이그레이션합니다. 이 프로세스는 가동 중지 시간 없이 원활하게 진행되며 고객이 모니터링 범위를 손실하지 않도록 보장합니다.

Azure Portal의 [경고(클래식) 섹션](monitoring-overview-alerts-classic.md)에서 경고를 새 Azure 경고로 자발적으로 마이그레이션할 수 있는 도구가 곧 제공될 것입니다. 새 Azure Monitor로 마이그레이션되는 경고(클래식)에 구성된 모든 규칙은 추가 비용 없이 유지되며 요금이 청구되지 않습니다. 또한 마이그레이션된 기본 경고 규칙에서 이메일, 웹후크 또는 LogicApp을 통해 알림을 푸시하는 경우에도 요금을 부담하지 않습니다. 그러나 최신 알림 또는 작업 유형(예: SMS 또는 음성 통화, ITSM 통합 등)을 사용하는 경우 마이그레이션된 경고 또는 새 경고에 추가되었는지 여부에 관계없이 비용이 청구될 수 있습니다. 자세한 내용은 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

또한 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/) 범위 내에서 비용을 청구할 수 있는 대상은 다음과 같습니다.

- 새 Azure Monitor 플랫폼에 체험 단위를 초과하여 만든 모든 새(마이그레이션되지 않음) 경고 규칙
- Azure Monitor에 포함된 체험 단위를 초과하여 수집되고 유지된 모든 데이터
- Application Insights에서 실행된 모든 다중 테스트 웹 테스트
- Azure Monitor에 포함된 체험 단위를 초과하여 저장된 모든 사용자 지정 메트릭

이 문서에 나오는 새 Azure 모니터링 및 경고 기능에 대한 링크 및 세부 정보와 새 Azure Monitor 플랫폼을 채택할 때 사용자를 지원하는 도구의 가용성은 지속적으로 업데이트됩니다.


## <a name="next-steps"></a>다음 단계

* [새로운 통합 Azure Monitor](../azure-monitor/overview.md)에 대해 자세히 알아봅니다.
* [새로운 Azure 경고](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
