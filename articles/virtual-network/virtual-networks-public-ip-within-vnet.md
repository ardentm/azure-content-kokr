<properties 
   pageTitle="가상 네트워크에서 공용 IP 주소를 사용하는 방법"
   description="공용 IP 주소를 사용하도록 가상 네트워크를 구성하는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="telmos" />

# VNet(가상 네트워크)의 공용 IP 주소 공간

VNet(가상 네트워크)는 공용 및 개인(RFC 1918 주소 블록) IP 주소 공간을 모두 포함할 수 있습니다. 공용 IP 주소 범위를 추가할 경우 이 주소 범위는 VNet 및 상호 연결된 Vnet 내에서 그리고 온-프레미스 위치에서만 연결할 수 있는 개인 VNet IP 주소 공간의 일부로 처리됩니다.

아래 그림은 공용 및 개인 IP 주소 공간이 포함된 VNet을 보여줍니다.

![공용 IP 개념](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## 공용 IP 주소 범위를 어떻게 추가하나요?

*netcfg* 파일을 사용하거나 [Azure 포털](http://portal.azure.com)에서 구성을 추가하여 개인 IP 주소 범위를 추가하는 것과 동일한 방식으로 공용 IP 주소 범위를 추가합니다. VNet을 만들 때 공용 IP 주소 범위를 추가하거나 나중에 다시 돌아와 추가할 수 있습니다. 아래 예제에서는 동일한 VNet에 구성된 공용 및 개인 IP 주소 공간을 모두 보여 줍니다.

![포털의 공용 IP 주소](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## 제한 사항이 있나요?

다음과 같이 허용되지 않는 IP 주소 범위가 몇 가지 있습니다.

- 224\.0.0.0/4(멀티캐스트)

- 255\.255.255.255/32(브로드캐스트)

- 127\.0.0.0/8(루프백)

- 169\.254.0.0/16(링크-로컬)

- 168\.63.129.16/32(내부 DNS)

## 다음 단계

[VNet에서 사용하는 DNS 서버를 관리하는 방법](../virtual-networks-manage-dns-in-vnet)

<!---HONumber=AcomDC_0504_2016-->