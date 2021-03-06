---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111967"
---
## <a name="create-an-iot-hub"></a>IoT Hub 만들기
시뮬레이션된 장치 앱을 연결할 IoT Hub를 만듭니다. 다음 단계는 Azure 포털을 사용하여 이 작업을 완료하는 방법을 보여줍니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **리소스 만들기** > **사물 인터넷** > **IoT Hub**를 선택합니다.
   
    ![Azure 포털 표시줄](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. **IoT Hub** 창에서 IoT Hub에 다음 정보를 입력합니다.

   * **구독**: 이 IoT Hub를 만드는 데 사용할 구독을 선택합니다.

   * **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../articles/azure-resource-manager/resource-group-portal.md)를 참조하세요.

   * **지역**: 가장 가까운 위치를 선택합니다.

   * **이름**: IoT Hub에 대한 이름을 만듭니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT Hub 기본 사항 창](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. **다음: 크기 및 규모**를 선택하여 IoT Hub를 계속 만듭니다. 

5. **가격 책정 및 규모 계층**을 선택합니다. 이 문서의 경우 구독에서 아직 사용할 수 있다면 **F1 - 무료** 계층을 선택합니다. 자세한 내용은 [가격 및 크기 계층](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

   ![IoT Hub 크기 및 규모 창](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. **검토 + 만들기**를 선택합니다.

7. IoT Hub 정보를 검토한 다음, **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행 상황을 모니터링할 수 있습니다.

8. 새 IoT 허브가 준비되면 Azure Portal에서 해당 타일을 클릭하여 속성 창을 엽니다. IoT 허브를 만들었으므로 IoT 허브에 장치 및 응용 프로그램을 연결하는 데 사용하는 중요한 정보를 찾습니다. **공유 액세스 정책**을 클릭합니다.
   
9. **공유 액세스 정책**에서 **iothubowner** 정책을 선택합니다. 나중에 사용하기 위해 IoT Hub **연결 문자열---기본 키**를 복사합니다. 자세한 내용은 "IoT Hub 개발자 가이드"의 [액세스 제어](../articles/iot-hub/iot-hub-devguide-security.md)를 참조하세요.
   
    ![공유 액세스 정책](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)