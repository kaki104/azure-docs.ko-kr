---
title: Visual Studio Code용 Azure IoT Toolkit 확장을 사용하여 Azure IoT Hub 클라우드 장치 메시징 관리 | Microsoft Docs
description: 장치-클라우드 메시지를 모니터링하고 클라우드를 Azure IoT Hub의 장치 메시지로 보내려면 Visual Studio Code용 Azure IoT Toolkit 확장을 사용하는 방법을 알아봅니다.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/20/2018
ms.author: junhan
ms.openlocfilehash: 7bcb6eebdb6ceba6b07aeb19c1a74309fd4227d0
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206688"
---
# <a name="use-azure-iot-toolkit-extension-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Visual Studio Code용 Azure IoT Toolkit 확장을 사용하여 장치와 IoT Hub 간에 메시지 보내고 받기

![종단 간 다이어그램](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)은 IoT Hub 관리를 쉽게 해주는 유용한 Visual Studio Code 확장입니다. 이 문서에서는 Visual Studio Code용 Azure IoT Toolkit 확장을 사용하여 장치와 IoT Hub 간에 메시지 보내고 받는 방법에 중점을 둡니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>알아볼 내용

장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 보내려면 Visual Studio Code용 Azure IoT Toolkit 확장을 사용하는 방법을 알아봅니다. 장치-클라우드 메시지는 장치에서 수집한 다음 IoT Hub로 보내는 센서 데이터일 수 있습니다. 클라우드-장치 메시지는 IoT Hub에서 장치로 보내 사용자 장치에 연결된 LED를 깜박이는 명령일 수 있습니다.

## <a name="what-you-will-do"></a>수행할 사항

- Visual Studio Code용 Azure IoT Toolkit 확장을 사용하여 장치-클라우드 메시지를 모니터링합니다.
- Visual Studio Code용 Azure IoT Toolkit 확장을 사용하여 클라우드-장치 메시지를 전송합니다.

## <a name="what-you-need"></a>필요한 항목

- 활성 Azure 구독.
- 구독 중인 Azure IoT Hub
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

1. **탐색기**에서 VS Code를 보고 왼쪽 아래 모서리에서 **Azure IoT Hub 장치** 섹션을 확장합니다.
1. 상황에 맞는 메뉴에서 **IoT Hub 선택**을 클릭합니다.
1. 처음으로 Azure에 로그인할 수 있게 하는 팝업 메시지가 오른쪽 아래 모서리에 표시됩니다.
1. 로그인 후 Azure 구독 목록이 표시되면 IoT Hub 및 Azure 구독을 선택합니다.
1. 잠시 후 장치 목록이 **Azure IoT Hub 장치** 탭에 표시됩니다.

   > [!Note]
   > **IoT Hub 연결 문자열 설정**을 선택하여 설정을 완료할 수도 있습니다. 팝업 창에서 IoT 장치를 연결할 IoT Hub의 연결 문자열을 입력합니다.
   
## <a name="monitor-device-to-cloud-messages"></a>장치-클라우드 메시지 모니터링

장치에서 IoT Hub로 보낸 메시지를 모니터링하려면 다음 단계를 수행합니다.

1. 장치를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
1. 모니터링되는 메시지가 **OUTPUT** > **Azure IoT Toolkit** 보기에 표시됩니다.
1. 모니터링을 중지하려면 **OUTPUT** 보기를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 중지**를 선택합니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-장치 메시지 보내기

IoT Hub에서 장치로 메시지를 보내려면 다음 단계를 수행합니다.

1. 장치를 마우스 오른쪽 단추로 클릭하고 **장치에 C2D 메시지 보내기**를 선택합니다. 
1. 입력 상자에 메시지를 입력합니다.
1. 결과가 **OUTPUT** > **Azure IoT Toolkit** 보기에 표시됩니다.

## <a name="next-steps"></a>다음 단계

IoT 장치와 Azure IoT Hub 간에 장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
