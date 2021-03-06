리소스|기본 제한
---|---
구독당 최대 저장소 계정 수|100<sup>1</sup>
저장소 계정당 TB|500TB
저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수|저장소 계정 용량 제한 500TB만 적용됨
단일 Blob 컨테이너, 테이블 또는 큐의 최대 크기|500TB
블록 Blob 또는 추가 Blob의 최대 블록 수|50,000
블록 Blob 또는 추가 Blob의 최대 블록 크기|4MB
블록 Blob 또는 추가 Blob의 최대 크기|50,000 X 4MB(약 195GB) 
최대 페이지 blob 크기 |1TB
최대 테이블 엔터티 크기|1MB
테이블 엔터티의 최대 속성 수|252
큐의 최대 메시지 크기|64KB
파일 공유의 최대 크기|5TB
파일 공유의 최대 파일 크기|1TB
파일 공유 내 파일의 최대 수|파일 공유의 총 용량 제한 5TB만 적용됨
공유당 최대 8KB IOPS|1000
파일 공유 내 파일의 최대 수|파일 공유의 총 용량 제한 5TB만 적용됨
저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수|저장소 계정 용량 제한 500TB만 적용됨
컨테이너, 파일 공유, 테이블 또는 큐당 저장된 액세스 정책의 최대 수|5
저장소 계정당 총 요청 빈도(개체 크기는 1KB로 가정함)|최대 20,000 개의 IOPS, 초 당 엔터티 또는 초 당 메시지
단일 Blob의 목표 처리량|초당 최대 60MB 또는 초당 최대 500개 요청
단일 큐의 목표 처리량(1KB 메시지)|초당 최대 2,000개의 메시지
단일 테이블 파티션의 목표 처리량(1KB 엔터티)|초당 최대 2,000개 엔터티
단일 파일 공유의 목표 처리량|초당 최대 60MB
저장소 계정당<sup>2</sup> 최대 수신(미국 지역)|GRS/ZRS<sup>3</sup>을 사용하는 경우 10Gbps, LRS의 경우 20Gbps
저장소 계정당<sup>2</sup> 최대 송신(미국 지역)|RA-GRS/GRS/ZRS<sup>3</sup>을 사용하는 경우 20Gbps, LRS의 경우 30Gbps
저장소 계정당<sup>2</sup> 최대 수신(유럽 및 아시아 지역)|GRS/ZRS<sup>3</sup>을 사용하는 경우 5Gbps, LRS의 경우 10Gbps
저장소 계정당<sup>2</sup> 최대 송신(유럽 및 아시아 지역)|RA-GRS/GRS/ZRS<sup>3</sup>을 사용하는 경우 10Gbps, LRS의 경우 15Gbps

<sup>1</sup>표준 및 프리미엄 저장소 계정이 모두 포함됩니다. 저장소 계정이 100개 이상 필요한 경우 [Azure 지원](https://azure.microsoft.com/support/faq/)에 요청합니다. Azure 저장소 팀이 비즈니스 사례를 검토하고 저장소 계정을 250개까지 승인할 수 있습니다.

<sup>2</sup>*수신*은 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다. *송신*은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

<sup>3</sup>Azure 저장소 복제 옵션은 다음을 포함합니다.

- **RA-GRS**: 읽기 액세스 지역 중복 저장소 RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
- **GRS**: 지역 중복 저장소 
- **ZRS**: 영역 중복 저장소. 블록 Blob에 대해서만 사용 가능합니다. 
- **LRS**: 로컬 중복 저장소 

<!---HONumber=AcomDC_0427_2016-->