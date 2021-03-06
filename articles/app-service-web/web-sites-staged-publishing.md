<properties
	pageTitle="Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정"
	description="Azure 앱 서비스에서 웹앱에 대한 준비된 개시를 사용하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정
<a name="Overview"></a>

웹앱을 [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포할 때 **표준** 또는 **프리미엄** 앱 서비스 계획 모드에서 실행 시 기본 프로덕션 슬롯 대신 개별 배포 슬롯으로 배포할 수 있습니다. 배포 슬롯은 실제로 고유한 호스트 이름이 있는 라이브 웹 앱입니다. 웹 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 응용 프로그램을 배포 슬롯에 배포하면 다음과 같은 이점이 있습니다.

- 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 웹 앱 변경 사항의 유효성을 검사할 수 있습니다.

- 먼저 슬롯으로 웹 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 웹 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요하지 않은 경우 [자동 교환](#configure-auto-swap-for-your-web-app)을 구성하여 이 전체 워크플로를 자동화할 수 있습니다.

- 교환 후에는 이전의 준비된 웹 앱이 들어 있던 슬롯 안에 이전의 프로덕션 웹 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다.

각 앱 서비스 계획 모드는 다양한 수의 배포 슬롯을 지원합니다. 웹앱 모드가 지원하는 슬롯의 수를 알아보려면 [앱 서비스 가격](/pricing/details/app-service/)을 참조하세요.

- 웹 앱에 여러 슬롯이 있을 때에는 모드를 변경할 수 없습니다.

- 프로덕션이 아닌 슬롯에 대해 크기 조정을 사용할 수 없습니다.

- 연결된 리소스 관리는 프로덕션이 아닌 슬롯에 대해 지원되지 않습니다. 특별히 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서는 프로덕션이 아닌 슬롯을 다른 앱 서비스 계획으로 일시적으로 전환함으로써 프로덕션 슬롯에 미칠 수 있는 영향을 방지할 수 있습니다. 프로덕션 슬롯이 아닌 경우에는 두 슬롯을 교환하려면 프로덕션 슬롯과 같은 모드로 다시 한 번 공유해야 합니다.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## 웹앱에 배포 슬롯 추가 ##

여러 배포 슬롯을 사용하려면 웹앱이 **표준** 또는 **프리미엄** 모드에서 실행 중이어야 합니다.

1. [Azure 포털](https://portal.azure.com/)에서 웹앱의 블레이드를 엽니다.
2. **설정**을 클릭한 다음 **배포 슬롯**을 클릭합니다. 그런 다음 **배포 슬롯** 블레이드에서 **슬롯 추가**를 클릭합니다.

	![새 배포 슬롯 추가][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	웹 앱이 **표준** 또는 **프리미엄** 모드가 아닌 경우 준비된 게시를 사용하려면 지원 모델을 나타내는 메시지를 받게 됩니다. 이때 **업그레이드**를 선택할 수 있는 옵션이 제공되고 계속하기 전에 웹 앱의 **크기 조정** 탭으로 이동합니다.

2. **슬롯 추가** 블레이드에서 슬롯에 이름을 지정하고, 다른 기존 배포 슬롯으로부터 웹 앱 구성을 복제할 것인지 여부를 선택합니다. 확인 표시를 클릭하여 계속합니다.

	![구성 원본][ConfigurationSource1]

	슬롯을 처음 추가할 때는 두 가지 선택만 가능합니다. 프로덕션의 기본 슬롯으로부터 구성을 복제하거나, 구성을 아예 복제하지 않는 것입니다.

	여러 개의 슬롯을 만든 후에는 프로덕션이 아닌 슬롯으로부터 구성을 복제할 수 있습니다.

	![구성 원본][MultipleConfigurationSources]

5. **배포 슬롯** 블레이드에서 배포 슬롯을 클릭하여 다른 모든 웹앱과 마찬가지로 구성 및 메트릭 집합이 있는 슬롯의 블레이드를 엽니다. 배포 슬롯을 보고 있음을 나타내는 **your-web-app-name-deployment-slot-name**이 블레이드 맨 위에 표시됩니다.

	![배포 슬롯 제목][StagingTitle]

5. 슬롯의 블레이드에서 앱 URL을 클릭합니다. 배포 슬롯은 고유의 호스트 이름을 가지고 있고 Live App이기도 합니다. 배포 슬롯에 대한 공용 액세스를 제한하려면 [앱 서비스 웹 앱 – 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)을 참조하세요.

배포 슬롯을 만든 후 콘텐츠가 없습니다. 다른 리포지토리 분기 또는 아예 다른 리포지토리로부터 슬롯에 배포할 수 있습니다. 슬롯의 구성을 변경할 수도 있습니다. 게시 프로필을 사용하거나 콘텐츠 업데이트를 위해 배포 슬롯에 연결된 배포 자격 증명을 사용합니다. 예를 들어 [git를 사용하여 이 슬롯에 게시](web-sites-publish-source-control.md)할 수 있습니다.

<a name="AboutConfiguration"></a>
## 배포 슬롯의 구성 ##
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 또한, 교환 후(특정 슬롯) 다른 구성 요소는 동일한 슬롯에 남아 있지만 일부 구성 요소는 교환(특정 슬롯 아님)에 따라 콘텐츠를 따릅니다. 다음 목록은 슬롯을 교환할 때 변경되는 구성을 보여 줍니다.

**교환된 설정**:

- 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
- 앱 설정(슬롯에 맞도록 구성할 수 있음)
- 연결 설정(슬롯에 맞도록 구성할 수 있음)
- 처리기 매핑
- 모니터링 및 진단 설정
- WebJob 콘텐츠

**교환되지 않은 설정**:

- 게시 끝점
- 사용자 지정 도메인 이름
- SSL 인증서 및 바인딩
- 크기 조정 설정
- WebJob 스케줄러

슬롯에 맞도록(교환되지 않음) 앱 설정 또는 연결 문자열을 구성하려면 특정 슬롯에 대해 **응용 프로그램 설정** 블레이드에 액세스한 다음 슬롯에 맞아야 하는 구성 요소에 대한 **슬롯 설정** 상자를 선택합니다. 특정 슬롯으로 구성 요소를 표시하면 웹 앱과 연결된 모든 배포 슬롯에서 교환할 수 없도록 요소를 설정하는 효과가 있습니다.

![슬롯 설정][SlotSettings]

<a name="Swap"></a>
## 배포 슬롯을 교환하려면 ##

>[AZURE.IMPORTANT] 배포 슬롯에서 프로덕션으로 웹 앱을 교환하기 전에 특정 슬롯이 아닌 모든 설정이 해당 교환 대상에서 원하는 대로 정확히 구성되어야 합니다.

1. 배포 슬롯을 교환하려면 웹앱의 명령 모음 또는 배포 슬롯의 명령 모음에서 **교환** 단추를 클릭합니다. 교환 원본 및 교환 대상이 제대로 설정되어야 합니다. 일반적으로 교환 대상은 프로덕션 슬롯입니다.  

	![교환 단추][SwapButtonBar]

3. **확인**을 클릭하여 작업을 완료합니다. 작업을 마치면 배포 슬롯이 교환됩니다.

## 웹앱에 대한 자동 교환 구성 ##

자동 교환은 웹 앱의 최종 사용자를 위해 중단 시간 및 콜드 부팅이 발생하지 않는 웹 앱을 지속적으로 배포하려는 DevOps 시나리오를 간소화합니다. 배포 슬롯이 자동 교환에 대해 프로덕션에 구성될 때, 해당 슬롯에 이미 준비된 후에 해당 슬롯에 코드 업데이트를 푸시할 때마다 앱 서비스가 웹 앱을 프로덕션으로 자동 교환합니다.

>[AZURE.IMPORTANT] 슬롯에 대해 자동 교환을 사용할 때 슬롯 구성은 정확히 대상 슬롯(일반적으로 프로덕션 슬롯)에 의도한 구성이어야 합니다.

슬롯에 대한 자동 교환 구성은 쉽습니다. 다음 단계를 따르세요.

1. **배포 슬롯** 블레이드에서 비 프로덕션 슬롯을 선택하고 해당 슬롯의 블레이드에 대해 **모든 설정**을 클릭합니다.  

	![][Autoswap1]

2. **응용 프로그램 설정**을 클릭합니다. **자동 교환**은 **켜기**로 선택하고 **자동 교환 슬롯**에서 원하는 대상 슬롯을 선택한 다음 명령 모음에서 **저장**을 클릭합니다. 슬롯에 대한 구성은 정확히 대상 슬롯에 의도한 구성이어야 합니다.

	작업이 완료되면 **알림** 탭에 **성공**이 녹색으로 깜박입니다.

	![][Autoswap2]

	>[AZURE.NOTE] 웹앱에 대한 자동 교환을 테스트하려면 먼저 **자동 교환 슬롯**에서 비 프로덕션 대상 슬롯을 선택하여 기능에 익숙해져야 합니다.

3. 해당 배포 슬롯에 코드 푸시를 실행합니다. 자동 교환은 짧은 시간 후에 발생하며 업데이트는 대상 슬롯의 URL에 반영됩니다.

<a name="Multi-Phase"></a>
## 웹앱에 대해 다단계 교환 사용 ##

다단계 교환은 연결 문자열 같은 슬롯에 맞도록 설계된 구성 요소의 컨텍스트에서 유효성 검사를 단순화하기 위해 사용할 수 있습니다. 이러한 경우 교환 대상에서 교환 원본으로 이러한 구성 요소를 적용하고 교환이 실제로 적용되기 전에 유효성을 검사하는 데 유용할 수 있습니다. 교환 대상 구성 요소가 교환 원본에 적용되면 사용 가능한 작업이 교환을 완료하거나 교환 원본에 대한 원래 구성으로 되돌아가 교환이 취소되는 효과도 있습니다. 다단계 교환에 사용 가능한 Azure PowerShell cmdlet 샘플은 배포 슬롯 섹션에 대한 Azure PowerShell cmdlet에 포함되어 있습니다.

<a name="Rollback"></a>
## 교환 후 프로덕션 앱을 롤백하려면 ##

슬롯 교환 후 프로덕션에서 오류가 발견되면 같은 두 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 롤백하세요.

<a name="Warm-up"></a>
## 교환하기 전에 사용자 지정 준비 ##

일부 앱에는 사용자 지정 준비 작업이 필요할 수 있습니다. web.config의 applicationInitialization 구성 요소를 사용하면 요청을 받기 전에 수행할 사용자 지정 초기화 작업을 지정할 수 있습니다. 스왑 작업은 이 사용자 지정 준비가 완료될 때까지 대기합니다. 샘플 web.config 조각은 다음과 같습니다.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## 배포 슬롯을 삭제하려면##

배포 슬롯에 대한 블레이드의 명령 모음에서 **삭제**를 클릭합니다.

![배포 슬롯 삭제][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## 배포 슬롯용 Azure PowerShell cmdlet

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure 앱 서비스에서 웹 앱 배포 슬롯을 관리하는 기능도 지원합니다.

- Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.  

----------

### 웹앱 만들기

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### 웹앱에 대한 배포 슬롯 만들기

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### 다단계 교환 시작 및 대상 슬롯 구성을 원본 슬롯에 적용

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### 다단계 스왑의 첫 번째 단계 되돌리기 및 원본 슬롯 구성 복원

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### 배포 슬롯 교환

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### 배포 슬롯 삭제

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## 배포 슬롯에 대한 Azure CLI(Azure 명령줄 인터페이스) 명령

Azure CLI는 Azure 작업을 위한 플랫폼 간 명령을 제공하며, 웹앱 배포 슬롯을 관리하는 기능을 지원합니다.

- Azure CLI 설치 및 구성 지침과 Azure CLI를 Azure 구독에 연결하는 방법에 대한 자세한 내용은 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.

-  Azure CLI에서 Azure 앱 서비스에 사용할 수 있는 명령을 나열하려면 `azure site -h`를 호출합니다.

----------
### azure site list
현재 구독의 웹앱 관련 정보를 확인하려면 다음 예에서와 같이 **azure site list**를 호출합니다.

`azure site list webappslotstest`

----------
### azure site create
배포 슬롯을 만들려면 다음 예에서와 같이 **azure site create**를 호출하고 기존 웹앱의 이름과 만들 슬롯의 이름을 지정합니다.

`azure site create webappslotstest --slot staging`

새 슬롯의 소스 제어를 사용하도록 설정하려면 다음 예에서와 같이 **--git** 옵션을 사용합니다.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
업데이트된 배포 슬롯을 프로덕션 앱으로 만들려면 다음 예에서와 같이 **azure site swap** 명령을 사용하여 교환 작업을 수행합니다. 이때 프로덕션 앱에는 중단 시간이나 콜드 부팅이 발생하지 않습니다.

`azure site swap webappslotstest`

----------
### azure site delete
더 이상 필요하지 않은 배포 슬롯을 삭제하려면 다음 예에서와 같이 **azure site delete** 명령을 사용합니다.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 다음 단계 ##
[Azure 앱 서비스 웹 앱 – 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 무료 평가판](/pricing/free-trial/)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=AcomDC_0309_2016-->