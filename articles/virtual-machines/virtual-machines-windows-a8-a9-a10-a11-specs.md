<properties
 pageTitle="Windows에서 A8 - A11 인스턴스 정보 | Microsoft Azure"
 description="Windows VM 및 클라우드 서비스에 대한 Azure A8, A9, A10 및 A11 계산 집약적 크기 사용에 관한 배경 정보와 고려사항을 얻습니다."
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="04/26/2016"
 ms.author="danlep"/>

# A8, A9, A10 및 A11 계산 집약적 인스턴스 정보

여기에는 *계산 집약적* 인스턴스로 알려진 Azure A8, A9, A10 및 A11 인스턴스 사용에 대한 일부 고려사항과 배경 정보가 있습니다. 이 문서는 Windows VM에 대해 이러한 인스턴스를 사용하는 것이 중점을 두고 있습니다. 이 문서는 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md)에도 적용됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 네트워크에 액세스

단일 클라우드 서비스, 가용성 집합, 또는 Azure 배치 풀 내에서 A8 및 A9 인스턴스는 인스턴스 간 통신을 위한 Microsoft Network Direct 인터페이스를 사용하는 Windows MPI 응용 프로그램을 실행하도록 Azure의 RDMA 네트워크에 액세스할 수 있습니다.

A8 또는 A9 인스턴스의 Windows 가상 컴퓨터, 클라우드 서비스, 및 Azure 배치 풀에서 RDMA 네트워크에 액세스하기 위한 MPI 응용 프로그램의 필수 요소는 다음 테이블을 참조하세요. 전형적인 배포 시나리오는 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) 및 [다중 인스턴스 작업을 사용하여 Azure 배치에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행](../batch/batch-mpi.md)을 참조하세요.


필수 요소 | 가상 컴퓨터 | 클라우드 서비스 또는 배치 풀 
---------- | ------------ | ------------- 
운영 체제 | Windows Server 2012 R2 또는 Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 게스트 OS 제품군 
MPI | MS-MPI 2012 R2 이상, 또는 Intel MPI Library 5 | MS-MPI 2012 R2 이상, 또는 Intel MPI Library 5 


>[AZURE.NOTE]A8 및 A9 크기의 가상 컴퓨터의 경우, RDMA 연결에 필요한 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 VM에 추가해야 합니다. 배포 방법에 따라 A8 또는 A9 크기의 VM에 HpcVmDrivers 확장이 자동으로 추가되거나 직접 추가해야 할 수도 있습니다. 확장을 직접 추가하려면 [VM 확장 관리](virtual-machines-windows-classic-manage-extensions.md)를 참조하세요.

## HPC 팩 및 Windows에 대한 고려 사항

HPC 팩은 Windows Server에서 A8, A9, A10 및 A11 인스턴스를 사용할 때는 필요하지 않지만, Azure의 RDMA 네트워크에 액세스하는 Windows 기반 MPI 응용 프로그램을 실행하는 경우 유용한 도구입니다. HPC 팩 2012 R2 이상의 버전은 A8 및 A9 인스턴스에 배포되는 경우 Azure RDMA 네트워크를 사용할 수 있는 Windows용 Microsoft 구현 메시지 전달 인터페이스(MS-MPI)에 대한 런타임 환경을 포함합니다.

Windows Server에서 HPC 팩을 사용하여 계산 집약적 인스턴스를 사용하는 방법에 대한 자세한 내용 및 검사 목록은 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)을 참조하세요.




## 다음 단계

* A8, A9, A10 및 A11 인스턴스의 가용성 및 가격에 대한 세부 정보는 [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) 및 [클라우드 서비스 가격](https://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요.

* 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-linux-sizes.md)를 참조하세요.

* Windows에서 HPC 팩을 사용하여 A8 및 A9 인스턴스의 배포 및 사용을 시작하려면 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)을 참조하세요.

* A8 및 A9 인스턴스를 사용하여 Azure 배치에서 MPI 응용 프로그램 실행에 대한 자세한 내용은 [다중 인스턴스 작업을 사용하여 Azure 배치에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행](../batch/batch-mpi.md)을 참조하세요.

<!---HONumber=AcomDC_0511_2016-->