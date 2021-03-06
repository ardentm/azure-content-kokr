<properties
   pageTitle="Azure SQL 데이터 웨어하우스에 대한 인증 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스에 대한 AAD(Azure Active Directory) 및 SQL Server 인증"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/17/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스에 대한 인증

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-connect-overview.md)
- [인증](sql-data-warehouse-authentication.md)
- [드라이버](sql-data-warehouse-connection-strings.md)

SQL 데이터 웨어하우스에 연결하려면 인증 목적으로 보안 자격 증명을 전달해야 합니다. 연결을 설정할 때 특정 연결 설정이 쿼리 세션을 설정하는 일부로 구성된다는 사실도 확인하게 될 것입니다.

보안에 대한 자세한 내용과 데이터 웨어하우스에 대한 연결을 설정하는 방법은 [SQL 데이터 웨어하우스에서 데이터베이스 보호][]를 참조하세요.

## SQL 인증
SQL 데이터 웨어하우스에 연결하려면 다음 정보를 제공해야 합니다.

- 정규화된 서버 이름
- SQL 인증 지정
- 사용자 이름
- 암호
- 기본 데이터베이스(옵션)

참고로 사용자는 SQL 인증을 사용하여 인증해야 합니다. 현재 현재할 수 있는 인증은 지원되지 않습니다.

기본적으로 사용자의 연결은 사용자의 사용자 데이터베이스가 아닌 마스터 데이터베이스에 연결합니다. 사용자의 사용자 데이터베이스에 연결하려면 다음과 같은 두 가지 작업 중 하나를 수행하도록 선택할 수 있습니다.

1. SSDT의 SQL Server 개체 탐색기에서 서버를 등록할 때 또는 응용 프로그램 연결 문자열에 기본 데이터베이스를 지정합니다. 예를 들어 ODBC 연결에 대해 InitialCatalog 매개 변수를 포함합니다.
2. SSDT에서 세션을 만들기 전에 먼저 사용자 데이터베이스를 강조 표시합니다.

> [AZURE.NOTE] SSDT를 사용하여 SQL 데이터 웨어하우스에 연결하는 지침은 [Visual Studio로 쿼리][] 문서를 다시 참조하세요.

연결을 위한 데이터베이스 변경의 경우 Transact-SQL 문 **USE<your DB>**이 지원되지 않는다는 데 유의해야 합니다.


## AAD(Azure Active Directory) 인증

Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 Microsoft Azure SQL 데이터 웨어하우스에 연결하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 SQL 데이터 웨어하우스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다.

### 이점

이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
- 한 위치에서의 암호 회전이 가능합니다.
- 고객이 외부(AAD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- Azure Active Directory 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- Azure Active Directory는 SQL 데이터 웨어하우스에 연결되는 응용 프로그램에 대한 토큰 기반 인증을 지원합니다.

> [AZURE.IMPORTANT] Azure Active Directory 인증은 미리 보기 기능으로, 사용권 계약(예: 기업 계약, Microsoft Azure 계약 또는 Microsoft 온라인 구독 계약)의 미리 보기 약관과 해당하는 [Microsoft Azure 미리 보기 추가 사용 특약](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

### 구성 단계

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

1. Azure Active Directory 만들기 및 채우기
2. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure SQL 데이터 웨어하우스에 대한 Azure Active Directory 관리자 만들기
4. 클라이언트 컴퓨터 구성
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기
6. Azure AD ID를 사용하여 데이터 웨어하우스에 연결합니다.

Azure SQL 데이터베이스와 함께 사용하는 Azure Active Directory 인증과 Azure SQL 데이터 웨어하우스와 함께 사용하는 Azure Active Directory 인증의 주요 차이점은 SQL 데이터 웨어하우스에 연결하기 위해서는 SQL Server Management Studio 대신 SQL Server Data Tools를 사용해야 한다는 점입니다. SQL 데이터 웨어하우스는 Visual Studio 2015용 SQL Server Data Tools의 2016년 4월 버전 이상을 필요로 합니다(버전 14.0.60311.1). 현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 봅니다.
  
### 세부 정보 찾기
- 자세한 단계를 완료합니다. Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스를 구성하고 Azure Active Directory 인증을 사용하는 자세한 단계는 거의 동일합니다. [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스 연결](../sql-database/sql-database-aad-authentication.md) 항목의 자세한 단계를 따릅니다.
- 사용자 지정 데이터베이스 역할을 만들고 역할에 사용자를 추가 합니다. 그런 다음 역할에 세부적인 권한을 부여합니다. 자세한 내용은 [데이터베이스 엔진 권한 시작](https://msdn.microsoft.com/library/mt667986.aspx)을 참조하세요.

## 다음 단계

Visual Studio 및 다른 응용 프로그램으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][]를 참조하세요.

<!-- Article references -->
[SQL 데이터 웨어하우스에서 데이터베이스 보호]: sql-data-warehouse-overview-manage-security.md
[Visual Studio로 쿼리]: sql-data-warehouse-query-visual-studio.md
[Visual Studio를 사용하여 쿼리]: sql-data-warehouse-query-visual-studio.md

<!---HONumber=AcomDC_0622_2016-->