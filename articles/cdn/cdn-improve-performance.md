<properties
	pageTitle="CDN - 파일을 압축하여 성능 향상"
	description="파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시킬 수 있습니다."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

# 파일을 압축하여 성능 향상

압축은 파일이 서버에서 전송되기 전에 파일 크기를 줄여서 파일 전송 속도를 개선하고 페이지 로드 성능을 높이는 간단하고 효과적인 방법입니다. 대역폭 비용을 절감하고 사용자에게 반응이 빠른 환경을 제공합니다.

압축을 사용하도록 설정하는 방법은 두 가지입니다.

- 원본 서버에서 압축을 사용하도록 설정할 수 있으며, 이런 경우 CDN은 압축된 파일을 거쳐서 압축된 파일을 요청한 클라이언트에 압축된 파일을 전달합니다.
- CDN 에지 서버에서 직접 압축을 사용하도록 설정할 수 있으며, 이런 경우 CDN이 파일을 압축하여(원본 서버에 의해 압축되지 않더라도) 최종 사용자에게 제공합니다.

> [AZURE.IMPORTANT] CDN 구성 변경이 네트워크 전체에 전파되려면 다소 시간이 걸립니다. <b>Akamai의 Azure CDN</b> 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. <b>Verizon의 Azure CDN</b> 프로필의 경우, 변경 내용이 일반적으로 90분 내에 적용됩니다. CDN 끝점에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다렸다가 문제 해결을 시도하는 것이 좋습니다.

## 압축을 사용하도록 설정

> [AZURE.NOTE] 표준 및 프리미엄 CDN 계층은 동일한 압축 기능을 제공하지만 사용자 인터페이스는 다릅니다. 표준과 프리미엄 CDN 계층 간의 차이점에 대한 자세한 내용은 [Azure CDN 개요](cdn-overview.md)를 참조하세요.

### 표준 계층

> [AZURE.NOTE] 이 섹션은 **Verizon의 Azure CDN Standard** 및 **Akamai의 Azure CDN Standard** 프로필에 적용됩니다.

1. CDN 프로필 블레이드에서 관리하려는 CDN 끝점을 클릭합니다.

	![CDN 프로필 블레이드 끝점](./media/cdn-file-compression/cdn-endpoints.png)

	CDN 끝점 블레이드가 열립니다.

2. **구성** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-file-compression/cdn-config-btn.png)

	CDN 구성 블레이드가 열립니다.

3. **압축**을 켭니다.

	![CDN 압축 옵션](./media/cdn-file-compression/cdn-compress-standard.png)

4. 기본 형식을 사용하거나 파일 형식을 추가 또는 제거하여 목록을 수정합니다.
	
	> [AZURE.TIP] 가능하기는 하지만 ZIP, MP3, MP4, JPG, 등 압축된 형식에 압축을 적용하는 것은 좋지 않습니다.
	
5. 변경한 후 **저장** 단추를 클릭합니다.

### 프리미엄 계층

> [AZURE.NOTE] 이 섹션은 **Verizon의 Azure CDN Premium** 프로필에 적용됩니다.

1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-file-compression/cdn-manage-btn.png)

	CDN 관리 포털이 열립니다.

2. **HTTP Large**(HTTP 크게) 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다. **압축**을 클릭합니다.

	압축 옵션이 표시됩니다.

	![파일 압축](./media/cdn-file-compression/cdn-compress-files.png)

3. **압축 사용** 라디오 단추를 클릭하여 압축을 활성화합니다. 쉼표로 구분된 목록(공백 없음)으로 압축하려는 MIME 유형을 **파일 유형** 텍스트 상자에 입력합니다.
		
	> [AZURE.TIP] 가능하기는 하지만 ZIP, MP3, MP4, JPG, 등 압축된 형식에 압축을 적용하는 것은 좋지 않습니다.

4. 변경한 후 **업데이트** 단추를 클릭합니다.


## 압축 규칙

이 테이블은 모든 시나리오에 적용되는 Azure CDN 압축 동작을 설명합니다.

> [AZURE.IMPORTANT] **Verizon의 Azure CDN**(Standard 및 Premium)의 경우, 적합한 파일만 압축될 수 있습니다. 압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
>
> - 128바이트 초과.
> - 1MB 미만.
> 
> **Akamai의 Azure CDN**의 경우, 모든 파일이 압축에 적합합니다.
>
> 모든 Azure CDN 제품의 경우, 파일은 [압축용으로 구성된](#enabling-compression) MIME 형식이어야 합니다.
>
> **Verizon의 Azure CDN** 프로필(Standard 및 Premium)은 **gzip**, **deflate**, 또는 **bzip2** 인코딩을 지원합니다. **Akamai의 Azure CDN** 프로필은 **gzip** 인코딩만 지원합니다.
>
> **Akamai의 Azure CDN** 끝점은 클라이언트 요청에 상관없이, 원본으로부터 항상 **gzip** 인코딩 파일을 요청합니다.

### 압축이 비활성화되었거나 파일이 압축에 부적합

|클라이언트 요청 형식(Accept-Encoding 헤더를 통한)|캐시된 파일 형식|클라이언트에 대한 CDN 응답|참고|
|----------------|-----------|------------|-----|
|압축|압축|압축| |
|압축|미압축|미압축| |	
|압축|캐시되지 않음|압축 또는 미압축|원본 응답에 따라 결정|
|미압축|압축|미압축| |
|미압축|미압축|미압축| |	
|미압축|캐시되지 않음|미압축| |

### 압축이 활성화되고 파일이 압축에 적합

|클라이언트 요청 형식(Accept-Encoding 헤더를 통한)|캐시된 파일 형식|클라이언트에 대한 CDN 응답|참고|
|----------------|-----------|------------|-----|
|압축|압축|압축|지원되는 형식 간 CDN 코드 변환|
|압축|미압축|압축|CDN이 압축 수행|
|압축|캐시되지 않음|압축|원본이 미압축 상태로 반환되면 CDN가 압축 수행|
|미압축|압축|미압축|CDN이 압축 해제 수행|
|미압축|미압축|미압축| |	
|미압축|캐시되지 않음|미압축| |	

## 참고
1. 미디어 서비스 CDN 사용 스트리밍 끝점의 경우 다음 콘텐츠 형식에 대해 기본적으로 압축이 사용됩니다. application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Azure 포털에서 언급된 형식에 대해 압축을 사용하거나 사용하지 않도록 설정할 수 없습니다.  

## 참고 항목
- [CDN 파일 압축 문제 해결](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0518_2016-->