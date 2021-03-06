---
title: Azure Digital Twins에서 사용자 정의 함수를 사용하는 방법 | Microsoft Docs
description: Azure Digital Twins를 사용하여 사용자 정의 함수, 선택기 및 역할 할당을 만드는 방법에 대한 지침입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 8094965da5fb0a5fad0313fd96e2878f86d78aa7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215500"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins에서 사용자 정의 함수를 사용하는 방법

[사용자 정의 함수](./concepts-user-defined-functions.md)를 사용하면 사용자가 들어오는 원격 분석 메시지 및 공간 그래프 메타데이터에 대한 사용자 지정 논리를 실행할 수 있으므로 사용자가 미리 정의된 엔드포인트에 이벤트를 보낼 수 있습니다. 이 가이드에서는 특정 온도를 초과하는 모든 읽기를 검색하고 경고를 보내는 온도 이벤트에 대해 조치하는 예제를 살펴보겠습니다.

아래 예제에서 `https://yourManagementApiUrl`은 Digital Twins API의 URI를 의미합니다.

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourInstanceName* | Azure Digital Twins 인스턴스의 이름 |
| *yourLocation* | 인스턴스를 호스팅하는 서버 지역 |

## <a name="client-library-reference"></a>클라이언트 라이브러리 참조

사용자 정의 함수 런타임에서 도우미 메서드로 사용할 수 있는 함수는 다음 [클라이언트 참조](#Client-Reference)에 열거됩니다.

## <a name="create-a-matcher"></a>검사기 만들기

검사기는 지정된 원격 분석 메시지에 대해 실행할 사용자 정의 기능을 결정하는 그래프 개체입니다.

유효한 검사기 조건 비교:

- `Equals`
- `NotEquals`
- `Contains`

유효한 검사기 조건 대상:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

다음 예제 검사기에서는 `"Temperature"`를 해당 데이터 형식 값으로 사용하여 센서 원격 분석 이벤트에서 true로 평가합니다. 사용자 정의 함수에 여러 검사기를 만들 수 있습니다.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "yourSpaceIdentifier"
}
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourManagementApiUrl* | 관리 API의 전체 URL 경로  |
| *yourSpaceIdentifier* | 인스턴스를 호스팅하는 서버 지역 |

## <a name="create-a-user-defined-function-udf"></a>UDF(사용자 정의 함수) 만들기

검사기를 만든 후에 POST 호출을 사용하여 함수 코드 조각을 업로드합니다.

> [!IMPORTANT]
> - 헤더에서 다음 `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`를 설정합니다.
> - 본문은 다중 파트입니다.
>   - 첫 번째 부분은 UDF에 필요한 메타데이터에 대한 것입니다.
>   - 두 번째 부분은 JavaScript 계산 논리를 보여줍니다.
> - `userDefinedBoundary` 섹션 `SpaceId` 및 `Machers` Guid에서 바꿉니다.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourManagementApiUrl* | 관리 API의 전체 URL 경로  |

본문:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourSpaceIdentifier* | 공간 식별자  |
| *yourMatcherIdentifier* | 사용하려는 검사기의 ID |

### <a name="example-functions"></a>예제 함수

`sensor.DataType`에 해당하는 **Temperature** 데이터 형식을 사용하여 센서에 대한 직접 센서 원격 분석 읽기를 설정합니다.

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

*telemetry* 매개 변수는 **SensorId** 및 **Message** 특성(센서에서 보낸 메시지에 해당)을 노출합니다. *executionContext* 매개 변수는 다음 특성을 노출합니다.

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

다음 예제에서는 센서 원격 분석 읽기가 미리 정의된 임계값을 초과하는 경우 메시지를 기록합니다. Digital Twins 인스턴스에서 진단 설정을 사용하는 경우 사용자 정의 함수의 로그도 전달됩니다.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

다음 코드는 온도 수준이 미리 정의된 상수를 초과하는 경우 알림을 트리거합니다.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

더 복잡한 UDF 코드 샘플은 [새로운 UDF를 사용하여 사용 가능한 공간 확인](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)을 참조하세요.

## <a name="create-a-role-assignment"></a>역할 할당 만들기

실행할 사용자 정의 함수에 대한 역할 할당을 만들어야 합니다. 그렇지 않으면 그래프 개체에서 작업을 수행할 관리 API와 상호 작용할 수 있는 적절한 사용 권한이 없게 됩니다. 사용자 정의 함수가 수행하는 작업은 Digital Twins 관리 API 내의 역할 기반 액세스 제어에서 제외됩니다. 특정 역할 또는 특정 액세스 제어 경로를 지정하여 범위를 제한할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어](./security-role-based-access-control.md) 설명서를 참조하세요.

1. 역할에 대해 쿼리하고 UDF에 할당할 역할의 ID를 가져와서 아래의 **RoleId**에 전달합니다.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourManagementApiUrl* | 관리 API의 전체 URL 경로  |

2. **ObjectId**는 앞서 만든 UDF ID입니다.
3. `fullpath`를 사용하여 공간을 쿼리하여 **Path** 값을 찾습니다.
4. 반환된 `spacePaths` 값을 복사합니다. 이 값을 아래에서 사용합니다.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourManagementApiUrl* | 관리 API의 전체 URL 경로  |
| *yourSpaceName* | 사용할 공간의 이름 |

4. 이제, 반환된 `spacePaths` 값을 **Path**에 붙여 넣어 UDF 역할 할당을 만듭니다.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| 사용자 지정 특성 이름 | 바꿀 항목 |
| --- | --- |
| *yourManagementApiUrl* | 관리 API의 전체 URL 경로  |
| *yourDesiredRoleIdentifier* | 원하는 역할의 식별자 |
| *yourUserDefinedFunctionId* | 사용할 UDF의 ID |
| *yourAccessControlPath* | 액세스 제어 경로 |

## <a name="send-telemetry-to-be-processed"></a>처리될 원격 분석 전송

그래프에 설명된 센서에서 생성된 원격 분석은 업로드된 사용자 정의 함수가 실행되도록 트리거해야 합니다. 원격 분석이 데이터 프로세서에 의해 선택되면 사용자 정의 함수 호출에 대한 실행 계획이 생성됩니다.

1. 읽기가 생성된 센서의 검사기를 검색합니다.
1. 성공적으로 평가된 검사기에 따라 연결된 사용자 정의 함수를 검색합니다.
1. 각 사용자 정의 함수를 실행합니다.

## <a name="client-reference"></a>클라이언트 참조

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

공간 식별자를 지정하여 그래프에서 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | 공간 식별자 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

센서 식별자를 지정하여 그래프에서 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | 센서 식별자 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

장치 식별자를 지정하여 그래프에서 장치를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 장치 식별자 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

센서 식별자 및 해당 데이터 형식을 지정하여 센서에 대한 현재 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | 센서 식별자 |
| *dataType*  | `string` | 센서 데이터 형식 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

공간 식별자 및 값 이름을 지정하여 공간 속성에 대한 현재 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 공간 식별자 |
| *valueName* | `string` | 공간 속성 이름 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

센서 식별자 및 해당 데이터 형식을 지정하여 센서에 대한 기록 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *dataType* | `string` | 센서 데이터 형식 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

공간 식별자 및 값 이름을 지정하여 공간의 해당 속성에 대한 기록 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *valueName* | `string` | 공간 속성 이름 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

공간 식별자를 지정하여 해당 부모 공간에 대한 자식 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

공간 식별자를 지정하여 해당 부모 공간에 대한 자식 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

공간 식별자를 지정하여 해당 부모 공간에 대한 자식 장치를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

장치 식별자를 지정하여 해당 부모 장치에 대한 자식 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 장치 식별자 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

공간 식별자를 지정하여 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 공간 식별자 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

센서 식별자를 지정하여 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 센서 식별자 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

장치 식별자를 지정하여 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | 장치 식별자 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

센서 식별자를 지정하여 해당 부모 장치를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 센서 식별자 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

공간 식별자를 지정하여 공간에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *propertyName* | `string` | 공간 속성 이름 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

센서 식별자를 지정하여 센서에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *propertyName* | `string` | 센서 속성 이름 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

장치 식별자를 지정하여 장치에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 장치 식별자 |
| *propertyName* | `string` | 장치 속성 이름 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

지정된 데이터 형식을 사용하여 센서 개체의 값을 설정합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *dataType*  | `string` | 센서 데이터 형식 |
| *값*  | `string` | 값 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

지정된 데이터 형식을 사용하여 공간 개체의 값을 설정합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *dataType* | `string` | 데이터 형식 |
| *값* | `string` | 값 |

### <a name="logmessage"></a>log(message)

사용자 정의 함수 내에서 다음 메시지를 기록합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 기록할 메시지 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

디스패치할 사용자 지정 알림을 전송합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | 그래프 개체 식별자(예: 공간/센서/장치 ID)|
| *topologyObjectType*  | `string` | 예: 공간/센서/장치)|
| *payload*  | `string` | 알림을 사용하여 보낼 JSON 페이로드입니다. |

## <a name="return-types"></a>반환 형식

위의 클라이언트 참조에서 반환 개체를 설명하는 모델은 다음과 같습니다.

### <a name="space"></a>공백

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>공간 메서드

#### <a name="parent--space"></a>Parent() ⇒ `space`

현재 공간의 부모 공간을 반환합니다.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

현재 공간의 자식 센서를 반환합니다.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

현재 공간의 자식 장치를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

현재 공간에 대한 확장된 속성 및 해당 값을 반환합니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

현재 공간의 값을 반환합니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 값의 이름 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

현재 공간의 기록 값을 반환합니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 값의 이름 |

#### <a name="notifypayload"></a>Notify(payload)

지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="device"></a>장치

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>장치 메서드

#### <a name="parent--space"></a>Parent() ⇒ `space`

현재 장치의 부모 공간을 반환합니다.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

현재 장치의 자식 센서를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

현재 장치에 대한 확장된 속성 및 해당 값을 반환합니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="notifypayload"></a>Notify(payload)

지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="sensor"></a>센서

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>센서 메서드

#### <a name="space--space"></a>Space() ⇒ `space`

현재 센서의 부모 공간을 반환합니다.

#### <a name="device--device"></a>Device() ⇒ `device`

현재 센서의 부모 장치를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

현재 센서에 대한 확장된 속성 및 해당 값을 반환합니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="value--value"></a>Value() ⇒ `value`

현재 센서의 값을 반환합니다.

#### <a name="history--value"></a>History() ⇒ `value[]`

현재 센서의 기록 값을 반환합니다.

#### <a name="notifypayload"></a>Notify(payload)

지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | type                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="value"></a>값

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>확장 속성

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>다음 단계

이벤트를 보낼 Digital Twins 엔드포인트를 만드는 방법을 알아보려면 [Digital Twins 엔드포인트 만들기](how-to-egress-endpoints.md)를 참고하세요.

Digital Twins 엔드포인트에 대한 자세한 내용은 [엔드포인트에 대한 자세한 방법](concepts-events-routing.md)을 참조하세요.
