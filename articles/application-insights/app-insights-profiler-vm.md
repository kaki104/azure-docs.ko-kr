---
title: Application Insights Profiler를 사용하여 Azure VM에서 실행되는 웹앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure VM에서 웹앱 프로파일링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 10cd05bd40262815e3b27c861982debc18e5b4f3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142909"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Application Insights Profiler를 사용하여 Azure Virtual Machine 또는 가상 머신 확장 집합에서 실행되는 웹앱 프로파일링
또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>가상 머신 또는 확장 집합에서 Profiler 배포
이 페이지에서는 Azure VM 또는 Azure 가상 머신 확장 집합에서 실행되는 Application Insights Profiler를 가져오는 데 필요한 단계를 안내합니다. Application Insights Profiler는 VM용 Windows Azure 진단 확장과 함께 설치됩니다. 이 Profiler를 실행하도록 확장을 구성해야 하며, 응용 프로그램에 기본 제공된 App Insights SDK가 VM에서 실행 중인 웹앱에 대한 프로필을 가져오도록 해야 합니다.

1. Application Insights SDK를 [ASP.Net 응용 프로그램](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) 또는 일반 [.NET 응용 프로그램](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json)에 추가합니다. 요청에 대한 프로필을 보려면 Application Insights에 요청 원격 분석을 전송해야 합니다.
1. VM에 Windows Azure 진단 확장을 설치합니다. 전체 Resource Manager 템플릿 예제를 보려면 다음을 참조하세요.  
    * [가상 머신](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [가상 머신 확장 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. 수정된 환경 배포 정의를 배포합니다.  

   수정 사항을 적용하려면 일반적으로 PowerShell cmdlet 또는 Visual Studio를 통한 전체 템플릿 배포 또는 클라우드 서비스 기반 게시가 필요합니다.  

   Azure 진단 확장명만 수정하는 기존 가상 머신에 대한 대안으로 다음과 같은 PowerShell 명령을 사용할 수 있습니다.  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 원하는 응용 프로그램이 [IIS](https://www.microsoft.com/web/downloads/platform.aspx)를 통해 실행 중인 경우에는 다음을 수행하여 `IIS Http Tracing` Windows 기능을 활성화합니다.  

   a. 환경에 대한 원격 액세스를 설정한 다음, [Windows 기능 추가]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 창을 사용하거나 PowerShell에서 (관리자로서) 다음 명령을 실행합니다.  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 다음 명령을 실행할 수 있습니다.  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 응용 프로그램을 배포합니다.

## <a name="enable-profiler-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler를 사용하도록 설정

온-프레미스 서버에서 Profiler를 사용하도록 설정하는 것은 독립 실행형 모드에서 Application Insights Profiler를 실행하는 것으로 알려져 있습니다. Azure 진단 확장명 수정과 연결되어 있지 않습니다.

온-프레미스 서버에서 Profiler를 공식 지원할 계획은 없습니다. 이 시나리오를 실험하려는 경우 [지원 코드를 다운로드](https://github.com/ramach-msft/AIProfiler-Standalone)할 수 있습니다. 해당 코드를 유지 관리하거나 코드와 관련된 문제 및 기능 요청에 응답할 책임은 *없습니다*.

## <a name="next-steps"></a>다음 단계

- 응용 프로그램에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability) 시작). 그런 다음 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- [Profiler 문제 해결](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)에서 Profiler 문제 해결에 대한 도움을 받으세요.
