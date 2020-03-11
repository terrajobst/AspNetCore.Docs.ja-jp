---
title: ASP.NET Core のコンテキストヘッダー
author: rick-anderson
description: ASP.NET Core データ保護コンテキストヘッダーの実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654578"
---
# <a name="context-headers-in-aspnet-core"></a>ASP.NET Core のコンテキストヘッダー

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>背景と理論

データ保護システムでは、"キー" は認証された暗号化サービスを提供できるオブジェクトを意味します。 各キーは一意の id (GUID) によって識別され、それにはアルゴリズム情報と entropic マテリアルが含まれます。 各キーは一意のエントロピを伝達することを目的としていますが、システムではこれを強制することはできません。また、キーリング内の既存のキーのアルゴリズム情報を変更することによって、キーリングを手動で変更する可能性がある開発者にも考慮する必要があります。 これらのケースによってセキュリティ要件を達成するために、データ保護システムには[暗号化の俊敏性](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/)の概念があります。これにより、複数の暗号化アルゴリズムで1つの entropic 値を安全に使用できます。

暗号化の機敏性をサポートするほとんどのシステムは、ペイロード内のアルゴリズムに関する特定の情報を含めます。 アルゴリズムの OID は、通常、このような場合に適しています。 ただし、1つの問題として、同じアルゴリズムを指定する方法は複数あります。 "AES" (CNG) とマネージ Aes、AesManaged、AesCryptoServiceProvider、AesCng、および RijndaelManaged (特定のパラメーター) クラスはすべて実際に同じです。これらのすべてを正しい OID にマッピングする必要があります。 開発者がカスタムアルゴリズム (または AES! の別の実装) を提供する必要がある場合は、その OID を知らせる必要があります。 この追加の登録手順では、システム構成が特に困難になります。

前のステップでは、間違った方向から問題に近づいていると判断しました。 OID はアルゴリズムがどのようなものかを示していますが、実際には気にしません。 2つの異なるアルゴリズムで1つの entropic 値を安全に使用する必要がある場合、アルゴリズムが実際にどのようなものかを知る必要はありません。 実際には、どのように動作するかということに注意してください。 任意の適正な対称ブロック暗号アルゴリズムは、強力な擬似乱数 (PRP) でもあります。入力 (キー、チェーンモード、IV、プレーンテキスト) を修正します。また、暗号化テキストの出力は、その他の対称ブロック暗号とは異なる可能性があります。同じ入力が指定されたアルゴリズム。 同様に、適切なキー付きハッシュ関数も強力な擬似乱数関数 (PRF) であり、固定された入力セットを指定すると、その出力は他のキー付きハッシュ関数とは異なる overwhelmingly になります。

コンテキストヘッダーを構築するには、この強力な PRPs と Prps の概念を使用します。 このコンテキストヘッダーは基本的に、特定の操作で使用されているアルゴリズムに対する安定したサムプリントとして機能し、データ保護システムで必要とされる暗号化の機敏性を提供します。 このヘッダーは再現可能で、後で[サブキー派生プロセス](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)の一部として使用されます。 コンテキストヘッダーを作成するには、基になるアルゴリズムの動作モードに応じて2つの方法があります。

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC モード暗号化 + HMAC 認証

<a name="data-protection-implementation-context-headers-cbc-components"></a>

コンテキストヘッダーは、次のコンポーネントで構成されています。

* [16 ビット]値 00 00。 "CBC encryption + HMAC authentication" という意味になります。

* [32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。

* [32 ビット]対称ブロック暗号アルゴリズムのブロックサイズ (バイト、ビッグエンディアン)。

* [32 ビット]HMAC アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。 (現在、キーサイズは常にダイジェストのサイズと一致しています)。

* [32 ビット]HMAC アルゴリズムのダイジェストサイズ (バイト、ビッグエンディアン)。

* EncCBC (K_E、IV、"")。これは、空の文字列入力を指定した場合は対称ブロック暗号アルゴリズムの出力、IV はすべてゼロのベクターです。 K_E の構築については、以下で説明します。

* MAC (K_H、"")。これは HMAC アルゴリズムの出力で、空の文字列入力が指定されています。 K_H の構築については、以下で説明します。

理想的には、K_E と K_H のすべてのゼロベクトルを渡すことができます。 ただし、基になるアルゴリズムが、何らかの操作 (特に DES と 3DES) を実行する前に、弱いキーの存在を確認するという状況を回避したいと考えています。

代わりに、カウンタモードで NIST SP800-108 KDF を使用します ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1 を参照)。長さ0のキー、ラベル、およびコンテキストと HMACSHA512 を基になる PRF として使用します。 派生した |K_E |+ |K_H |出力のバイト数。その後、結果を K_E に分解して K_H します。 数学的には、次のように表されます。

( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>例: AES-192-CBC + HMACSHA256

例として、対称ブロック暗号アルゴリズムが AES-192-CBC で、検証アルゴリズムが HMACSHA256 の場合を考えてみます。 システムは、次の手順を使用してコンテキストヘッダーを生成します。

まず、let (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512、key = ""、label = ""、context = "")、where |K_E |= 192 ビットと |K_H |= 指定されたアルゴリズムごとに256ビット。 これにより、K_E = 5BB6..21DD および K_H = A04A..00A9 の例を次に示します。

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

次に、AES-192-CBC 指定された IV = 0 * と、上記と同じ K_E を計算 Enc_CBC (K_E、IV、"") します。

結果: = F474B1872B3B53E4721DE19C0841DB6F

次に、上記の K_H に対して、HMACSHA256 の計算用 MAC (K_H、"") を指定します。

結果: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

これにより、以下の完全なコンテキストヘッダーが生成されます。

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

このコンテキストヘッダーは、認証された暗号化アルゴリズムペアの拇印です (AES-192-CBC encryption + HMACSHA256 validation)。 [上記](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)のコンポーネントは次のとおりです。

* マーカー (00 00)

* ブロック暗号キーの長さ (00 00 00 18)

* ブロック暗号ブロックサイズ (00 00 00 10)

* HMAC キーの長さ (00 00 00 20)

* HMAC ダイジェストのサイズ (00 00 00 20)

* ブロック暗号 PRP 出力 (F4 74-DB 6F) および

* HMAC PRF 出力 (D4 79-end)。

> [!NOTE]
> CBC モード暗号化 + HMAC 認証コンテキストヘッダーは、アルゴリズムの実装が Windows CNG によって提供されるか、マネージ SymmetricAlgorithm と KeyedHashAlgorithm 型によって提供されるかにかかわらず、同じように構築されます。 これにより、異なるオペレーティングシステムで実行されているアプリケーションは、Os によってアルゴリズムの実装が異なる場合でも、同じコンテキストヘッダーを確実に生成できます。 (実際には、KeyedHashAlgorithm は適切な HMAC である必要はありません。 任意のキー付きハッシュアルゴリズムの種類を使用できます)。

### <a name="example-3des-192-cbc--hmacsha1"></a>例: 3DES-192-CBC + HMACSHA1

まず、let (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512、key = ""、label = ""、context = "")、where |K_E |= 192 ビットと |K_H |= 指定されたアルゴリズムごとに160ビット。 これにより、K_E = A219 になります。E2BB および K_H = DC4A..B464 の例を次に示します。

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

次に、Enc_CBC (K_E, IV, "") を 3DES-192-CBC 指定した IV = 0 * と K_E 上と同じように計算します。

結果: = ABB100F81E53E10E

次に、上記の K_H に対して、HMACSHA1 の計算用 MAC (K_H、"") を指定します。

結果: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

これにより、次に示すように、認証された暗号化アルゴリズムペア (3DES-192-CBC encryption + HMACSHA1 validation) の拇印である完全なコンテキストヘッダーが生成されます。

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

コンポーネントは次のように分類されます。

* マーカー (00 00)

* ブロック暗号キーの長さ (00 00 00 18)

* ブロック暗号ブロックサイズ (00 00 00 08)

* HMAC キーの長さ (00 00 00 14)

* HMAC ダイジェストのサイズ (00 00 00 14)

* ブロック暗号 PRP 出力 (AB B1-E1 0E) と

* HMAC PRF 出力 (76 EB-end)。

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/カウンタモードの暗号化 + 認証

コンテキストヘッダーは、次のコンポーネントで構成されています。

* [16 ビット]値 00 01。 "GCM encryption + authentication" を意味します。

* [32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。

* [32 ビット]認証済みの暗号化操作中に使用される nonce のサイズ (バイト単位、ビッグエンディアン)。 (システムの場合は、nonce のサイズが96ビットに固定されています)。

* [32 ビット]対称ブロック暗号アルゴリズムのブロックサイズ (バイト、ビッグエンディアン)。 (GCM の場合は、ブロックサイズが128ビットに固定されています)。

* [32 ビット]認証された暗号化関数によって生成される認証タグのサイズ (バイト、ビッグエンディアン)。 (システムの場合は、タグサイズが128ビットに固定されています)。

* [128 ビット]Enc_GCM (K_E、nonce、"") のタグ。これは、空の文字列入力を指定した場合は対称ブロック暗号アルゴリズムの出力であり、nonce は96ビットのすべてゼロベクターです。

K_E は、CBC encryption + HMAC 認証シナリオと同じメカニズムを使用して派生します。 ただし、ここでは K_H がないため、基本的には |K_H |= 0 の場合、アルゴリズムは次の形式に折りたたまれます。

K_E = SP800_108_CTR (prf = HMACSHA512、key = ""、label = ""、context = "")

### <a name="example-aes-256-gcm"></a>例: AES-256-GCM

最初に K_E = SP800_108_CTR (prf = HMACSHA512、key = ""、label = ""、context = "") を指定します。ここで |K_E |= 256 ビット。

K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

次に、Enc_GCM の認証タグ (K_E、nonce、"") 256 を計算します。これは、上記のように、指定された nonce = 096 と K_E に与えられています。

結果: = E7DCCE66DF855A323A6BB7BD7A59BE45

これにより、以下の完全なコンテキストヘッダーが生成されます。

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

コンポーネントは次のように分類されます。

* マーカー (00 01)

* ブロック暗号キーの長さ (00 00 00 20)

* nonce のサイズ (00 00 00 0C)

* ブロック暗号ブロックサイズ (00 00 00 10)

* 認証タグのサイズ (00 00 00 10) と

* ブロック暗号 (E7 DC-end) を実行する認証タグ。
