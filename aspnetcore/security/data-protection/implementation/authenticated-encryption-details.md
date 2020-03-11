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
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="5ff6a-103">ASP.NET Core での認証された暗号化の詳細</span><span class="sxs-lookup"><span data-stu-id="5ff6a-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="5ff6a-104">IDataProtector の呼び出しは、認証された暗号化操作です。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="5ff6a-105">Protect メソッドは、機密性と信頼性の両方を提供します。これは、ルート IDataProtectionProvider からこの特定の IDataProtector インスタンスを派生させるために使用された目的のチェーンに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="5ff6a-106">IDataProtector は byte [] プレーンテキストパラメーターを受け取り、byte [] 保護されたペイロードを生成します。その形式を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="5ff6a-107">(文字列の plaintext パラメーターを受け取り、文字列で保護されたペイロードを返す拡張メソッドのオーバーロードもあります。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="5ff6a-108">この API が使用されている場合、保護されたペイロード形式の構造は次のようになりますが、 [base64url エンコード](https://tools.ietf.org/html/rfc4648#section-5)されます)。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="5ff6a-109">保護されたペイロード形式</span><span class="sxs-lookup"><span data-stu-id="5ff6a-109">Protected payload format</span></span>

<span data-ttu-id="5ff6a-110">保護されたペイロード形式は、次の3つの主要コンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="5ff6a-111">データ保護システムのバージョンを識別する32ビットマジックヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="5ff6a-112">この特定のペイロードを保護するために使用されるキーを識別する、128ビットのキー id。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="5ff6a-113">保護されたペイロードの残りの部分は、[このキーによってカプセル化](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)された暗号化機能に固有です。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="5ff6a-114">次の例では、キーは AES-256-CBC + HMACSHA256 暗号化機能を表し、ペイロードはさらに次のように細分化されています。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="5ff6a-115">128ビットのキー修飾子。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="5ff6a-116">128ビットの初期化ベクター。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="5ff6a-117">48バイトの AES-256-CBC 出力。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="5ff6a-118">HMACSHA256 authentication タグ。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="5ff6a-119">保護されたペイロードの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="5ff6a-120">最初の32ビットより上のペイロード形式から、または4バイトがバージョンを識別するマジックヘッダー (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="5ff6a-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="5ff6a-121">次の128ビット、または16バイトはキー識別子です (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="5ff6a-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="5ff6a-122">残りはペイロードを含み、使用される形式に固有のものです。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="5ff6a-123">特定のキーに対して保護されているすべてのペイロードは、同じ20バイト (マジック値, キー id) ヘッダーで開始されます。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="5ff6a-124">管理者は、この事実を診断のために使用して、ペイロードが生成されたことを概算することができます。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="5ff6a-125">たとえば、上記のペイロードはキー {0c819c80-6619-4019-9536-53f8aaffee57} に対応しています。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="5ff6a-126">キーリポジトリを確認した後に、この特定のキーのライセンス認証日が2015-01-01 であり、その有効期限が2015-03-01 であることが判明した場合は、ペイロード (改ざんされていない場合) がそのウィンドウ内に生成されたと見なすことができます。どちらかの側のファッジ factor。</span><span class="sxs-lookup"><span data-stu-id="5ff6a-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
