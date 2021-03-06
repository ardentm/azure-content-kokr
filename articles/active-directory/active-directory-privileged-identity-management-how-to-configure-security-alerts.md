<properties
   pageTitle="보안 경고를 구성하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 확장에 대한 보안 경고를 구성하는 방법 배우기"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management 에서 보안 경고를 구성하는 방법

## 보안 경고
Azure PIM(Privileged Identity Management)은 PIM 대시보드의 경고 섹션에서 볼 수 있는 다음과 같은 경고를 생성합니다.

| 경고 | 트리거 | 권장 사항 |
| ----- | ------- | -------------- |
| **영구 활성화** | 관리자가 영구적으로 PIM 외부에서 역할에 할당되었습니다. | 새 역할 할당을 검토하고 필요한 경우 임시로 변경합니다. |
| **권한 있는 역할의 의심스러운 활성화 갱신** | 설정에 허용된 시간 내에 동일한 역할을 다시 활성화한 횟수가 너무 많습니다. | 역할을 성공적으로 활성화할 수 있는지 사용자에게 확인합니다. |
| **역할 활성화에 대해 인증이 약하게 구성되었음** | 설정에 MFA가 없는 역할이 있습니다. | 모든 역할의 활성화에 대해 MFA를 요구하는 것이 좋습니다. |
| **중복 관리자** | 최근에 해당 역할을 활성화하지 않은 임시 관리자가 있습니다. | 더 이상 필요하지 않은 역할 할당을 제거합니다. |
| **너무 많은 전역 관리자** | 권장하는 것보다 전역 관리자가 많습니다. | 더 이상 필요 없는 역할 할당을 제거하거나 그중 일부를 임시 역할로 할당합니다. |

## 보안 경고 설정 구성

### “권한 있는 역할의 의심스러운 활성화 갱신” 경고

**활성화 갱신 기간** 및 **Number of activation renewals(활성화 갱신 횟수)** 설정을 구성하여 이 경고가 트리거되는 시기를 제어합니다.

1. 대시보드의 **활동** 섹션에서 **보안 경고**를 선택합니다. **Active security alerts**(활동 보안 경고) 블레이드가 표시됩니다.
2. **설정**을 클릭합니다.
3. 슬라이드를 조정하거나 텍스트 필드에 시간(분)을 입력하여 **Activation renewal timeframe**(활성화 갱신 기간)을 설정합니다. 최대값은 100입니다.
4. 슬라이드를 조정하거나 텍스트 필드에 갱신 횟수를 입력하여 활성화 갱신 기간 내 **Number of activation renewals**(활성화 갱신 횟수)를 설정합니다. 최대값은 100입니다.
5. **Save**를 클릭합니다.

### "중복 관리자" 경고
1. 대시보드의 **활동** 섹션에서 **보안 경고**를 선택합니다. **Active security alerts**(활동 보안 경고) 블레이드가 표시됩니다.
2. **설정**을 클릭합니다.
3. 슬라이드를 조정하거나 텍스트 필드에 기간(일수)을 입력하여 역할을 활성화하지 않고 허용되는 기간(일수)을 선택합니다.
4. **Save**를 클릭합니다.

### "너무 많은 전역 관리자" 경고

이 경고를 트리거할 수 있는 설정이 두 가지 있습니다.
- **Minimum number of Global Administrators(최소 전역 관리자 수)**는 관리자가 허용된 관리자의 수보다 많은 경우 경고를 트리거합니다.
- 전역 관리자인 관리자의 비율이 허용된 설정보다 높으면 **전역 관리자의 비율**이 경고를 트리거합니다.

1. 대시보드의 **활동** 섹션에서 **보안 경고**를 선택합니다. **Active security alerts**(활동 보안 경고) 블레이드가 표시됩니다.
2. **설정**을 클릭합니다.
3. 슬라이드를 조정하거나 텍스트 필드에 숫자를 입력하여 **Minimum number of Global Administrators**(최소 전역 관리자 수)를 설정합니다.
4. 슬라이드를 조정하거나 텍스트 필드에 백분율을 입력하여 **Percentage of Global Administrators(전역 관리자 비율)**을 설정합니다.
5. **Save**를 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->