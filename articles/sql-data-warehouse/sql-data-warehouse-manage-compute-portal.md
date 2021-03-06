<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 계산 능력 관리(Azure 포털) | Microsoft Azure"
   description="계산 능력을 관리하는 Azure 포털 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 계산 능력 관리(Azure 포털)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-manage-compute-overview.md)
- [포털](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


워크로드의 변화하는 요구를 충족시키도록 계산 리소스와 메모리를 확장하여 크기 조정을 실시합니다. 사용량이 많지 않은 시간 동안 리소스를 다시 조정하거나 계산 전체를 일시 중지하여 비용을 절감합니다.

이 작업 컬렉션은 Azure 포털을 사용하여 다음을 수행합니다.

- 계산 조정
- 계산 일시 중지
- 계산 다시 시작

이에 대해 알아보려면 [계산 관리 개요][]를 참조하세요.

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## 계산 능력 크기 조정

[AZURE.INCLUDE [SQL 데이터 웨어하우스 크기 조정 DWU 설명](../../includes/sql-data-warehouse-scale-dwus-description.md)]

계산 리소스를 변경하려면

1. [Azure 포털][]을 열고 데이터베이스를 연 다음 **조정**을 클릭합니다.

    ![크기 조정을 클릭합니다.][1]

1. 확장 블레이드에서 슬라이더를 왼쪽 또는 오른쪽으로 이동해 DWU 설정을 변경합니다.

    ![슬라이더를 이동합니다][2]

1. **Save**를 클릭합니다. 확인 메시지가 표시됩니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

    ![저장을 클릭합니다.][3]

<a name="pause-compute-bk"></a>

## 계산 일시 중지

[AZURE.INCLUDE [SQL 데이터 웨어하우스 일시 중지 설명](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면

1. [Azure 포털][]을 열고 데이터베이스를 엽니다. 상태가 **온라인**입니다. 

    ![온라인 상태][6]

1. 계산과 메모리 리소스를 일시 중지하기 위해 **일시 중지**를 클릭하면 확인 메시지가 나타납니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

    ![일시 중지 확인][7]

1. SQL 데이터 웨어하우스가 시작되는 동안 데이터베이스 상태는 "일시 중지하는 중"이 됩니다.
2. 상태가 **일시 중지됨**일 경우 일시 중지 작업이 완료된 것이며 더 이상 DWU 비용이 청구되지 않습니다.

    ![일시 중지 상태][4]

<a name="resume-compute-bk"></a>

## 계산 다시 시작

[AZURE.INCLUDE [SQL 데이터 웨어하우스 다시 시작 설명](../../includes/sql-data-warehouse-resume-description.md)]데이터베이스를 다시 시작하려면

1. [Azure 포털][]을 열고 데이터베이스를 엽니다. 상태가 **일시 중지됨**입니다. 

    ![데이터베이스 일시 중지][4]

1. 데이터베이스를 다시 시작하기 위해 **시작**을 클릭하면 확인 메시지가 나타납니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

    ![다시 시작 확인][5]

1. SQL 데이터 웨어하우스가 시작되는 동안 데이터베이스 상태는 "다시 시작하는 중"이 됩니다.
2. 상태가 **온라인**이면 데이터가 준비된 것입니다.

    ![온라인 상태][6]

<a name="next-steps-bk"></a>

## 다음 단계
자세한 내용은 [관리 개요][]를 참조하세요.

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[계산 관리 개요]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure 포털]: http://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->