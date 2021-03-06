<properties 
   pageTitle="비즈니스 연속성을 위한 SQL 데이터베이스 설계" 
   description="이 섹션에서 설명하는 선택 지침은 어떤 BCDR 기능을 사용해야 하며 그 시기는 언제인지 선택하기 위한 것입니다. 여기에는 SQL DB를 사용할 때 자동으로 얻게 되는 기능도 포함됩니다."
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="carlrab"/>

# 비즈니스 연속성을 위한 설계

비즈니스 연속성을 위해 응용 프로그램을 설계하려면 다음 질문에 대답해야 합니다.

1. 내 응용 프로그램을 가동 중단에서 보호하기 위해 적절한 비즈니스 연속성 기능은 무엇인가?
2. 어떤 수준의 중복성 및 복제 토폴로지를 사용해야 하는가?

탄력적 풀을 사용하는 경우 자세한 복구 전략에 대해서는 [SQL 데이터베이스의 탄력적 풀을 사용하는 응용 프로그램에 대한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.

## 지역 복원을 사용할 시기

[지역 복원](sql-database-geo-restore.md)은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. SQL 데이터베이스는 기본적으로 모든 데이터베이스를 보호할 수 있는 기본 제공 보호 기능을 제공합니다. 이러한 보호는 지역 중복 Azure 저장소(GRS)에서 [데이터베이스 백업](sql-database-automated-backups.md)을 수행하고 저장하면 됩니다. 이 방법을 선택할 경우 특별한 구성 또는 추거 리소스 할당이 필요하지 않습니다. 이러한 백업이 있으면 지역 복원 명령을 사용하여 어느 지역에서든 데이터베이스를 복구할 수 있습니다. 응용 프로그램 복구를 위해 지역 복원을 사용하는 방법은 [가동 중단에서 복구](sql-database-disaster-recovery.md) 섹션을 참조하세요.

응용 프로그램이 다음 조건에 맞는 경우 기본 제공 보호 기능을 사용하면 됩니다.

1. 중요 업무용으로 간주되지 않습니다. 연계된 SLA가 없으므로 24시간 이상의 가동 중지가 발생해도 재무적 책임이 없습니다.
2. 데이터 변경 비율(예: 시간당 트랜잭션 수)이 낮습니다. 1시간의 RPO로 방대한 데이터가 손실되지 않습니다.
3. 응용 프로그램이 비용에 민감하여 지역에서 복제를 위한 추가적 비용을 지불할 수 없습니다. 

> [AZURE.NOTE] 지역 복원은 작동 중단 시간 동안 백업에서 활성 데이터베이스를 복원하는 데 필요한 특정 지역의 계산 용량을 미리 할당하지 않습니다. 서비스는 지역 복원 요청에 관련된 작업을 지역 내의 기존 데이터베이스에 미치는 영향을 최소화하고 용량 요구 사항을 우선하는 방식으로 관리합니다. 따라서 데이터베이스의 복구 시간은 동시에 같은 지역에서 다른 데이터베이스가 얼마나 복구되는지에 따라 달라집니다.

## 활성 지역 복제를 사용할 시기

[활성 지역 복제](sql-database-geo-replication-overview.md)를 사용하면 기본 데이터베이스에서 다른 지역에 읽을 수 있는(보조) 데이터베이스를 만들 수 있습니다. 이를 통해 데이터베이스가 복구 후 응용 프로그램의 작업을 지원하는 데 필요한 데이터와 계산 리소스를 보유할 수 있게 됩니다. 장애 조치(failover)를 사용하여 응용 프로그램을 복구하는 방법은 [가동 중단에서 복구](sql-database-disaster-recovery.md) 섹션을 참조하세요.

응용 프로그램이 다음 조건에 맞는 경우 지역에서 복제 기능을 사용하면 됩니다.

1. 중요 업무용입니다. 연계된 SLA가 있고 RPO와 RTO에 민감합니다. 데이터 및 가용성이 손실될 경우 재무적 책임이 발생할 수 있습니다. 
2. 데이터 변경 비율(예: 분 또는 초당당 트랜잭션 수)이 높습니다. 기본 보호 기능을 적용한 상태에서 1시간의 RPO가 허용할 수 없는 수준의 데이터 손실로 이어질 수 있습니다.
3. 지역에서 복제를 사용하는 것에 관련된 비용이 잠재적인 재무적 책임과 연계된 비즈니스 손실보다 크게 낮습니다.

활성 지역 복제를 사용하도록 설정하려면 [Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-portal.md)을 참조하세요.

> [AZURE.NOTE] 활성 지역 복제는 보조 데이터베이스에 대한 읽기 전용 액세스도 지원하므로 읽기 전용 작업에 대한 추가 용량도 확보됩니다.

##장애 조치(failover) 구성을 선택하는 방법 

비즈니스 연속성을 위해 응용 프로그램을 설계할 때는 몇 가지 구성 옵션을 고려해야 합니다. 선택할 옵션은 응용 프로그램 배포 토폴로지와 응용 프로그램의 어느 부분이 작동 중단에 가장 취약한지에 따라 달라집니다. 지침은 [지역에서 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## 다음 단계

- [지역에서 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

## 추가 리소스

- [SQL 데이터베이스의 탄력적 풀을 사용하는 응용 프로그램에 대한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) 

<!---HONumber=AcomDC_0615_2016-->