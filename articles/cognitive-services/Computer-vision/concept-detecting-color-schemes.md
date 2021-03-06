---
title: 색 구성표 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 색 구성표를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 5d0cb6ca751c844846288e8fe26f6ae542e89831
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339495"
---
# <a name="detecting-color-schemes"></a>색 구성표 검색

Computer Vision은 이미지에서 색을 추출합니다. 색은 주조 전경색, 주조 배경색 및 전체 이미지의 주조색이라는 세 가지 컨텍스트에서 분석됩니다. 색은 12가지 기준 강조색으로 그룹화됩니다. 해당 강조색은 검정, 파랑, 갈색, 회색, 초록, 주황, 분홍, 자주, 청록, 흰색 및 노랑입니다. Computer Vision은 이미지에서 추출된 색을 분석하여 주조색 및 채도의 조합을 통해 시청자에게 이미지에 대한 가장 활발한 색을 나타내는 강조색을 반환합니다. 이미지의 색에 따라 단순한 흑백 또는 강조색이 16진수 색 코드로 반환될 수 있습니다. 또한 Computer Vision은 이미지가 흑백인지 여부를 나타내는 부울 값을 반환합니다.

## <a name="color-scheme-detection-examples"></a>색 구성표 검색 예제

다음 예제에서는 예제 이미지의 색 구성표를 검색할 때 Computer Vision에서 반환된 JSON 응답을 보여줍니다. 이 경우에 예제 이미지는 흑백 이미지가 아니지만 기조 전경색 및 배경색이 검은색이고 전체 이미지에 대한 주조색이 검은색 및 흰색입니다.

![옥외 산](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>주조색 예제

다음 표에서는 Computer Vision에서 반환된 각 예제 이미지에 대한 주조 전경색, 배경색 및 이미지 색에 대해 설명합니다.

| 이미지 | 주조색 |
|-------|-----------------|
|![비전 분석 꽃](./Images/flower.png)| 전경색: 검은색<br/>배경색: 흰색<br/>색: 검은색, 흰색, 녹색|
![비전 분석 학습 스테이션](./Images/train_station.png) | 전경색: 검은색<br/>배경색: 검은색<br/>색: 검은색 |

### <a name="accent-color-examples"></a>강조색 예제

 다음 표에서는 Computer Vision에서 반환된 각 예제 이미지에 대한 강조색을 16진수 HTML 색상 값으로 설명합니다.

| 이미지 | 강조 색 |
|-------|--------------|
|![옥외 산](./Images/mountain_vista.png) | #BB6D10 |
|![비전 분석 꽃](./Images/flower.png) | #C6A205 |
|![비전 분석 학습 스테이션](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>흑백 검색 예제

다음 표에서는 Computer Vision에서 반환한 대로 각 예제 이미지가 흑백인지를 나타냅니다.

| 이미지 | 흑백? |
|-------|----------------|
|![비전 분석 건물](./Images/bw_buildings.png) | true |
|![비전 분석 주택 야드](./Images/house_yard.png) | false |

## <a name="next-steps"></a>다음 단계

[이미지 형식 검색](concept-detecting-image-types.md)에 대한 개념을 알아봅니다.