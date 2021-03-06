---
title: '자습서: Central Desktop와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Central Desktop 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429049"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>자습서: Central Desktop와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Central Desktop을 통합하는 방법에 대해 알아봅니다.

Central Desktop을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Central Desktop에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Central Desktop에 자동으로 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Central Desktop과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Central Desktop Single Sign-On 사용 가능 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- 아직 Azure AD 평가판 환경이 없으면 [1개월 평가판을 받으세요](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Central Desktop 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-central-desktop-from-the-gallery"></a>갤러리에서 Central Desktop 추가
Central Desktop과 Azure AD의 통합을 구성하려면 갤러리의 Central Desktop을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Central Desktop을 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
1. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

1. 검색 상자에 **Central Desktop**을 입력합니다. 결과 패널에서 **Central Desktop**을 선택한 다음, **추가**를 선택하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Central Desktop](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Central Desktop에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Central Desktop 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Central Desktop의 관련 사용자 간에 연결이 형성되어야 합니다.

Central Desktop에서 Azure AD의 **사용자 이름**과 동일한 **Username** 값을 제공합니다. 이렇게 해서 두 사용자 간의 연결 관계를 형성했습니다.

Central Desktop에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. [Central Desktop 테스트 사용자 만들기](#create-a-central-desktop-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Central Desktop에 만듭니다.
1. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Central Desktop 응용 프로그램에서 Single Sign-On을 구성합니다.

**Central Desktop에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Central Desktop** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

1. Single Sign-On을 사용하려면 **Single Sign-On** 대화 상자의 **모드** 드롭다운 목록에서 **SAML 기반 로그온**을 선택합니다.
 
    ![Single Sign-On 대화 상자](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. **Central Desktop 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Central Desktop 도메인 및 URL Single Sign-On 정보](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. **로그온 URL** 텍스트 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<companyname>.centraldesktop.com`

    나. **식별자** 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    다. **회신 URL** 상자에 다음 패턴으로 URL을 입력합니다. `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Central Desktop 클라이언트 지원 팀](https://imeetcentral.com/contact-us)에 문의하세요. 

1. **SAML 서명 인증서** 섹션 아래에서 **인증서**를 선택합니다. 그런 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. **저장** 단추를 선택합니다.

    ![Single Sign-On 저장 단추 구성](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. **Central Desktop 구성** 섹션에서 **Central Desktop 구성**을 선택하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Central Desktop 구성](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. **Central Desktop** 테넌트에 로그인합니다.

1. **설정**으로 이동합니다. **고급**을 선택한 다음, **Single Sign-On**을 선택합니다.

    ![설정 - 고급](./media/central-desktop-tutorial/ic769563.png "설정 - 고급")

1. **Single Sign On 설정** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 설정](./media/central-desktop-tutorial/ic769564.png "Single Sign-On 설정")
    
    a. **SAML v2 Single Sign-On 사용**을 선택합니다.
    
    나. **SSO URL** 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.
    
    다. **SSO 로그인 URL** 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.
    
    d. **SSO 로그아웃 URL** 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

1. **메시지 서명 확인 방법** 섹션에서 다음 단계를 수행합니다.

    ![메시지 서명 확인 방법](./media/central-desktop-tutorial/ic769565.png "메시지 서명 확인 방법") **인증서**를 선택합니다.
    
    나. **SSO 인증서** 목록에서 **RSH SHA256**을 선택합니다.
    
    다. 다운로드한 인증서를 메모장에서 엽니다. 그런 다음, 인증서의 내용을 복사하여 **SSO 인증서** 필드에 붙여넣습니다.
        
    d. **SAMLv2 로그인 페이지의 링크 표시**를 선택합니다.
    
    e. **업데이트**를 선택합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추](./media/central-desktop-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동합니다. 그런 다음 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/central-desktop-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/central-desktop-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-central-desktop-test-user"></a>Central Desktop 테스트 사용자 만들기

Azure AD 사용자가 로그인할 수 있도록 Central Desktop 응용 프로그램에 프로비전되어야 합니다. 이 섹션은 Central Desktop에 Azure AD 사용자 계정을 만드는 방법을 설명합니다.

> [!NOTE]
> Azure AD 사용자 계정을 프로비전하려면 다른 Central Desktop 사용자 계정 생성 도구 또는 Central Desktop가 제공한 API를 사용합니다.

**Central Desktop에 사용자 계정을 프로비전하려면**

1. Central Desktop 테넌트에 로그인합니다.

1. **사람** > **내부 멤버**로 이동합니다.

1. **내부 멤버 추가**를 선택합니다.

    ![사람](./media/central-desktop-tutorial/ic781051.png "사람")
    
1. **새 멤버의 메일 주소** 상자에 프로비전할 Azure AD 계정을 입력한 후, **다음**을 선택합니다.

    ![새 멤버의 메일 주소](./media/central-desktop-tutorial/ic781052.png "새 멤버의 메일 주소")

1. **내부 멤버 추가**를 선택합니다.

    ![내부 멤버 추가](./media/central-desktop-tutorial/ic781053.png "내부 멤버 추가")
   
   >[!NOTE]
   >추가한 사용자는 해당 계정을 활성화하기 위한 확인 링크가 포함된 메일을 받습니다.
   
### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 사용자 Britta Simon에게 Central Desktop에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Central Desktop에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 엽니다. 디렉터리 보기로 이동한 다음, **엔터프라이즈 응용 프로그램**으로 이동합니다.

1. **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **Central Desktop**을 선택합니다.

    ![응용 프로그램 목록의 Central Desktop 링크](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Central Desktop 타일을 선택하면 Central Desktop 응용 프로그램에 자동으로 로그인됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

