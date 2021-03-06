### VPN 게이트웨이 연결을 아직 만들지 않은 경우 접두사 추가 또는 제거

- 생성한 로컬 네트워크 게이트웨이에 추가 주소 접두사를 **추가하지만** VPN 게이트웨이 연결이 아직 없는 경우 다음 예제를 사용합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- VPN 연결이 없는 로컬 네트워크 게이트웨이에서 주소 접두사를 **제거하려면** 다음 예제를 사용합니다. 더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 접두사 20.0.0.0/24(이전 예제)가 더 이상 필요하지 않으므로 로컬 네트워크 게이트웨이를 업데이트하고 해당 접두사를 제외합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### VPN 게이트웨이 연결을 이미 만든 경우 접두사 추가 또는 제거

VPN 연결을 만들었고 로컬 네트워크 게이트웨이에 포함된 IP 주소 접두사를 추가 또는 제거하려면 다음 단계를 순서대로 수행해야 합니다. 이로 인해 VPN 연결에 다소 간의 가동 중지 시간이 발생합니다.

>[AZURE.IMPORTANT] VPN 게이트웨이는 삭제하지 않습니다. 삭제할 경우 다시 만들기 위해 이전 단계를 수행해야 할 뿐 아니라 온-프레미스 라우터를 새 설정으로 다시 구성해야 합니다.
 
1. IPsec 연결을 제거합니다. 
2. 로컬 네트워크 게이트웨이에 대한 접두사를 수정합니다. 
3. 새 IPsec 연결을 만듭니다. 

다음 샘플을 지침으로 사용할 수 있습니다.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0427_2016-->