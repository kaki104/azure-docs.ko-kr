---
title: Avere vFXT for Azure에 대한 추가 링크
description: Avere vFXT for Azure에 대한 추가 정보 링크를 제공합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669790"
---
# <a name="additional-documentation"></a>추가 설명서

이 문서에는 Avere 제어판 관리 인터페이스 및 관련 항목에 대한 추가 설명서 링크가 있습니다. 

## <a name="avere-cluster-documentation"></a>Avere 클러스터 설명서

추가 Avere 클러스터 설명서는 <http://library.averesystems.com/> 웹 사이트에서 찾을 수 있습니다. 클러스터의 기능 및 설정을 구성하는 방법을 이해하는 데 도움이 되는 문서는 다음과 같습니다. 

* [FXT 클러스터 만들기 가이드](<http://library.averesystems.com/#fxt_cluster>)는 물리적 하드웨어 노드로 구성된 클러스터를 위해 설계되었지만, 문서의 정보 중 일부는 vFXT 클러스터와도 관련이 있습니다. 특히 새 vFXT 클러스터 관리자는 다음 섹션을 참조하여 이점을 얻을 수 있습니다.
  * [지원 및 모니터링 설정 사용자 지정](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>)에서는 지원 업로드 설정을 사용자 지정하고, 원격 모니터링을 사용하도록 설정하는 방법을 설명합니다. 
  * [VServers 및 글로벌 네임스페이스 구성](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>)에는 클라이언트 측 네임스페이스를 만드는 방법에 대한 정보가 나와 있습니다.
  * [Avere 클러스터에 대한 DNS를 구성](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>)에서는 라운드 로빈 DNS를 구성하는 방법을 설명합니다.
  * [백 엔드 저장소 추가](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)에서는 코어 파일러를 추가하는 방법을 설명합니다.

* [클러스터 구성 가이드](<http://library.averesystems.com/#operations>)는 Avere 클러스터에 대한 설정 및 옵션에 대한 전체 참조입니다. vFXT 클러스터는 이러한 옵션의 하위 집합을 사용하지만, 동일한 구성 페이지의 대부분이 적용됩니다.

* [대시보드 가이드](<http://library.averesystems.com/#operations>)에서는 Avere 제어판의 클러스터 모니터링 기능을 사용하는 방법을 설명합니다.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT 만들기 및 관리 설명서

클라우드 클러스터 만들기 및 관리 유틸리티인 vfxt.py를 사용하는 방법에 대한 전체 가이드는 <https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md>에서 제공됩니다.  
