---
title: サブキーの派生と ASP.NET Core での認証された暗号化
author: rick-anderson
description: サブキーの派生と認証暗号化を ASP.NET Core データ保護の実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: bbfde378755b09cd5b1217b8cf66249b9fa1d6ad
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814386"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>サブキーの派生と ASP.NET Core での認証された暗号化

<a name="data-protection-implementation-subkey-derivation"></a>

キー リング内のほとんどのキーは、何らかの形式のエントロピが含まれ、「CBC モードの暗号化 + HMAC の検証」というアルゴリズムの情報が含まれますか「GCM 暗号化 + 検証」です。 このような場合は、埋め込みのエントロピをこのキーのマスター_キー生成情報 (または KM) と呼びます、実際の暗号化操作に使用されるキーを派生させるキー派生関数を実行します。

> [!NOTE]
> キーは抽象クラスで、カスタム実装が次に示すように動作しない可能性があります。 キーは、独自の実装を提供する場合`IAuthenticatedEncryptor`組み込み工場のいずれかを使用してではなくこのセクションで説明されているメカニズムが適用されなくなった。

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>追加の認証済みデータとサブキーの派生

`IAuthenticatedEncryptor`インターフェイスは、すべての認証された暗号化操作の主要なインターフェイスとして機能します。 その`Encrypt`メソッドは 2 つのバッファーを受け取ります。 プレーン テキストと additionalAuthenticatedData (AAD)。 プレーン テキスト コンテンツのフローへの呼び出しをそのまま`IDataProtector.Protect`AAD は、システムによって生成され、3 つのコンポーネントで構成されていますが。

1. 32 ビット マジック ヘッダー 09 F0 C9 F0 このバージョンのデータの保護システムを識別します。

2. 128 ビットのキー id。

3. 可変長文字列の形式を作成した目的のチェーンから、`IDataProtector`この操作を実行しています。

AAD は 3 つの要素のタプルに対して一意であるためを KM から KM 自体ですべての暗号化の操作を使用する代わりに新しいキーを派生させるために使用できます。 すべての呼び出しの`IAuthenticatedEncryptor.Encrypt`、次のキー派生のプロセスを実行します。

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

ここでは、NIST SP800 108 の KDF カウンター モードで呼び出しています (を参照してください[108-NIST SP800](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)、1 秒あたりの 5.1) は次のパラメーター。

* キー派生キー (KDK) K_M を =

* PRF = HMACSHA512

* label = additionalAuthenticatedData

* context = contextHeader || keyModifier

コンテキスト ヘッダーは可変長の基本的に拇印を K_E と K_H を派生しているアルゴリズムの役割を果たします。 キーの修飾子は、呼び出しごとにランダムに生成される 128 ビット文字列`Encrypt`KE と KH がこの特定の認証の暗号化操作の一意の KDF への他のすべての入力が定数の場合でも確率を過負荷を確実に機能します。

CBC モードの暗号化 + HMAC 検証操作では、|K_E |対称ブロック暗号キーの長さと |K_H |HMAC ルーチンのダイジェストのサイズです。 GCM の暗号化と検証の操作 |K_H |= 0。

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC モードの暗号化 + HMAC の検証

K_E が上記のメカニズムを使用して生成されるはランダムな初期化ベクトルを生成し、プレーン テキストを暗号化する対称ブロック暗号アルゴリズムを実行します。 初期化ベクター、暗号文は K_H MAC を生成するためにキーを使用して初期化 HMAC ルーチンを実行し、 このプロセスと戻り値は、以下でグラフィカルに表されます。

![CBC モードの処理と戻り値](subkeyderivation/_static/cbcprocess.png)

*出力: keyModifier を = | |iv | |E_cbc (K_E、iv、データ) | |HMAC (K_H、iv | |E_cbc (K_E、iv、データ))*

> [!NOTE]
> `IDataProtector.Protect`実装は[ヘッダー マジックとキーの id を付加](xref:security/data-protection/implementation/authenticated-encryption-details)を呼び出し元に返す前に出力します。 ヘッダーのマジックとキーの id は、暗黙的にするための一部[AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)、キー修飾子は、KDF への入力としてが取り込まれる、ため最終的なペイロードは返されるは 1 バイトずつが MAC で認証されると

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/カウンター モード暗号化と検証

K_E が上記のメカニズムを使用して生成されると、私たちランダム 96 ビットの nonce を生成し、プレーン テキストを暗号化して、128 ビット認証タグを生成する対称ブロック暗号アルゴリズムを実行します。

![GCM モードの処理と戻り値](subkeyderivation/_static/galoisprocess.png)

*出力: keyModifier を = | |nonce | |E_gcm (K_E、nonce、データ) | |authTag*

> [!NOTE]
> GCM ネイティブ AAD の概念をサポートする場合でもいるまだ供給 AAD 元の KDF にのみ GCM に AAD パラメーターを空の文字列を渡すこと。 この理由は、2 つあります。 まず、[機敏性をサポートするために](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers)暗号化キーとして直接 K_M を使用することはありません。 さらに、GCM では、その入力に一意性を非常に厳格な要件を課します。 GCM の暗号化ルーチンが 2 つが呼び出されたかより明確である確率を設定 (キー、nonce) 同じ入力データのペアが 2 を超えてはいけません ^32 です。 2 以上を実行できない場合 K_E を修正しました ^2 への実行前に、32 の暗号化操作 ^-32 を制限します。 操作の非常に大きい番号のように見えるかもしれませんが、高トラフィックの web サーバーは、これらのキーの通常の有効期間の範囲内の単なる日で 40億要求を通過できます。 2 の準拠を維持する ^128 ビット キー修飾子と 96 ビットの nonce は、任意の特定 K_M の使用可能な操作の数を大幅に拡張を使用していく-32 確率の制限。 わかりやすくするためのデザインの共有、CBC と GCM 操作の間の KDF コード パスと、AAD が既に、KDF でと見なされるためには、GCM ルーチンに転送する必要はありません。
