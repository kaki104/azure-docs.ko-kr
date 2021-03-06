---
title: PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 방법
description: PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 3cd0a88747379edb15385014fcc93287d95295e0
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114042"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 다음과 같은 관리 ID 작업을 수행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할 기반 액세스 제어가 할당되어야 합니다.

    > [!NOTE]
    > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

    - [가상 머신 참가자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): VM을 만들고, Azure VM에서 시스템 및/또는 사용자가 할당한 관리 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 참가자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자 할당 관리 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: VM으로 사용자 할당 ID를 할당하거나 VM에서 사용자 할당 관리 ID를 제거합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)을 설치합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID가 사용된 Azure VM을 만들려면:

1. 다음 Azure VM 퀵 스타트 중 하나를 참조하여 필요한 섹션만 완료하세요("Azure에 로그인", "리소스 그룹 만들기", "네트워킹 그룹 만들기", "VM 만들기").
    
    "VM 만들기" 섹션으로 이동하려면 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 구문을 조금 수정합니다. 시스템 할당 ID가 사용된 VM을 프로비전하려면 `-AssignIdentity:$SystemAssigned` 매개 변수를 추가해야 합니다. 예를 들면 다음과 같습니다.
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [PowerShell을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md)

2. (선택 사항) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단될 예정)을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다. Azure 리소스에 대한 관리 ID VM 확장은 2019년 1월에 사용 중단될 예정입니다. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>기존 Azure VM에서 시스템 할당 관리 ID를 사용하도록 설정

기존 Virtual Machine에서 시스템 할당 관리 ID를 사용하도록 설정해야 하는 경우:

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Login-AzureRmAccount
   ```

2. 먼저 `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 후에 시스템 할당 관리 ID를 사용하도록 설정하려면 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-AssignIdentity` 스위치를 사용합니다.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (선택 사항) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단될 예정)을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다. 기존 VM의 위치와 일치하는 올바른 `-Location` 매개 변수를 지정해야 합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID는 여전히 필요한 Virtual Machine이 있는 경우 다음 cmdlet을 사용합니다.

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Login-AzureRmAccount
   ```

2. `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색하고 `-IdentityType` 매개 변수를 `UserAssigned`로 설정합니다.

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID가 없는 가상 머신이 있는 경우 다음 명령을 사용합니다.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Azure 리소스에 대한 관리 ID VM 확장을 제거하려면 -Name 스위치를 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet과 함께 사용하여 확장을 추가할 때 사용한 것과 같은 이름을 지정합니다.

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 VM에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>생성 중에 VM에 사용자 할당 관리 ID 할당

VM을 생성할 때 Azure VM에 사용자 할당 관리 ID를 할당하려면:

1. 다음 Azure VM 퀵 스타트 중 하나를 참조하여 필요한 섹션만 완료하세요("Azure에 로그인", "리소스 그룹 만들기", "네트워킹 그룹 만들기", "VM 만들기"). 
  
    “VM 만들기” 섹션으로 이동할 때 [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 구문을 조금 수정합니다. 사용자 할당 ID가 있는 VM을 프로비전하려면 `-IdentityType UserAssigned` 및 `-IdentityID ` 매개 변수를 추가합니다.  `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>`을 사용자 고유의 값을 바꿉니다.  예: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [PowerShell을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md)

2. (선택 사항) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 Azure 리소스에 대한 관리 ID VM 확장을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다. 기존 VM의 위치와 일치하는 올바른 `-Location` 매개 변수를 지정해야 합니다.
      > [!NOTE]
    > 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다. Azure 리소스에 대한 관리 ID VM 확장은 2019년 1월에 사용 중단될 예정입니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>기존 Azure VM에 사용자 할당 관리 ID 할당

기존 Azure VM에 사용자 할당 관리 ID를 할당하려면:

1. `Connect-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Connect-AzureRmAccount
   ```

2. [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) cmdlet을 사용하여 사용자 할당 관리 ID를 만듭니다.  다음 단계에서 필요하므로 출력에서 `Id`를 메모해 둡니다.

   > [!IMPORTANT]
   > 사용자 할당 관리 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하세요. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 다음, Azure VM에 사용자 할당 관리 ID를 할당하고 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-IdentityType` 및 `-IdentityID` 스위치를 사용합니다.  `-IdentityId` 매개 변수의 값은 이전 단계에서 메모해 둔 `Id`입니다.  `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>`을 사용자 고유의 값으로 바꿉니다.

   > [!WARNING]
   > VM에 할당된 이전에 사용자가 할당된 관리 ID를 유지하려면 VM 개체의 `Identity` 속성(예: `$vm.Identity`)을 쿼리합니다.  사용자가 할당한 관리 ID가 반환되면 이 관리 ID와 VM에 할당하려는 새 사용자 할당 관리 ID를 함께 다음 명령에 포함합니다.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단될 예정)을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다. 기존 VM의 위치와 일치하는 올바른 `-Location` 매개 변수를 지정합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 사용자 할당 관리 ID 제거

VM에 여러 사용자 할당 관리 ID가 있는 경우 다음 명령을 사용하여 마지막 것을 제외하고 모두 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY NAME>`은 사용자 할당 관리 ID의 이름 속성으로, VM에 남아 있어야 합니다. 이 정보는 VM 개체의 `Identity` 속성을 쿼리하여 찾을 수 있습니다.  예 `$vm.Identity`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
VM에 시스템 할당 관리 ID가 없고 모든 사용자 할당 관리 ID를 제거하려는 경우 다음 명령을 사용합니다.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
VM에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 시스템 할당 관리 ID만 사용하도록 전환하여 모든 사용자 할당 관리 ID를 제거할 수 있습니다.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md) 
