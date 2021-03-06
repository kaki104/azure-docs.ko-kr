---
title: Azure Maps를 사용하여 트래픽 표시 | Microsoft Docs
description: Javascript 맵에서 트래픽 데이터를 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 532001a0cda22903d0bdf807ee868aef211336e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240089"
---
# <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

이 문서에서는 맵에 트래픽 및 인시던트 정보를 표시하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

<iframe height='456' scrolling='no' title='맵에 트래픽 표시' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>맵에 트래픽 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](map-create.md)를 참조하세요.

두 번째 코드 블록은 맵 클래스의 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) 기능을 사용하여 맵에 트래픽 흐름 및 인시던트를 렌더링합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 샘플은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)

다음 문서를 참조하여 사용자 환경을 개선할 수 있습니다.

> [!div class="nextstepaction"]
> [마우스 이벤트와 맵의 상호 작용](./map-events.md)

> [!div class="nextstepaction"]
> [액세스할 수 있는 맵 작성](./map-accessibility.md)
