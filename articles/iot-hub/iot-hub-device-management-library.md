<properties
 pageTitle="장치 관리 라이브러리 소개 | Microsoft Azure"
 description="Azure IoT Hub 장치 관리(DM) 클라이언트 라이브러리"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Azure IoT Hub 장치 관리(DM) 클라이언트 라이브러리 소개

## 개요

Azure IoT Hub 장치 관리 클라이언트 라이브러리를 통해 Azure IoT Hub를 사용하여 IoT 장치를 관리할 수 있습니다. "관리"는 다시 부팅, 공장 재설정 및 펌웨어 업데이트와 같은 작업을 포함합니다. 현재 플랫폼 독립적인 C 라이브러리를 제공하지만 곧 다른 언어에 대한 지원을 추가할 것입니다. [Azure IoT Hub 장치 관리 개요][lnk-dm-overview]에서 설명한 대로 IoT Hub 장치 관리의 세 가지 주요 개념이 있습니다.

- 장치 쌍
- 장치 작업
- 장치 쿼리

이 개념에 대해 잘 알지 못하는 경우 계속하기 전에 해당 개요를 읽는 것이 좋습니다. 이러한 개념은 모두 클라이언트 라이브러리에 밀접하게 연결되어 있습니다.

DM 클라이언트 라이브러리는 장치 관리에 관하여 두 가지 주요 책임이 있습니다.

- 물리적 장치의 속성을 IoT Hub의 해당 장치 쌍과 동기화
- IoT Hub가 장치에 보낸 장치 작업 구성

물리적 장치의 장치 속성(예: 배터리 수준 및 일련 번호)은 주기적으로 클라우드 기반 장치 쌍과 동기화되므로 IoT Hub는 항상 각 물리적 장치의 최신 보기를 가지고 있습니다(장치가 연결되어 있는 한).

서비스가 물리적 장치와 상호 작용하는 주된 방법은 장치 작업과 장치 쌍을 통해 수행하는 것입니다. 다음 작업 유형이 IoT Hub에 제공됩니다. DM 클라이언트 라이브러리는 장치 작업에 대한 오케스트레이션을 대부분 관리하지만 개발자는 각 작업 유형을 지원하기 위해 자신의 장치에 대해 필요한 콜백을 구현해야 합니다.

1.	**펌웨어 업데이트**: 물리적 장치의 펌웨어(또는 OS 이미지)를 업데이트합니다.
2.	**다시 부팅**: 물리적 장치를 다시 부팅합니다.
3.	**공장 재설정**: 물리적 장치의 펌웨어(또는 OS 이미지)를 장치에 저장되어 있는 공장에서 제공한 백업 이미지로 되돌립니다.
4.	**구성 업데이트**: 물리적 장치에서 실행되는 IoT Hub 클라이언트 에이전트를 구성합니다.
5.	**장치 속성 읽기**: 물리적 장치에서 장치 속성에 대한 최신 값을 가져옵니다.
6.	**장치 속성 쓰기:** 물리적 장치의 장치 속성을 변경합니다.

다음 섹션에서는 클라이언트 라이브러리 아키텍처를 살펴볼 뿐만 아니라 장치에 대한 서로 다른 장치 개체를 구현하는 방법에 관한 지침도 제공합니다.

## 장치 관리 클라이언트 라이브러리 디자인 원칙 및 기능 개념
DM 클라이언트 라이브러리는 이식성 및 플랫폼 간 통합을 고려하여 설계되었습니다. 이러한 목표는 다음과 같은 설계 결정에 의해 달성되었습니다.

1.	다양한 장치에 대한 확장성을 수용하기 위해 COAP 표준 프로토콜 상의 LWM2M을 기반으로 하였습니다.
2.	광범위한 플랫폼에 대한 이식성을 촉진하기 위해 ANSI C99에서 작성되었습니다.
3.	높은 보안 시나리오에서 사용할 수 있도록 TCP/TLS 및 Azure IoT Hub 인증(SAS 토큰)을 통해 보안이 설정됩니다.
4.	기존 코드를 활용하고 커뮤니티에 다시 기여하기 위해 [Eclipse Wakaama][lnk-Wakaama] OSS 프로젝트를 기반으로 합니다.

### 관련 LWM2M 개념
다양한 장치에 대한 확장성을 수용하기 위해 LWM2M 표준을 선택하였습니다. 개발 환경을 단순화하기 위해 대부분의 프로토콜을 추상화하였습니다. 그러나 라이브러리의 기본 원칙, 주로 해당 데이터 모델 및 데이터가 송신되는 방법을 이해해야 합니다.

#### 개체 및 리소스: LWM2M의 데이터 모델
LWM2M 데이터 모델은 개체 및 리소스라는 개념을 도입합니다.

- **개체**는 시스템 내에서 서로 관련된 기능 엔터티 집합을 설명합니다(예: 장치 및 펌웨어 업데이트).
- **리소스**는 그러한 개체에 포함된 특성이나 작업을 설명합니다(예: 배터리 잔량 정보 및 다시 부팅 작업).

참고로 이 모델에 두 개의 "1:다" 관계가 있습니다.

- **장치 및 개체**: 각 장치에 개체가 여러 개 있을 수 있습니다. 예를 들어 Contoso 장치에는 “장치 개체" 및 “서버 개체"가 있습니다.(다음 섹션에서 개체에 대해 자세히 설명합니다.)
- **개체 및 리소스**: 각 개체에 리소스가 여러 개 있을 수 있습니다. 예를 들어 개체가 새 이미지를 저장하는 패키지 URI와 같은 Contoso 장치 펌웨어 업데이트 리소스를 포함할 수 있습니다.

#### 관찰/알림 패턴: LWM2M에서 데이터가 송신되는 방법
이러한 개념 외에도 데이터가 장치에서 서비스로 흐르는 방법을 이해해야 합니다. 이를 위해 LWM2M은 "관찰/알림" 패턴을 정의합니다. 물리적 장치가 서비스에 연결할 때 IoT Hub는 선택된 장치 속성에 대한 “관찰”을 시작합니다. 그런 다음 물리적 장치가 장치 속성 변경 내용을 서비스에 "알립니다".

클라이언트 라이브러리에서는 장치 관리 데이터를 장치에서 IoT Hub에 보내는 방법으로 관찰/알림 패턴을 구현하였습니다. 이 패턴은 두 매개 변수에 의해 제어됩니다.

- **최소 기간**: 장치가 관찰된 속성에 대한 업데이트 보내기를 지연시키는 시간의 기간입니다. 이는 5분으로 설정됩니다. 따라서 장치는 값이 더 자주 변경되더라도 최대 5분마다 관찰된 속성에 대한 업데이트를 보냅니다. 즉, 속성이 매 분마다 변경되더라도 서비스는 5분이 되었을 때에만 마지막 변경 내용을 확인합니다.

- **최대 기간**: 관찰된 속성이 변경되었는지 여부와 상관없이 장치가 속성의 값에 대한 업데이트를 보내는 시간의 기간입니다. 이는 6시간으로 설정됩니다. 따라서 장치는 값이 변경되지 않았더라도 최소 매 6시간마다 관찰된 속성의 값에 대한 업데이트를 보냅니다.

> [AZURE.NOTE]  이러한 매개 변수에 대한 유일한 예외는 펌웨어 업데이트 작업에 사용되는 속성의 최소 기간이 30초로 설정된다는 것입니다. 이렇게 하는 이유는 이러한 속성이 작업 실행 중에 매우 자주 변하기 때문이며, 따라서 업데이트 프로세스를 촉진하기 위해 최소 기간을 줄였습니다.

## 클라이언트 라이브러리 기능 및 아키텍처
개요에서 살펴본 것처럼 클라이언트 라이브러리가 관리하는 두 가지 주요 기능이 있습니다.

- 물리적 장치를 IoT Hub의 해당 장치 쌍과 동기화
- IoT Hub가 장치에 보낸 장치 작업을 구성합니다.

이 섹션에서는 각 기능을 심도 있게 살펴봅니다.

### 물리적 장치와 해당 장치 쌍 동기화
클라이언트 라이브러리는 관찰/알림 패턴을 사용하여 장치 쌍을 업데이트되도록 유지합니다. 장치 쌍은 물리적 장치의 서비스 쪽 표시임을 기억하세요. 이 동기화를 수행하기 위해 다음과 같은 프로세스가 이루어집니다.

1. 장치는 일반적으로 초기화 중에 서비스를 등록합니다. 예: "나는 장치이며, 나는 액세스 토큰 Y를 포함한 장치 ID Contoso를 가지고 있습니다."
2. 서비스는 개체에 대한 리소스를 관찰합니다. 예: "내 Contoso 장치의 배터리 수준을 업데이트되도록 유지합니다."
3. 장치는 리소스의 값을 서비스에 알립니다. 알림의 빈도 최소 및 최대 기간을 기반으로 합니다. 예: "오후 5시 00분 배터리 수준 99%, 오후 5시 05분 배터리 수준 90% 등"

### 장치 작업 구성: 장치 쌍 및 장치 작업이 함께 작동하는 방법
물리적 장치에 대해 작업을 수행하려면 서비스는 연결된 장치 쌍을 찾아야 합니다. 속성에 대해 쿼리하거나 특정 ID를 검색하여 이 작업을 수행할 수 있습니다. 쌍 ID(및 따라서 물리적 장치에 대한 일치)를 알고 있으면 서비스가 물리적 장치에 대해 장치 작업을 시작할 준비가 된 것입니다.

장치 작업은 수행할 특정 프로세스(예: 펌웨어 업데이트 단계)를 표시하는 다양한 명령의 집합을 표시합니다. 장치 작업은 여러 단계로 구성될 수 있습니다.

1.	장치 쌍은 장치 작업의 상태를 표시하는 속성을 가지고 있습니다.
2.	장치 작업의 서로 다른 프로세스를 진행하려면 장치 쌍의 속성이 특정 값이어야 하므로, 물리적 장치는 장치 쌍에 동기화하고 작업을 진행할 수 있도록 올바른 속성을 설정해야 합니다.

프로세스가 여러 단계로 구성된 경우 이 순서가 반복됩니다.(예: 펌웨어 업데이트 중에 장치 쌍은 작업 완료를 고려하기 전에 여러 단계에 걸쳐 해당 속성을 여러 번 변경합니다.)

## 클라이언트에서 장치 관리를 구현하기 위한 지침
이전 섹션에서는 장치 관리 클라이언트 라이브러리의 기능, 해당 디자인 원칙 및 해당 기능이 LWM2M과 관련되는 방법에 관하여 알아보았습니다. 이제 각 부분이 런타임에 통합되는 방법을 중점적으로 설명합니다.

기본적으로 클라이언트 라이브러리는 장치와 서비스 간의 통신을 처리하므로 장치별 논리의 구현만 남습니다. 이는 두 부분으로 구성됩니다.

1.	**장치별 세부 정보 구현**: 이 구현 과정에서는 해당 콜백에서 서비스가 요청한 작업(예: 펌웨어 패키지 다운로드)을 수행하는 장치별 논리를 씁니다. 펌웨어 업데이트 패키지에 대한 이 콜백의 예제를 아래에 열거합니다. [여기][lnk-github1] 폴더의 .c 파일에서 콜백을 찾을 수 있습니다.

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**속성이 변경될 때 클라이언트 라이브러리에 알림**: [여기][lnk-github2] 폴더의 .h 파일에서 해당 Set 함수를 호출하여 이 목적을 달성합니다.

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### DM 클라이언트 라이브러리에서 구현해야 하는 개체

방금 전까지 장치 작업을 수행하기 위해 장치별 논리를 구현하는 방법을 설명했습니다. 이제 사용할 수 있는 개체에 대해 설명합니다.

이러한 개체 중 일부는 필수입니다. 즉, 해당 개체가 IoT Hub 장치 관리의 일부가 되려면 장치별 논리를 구현해야 합니다. 다른 개체는 선택 사항이므로 서비스 필요에 따라 선택할 수 있습니다(예: IoT Hub를 사용하여 펌웨어 업데이트를 수행하지 않도록 선택할 수 있음). 다음은 각각에 대해 설명입니다.

- **장치 개체(필수)**: 제조업체 정보, 모델 번호, 일련 번호, 장치 시간과 같은 장치별 정보를 제공합니다. 서비스는 이 정보를 읽고 경우에 따라 업데이트할 수 있습니다. 또한 서비스는 장치에 대해 수행할 수 있는 두 가지 동작: 다시 부팅 및 공장 재설정을 정의합니다.
- **서버 개체(필수)**: 등록 및 전송 바인딩의 수명 등 IoT Hub에 연결하는 데 사용되는 연결 매개 변수 및 설정을 포함합니다. 서비스는 이 정보를 읽을 수만 있습니다.
- **구성 개체(선택 사항)**: 장치 구성을 촉진하기 위해 장치에서 쿼리하거나 서비스에서 장치로 푸시할 수 있는 사용자 정의 구성 정보를 포함합니다. 서비스는 이 정보를 읽고 업데이트할 수 있습니다.
- **펌웨어 업데이트 개체(선택 사항)**: 서비스가 호출할 수 있는 펌웨어 업데이트 작업을 제공합니다. 또한 펌웨어 패키지의 위치 및 진행 중인 펌웨어 업데이트 작업의 상태와 같은 정보도 제공합니다.

이러한 각 개체에는 연결된 리소스 집합이 있습니다(1:다 연결임을 기억하세요). LWM2M 개체 및 Azure IoT Hub 장치 관리에서 지원되는 모든 연결된 리소스의 목록은 이 문서의 끝에 포함되어 있습니다.

> [AZURE.NOTE] 사용자 지정 장치 속성, 여러 리소스 인스턴스 및 다중 개체 인스턴스는 시스템의 현재 릴리스에서 지원되지 않습니다.

### 모든 항목 요약

아래에서 모든 부분을 통합한 다이어그램을 볼 수 있습니다. 파란색으로 나타난 오른쪽에서는 장치 관리 라이브러리의 서로 다른 구성 요소: 개체, 처리기 및 Notify 메서드를 볼 수 있습니다. 녹색으로 나타난 왼쪽은 장치 응용 프로그램 수준에서 써야 하는 논리를 표시합니다. 클라이언트 라이브러리는 응용 프로그램 논리를 IoT Hub와 연결하여 통신 및 구성이 적절히 처리되도록 합니다.

아래 그림은 DM 클라이언트 라이브러리 구성 요소를 나타냅니다.

![][img-library-overview]

## 다음 단계: 실습 경험 얻기
이 문서에서는 C에 대한 IoT Hub 장치 관리 클라이언트 라이브러리 사용의 기본을 다루었습니다.

실습 경험을 얻으려면 다음 리소스에 액세스할 수 있습니다.

- Intel Edison 펌웨어 업데이트 샘플: Intel Edison 장치를 사용하여 사용하도록 설정된 장치 관리 기능이 포함된 샘플입니다. [iotdm\_edison\_sample][lnk-edison-sample]을 참조하세요.
- 시뮬레이션된 장치 샘플: Linux 및 Windows 장치에서 실행되는 플랫폼 독립적인 장치 샘플입니다. [iotdm\_simple\_sample][lnk-simple-sample] 참조
- LWM2M 개체에 대해 더 자세히 알아보려면 [OMA LWM2M 개체 및 리소스 레지스트리][lnk-oma] 참조

## 부록: 현재 지원되는 LWM2M 개체 및 리소스

### 장치 개체

| 리소스 이름 | 리소스에 허용되는 원격 작업 | 형식 | 범위 및 단위 | 설명 |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| 제조업체 | 읽기 | 문자열 | | 제조업체 이름 |
| ModelNumber | 읽기 | 문자열 | | 모델 ID(제조업체가 지정한 문자열) |
| DeviceType | 읽기 | 문자열 | | 장치의 유형(제조업체가 지정한 문자열)<br/>참고: 이 유형은 서버 쪽 API **SystemPropertyNames.DeviceDescription**에 매핑됨 |
| SerialNumber | 읽기 | 문자열 | | 장치의 일련 번호 |
| FirmwareVersion | 읽기 | 문자열 | | 장치의 현재 펌웨어 버전 |
| HardwareVersion | 읽기 | 문자열 | | 장치의 현재 하드웨어 버전입니다. |
| Reboot | 실행 | | | 장치를 다시 부팅합니다. |
| FactoryReset | 실행 | | | 장치가 초기 배포 시와 같은 구성을 갖도록 장치에 대해 공장 재설정을 수행합니다. |
| CurrentTime | 읽기<br/>쓰기 | Time | | 장치의 현재 UNIX 시간입니다. 클라이언트는 매 초가 경과할 때마다 이 시간을 증가시켜야 합니다.<br/>DM 서버는 클라이언트가 서버의 시간과 동기화되도록 이 리소스를 쓸 수 있습니다. |
| UTCOffset | 읽기<br/>쓰기 | 문자열 | | 유효한 UTC 오프셋입니다. |
| 표준 시간대 | 읽기<br/>쓰기 | 문자열 | | 장치가 위치한 표준 시간대를 나타냅니다. |
| MemoryFree | 읽기 | Integer | KB | 데이터 및 소프트웨어를 장치에 저장할 수 있는 저장소 공간의 현재 추정 사용 가능 메모리 |
| MemoryTotal | 읽기 | Integer | KB | 데이터 및 소프트웨어를 장치에 저장할 수 있는 저장소 공간의 총량 |
| BatteryLevel | 읽기 | Integer | 0-100% | 백분율(0 ~ 100)로 표시한 현재 배터리 수준 |
| BatteryStatus | 읽기 | Integer | 0-6 | **0**: 배터리가 올바르게 작동하고 있으며 전원에 연결되지 않았습니다.<br/>**1**: 배터리가 충전 중입니다.<br/>**2**: 배터리가 완전히 충전되었으며 상용 전원에 연결되었습니다.<br/>**3**: 배터리가 손상되었습니다.<br/>**4**: 배터리 충전량이 부족합니다.<br/>**5**: 배터리가 존재하지 않습니다.<br/> **6**: 배터리 정보를 사용할 수 없습니다. |

### 펌웨어 업데이트 개체

| 리소스 이름 | 작업 | 형식 | 범위 및 단위 | 설명 |
|----------------|-----------|---------|-----------------|-------------|
| 패키지 | 쓰기 | 불투명 | | 이진 형식의 펌웨어 패키지.<br/>서비스 API에 매핑:<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageURI | 쓰기 | 문자열 | 0-255바이트 | 장치가 펌웨어 패키지를 다운로드할 수 있는 URI.<br/>서비스 API에 매핑: **SystemPropertyNames.FirmwarePackageUri** |
| 업데이트 | 실행 | | | 패키지에 저장된 펌웨어 패키지를 사용하거나 패키지 URI에서 다운로드한 펌웨어를 사용하여 펌웨어 업데이트.<br/>서비스 API에 매핑:<br/>**ScheduleFirmwareUpdateAsync** |
| 시스템 상태 | 읽기 | Integer | 1-3 | 펌웨어 업데이트 프로세스의 상태:<br/>**1**: 유휴 상태입니다. 이 프로세스는 펌웨어 패키지를 다운로드하기 전 또는 펌웨어 패키지를 적용한 후에 이루어질 수 있습니다.<br/>**2**: 펌웨어 패키지 다운로드.<br/>**3**: 펌웨어 패키지 다운로드됨.<br/> 서비스 API에 매핑: **SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | 읽기 | Integer | 0-6 | 펌웨어 다운로드 또는 업데이트의 결과<br/>**0**: 기본값.<br/>**1**: 펌웨어 업데이트 성공.<br/>**2**: 새 펌웨어 패키지에 대한 저장소 부족.<br/>**3**: 펌웨어 패키지 다운로드 중에 메모리 부족.<br/>**4**: 펌웨어 패키지 다운로드 중에 연결이 끊김.<br/>**5**: 새 다운로드된 패키지에 대한 CRC 검사 실패.<br/>**6**: 지원되지 않는 펌웨어 패키지 유형.<br/>**7**: 잘못된 URI. 서비스 API에 매핑: **SystemPropertyNames.FirmwareUpdateResult** |
| PkgName | 읽기 | 문자열 | 0-255바이트 | **패키지** 리소스가 참조한 펌웨어 패키지의 설명 이름<br/>서비스 API에 매핑:<br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | 읽기 | 문자열 | 0-255바이트 | **패키지** 리소스가 참조한 펌웨어 패키지의 버전<br/>서비스 API에 매핑:<br/>**SystemPropertyNames.FirmwarePackageVersion** |

### LWM2M 서버 개체

| 리소스 이름 | 작업 | 형식 | 범위 및 단위 | 설명 |
|------------------------|------------|---------|-----------------|---------------|
| 기본 최소 기간 | 읽기 쓰기 | Integer | 초 | 장치가 관찰된 속성에 대한 업데이트 보내기를 지연시키는 시간의 기간입니다. 예를 들어 **DefaultMinPeriod**가 5분으로 지정된 경우 장치는 값이 더 자주 변경되더라도 최대 5분마다 관찰된 속성에 대한 업데이트를 보냅니다. 서비스 API에 매핑: **SystemPropertyNames.DefaultMinPeriod** |
| 기본 최대 기간 | 읽기 쓰기 | Integer | 초 | 관찰된 속성이 변경되었는지 여부와 상관없이 장치가 속성의 값에 대한 업데이트를 보내는 시간의 기간(초)입니다. 예를 들어 **DefaultMaxPeriod**가 6시간으로 지정된 경우 관찰된 속성은 리소스 변경과 상관없이 최소 매 6시간마다 해당 속성의 값에 대한 업데이트를 보냅니다.<br/>서비스 API에 매핑:<br/>**SystemPropertyNames.DefaultMaxPeriod** |
| 수명 | 읽기 쓰기 | Integer | 초 | 장치의 등록 수명입니다. 새 등록 또는 업데이트 요청은 이 수명 이내에 장치에서 검색해야 하며, 그렇지 않으면 장치는 서비스에서 등록 해제됩니다.<br/>서비스 API에 대매핑:<br/>**SystemPropertyNames.RegistrationLifetime** |

### Config 개체

| 리소스 이름 | 작업 | 형식 | 범위 및 단위 | 설명 |
|---------------|------------|--------|-----------------|-------------|
| 이름 | 읽기 쓰기 | 문자열 | | 읽거나 업데이트할 장치 구성의 이름을 고유하게 식별합니다. |
| 값 | 읽기 쓰기 | 문자열 | | 읽거나 업데이트할 구성 값을 고유하게 식별합니다. |
| 적용 | 실행 | | | 구성 변경 내용을 장치에 적용합니다. |

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-run-linux]: http://TODO
[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

<!---HONumber=AcomDC_0608_2016-->