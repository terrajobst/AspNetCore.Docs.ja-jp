---
title: ASP.NET Core での認証された暗号化の詳細
author: rick-anderson
description: ASP.NET Core データ保護で認証された暗号化の実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655004"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>ASP.NET Core での認証された暗号化の詳細

<a name="data-protection-implementation-authenticated-encryption-details"></a>

IDataProtector の呼び出しは、認証された暗号化操作です。 Protect メソッドは、機密性と信頼性の両方を提供します。これは、ルート IDataProtectionProvider からこの特定の IDataProtector インスタンスを派生させるために使用された目的のチェーンに関連付けられています。

IDataProtector は byte [] プレーンテキストパラメーターを受け取り、byte [] 保護されたペイロードを生成します。その形式を次に示します。 (文字列の plaintext パラメーターを受け取り、文字列で保護されたペイロードを返す拡張メソッドのオーバーロードもあります。 この API が使用されている場合、保護されたペイロード形式の構造は次のようになりますが、 [base64url エンコード](https://tools.ietf.org/html/rfc4648#section-5)されます)。

## <a name="protected-payload-format"></a>保護されたペイロード形式

保護されたペイロード形式は、次の3つの主要コンポーネントで構成されます。

* データ保護システムのバージョンを識別する32ビットマジックヘッダー。

* この特定のペイロードを保護するために使用されるキーを識別する、128ビットのキー id。

* 保護されたペイロードの残りの部分は、[このキーによってカプセル化](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)された暗号化機能に固有です。 次の例では、キーは AES-256-CBC + HMACSHA256 暗号化機能を表し、ペイロードはさらに次のように細分化されています。
  * 128ビットのキー修飾子。
  * 128ビットの初期化ベクター。
  * 48バイトの AES-256-CBC 出力。
  * HMACSHA256 authentication タグ。

保護されたペイロードの例を次に示します。

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

最初の32ビットより上のペイロード形式から、または4バイトがバージョンを識別するマジックヘッダー (09 F0 C9 F0)

次の128ビット、または16バイトはキー識別子です (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

残りはペイロードを含み、使用される形式に固有のものです。

> [!WARNING]
> 特定のキーに対して保護されているすべてのペイロードは、同じ20バイト (マジック値, キー id) ヘッダーで開始されます。 管理者は、この事実を診断のために使用して、ペイロードが生成されたことを概算することができます。 たとえば、上記のペイロードはキー {0c819c80-6619-4019-9536-53f8aaffee57} に対応しています。 キーリポジトリを確認した後に、この特定のキーのライセンス認証日が2015-01-01 であり、その有効期限が2015-03-01 であることが判明した場合は、ペイロード (改ざんされていない場合) がそのウィンドウ内に生成されたと見なすことができます。どちらかの側のファッジ factor。
