---
title: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 통해 SQL Database 확장을 사용하여 SQL BACPAC 파일을 가져오는 방법 알아보기
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/29/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa7d48c9a8079dc9171879ab5b72e3f04f870ebc
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036179"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기

Azure SQL Database 확장을 사용하여 BACPAC 파일을 가져오는 방법을 알아봅니다. 이 자습서에서는 Azure SQL Server, SQL Database 및 BACPAC 파일을 배포하는 템플릿을 만듭니다. Azure Resource Manager 템플릿을 사용하여 Azure 가상 머신 확장을 배포하는 방법은 [# 자습서: Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 배포](./resource-manager-tutorial-deploy-vm-extensions.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * BACPAC 파일 준비
> * 빠른 시작 템플릿 열기
> * 템플릿 편집
> * 템플릿 배포
> * 배포 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager Tools 확장이 있는 [Visual Studio Code](https://code.visualstudio.com/)  [확장 설치](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)를 참조하세요.
* 보안을 강화하려면 SQL Server 관리자 계정에 대해 생성된 암호를 사용하세요. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-a-bacpac-file"></a>BACPAC 파일 준비

BACPAC 파일은 [공개적으로 액세스 가능한 Azure Storage 계정](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)에서 공유됩니다. 사용자 고유의 파일을 만들려면 [Azure SQL 데이터베이스를 BACPAC 파일로 내보내기](../sql-database/sql-database-export.md)를 참조하세요. 사용자 고유의 위치에 파일을 게시하기로 선택하는 경우 자습서의 뒷부분에서 템플릿을 업데이트해야 합니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에서는 [위협 검색이 포함된 Azure SQL Server 배포](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/)라는 템플릿을 사용합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.

    템플릿에 3개 리소스가 정의되어 있습니다.

    * `Microsoft.Sql/servers` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)를 참조하세요.
    * `Microsoft.SQL/servers/securityAlertPolicies` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies)를 참조하세요.
    * `Microsoft.SQL.servers/databases`  [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)를 참조하세요.
    템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
4. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

두 개의 추가 리소스를 템플릿에 추가해야 합니다.

* SQL 데이터베이스 확장이 BACPAC 파일을 가져올 수 있도록 Azure 서비스에 대한 액세스를 허용해야 합니다. SQL 서버 정의에 다음 JSON을 추가합니다.

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    템플릿은 다음과 비슷합니다.

    ![Azure Resource Manager가 sql 확장 BACPAC 배포](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* 다음 JSON을 사용하여 데이터베이스 정의에 SQL Database 확장 리소스를 추가합니다.

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    템플릿은 다음과 비슷합니다.

    ![Azure Resource Manager가 sql 확장 BACPAC 배포](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    리소스 정의를 이해하려면 [SQL Database 확장 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)를 확인하세요. 다음은 중요한 요소입니다.

    * **dependsOn**: SQL 데이터베이스를 만든 후 확장 리소스를 만들어야 합니다.
    * **storageKeyType**: 사용할 저장소 키의 형식입니다. 값은 `StorageAccessKey` 또는 `SharedAccessKey`입니다. 제공된 BACPAC 파일은 공개적으로 액세스 가능한 Azure Storage 계정에서 공유되므로 여기서는 'SharedAccessKey'가 사용됩니다.
    * **storageKey**: 사용할 저장소 키입니다. 저장소 키 형식이 SharedAccessKey이면 앞에 "?"가 있어야 합니다.
    * **storageUri**: 사용할 저장소 uri입니다. 제공된 BACPAC 파일을 사용하지 않기로 선택하는 경우 값을 업데이트해야 합니다.
    * **administratorLoginPassword**: SQL 관리자의 암호입니다. 생성된 암호를 사용하는 것이 좋습니다. [필수 조건](#prerequisites)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 [템플릿 배포](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) 섹션을 참조하세요. 다음 PowerShell 배포 스크립트를 대신 사용합니다.

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

생성된 암호를 사용하는 것이 좋습니다. [필수 조건](#prerequisites)을 참조하세요.

## <a name="verify-the-deployment"></a>배포 확인

포털에서 새로 배포된 리소스 그룹의 SQL 데이터베이스를 선택합니다. **쿼리 편집기(미리 보기)** 를 선택한 다음, 관리자 자격 증명을 입력합니다. 두 테이블을 데이터베이스로 가져온 것을 볼 수 있습니다.

![Azure Resource Manager가 sql 확장 BACPAC 배포](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SQL Server, SQL Database를 배포하고 BACPAC 파일을 가져왔습니다. 여러 지역에 Azure 리소스를 배포하는 방법 및 안전한 배포 사례를 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Deployment Manager 사용](./resource-manager-tutorial-deploy-vm-extensions.md)
