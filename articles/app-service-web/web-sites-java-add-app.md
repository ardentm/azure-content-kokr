<properties 
	pageTitle="Azure 앱 서비스 웹 앱에 Java 응용 프로그램 추가" 
	description="이 자습서에서는 페이지 또는 응용 프로그램을 Java를 사용하도록 이미 구성된 Azure 앱 서비스 웹 앱의 인스턴스를 추가하는 방법을 보여줍니다." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="robmcm"/>

# Azure 앱 서비스 웹 앱에 Java 응용 프로그램 추가

[Azure 앱 서비스에서 Java 웹 앱 만들기](web-sites-java-get-started.md)에 설명된 대로 [Azure 앱 서비스][]에서 Java 웹 앱을 초기화하면, **webapps** 폴더에 WAR을 배치하여 응용 프로그램을 업로드할 수 있습니다.

**webapps** 폴더의 탐색 경로는 웹 앱 인스턴스 설정 방법에 따라 다릅니다.

- Azure 마켓플레이스를 사용하여 웹 앱을 설정한 경우 **webapps** 폴더의 경로는 **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**의 형식이며, 여기서 **application\_server**는 웹 앱 인스턴스에 적용되는 응용 프로그램 서버의 이름입니다. 
- Azure 구성 UI를 사용하여 웹 앱을 설정한 경우 **webapps** 폴더의 경로는 **d:\\home\\site\\wwwroot\\webapps**의 형식입니다. 

소스 제어를 사용하여 연속 통합 시나리오에 포함된 응용 프로그램 또는 웹 페이지를 업로드할 수 있습니다. 웹 앱에서 소스 제어 사용에 관한 지침은 [Azure 앱 서비스에서 GIT를 사용하는 지속적인 배포](web-sites-publish-source-control.md)에 있습니다. FTP를 사용하여 응용 프로그램 또는 웹 페이지를 업로드할 수도 있습니다.

Tomcat 웹 앱에 대한 참고 사항: **webapps** 폴더에 WAR 파일을 업로드하면 Tomcat 응용 프로그램 서버에서 해당 파일을 추가했는지 검색한 후 자동으로 로드합니다. 파일(WAR 이외 파일)을 루트 디렉터리로 복사한 경우에는 해당 파일을 사용하기 전에 응용 프로그램 서버를 다시 시작해야 합니다. Azure에서 실행되는 Tomcat Java 웹 앱의 자동 로드 기능은 추가 중인 새 WAR 파일이나 **webapps** 폴더에 추가된 새 파일 또는 디렉터리를 기반으로 합니다.

## 다음 단계

자세한 내용은 [Java개발자 센터](/develop/java/)를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure 앱 서비스]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0504_2016-->