---
title: Azure Reserved VM Instances 할인 이해 | Microsoft Docs
description: 실행 중인 VM에 Azure Reserved Virtual Machine Instances 할인이 적용되는 방식을 알아봅니다.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: e43188459bf495748a054b0f24936c9141bd218c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025717"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Azure Reservation 할인이 가상 머신에 적용되는 방식 이해

Azure Reserved Virtual Machine Instance를 구입한 후, 예약 할인이 예약의 특성 및 수량과 일치하는 가상 머신에 자동으로 적용됩니다. 예약은 가상 머신의 계산 비용을 포함합니다.

SQL Database 예약된 용량의 경우 [Azure Reserved Instances 할인](billing-understand-reservation-charges.md)을 참조하세요.

다음 표에서는 Reserved VM Instance를 구입한 후의 가상 머신에 대한 비용을 설명합니다. 모든 경우에는 정상 요금으로 저장소 및 네트워킹에 대한 요금이 청구됩니다.

| 가상 머신 형식  | Reserved VM Instance 요금 |
|-----------------------|--------------------------------------------|
|추가 소프트웨어가 없는 Linux VM | 예약은 VM 인프라 비용을 포함합니다.|
|소프트웨어 요금이 포함된 Linux VM(예: Red Hat) | 예약은 인프라 비용을 포함합니다. 추가 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 없는 Windows VM |예약은 인프라 비용을 포함합니다. Windows 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 있는 Windows VM(예: SQL Server) | 예약은 인프라 비용을 포함합니다. Windows 소프트웨어 및 추가 소프트웨어에 대한 요금이 청구됩니다.|
|[Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)을 포함한 Windows VM | 예약은 인프라 비용을 포함합니다. Windows 소프트웨어 비용은 Azure Hybrid Benefit에 포함되어 있습니다. 추가 소프트웨어는 별도로 청구됩니다.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Windows 외 VM 대상 응용 프로그램 예약 할인

 Azure Reservation 할인은 시간 단위로 실행 중인 VM 인스턴스에 적용됩니다. 구입한 예약은 예약 할인을 적용할 실행 중인 VM의 사용량과 일치합니다. 전체 시간을 실행하지 못한 VM의 경우 예약은 현재 실행 중인 VM을 비롯하여 예약을 사용하지 않는 다른 VM에서 채워집니다. 시간이 다 지날 때쯤 해당 시간의 VM에 대한 예약 응용 프로그램이 잠깁니다. 1시간 동안 VM이 실행되지 않거나 시간 내 동시 VM이 예약 시간을 채우지 못하는 경우 예약이 해당 시간 동안 미달 사용됩니다. 다음 그래프는 청구 가능한 VM 사용량에 예약을 적용하는 작업을 보여줍니다. 일러스트레이션은 하나의 예약 구매 및 일치하는 두 개의 VM 인스턴스를 기반으로 합니다.

![적용된 하나의 예약 및 일치하는 두 개의 VM 인스턴스에 대한 스크린샷](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. 예약 줄을 초과하는 사용량은 일반 종량제 요금으로 청구됩니다. 예약 선을 초과하지 않는 사용량은 이미 예약 구매의 일부로 지불되었기 때문에 청구되지 않습니다.
2. 시간 1 중에 인스턴스 1은 0.75시간 동안 실행되고 인스턴스 2는 0.5시간 동안 실행됩니다. 시간 1의 총 사용량은 1.25시간입니다. 나머지 0.25시간에 대한 종량제 요금이 청구됩니다.
3. 시간 2 및 시간 3의 경우 두 인스턴스는 1시간 동안 실행되었습니다. 하나의 인스턴스는 예약에 포함되고 다른 하나는 종량제 요금으로 요금이 부과됩니다.
4. 시간 4의 경우 인스턴스 1은 0.5시간 동안 실행되고 인스턴스 2는 1시간 동안 실행됩니다. 인스턴스 1은 예약에 완전히 포함되고 인스턴스 2 중 0.5시간도 포함됩니다. 나머지 0.5시간에 대한 종량제 요금이 청구됩니다.

청구 사용량 보고서에서 Azure 예약 적용 결과를 이해하고 확인하려면 [예약 사용량 이해](https://go.microsoft.com/fwlink/?linkid=862757)를 참조하세요.

## <a name="application-of-reservation-discount-to-windows-vms"></a>Windows VM 대상 응용 프로그램 예약 할인

Windows VM 인스턴스를 실행하면 인프라 비용을 포함하도록 예약이 적용됩니다. Windows VM의 VM 인프라 비용에 예약을 적용하는 것은 Windows 외 VM의 경우와 동일합니다. vCPU를 기준으로 Windows 소프트웨어에 대한 요금이 별도로 청구됩니다. [예약 사용 시 Windows 소프트웨어 비용](https://go.microsoft.com/fwlink/?linkid=862756)을 참조하세요. [Windows Server용 Azure 하이브리드 혜택](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)으로 Windows 라이선스 비용을 처리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록의 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
