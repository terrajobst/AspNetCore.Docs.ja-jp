---
title: ASP.NET Core でのデータ保護コンピューター全体のポリシーサポート
author: rick-anderson
description: ASP.NET Core データ保護を使用するすべてのアプリに対して、コンピューター全体の既定のポリシーを設定するためのサポートについて説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655064"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core でのデータ保護コンピューター全体のポリシーサポート

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Windows で実行している場合、データ保護システムでは、ASP.NET Core データ保護を使用するすべてのアプリに対して、コンピューター全体の既定のポリシーを設定するためのサポートが制限されています。 一般的な考え方として、管理者は、コンピューター上のすべてのアプリを手動で更新する必要なく、使用されるアルゴリズムやキーの有効期間など、既定の設定を変更することができます。

> [!WARNING]
> システム管理者は既定のポリシーを設定できますが、強制することはできません。 アプリ開発者は、任意の値を選択していつでもオーバーライドできます。 既定のポリシーは、開発者が設定に明示的な値を指定していないアプリにのみ影響します。

## <a name="setting-default-policy"></a>既定のポリシーの設定

既定のポリシーを設定するには、管理者は次のレジストリキーの下にあるシステムレジストリで既知の値を設定できます。

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

64ビットのオペレーティングシステムを使用していて、32ビットアプリの動作に影響を与える場合は、上記のキーと同等の Wow6432Node を構成することを忘れないでください。

サポートされている値を以下に示します。

| 値              | 種類   | 説明 |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | データ保護に使用するアルゴリズムを指定します。 値は CNG、CNG、または管理されている必要があります。詳細については、後述します。 |
| DefaultKeyLifetime | DWORD  | 新しく生成されたキーの有効期間を指定します。 値は日数で指定し、> = 7 にする必要があります。 |
| KeyEscrowSinks     | string | キーエスクローに使用される型を指定します。 値は、キーエスクローシンクのセミコロン区切りのリストです。リスト内の各要素は、 [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)を実装する型のアセンブリ修飾名です。 |

## <a name="encryption-types"></a>暗号化の種類

EncryptionType が CNG-CBC の場合、システムは、Windows CNG によって提供されるサービスとの信頼性を確保するために、CBC モードの対称ブロック暗号を使用するように構成されています (詳細については、「[カスタム WINDOWS cng アルゴリズムの指定](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)」を参照してください)。 次の追加の値がサポートされており、それぞれが CngCbcAuthenticatedEncryptionSettings 型のプロパティに対応しています。

| 値                       | 種類   | 説明 |
| --------------------------- | :----: | ----------- |
| [EncryptionAlgorithm]         | string | CNG によって認識される対称ブロック暗号アルゴリズムの名前。 このアルゴリズムは、CBC モードで開かれています。 |
| EncryptionAlgorithmProvider | string | アルゴリズム EncryptionAlgorithm を生成できる CNG プロバイダー実装の名前。 |
| EncryptionAlgorithmKeySize  | DWORD  | 対称ブロック暗号アルゴリズム用に派生させるキーの長さ (ビット単位)。 |
| HashAlgorithm               | string | CNG によって認識されるハッシュアルゴリズムの名前。 このアルゴリズムは、HMAC モードで開かれています。 |
| HashAlgorithmProvider       | string | アルゴリズム HashAlgorithm を生成できる CNG プロバイダー実装の名前。 |

EncryptionType が CNG-GCM の場合、システムは、Windows CNG によって提供されるサービスとの機密性および信頼性を確保するために、Galois/カウンタモードの対称ブロック暗号を使用するように構成されています (詳細については、「[カスタム WINDOWS cng アルゴリズムの指定](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)」を参照してください)。 次の追加の値がサポートされており、それぞれが CngGcmAuthenticatedEncryptionSettings 型のプロパティに対応しています。

| 値                       | 種類   | 説明 |
| --------------------------- | :----: | ----------- |
| [EncryptionAlgorithm]         | string | CNG によって認識される対称ブロック暗号アルゴリズムの名前。 このアルゴリズムは、Galois/カウンタモードで開かれています。 |
| EncryptionAlgorithmProvider | string | アルゴリズム EncryptionAlgorithm を生成できる CNG プロバイダー実装の名前。 |
| EncryptionAlgorithmKeySize  | DWORD  | 対称ブロック暗号アルゴリズム用に派生させるキーの長さ (ビット単位)。 |

EncryptionType が管理されている場合、システムは、機密性と KeyedHashAlgorithm に対してマネージ SymmetricAlgorithm を使用するように構成されています (詳細については、「[カスタムマネージアルゴリズムの指定](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)」を参照してください)。 次の追加の値がサポートされており、それぞれが ManagedAuthenticatedEncryptionSettings 型のプロパティに対応しています。

| 値                      | 種類   | 説明 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | SymmetricAlgorithm を実装する型のアセンブリ修飾名。 |
| EncryptionAlgorithmKeySize | DWORD  | 対称暗号化アルゴリズム用に派生させるキーの長さ (ビット単位)。 |
| ValidationAlgorithmType    | string | KeyedHashAlgorithm を実装する型のアセンブリ修飾名。 |

EncryptionType の値が null または空以外の場合は、起動時にデータ保護システムによって例外がスローされます。

> [!WARNING]
> 型名 (EncryptionAlgorithmType、ValidationAlgorithmType、KeyEscrowSinks) を含む既定のポリシー設定を構成する場合は、アプリで種類を使用できるようにする必要があります。 これは、デスクトップ CLR で実行されているアプリの場合、これらの型を含むアセンブリがグローバルアセンブリキャッシュ (GAC) に存在する必要があることを意味します。 .NET Core で実行されている ASP.NET Core アプリの場合は、これらの種類を含むパッケージをインストールする必要があります。
