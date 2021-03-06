---
title: Avere vFXT 제어판에 액세스 - Azure
description: vFXT 클러스터 및 브라우저 기반 Avere 제어판에 연결하여 Avere vFXT를 구성하는 방법입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670060"
---
# <a name="access-the-vfxt-cluster"></a>vFXT 클러스터에 액세스

설정을 변경하고 Avere vFXT 클러스터를 모니터링하려면 Avere 제어판을 사용합니다. Avere 제어판은 클러스터에 대한 브라우저 기반의 그래픽 인터페이스입니다.

vFXT 클러스터는 사설 가상 네트워크 내에 있으므로 SSH 터널을 만들거나 다른 방법을 사용하여 클러스터의 관리 IP 주소에 연결해야 합니다. 다음과 같은 두 가지 기본 단계가 있습니다. 

1. 워크스테이션과 사설 vnet 간의 연결 만들기 
1. 클러스터의 관리 IP 주소를 사용하여 웹 브라우저에서 제어판 로드 

> [!NOTE] 
> 이 문서에서는 클러스터 컨트롤러 또는 클러스터의 가상 네트워크 내부에 있는 다른 VM에 공용 IP 주소를 설정했다고 가정합니다. VPN 또는 ExpressRoute를 사용하여 vnet에 액세스하는 경우 [Avere 제어판에 연결](#connect-to-the-avere-control-panel-in-a-browser)로 건너뜁니다.

연결하기 전에 클러스터 컨트롤러를 만들 때 사용한 SSH 공개/개인 키 쌍이 로컬 머신에 설치되어 있는지 확인합니다. 도움이 필요한 경우 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) 또는 [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)용 SSH 키 설명서를 참조하세요.  

## <a name="access-with-a-linux-host"></a>Linux 호스트를 사용하여 액세스

다음 형식을 사용합니다. 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*@*controller_public_IP* 

이 명령은 클러스터 컨트롤러의 IP 주소를 통해 클러스터의 관리 IP 주소에 연결합니다.

예제:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

SSH 공개 키를 사용하여 클러스터를 만들고 일치하는 키가 클라이언트 시스템에 설치된 경우 인증이 자동으로 수행됩니다.


## <a name="access-with-a-windows-host"></a>Windows 호스트를 사용하여 액세스

PuTTY를 사용하는 경우 **호스트 이름** 필드에 클러스터 컨트롤러 사용자 이름과 해당 IP 주소(*your_username*@*controller_public_IP*)를 입력합니다.

예제: ``azureuser@203.0.113.51``

**구성** 패널에서 다음을 수행합니다.

1. 왼쪽에서 **연결** > **SSH**를 차례로 펼칩니다. 
1. **터널**을 클릭합니다. 
1. 8443과 같은 원본 포트를 입력합니다. 
1. 대상에 대해 vFXT 클러스터의 관리 IP 주소와 443 포트를 입력합니다. 
   예제: ``203.0.113.51:443``
1. **추가**를 클릭합니다.
1. **열기**를 클릭합니다.

![터널을 추가하기 위해 클릭할 위치를 보여 주는 Putty 응용 프로그램의 스크린샷](media/avere-vfxt-ptty-numbered.png)

SSH 공개 키를 사용하여 클러스터를 만들고 일치하는 키가 클라이언트 시스템에 설치된 경우 인증이 자동으로 수행됩니다.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>브라우저에서 Avere 제어판에 연결

이 단계에서는 웹 브라우저를 사용하여 vFXT 클러스터에서 실행되는 구성 유틸리티에 연결합니다.

웹 브라우저를 열고, https://127.0.0.1:8443로 이동합니다. 

브라우저에 따라 **고급**을 클릭하고 페이지로 계속 진행하는 것이 안전한지 확인해야 할 수 있습니다.

클러스터를 만들 때 제공한 `admin` 사용자 이름과 암호를 입력합니다.

!['admin' 사용자 이름과 암호로 채워진 Avere 로그인 페이지의 스크린샷](media/avere-vfxt-gui-login.png)

**로그인**을 클릭하거나 키보드에서 Enter 키를 누릅니다.

## <a name="next-steps"></a>다음 단계

이제 클러스터에 액세스할 수 있으므로 [지원](avere-vfxt-enable-support.md)을 사용하도록 설정합니다.
