<properties
   pageTitle="StorSimple 관리자 장치 대시보드 사용 | Microsoft Azure"
   description="StorSimple 관리자 서비스 장치 대시보드 및 이를 사용하여 저장소 메트릭 및 연결된 초기자를 보고 일련 번호 및 IQN을 찾는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/09/2016"
   ms.author="alkohli" />

# StorSimple 관리자 장치 대시보드 사용

## 개요

StorSimple 관리자 장치 대시보드는 Microsoft Azure StorSimple 솔루션에 포함된 모든 장치에 대한 정보를 제공하는 서비스 대시보드에 대한 특정 StorSimple 장치에 대한 정보 개요를 제공합니다.

![장치 대시보드 페이지](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

대시보드에는 다음 정보가 포함됩니다.

- **차트 영역** – 대시보드 상단 차트 영역에서 관련 저장소 메트릭을 볼 수 있습니다. 이 차트에서 총 기본 저장소(장치에 대한 호스트가 기록한 데이터의 양)와 일정 기간 동안 장치가 사용한 총 클라우드 저장소에 대한 메트릭을 볼 수 있습니다.

     이 컨텍스트에서 *기본 저장소*는 호스트가 기록한 데이터의 총 크기를 참조하고 볼륨 유형으로 세분화될 수 있습니다. *계층화된 기본 저장소*는 로컬에 저장된 데이터 및 클라우드에 계층화된 데이터를 모두 포함합니다. *로컬로 고정된 기본 저장소*는 로컬로 저장된 데이터만을 포함합니다. 반면에 *클라우드 저장소*는 클라우드에 계층화된 총 데이터를 측정합니다. 여기에는 계층화된 데이터 및 백업이 포함됩니다. 클라우드에 저장된 데이터는 중복 제거되고 압축되는 반면 기본 저장소는 데이터가 중복 제거되고 압축되기 전에 사용된 저장소의 양을 나타냅니다. (압축 비율을 파악하도록 이러한 두 값을 비교할 수 있습니다.) 기본 및 클라우드 저장소의 경우 표시되는 양은 사용자가 구성하는 추적 빈도에 따라 달라집니다. 예를 들어, 1주일 빈도를 선택하면 이전 주의 각 날짜에 대한 데이터가 차트에 표시됩니다.

	 차트는 다음과 같이 구성할 수 있습니다.

	 - 시간이 지남에 따라 사용되는 클라우드 저장소의 크기를 보려면 **CLOUD STORAGE USED(사용된 클라우드 저장소)** 옵션을 선택합니다. 호스트가 작성한 총 저장소를 보려면 **계층화되고 사용된 기본 저장소** 및 **로컬로 고정된 사용된 기본 저장소** 옵션을 선택합니다. 그림에서는 두 옵션이 모두 선택됩니다. 따라서 차트는 클라우드 및 기본 저장소에 대한 저장소 크기를 모두 보여줍니다. 업데이트 2를 설치하기 전에 사용된 기본 저장소는 **계층화되고 사용된 기본 저장소** 선에서 나타납니다.
	 - 1주, 1개월, 3개월 또는 1년 단위를 지정하려면 차트 오른쪽 상단의 드롭다운 메뉴를 사용하세요. 최상위 차트는 하루에 한번만 갱신되므로 전날의 합계를 반영합니다.

     자세한 내용은 [StorSimple Manager 서비스를 사용하여 StorSimple 장치 모니터링](storsimple-monitor-device.md)을 참조하세요.

- **사용 개요** – **사용량 개요** 영역에서 사용된 기본 저장소의 크기, 프로비전된 저장소의 크기 및 장치에 대한 최대 저장소 용량을 볼 수 있습니다. 이러한 사용량 수치와 사용할 수 있는 저장소의 최대 크기를 비교하여 추가 저장소가 필요한지 한 눈에 확인할 수 있습니다. 이 개요는 15분 마다 업데이트되며 업데이트 빈도의 차이로 인해 매일 업데이트되는 위의 차트 영역에 표시된 수치와 다를 수 있습니다. 자세한 내용은 [StorSimple Manager 서비스를 사용하여 StorSimple 장치 모니터링](storsimple-monitor-device.md)을 참조하세요.


- **경고** – **경고** 영역은 장치에 대한 경고의 개요를 포함합니다. 경고는 심각도별로 그룹화되며 숫자는 각 심각도 수준에 따른 경고의 수입니다. 경고 심각도를 클릭하면 범위가 지정된 경고 탭이 열려 이 장치에서 해당 심각도 수준의 경고만 표시합니다.

- **작업** – **작업** 영역은 최근 작업 활동의 결과를 보여 줍니다. 이를 통해 시스템이 예상대로 작동함을 알 수 있습니다. 또는 수정 작업을 취할 필요가 있는지 알 수 있습니다. 최근에 완료된 작업에 대한 자세한 내용은 **최근 24시간 동안 성공한 작업**을 클릭하세요.

- 대시보드 오른쪽의 **간략 상태** 영역에서는 장치 모델, 일련 번호, 상태, 설명 및 볼륨과 같은 유용한 정보를 제공합니다.

장치 대시보드에서 장애 조치를 구성하고 연결된 초기자를 볼 수도 있습니다.

이 페이지에서 수행할 수 있는 일반적인 작업은 다음과 같습니다.

- 연결된 초기자 보기

- 장치 일련 번호 찾기

- 장치 대상 IQN 찾기

## 연결된 초기자 보기

장치 대시보드의 **간략 보기** 영역에 있는 **연결된 초기자 보기** 링크를 클릭하여 장치에 연결된 iSCSI 초기자를 볼 수 있습니다. 이 페이지에서는 장치에 성공적으로 연결된 초기자 목록을 표 형식으로 제공합니다. 각 초기자에 대해 다음을 확인할 수 있습니다.

- 연결된 초기자의 IQN(정규화된 iSCSI 이름)입니다.

- 이 연결된 초기자를 허용하는 ACR(액세스 제어 레코드)의 이름입니다.

- 연결된 초기자의 IP 주소입니다.

- 초기자가 저장소 장치에 연결된 네트워크 인터페이스입니다. 이러한 범위는 DATA 0에서 DATA 5입니다.

- 연결된 초기자가 현재 ACR 구성에 따라 액세스하도록 허용된 모든 볼륨입니다.

이 목록에서 예상치 못한 초기자가 보이는 경우 또는 예상한 초기자가 보이지 않는 경우, ACR 구성을 확인합니다. 최대 512개의 초기자를 장치에 연결할 수 있습니다.

## 장치 일련 번호 찾기

장치에서 Microsoft MPIO(다중 경로 I/O)를 구성할 때 장치 일련 번호가 필요할 수 있습니다. 장치 일련 번호를 찾으려면 다음 단계를 수행합니다.

#### 장치 일련 번호 찾는 방법

1. **장치** > **대시보드**로 이동합니다.

2. 대시보드의 오른쪽 창에서 **간략 상태** 영역을 찾습니다.

3. 아래로 스크롤하여 일련 번호를 찾습니다.

## 장치 대상 IQN 찾기

StorSimple 장치에는 CHAP(Challenge Handshake 인증 프로토콜)를 구성할 때 장치 대상 IQN이 필요할 수 있습니다. 장치 대상 IQN을 찾으려면 다음 단계를 수행합니다.

### 장치 대상 IQN 찾는 방법

1. **장치** > **대시보드**로 이동합니다.

1. 대시보드의 오른쪽 창에서 **간략 상태** 영역을 찾습니다.

1. 아래로 스크롤하여 대상 IQN을 찾습니다.

## 다음 단계

- [StorSimple Manager 서비스 대시보드](storsimple-service-dashboard.md)에 대해 자세히 알아봅니다.
- [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

<!---HONumber=AcomDC_0601_2016-->