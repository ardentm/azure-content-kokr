<properties 
	pageTitle="DocumentDB 간 데이터 이동 | Microsoft Azure" 
	description="Azure 데이터 팩터리를 사용하여 Azure DocumentDB 컬렉션 간 데이터를 이동하는 방법에 대해 알아봅니다." 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/09/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 DocumentDB 간 데이터 이동

이 문서에서는 Azure Data Factory에서 복사 작업을 사용하여 다른 데이터 저장소에서 Azure DocumentDB로 데이터를 이동하고 DocumentDB에서 다른 데이터 저장소로 데이터를 이동하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

다음 샘플은 Azure DocumentDB 및 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.


## 샘플: DocumentDB에서 Azure Blob로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1. [DocumentDb](#azure-documentdb-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다. 
3. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다. 
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 DocumentDB Azure에서 Azure Blob에 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure DocumentDB 연결된 서비스:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure 문서 DB 입력 데이터 집합:**

예제는 Azure DocumentDB 데이터베이스에 **사람**이라는 컬렉션이 있다고 가정합니다.
 
"외부":"true" 설정 및 externalData 정책 지정은 Azure 데이터 팩터리 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Azure Blob 출력 데이터 집합:**

데이터는 시간 세분성으로 특정 날짜와 시간을 반영하는 blob에 대한 경로가 있는 새 blob로 매시간 복사됩니다.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

DocumentDB 데이터베이스의 사용자 컬렉션에서 샘플 JSON 문서:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB는 계층적 JSON 문서에 대한 구문과 같이 SQL을 사용하여 쿼리 문서를 지원합니다.

예제: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

다음 파이프라인은 DocumentDB 데이터베이스의 Person 컬렉션에서 Azure blob에 데이터를 복사합니다. 복사 작업의 부분인 입력 및 출력 데이터 집합이 지정되었습니다.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## 샘플: Azure Blob에서 Azure DocumentDB로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1. [DocumentDb](#azure-documentdb-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다. 
4. [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 및 [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.


샘플은 Azure Blob에서 Azure DocumentDB로 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Blob 저장소 연결된 서비스:**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure DocumentDB 연결된 서비스:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Azure Blob 입력 데이터 집합:**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Azure DocumentDB 출력 데이터 집합:**

샘플은 "Person"이라는 컬렉션에 데이터를 복사합니다.

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

다음 파이프라인은 Azure Blob에서 DocumentDB의 사용자 컬렉션에 데이터를 복사합니다. 복사 작업의 부분인 입력 및 출력 데이터 집합이 지정되었습니다.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
샘플 blob 입력이 인 경우

	1,John,,Doe

DocumentDB에서 출력 JSON은 다음과 같습니다.

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB는 중첩된 구조를 허용하는 JSON 문서용 NoSQL 저장소입니다. Azure 데이터 팩터리를 사용하면 **nestingSeparator** 즉, 이 예에서 "."를 통해 계층 구조를 표시할 수 있습니다. 테이블 정의에서 "Name.First", "Name.Middle" 및 "Name.Last"에 따르면 구분 기호를 사용하여 복사 작업이 3개의 자식 요소(처음, 중간 및 마지막)가 있는 "Name" 개체를 생성합니다.

## Azure DocumentDB 연결된 서비스 속성

다음 테이블은 Azure DocumentDB 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| **속성** | **설명** | **필수** |
| -------- | ----------- | --------- |
| type | 형식 속성은 **DocumentDb**로 설정되어야 합니다. | 예 |
| connectionString | Azure DocumentDB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다. | 예 |

## Azure DocumentDB 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하십시오. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.(SQL Azure, Azure Blob, Azure 테이블 등)
 
typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **DocumentDbCollection** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| **속성** | **설명** | **필수** |
| -------- | ----------- | -------- |
| collectionName | DocumentDB 문서 컬렉션의 이름입니다. | 예 |


예제:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Data Factory에서의 스키마
DocumentDB와 같은 스키마 없는 데이터 저장소의 경우 Data Factory 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

1.	데이터 집합 정의에서 **structure** 속성을 사용하여 데이터의 구조를 지정하는 경우 Data Factory 서비스는 이 구조를 스키마로 인식합니다. 이 경우 행에 열의 값이 포함되어 있지 않으면 null 값이 제공됩니다.
2.	데이터 집합 정의에서 **structure** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory 서비스는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

따라서 스키마 없는 데이터 원본에 대한 모범 사례는 **structure** 속성을 사용하여 데이터의 구조를 지정하는 것입니다.

## Azure DocumentDB 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하십시오. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.
 
**참고:** 복사 작업은 하나의 입력을 사용하여 하나의 출력을 생성합니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 **DocumentDbCollectionSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| **속성** | **설명** | **허용되는 값** | **필수** |
| ------------ | --------------- | ------------------ | ------------ |
| 쿼리 | 데이터를 읽는 쿼리를 지정합니다. | DocumentDB에서 지원하는 쿼리 문자열입니다. <br/><br/>예: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00" | 아니요 <br/><br/>지정하지 않는 경우 실행되는 SQL 문: select <columns defined in structure> from mycollection 
| nestingSeparator | 문서가 중첩됨을 나타내는 특수 문자 | 모든 character입니다. <br/><br/>DocumentDB는 중첩된 구조를 허용하는 JSON 문서용 NoSQL 저장소입니다. Azure 데이터 팩터리를 사용하면 nestingSeparator 즉, 위의 예에서 "."를 통해 계층 구조를 표시할 수 있습니다. 테이블 정의에서 "Name.First", "Name.Middle" 및 "Name.Last"에 따르면 구분 기호를 사용하여 복사 작업이 3개의 자식 요소(처음, 중간 및 마지막)가 있는 "Name" 개체를 생성합니다. | 아니요

**DocumentDbCollectionSink**는 다음 속성을 지원합니다.

| **속성** | **설명** | **허용되는 값** | **필수** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | 중첩된 해당 문서를 나타내는 원본 열 이름에 특수 문자가 필요합니다. <br/><br/>위의 예에서 출력 테이블의 Name.First는 DocumentDB 문서에서 다음 JSON 구조를 생성합니다.<br/><br/>"이름": {<br/> "First": "John"<br/>}, | 중첩 수준을 구분하는데 사용되는 문자입니다.<br/><br/>기본값은 .(점)입니다. | 중첩 수준을 구분하는데 사용되는 문자입니다. <br/><br/>기본값은 .(점)입니다. | 아니요 | 
| writeBatchSize | 병렬 수가 DocumentDB 서비스에 문서를 만들도록 요청합니다.<br/><br/>이 속성을 사용하여 DocumentDB에서 데이터를 복사하는 경우 성능을 미세 조정할 수 있습니다. DocumentDB에 더 많은 병렬 요청이 전송되기 때문에 writeBatchSize 증가하는 경우 더 나은 성능을 기대할 수 있습니다. 하지만 "요청 속도가 큽니다."라는 오류 메시지를 발생할 수 있는 제한을 방지해야 합니다. <br/><br/>제한은 문서 크기, 문서에서 용어 수, 대상 컬렉션의 인덱싱 정책 등을 포함하는 많은 요인으로 결정됩니다. 복사 작업의 경우 더 나은 컬렉션(예: S3)을 사용하여 사용할 수 있는 처리량(2,500개의 요청 단위/초)을 보유할 수 있습니다. | 정수 값 | 아니요 |
| writeBatchTimeout | 시간이 초과 되기 전에 완료하려는 작업을 위한 대기 시간입니다. | (단위 = timespan) 예를 들어 “00:30:00”(30분)입니다. | 아니요 |
 
## 부록
1. **질문:** 복사 작업은 기존 레코드의 업데이트를 지원합니까?

	**대답:** 아니요.

2. **질문:** DocumentDB로 복사 재시도는 이미 복사된 레코드를 어떻게 처리하나요?

	**대답:** 레코드에 "ID" 필드가 있고 복사 작업이 동일한 ID를 가진 레코드를 삽입하려고 시도하는 경우 복사 작업에서 오류가 발생합니다.
 
3. **질문:** 데이터 팩터리는 [범위 또는 해시 기반 데이터 분할](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)을 지원합니까?

	**대답:** 아니요. 
4. **질문:** 하나의 테이블에 대해 하나 이상의 DocumentDB 컬렉션을 지정할 수 있습니까?
	
	**대답:** 아니요. 이 경우 하나의 컬렉션만 지정할 수 있습니다.
     
## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0518_2016-->