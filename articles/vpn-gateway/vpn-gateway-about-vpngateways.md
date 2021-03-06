<properties 
   pageTitle="VPN 게이트웨이 정보 | Microsoft Azure"
   description="Azure 가상 네트워크용 VPN 게이트웨이에 대해 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# VPN 게이트웨이 정보

VPN 게이트웨이는 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용됩니다. 또한 Azure 내의 여러 가상 네트워크 간(VNet 간)에 트래픽을 보내는 데에도 사용됩니다. 아래 섹션에서는 VPN 게이트웨이와 관련된 항목을 설명합니다.

VPN 게이트웨이를 만드는 데 사용할 지침은 가상 네트워크를 만드는 데 사용되는 배포 모델에 따라 달라집니다. 예를 들어 클래식 배포 모델을 사용하여 VNet을 만든 경우 클래식 배포 모델에 대한 가이드 및 지침을 사용하여 VPN 게이트웨이를 만들고 구성합니다. 클래식 배포 모델 가상 네트워크에 리소스 관리자 VPN 게이트웨이를 만들 수 없습니다.

배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 모델 이해](../resource-manager-deployment-model.md)를 참조하세요.


## <a name="gwsub"></a>게이트웨이 서브넷

VPN 게이트웨이를 구성하려면 먼저 VNet에 대한 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷이 제대로 작동하려면 이름을 *GatewaySubnet*으로 지정해야 합니다. 이 이름을 통해 Azure은 서브넷이 게이트웨이에 사용됨을 알 수 있습니다.<BR>클래식 포털을 사용하는 경우 포털 인터페이스에서 게이트웨이 서브넷이 *게이트웨이*로 자동으로 이름 지정됩니다. 클래식 포털에서 게이트웨이 서브넷을 보는 것에만 해당됩니다. 이 경우에 서브넷은 Azure에서 *GatewaySubnet*으로 실제로 만들어지며 Azure 포털 및 PowerShell에서 이러한 방식으로 볼 수 있습니다.

게이트웨이 서브넷 최소 크기는 전적으로 만들려는 구성에 따라 달라집니다. 게이트웨이 서브넷을 일부 구성에 대해 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /28 이상으로 만드는 것이 좋습니다(/28, /27, /26 등).

더 큰 게이트웨이 크기를 만드는 경우 게이트웨이 크기 제한에 대해 실행되지 않도록 방지합니다. 예를 들어 서브넷 크기가 /29인 게이트웨이를 만들고 사이트 간/Express 경로 공존 구성을 구성하려는 경우 게이트웨이를 삭제하고 게이트웨이 서브넷을 삭제하며 /28 이상인 게이트웨이 서브넷을 만들고 게이트웨이를 다시 작성해야 합니다.

처음부터 더 큰 크기의 게이트웨이 서브넷을 만들어서 네트워크 환경에 새 구성 기능을 추가할 경우 나중에 시간을 절약할 수 있습니다.

아래 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] 연결에 오류를 발생시킬 수 있기 때문에 GatewaySubnet에 적용되는 NSG(네트워크 보안 그룹)가 없는지 확인합니다.

## <a name="gwtype"></a>게이트웨이 유형

게이트웨이 유형은 게이트웨이 자체를 연결하는 방법을 지정하며 리소스 관리자 배포 모델에 대한 필수 구성 설정입니다. VPN에 대한 라우팅의 유형을 지정하는 VPN 유형과 게이트웨이 유형을 혼동하지 마세요. `-GatewayType`에 사용 가능한 값은 다음과 같습니다.

- Vpn
- Express 경로


리소스 관리자 배포 모델에 대한 이 예제는 -GatewayType을 *Vpn*으로 지정합니다. 게이트웨이를 만들 때 게이트웨이 유형이 구성에 정확한지 확인해야 합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>게이트웨이 SKU

VPN 게이트웨이 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 세 가지 VPN 게이트웨이 SKU가 있습니다.

- Basic
- 표준
- HighPerformance

다음 예제에서는 `-GatewaySku`를 *표준*으로 지정합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>SKU 및 게이트웨이 유형에서 예상된 총 처리량


아래 표는 게이트웨이 유형과 예상 총 처리량을 보여 줍니다. 가격은 게이트웨이 SKU마다 다릅니다. 가격에 대한 자세한 내용은 [VPN 게이트웨이 가격](https://azure.microsoft.com/pricing/details/vpn-gateway/)을 참조하세요. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>VPN 유형

각 구성이 작동하려면 특정 VPN 유형이 필요합니다. 동일한 VNet에 사이트 간 연결 및 지점 및 사이트 간 연결을 만드는 것과 같은 두 가지 구성을 결합하는 경우 두 연결 요구 사항을 충족하는 VPN 유형을 사용해야 합니다.

지점 및 사이트 간 및 사이트 간 공존 연결의 경우 Azure Resource Manager 배포 모델 또는 동적 게이트웨이로 작업하거나 클래식 배포 모드로 작업하면 경로 기반 VPN 유형을 사용해야 합니다.

구성을 만든 경우 연결에 필요한 VPN 유형을 선택합니다.

두 가지 VPN 유형이 있습니다.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

리소스 관리자 배포 모델에 대한 이 예제는 `-VpnType`을 *RouteBased*로 지정합니다. 게이트웨이를 만들 때 -VpnType이 구성에 정확한지 확인해야 합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>연결 유형

각 구성이 작동하려면 특정 연결 형식이 필요합니다. `-ConnectionType`에 대해 사용 가능한 리소스 관리자 PowerShell 값은 다음과 같습니다.

- IPsec
- Vnet2Vnet
- Express 경로
- VPNClient

아래 예제에서는 "IPsec" 연결 형식을 필요로 하는 사이트 간 연결을 만듭니다.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>로컬 네트워크 게이트웨이

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 클래식 배포 모델에서 로컬 네트워크 게이트웨이는 로컬 사이트라고 했습니다. 로컬 네트워크 게이트웨이에 이름인 온-프레미스 VPN 장치의 공용 IP 주소를 지정하고 온-프레미스 위치에 있는 주소 접두사를 지정합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다. 필요에 따라 이러한 주소 접두사를 수정할 수 있습니다.



### 주소 접두사 수정 - 리소스 관리자

주소 접두사를 수정할 경우 절차는 VPN 게이트웨이를 만들었는지 여부에 따라 다릅니다. 문서의 [로컬 네트워크 게이트웨이에 대한 주소 접두사 수정](vpn-gateway-create-site-to-site-rm-powershell.md#modify) 섹션을 참조하세요.

아래 예에서 MyOnPremiseWest라는 로컬 네트워크 게이트웨이가 지정되고 두 개의 IP 주소 접두사를 포함하는 것을 확인할 수 있습니다.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### 주소 접두사 수정 - 클래식 배포

클래식 배포 모델을 사용할 때 로컬 사이트를 수정해야 할 경우 클래식 포털에서 로컬 네트워크 구성 페이지를 사용하거나 네트워크 구성 파일인 NETCFG.XML을 직접 수정할 수 있습니다.


##  <a name="devices"></a> VPN 장치

VPN 장치를 사용하려면 사용자 구성에 필요한 VPN 유형을 지원하는지 확인해야 합니다. 호환 가능한 VPN 장치에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.

##  <a name="requirements"></a>게이트웨이 요구 사항


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## 다음 단계

구성을 계획 및 디자인하도록 계속 진행하기 전에 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md) 문서를 참조하세요.





 

<!---HONumber=AcomDC_0525_2016-->