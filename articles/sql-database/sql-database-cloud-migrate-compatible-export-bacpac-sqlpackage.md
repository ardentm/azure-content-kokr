<properties
   pageTitle="SqlPackage를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 내보내기, BACPAC 파일 내보내기, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# SqlPackage를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

이 문서에서는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용하여 SQL Server 데이터베이스를 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일로 내보내는 방법을 보여 줍니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 최신 버전의 SQL Server Data Tools를 [다운로드](https://msdn.microsoft.com/library/mt204009.aspx)하여 이 유틸리티를 가져올 수도 있습니다.

1. 명령 프롬프트를 열고 sqlpackage.exe 명령줄 유틸리티가 들어 있는 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 컴퓨터에서 검색을 사용하여 사용자 환경에서 경로를 찾습니다.
2. 사용자 환경에 대해 다음 인수를 사용하여 다음 sqlpackage.exe 명령을 실행하세요.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| 인수 | 설명 |
	|---|---|
	| < server_name > | 원본 서버 이름 |
	| < database_name > | 원본 데이터베이스 이름 |
	| < target_file > | 파일 이름 및 BACPAC 파일의 위치 |

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
- [SSMS을 사용하여 Azure SQL 데이터베이스로 BACPAC 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Azure SQL 데이터베이스 SqlPackage로 BACPAC 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure SQL 데이터베이스 Azure 포털로 BACPAC 가져오기](sql-database-import.md)
- [Azure SQL 데이터베이스 PowerShell로 BACPAC 가져오기](sql-database-import-powershell.md)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->