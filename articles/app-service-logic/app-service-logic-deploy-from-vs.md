<properties 
	pageTitle="Visual Studio에서 논리 앱 배포 | Microsoft Azure" 
	description="논리 앱을 관리하는 Visual Studio에서 프로젝트를 만듭니다." 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="stepsic"/>
	
# Visual Studio에서 배포

[Azure 포털](https://portal.azure.com/)에서 논리 앱을 디자인하고 관리하는 훌륭한 방법을 제공하지만 대신 Visual Studio에서 논리 앱을 배포하고자 할 수도 있습니다. 사용할 수 있는 몇 가지 주요 기능이 있습니다.

- 솔루션의 다른 자산과 함께 논리 앱을 저장하면 응용 프로그램의 모든 측면을 포함할 수 있습니다.
- 소스 제어에서 확인된 논리 앱 정의를 유지하면 TFS 또는 Git를 사용하여 수정 버전을 추적할 수 있습니다. 

아래의 다음 단계를 수행하기 위해 Azure SDK 2.7이 설치되어 있어야 합니다. 여기서 [VS용 최신 SDK](https://azure.microsoft.com/downloads/)을 찾습니다.

## 프로젝트 만들기

1. **파일** 메뉴로 이동하여 **새로 만들기** > **프로젝트** 선택합니다(또는 **추가**로 이동한 다음 **새 프로젝트**를 선택하여 기존 솔루션에 추가 가능). ![파일 메뉴](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. 대화 상자에서 **클라우드**를 찾아 **Azure 리소스 그룹**을 선택합니다. **이름**을 입력한 다음 **확인**을 클릭합니다. ![새 프로젝트 추가](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. 이제 **논리 앱** 또는 **논리 앱 및 API 앱**을 원하는지 선택해야 합니다. **논리 앱**을 선택하면 기존 API를 가리키도록 요청합니다. **논리 앱 및 API 앱**을 선택하면서 동시에 비어 있는 새 API 앱을 만들 수도 있습니다. 이 문서에서는 논리 앱을 선택했습니다. ![Azure 템플릿 선택](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. **템플릿**을 선택하면 **확인**을 누릅니다.

이제 논리 앱 프로젝트가 솔루션에 추가됩니다. 솔루션 탐색기에 배포 파일이 표시되어야 합니다. ![배포](./media/app-service-logic-deploy-from-vs/deployment.png)

## 논리 앱 구성

프로젝트가 있으면 Visual Studio 내에서 논리 앱 정의를 편집할 수 있습니다. 솔루션 탐색기에서 JSON 파일을 클릭합니다. 응용 프로그램의 논리를 사용하여 채울 수 있는 자리 표시자 정의를 확인합니다.

정의에 **매개 변수**를 사용하는 것이 좋습니다. 이는 개발 및 프로덕션 환경에 둘 다 배포하려는 경우에 유용합니다. 이 경우에 `*.parameters.json` 파일의 모든 환경 관련 구성을 배치하고 실제 문자열 대신 매개 변수를 사용해야 합니다.

현재 Visual Studio에 기본 제공된 JSON 설계자가 없으므로 그래픽 인터페이스(JSON와 대조적)를 사용하려는 경우 Azure 포털을 사용합니다.

이전에 Azure 포털 내에서 논리 앱을 만들고 이제 원본 제어로 체크 인하려는 경우 이를 수행할 수 있는 세 가지 방법이 있습니다.

- 포털에서 **코드 뷰**로 이동하여 정의를 복사합니다.
- 논리 앱 [REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx)를 사용하여 정의를 가져옵니다.
- [Azure Resource Manager PowerShell](../powershell-azure-resource-manager.md), 특히 [`Get-AzureResource` 명령](https://msdn.microsoft.com/library/dn654579.aspx)을 사용하여 정의를 다운로드합니다.

## 논리 앱 배포

마지막으로, 앱을 구성한 후 몇 단계 만에 Visual Studio에서 직접 배포할 수 있습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** > **새 배포...** ![새 배포](./media/app-service-logic-deploy-from-vs/newdeployment.png)로 이동합니다.

2. Azure 구독에 로그인하라는 메시지가 표시됩니다.

3. 이제 논리 앱을 배포하려는 리소스 그룹의 세부 정보를 선택해야 합니다. ![리소스 그룹 배포](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    리소스 그룹(예: 프로덕션 환경에 배포하는 경우 프로덕션 매개 변수 파일을 선택하고 싶어함)에 대한 올바른 템플릿 및 매개 변수 파일을 선택해야 합니다. 
4.  배포 단추를 선택합니다.
5. 감지하는 모든 오류를 해결하라는 메시지가 표시됩니다. 예: ![리소스 그룹 배포](./media/app-service-logic-deploy-from-vs/deploytoresourcegrouperror.png)
 
    
6. 배포의 상태가 **출력** 창(**Azure 프로비전**을 선택해야 할 수 있음)에 표시됩니다. ![출력](./media/app-service-logic-deploy-from-vs/output.png)

나중에 소스 제어에서 논리 앱을 수정하고 새 버전을 배포하기 위해 Visual Studio를 사용할 수 있습니다.

> [AZURE.NOTE] Azure 포털에서 정의를 직접 수정한 경우 다음에 Visual Studio에서 변경 내용을 배포하면 해당 변경 내용이 덮어쓰여집니다.

> [AZURE.TIP] Visual Studio를 사용하지 않고 싶지만 소스 제어에서 논리 앱을 배포하기 위해 여전히 도구를 사용하고자 하는 경우 [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) 또는 [Powershell](../powershell-azure-resource-manager.md)을 직접 사용하여 배포를 자동화하는 데 사용할 수 있습니다.

<!---HONumber=AcomDC_0511_2016-->