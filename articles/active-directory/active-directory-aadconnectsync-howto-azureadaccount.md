<properties
	pageTitle="Azure AD Connect 동기화: Azure AD 서비스 계정을 관리하는 방법 | Microsoft Azure"
	description="이 항목은 Azure AD 서비스 계정을 복원하는 방법을 설명합니다."
	services="active-directory"
    keywords="Azure AD Connect 동기화 커넥터 서비스 계정의 암호를 재설정하는 방법"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect 동기화: Azure AD 서비스 계정을 관리하는 방법
Azure AD Connector가 사용하는 서비스 계정은 무료입니다. 그러나 자격 증명을 재설정해야 할 경우, 이 항목을 참조하세요. 예를 들어, 전역 관리자가 PowerShell을 사용하여 실수로 암호를 재설정한 경우 이러한 문제가 발생할 수 있습니다.

## 자격 증명 다시 설정
인증 문제로 인해 Azure AD Connector에서 정의된 서비스 계정으로 Azure AD에 연결할 수 없다면 암호를 재설정할 수 있습니다.

1. Azure AD Connect 동기화 서버에 로그인하고 PowerShell을 시작합니다.
2. `Add-ADSyncAADServiceAccount`. ![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)을(를) 실행합니다.
3. Azure AD 전역 관리자 자격 증명을 제공합니다.

이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0615_2016-->