---
title: Azure Lab Services의 클래스룸 랩에 액세스 | Microsoft Docs
description: 이 자습서에서는 교수가 설정한 클래스룸 랩의 가상 머신에 액세스합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: dadc90e6a39b9e9689bab0249e6496fdea6f6205
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450218"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>자습서: Azure Lab Services의 클래스룸 랩에 액세스
이 자습서에서는 사용자가 학생이 되어 클래스룸 랩의 VM(가상 머신)에 연결합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 등록 링크 사용 
> * 가상 머신에 연결

## <a name="use-the-registration-link"></a>등록 링크 사용

1. 교수/강사로부터 받은 **등록 URL**로 이동합니다. 
2. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 
3. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 
2. 가상 머신이 준비될 때까지 기다렸다가 VM을 **시작**합니다.

    ![VM 시작](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

1. 액세스하려는 랩의 가상 머신을 나타내는 타일에서 **연결**을 선택합니다. 

    ![VM에 연결](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. RDP 파일을 하드 디스크에 저장하고 엽니다. 
3. 강사/교수로부터 받은 **사용자 이름**과 **암호**를 사용하여 컴퓨터에 로그인합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 강사/교수로부터 받은 등록 링크를 사용하여 클래스룸 랩에 액세스했습니다.

랩 소유자는 랩에 누가 등록되었는지 확인하고 VM 사용량을 추적하고 싶을 것입니다. 자세한 방법을 보려면 그 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [랩 사용량 추적](tutorial-track-usage.md) 