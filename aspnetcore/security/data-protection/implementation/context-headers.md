---
title: ASP.NET Core でのコンテキスト ヘッダー
author: rick-anderson
description: コンテキスト ヘッダーを ASP.NET Core データ保護の実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814023"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="e4914-103">ASP.NET Core でのコンテキスト ヘッダー</span><span class="sxs-lookup"><span data-stu-id="e4914-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="e4914-104">背景と理論的には</span><span class="sxs-lookup"><span data-stu-id="e4914-104">Background and theory</span></span>

<span data-ttu-id="e4914-105">データ保護システムでは、「キー」は、認証された暗号化サービスを提供できるオブジェクトを意味します。</span><span class="sxs-lookup"><span data-stu-id="e4914-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="e4914-106">各キーは一意の id (GUID) によって識別され、それが伴うことアルゴリズム情報や entropic マテリアル。</span><span class="sxs-lookup"><span data-stu-id="e4914-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="e4914-107">各キーが一意のエントロピを実行するが、システムを適用することはできず、キー リング内の既存のキーのアルゴリズム情報を変更してキー リングを手動で変更が開発者のために考慮する必要もありますが目的です。</span><span class="sxs-lookup"><span data-stu-id="e4914-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="e4914-108">このような場合に指定された、セキュリティ要件を実現するために、データ保護システムがの概念を持つ[暗号化方式の指定](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/)、entropic 1 つの値を使用して複数の暗号化アルゴリズムの間で安全にできます。</span><span class="sxs-lookup"><span data-stu-id="e4914-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="e4914-109">暗号化方式の指定をサポートするほとんどのシステムでは、ペイロード内でアルゴリズムに関するいくつかの識別情報を含めることによって行います。</span><span class="sxs-lookup"><span data-stu-id="e4914-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="e4914-110">アルゴリズムの OID は、通常、これに適した候補です。</span><span class="sxs-lookup"><span data-stu-id="e4914-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="e4914-111">しかし、私たちが直面する問題の 1 つでは、同じアルゴリズムを指定する複数の方法があることです。"AES"(CNG) と、マネージ Aes、AesManaged、AesCryptoServiceProvider、AesCng、および (指定した特定のパラメーター) RijndaelManaged クラスは、同じすべて実際には、適切な OID にこれらすべてのマッピングを維持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4914-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="e4914-112">開発者は、カスタム アルゴリズム (または別の実装の AES!) を提供する場合は、その OID お知らせする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4914-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="e4914-113">この余分な登録ステップでシステムの構成は特に厄介です。</span><span class="sxs-lookup"><span data-stu-id="e4914-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="e4914-114">誤った方向から問題に取り組みされたことを決めましたステップ バック。</span><span class="sxs-lookup"><span data-stu-id="e4914-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="e4914-115">OID は、アルゴリズムを示しますが、実際にこれについては注目しません。</span><span class="sxs-lookup"><span data-stu-id="e4914-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="e4914-116">場合は、2 つの異なるアルゴリズムの 1 つの entropic 値を安全に使用する必要があります、アルゴリズムが実際には何を把握する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e4914-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="e4914-117">どのような非常に心配、動作方法です。</span><span class="sxs-lookup"><span data-stu-id="e4914-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="e4914-118">任意の適切な対称ブロック暗号アルゴリズムも強力な擬似順列 (PRP): (キー、モード、IV、プレーン テキストの組み合わせ) の入力を修正し、暗号化テキストの出力は確率を過負荷に異なる名前に、その他の対称ブロック暗号アルゴリズムは、同じ入力を指定します。</span><span class="sxs-lookup"><span data-stu-id="e4914-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="e4914-119">同様に、任意の適切なキー付きハッシュ関数は厳密な擬似乱数関数 (PRF) でも、指定した固定の入力セットの出力は圧倒的に他のすべてのキー付きハッシュ関数とは異なります。</span><span class="sxs-lookup"><span data-stu-id="e4914-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="e4914-120">コンテキスト ヘッダーを構築する PRPs と PRFs の強力なは、この概念を使用します。</span><span class="sxs-lookup"><span data-stu-id="e4914-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="e4914-121">このコンテキスト ヘッダーが本質的には、アルゴリズムで使用して、特定の操作を安定した拇印として機能し、データ保護システムで必要な暗号化方式の指定を提供します。</span><span class="sxs-lookup"><span data-stu-id="e4914-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="e4914-122">このヘッダーが再現可能およびの一部として後で使用されて、[サブキーの派生プロセス](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)します。</span><span class="sxs-lookup"><span data-stu-id="e4914-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="e4914-123">基になるアルゴリズムの動作モードに応じてコンテキスト ヘッダーを構築する 2 つのさまざまな方法はあります。</span><span class="sxs-lookup"><span data-stu-id="e4914-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="e4914-124">CBC モードの暗号化 + HMAC 認証</span><span class="sxs-lookup"><span data-stu-id="e4914-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="e4914-125">コンテキスト ヘッダーは、次のコンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="e4914-126">[16 ビット]値 00 時 00 分、マーカーである「CBC 暗号化 + HMAC 認証」を意味します。</span><span class="sxs-lookup"><span data-stu-id="e4914-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="e4914-127">[32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e4914-128">[32 ビット]対称ブロック暗号アルゴリズムのブロック サイズ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e4914-129">[32 ビット]HMAC アルゴリズムのキーの長さ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="e4914-130">(現在、キー サイズ常に一致ダイジェストのサイズ。)</span><span class="sxs-lookup"><span data-stu-id="e4914-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="e4914-131">[32 ビット]HMAC アルゴリズムのダイジェスト サイズ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="e4914-132">EncCBC (K_E、IV、"")、これは、指定した空の文字列入力対称ブロック暗号アルゴリズムの出力および IV が、すべてゼロのベクトルが。</span><span class="sxs-lookup"><span data-stu-id="e4914-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="e4914-133">K_E の構築を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="e4914-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="e4914-134">MAC (K_H、"")、指定した空の文字列入力 HMAC アルゴリズムの出力であります。</span><span class="sxs-lookup"><span data-stu-id="e4914-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="e4914-135">K_H の構築を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="e4914-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="e4914-136">理想的には、K_E と K_H すべてゼロのベクトルを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="e4914-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="e4914-137">ただし、基になるアルゴリズムが、すべてゼロのベクターのような単純型または反復可能なパターンを使用して適応 (特に DES および 3 des) の操作を実行する前に脆弱なキーの存在をチェックする状況を回避します。</span><span class="sxs-lookup"><span data-stu-id="e4914-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="e4914-138">代わりに、NIST SP800 108 の KDF カウンター モードの使用 (を参照してください[108-NIST SP800](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)、Sec. 5.1) の長さ 0 のキー、ラベルとコンテキスト、および基になる PRF として HMACSHA512。</span><span class="sxs-lookup"><span data-stu-id="e4914-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="e4914-139">派生 |K_E |+ |K_H |出力のバイトし、結果に分解 K_E と K_H 自体。</span><span class="sxs-lookup"><span data-stu-id="e4914-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="e4914-140">数学的に、これは、ように表されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="e4914-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="e4914-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="e4914-142">例:AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="e4914-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="e4914-143">例として、対称ブロック暗号アルゴリズムは、AES-CBC 192 検証アルゴリズムが HMACSHA256 場合を検討してください。</span><span class="sxs-lookup"><span data-stu-id="e4914-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="e4914-144">システムでは、次の手順を使用して、コンテキスト ヘッダーを生成します。</span><span class="sxs-lookup"><span data-stu-id="e4914-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="e4914-145">まず、(K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512、キー =""、ラベル =""、コンテキスト ="")、|K_E |= 192 ビット、|K_H |指定したアルゴリズムごとの 256 ビットを = です。</span><span class="sxs-lookup"><span data-stu-id="e4914-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="e4914-146">これにより、K_E 5BB6 =.21DD と K_H A04A =.次の例で 00A9:</span><span class="sxs-lookup"><span data-stu-id="e4914-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="e4914-147">Enc_CBC を次に、コンピューティング (K_E、IV、"") = 0 CBC 192 AES IV を指定 \* と K_E 上とします。</span><span class="sxs-lookup"><span data-stu-id="e4914-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="e4914-148">結果: F474B1872B3B53E4721DE19C0841DB6F を =</span><span class="sxs-lookup"><span data-stu-id="e4914-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="e4914-149">次に、MAC の計算 (K_H、"") の HMACSHA256 K_H 上記として指定されています。</span><span class="sxs-lookup"><span data-stu-id="e4914-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="e4914-150">結果: D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C を =</span><span class="sxs-lookup"><span data-stu-id="e4914-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="e4914-151">これには、次の完全なコンテキスト ヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="e4914-152">このコンテキスト ヘッダーには、認証された暗号化アルゴリズムのペア (AES-CBC 192 暗号化 + HMACSHA256 検証) の拇印です。</span><span class="sxs-lookup"><span data-stu-id="e4914-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="e4914-153">説明に従って、コンポーネント[上](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)は。</span><span class="sxs-lookup"><span data-stu-id="e4914-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="e4914-154">マーカー (00 00)</span><span class="sxs-lookup"><span data-stu-id="e4914-154">the marker (00 00)</span></span>

* <span data-ttu-id="e4914-155">ブロック暗号キー長 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="e4914-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="e4914-156">ブロック暗号ブロック サイズ (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="e4914-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="e4914-157">HMAC キーの長さ (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="e4914-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="e4914-158">HMAC ダイジェストのサイズ (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="e4914-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="e4914-159">PRP の出力 (F4 74 - DB 6F) ブロック暗号と</span><span class="sxs-lookup"><span data-stu-id="e4914-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="e4914-160">HMAC PRF 出力 (D4 79 - 終了)。</span><span class="sxs-lookup"><span data-stu-id="e4914-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="e4914-161">CBC モードの暗号化 + HMAC 認証コンテキスト ヘッダーが Windows CNG または SymmetricAlgorithm と KeyedHashAlgorithm のマネージ型に、アルゴリズムの実装を提供するかどうかに関係なく同じように構築されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="e4914-162">これにより、アルゴリズムの実装は Os によって異なる場合でも確実に同じコンテキスト ヘッダーを生成するためにさまざまなオペレーティング システムで実行されているアプリケーション。</span><span class="sxs-lookup"><span data-stu-id="e4914-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="e4914-163">(実際には、KeyedHashAlgorithm が、適切な HMAC がありません。</span><span class="sxs-lookup"><span data-stu-id="e4914-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="e4914-164">できます任意のキー付きハッシュ アルゴリズムの種類。)</span><span class="sxs-lookup"><span data-stu-id="e4914-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="e4914-165">例:3 DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="e4914-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="e4914-166">まず、(K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512、キー =""、ラベル =""、コンテキスト ="")、|K_E |= 192 ビット、|K_H |指定したアルゴリズムごとの 160 ビットを = です。</span><span class="sxs-lookup"><span data-stu-id="e4914-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="e4914-167">これにより、K_E A219 =.E2BB と K_H DC4A =.次の例で B464:</span><span class="sxs-lookup"><span data-stu-id="e4914-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="e4914-168">Enc_CBC を次に、コンピューティング (K_E、IV、"") = 0 CBC 192 3 des IV を指定 \* と K_E 上とします。</span><span class="sxs-lookup"><span data-stu-id="e4914-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="e4914-169">結果: ABB100F81E53E10E を =</span><span class="sxs-lookup"><span data-stu-id="e4914-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="e4914-170">次に、MAC の計算 (K_H、"") の HMACSHA1 K_H 上記として指定されています。</span><span class="sxs-lookup"><span data-stu-id="e4914-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="e4914-171">結果: 76EB189B35CF03461DDF877CD9F4B1B4D63A7555 を =</span><span class="sxs-lookup"><span data-stu-id="e4914-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="e4914-172">これにより、認証の拇印である完全なコンテキスト ヘッダーが生成されます。 暗号化アルゴリズムのペア (3 des-192-CBC 暗号化 + HMACSHA1 検証)、次に示します。</span><span class="sxs-lookup"><span data-stu-id="e4914-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="e4914-173">コンポーネントが次のように分割します。</span><span class="sxs-lookup"><span data-stu-id="e4914-173">The components break down as follows:</span></span>

* <span data-ttu-id="e4914-174">マーカー (00 00)</span><span class="sxs-lookup"><span data-stu-id="e4914-174">the marker (00 00)</span></span>

* <span data-ttu-id="e4914-175">ブロック暗号キー長 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="e4914-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="e4914-176">ブロック暗号ブロック サイズ (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="e4914-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="e4914-177">HMAC キーの長さ (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="e4914-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="e4914-178">HMAC ダイジェストのサイズ (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="e4914-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="e4914-179">PRP の出力 (AB B1 - E1 0 e) ブロック暗号と</span><span class="sxs-lookup"><span data-stu-id="e4914-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="e4914-180">HMAC PRF 出力 (76 EB - 終了)。</span><span class="sxs-lookup"><span data-stu-id="e4914-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="e4914-181">Galois/カウンター モード暗号化 + 認証</span><span class="sxs-lookup"><span data-stu-id="e4914-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="e4914-182">コンテキスト ヘッダーは、次のコンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="e4914-183">[16 ビット]値 00 はマーカー 01、「GCM 暗号化 + 認証」を意味します。</span><span class="sxs-lookup"><span data-stu-id="e4914-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="e4914-184">[32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e4914-185">[32 ビット]Nonce サイズ (バイト単位、ビッグ エンディアン) 認証された暗号化の操作中に使用します。</span><span class="sxs-lookup"><span data-stu-id="e4914-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="e4914-186">(Nonce のサイズに固定このシステムは、の 96 ビットを = です)。</span><span class="sxs-lookup"><span data-stu-id="e4914-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="e4914-187">[32 ビット]対称ブロック暗号アルゴリズムのブロック サイズ (単位: バイト、ビッグ エンディアン)。</span><span class="sxs-lookup"><span data-stu-id="e4914-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="e4914-188">(ブロック サイズに固定これの GCM、128 ビットを = です)。</span><span class="sxs-lookup"><span data-stu-id="e4914-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="e4914-189">[32 ビット]認証タグ サイズ (バイト、ビッグ エンディアン) 認証された暗号化関数によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="e4914-190">(、システムの場合は、タグのサイズに固定してこれが 128 ビットを = です)。</span><span class="sxs-lookup"><span data-stu-id="e4914-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="e4914-191">[128 ビット]Enc_GCM のタグ (K_E、nonce、"")、これは、指定した空の文字列入力対称ブロック暗号アルゴリズムの出力および nonce が 96 ビットすべてゼロのベクトルが。</span><span class="sxs-lookup"><span data-stu-id="e4914-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="e4914-192">K_E は CBC 暗号化 + 認証シナリオの HMAC のように同じメカニズムを使用して派生します。</span><span class="sxs-lookup"><span data-stu-id="e4914-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="e4914-193">ただし、このアプローチで K_H がないため本質的が |K_H |= 0 の場合、アルゴリズムを解除し、フォームの下。</span><span class="sxs-lookup"><span data-stu-id="e4914-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="e4914-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="e4914-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="e4914-195">例:AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="e4914-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="e4914-196">K_E おきます SP800_108_CTR = (prf = HMACSHA512、キー =""、ラベル =""、コンテキスト ="")、|K_E |= 256 ビットです。</span><span class="sxs-lookup"><span data-stu-id="e4914-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="e4914-197">K_E: 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8 を =</span><span class="sxs-lookup"><span data-stu-id="e4914-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="e4914-198">Enc_GCM の認証タグを次に、コンピューティング (K_E、nonce、"") GCM 256 AES nonce を指定するのには、上記 096 と K_E を = です。</span><span class="sxs-lookup"><span data-stu-id="e4914-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="e4914-199">結果: E7DCCE66DF855A323A6BB7BD7A59BE45 を =</span><span class="sxs-lookup"><span data-stu-id="e4914-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="e4914-200">これには、次の完全なコンテキスト ヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e4914-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="e4914-201">コンポーネントが次のように分割します。</span><span class="sxs-lookup"><span data-stu-id="e4914-201">The components break down as follows:</span></span>

* <span data-ttu-id="e4914-202">マーカー (00 01)</span><span class="sxs-lookup"><span data-stu-id="e4914-202">the marker (00 01)</span></span>

* <span data-ttu-id="e4914-203">ブロック暗号キー長 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="e4914-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="e4914-204">nonce のサイズ (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="e4914-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="e4914-205">ブロック暗号ブロック サイズ (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="e4914-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="e4914-206">認証タグのサイズ (00 00 00 10) と</span><span class="sxs-lookup"><span data-stu-id="e4914-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="e4914-207">ブロック暗号を実行してから認証タグ (E7 DC の終了)。</span><span class="sxs-lookup"><span data-stu-id="e4914-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
