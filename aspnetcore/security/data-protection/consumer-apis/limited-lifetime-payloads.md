---
title: 保護されたペイロードの有効期間を制限する ASP.NET Core
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用して、保護されたペイロードの有効期間を制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651440"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>保護されたペイロードの有効期間を制限する ASP.NET Core

アプリケーション開発者が、一定の時間が経過すると期限切れになる保護されたペイロードを作成しようとするシナリオがあります。 たとえば、保護されたペイロードは、1時間だけ有効なパスワードリセットトークンを表す場合があります。 開発者は、埋め込み有効期限を含む独自のペイロード形式を作成できますが、上級開発者はこれを行うことをお勧めしますが、このような有効期限を管理する開発者の大部分は煩雑になります。

これを開発者にとって簡単にするために、パッケージ[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)には、設定した時間が経過すると自動的に期限切れになるペイロードを作成するためのユーティリティ api が含まれています。 これらの Api は `ITimeLimitedDataProtector` の種類から切断されます。

## <a name="api-usage"></a>API の使用

`ITimeLimitedDataProtector` インターフェイスは、時間制限のある、または有効期限が切れたペイロードを保護および復号化するためのコアインターフェイスです。 `ITimeLimitedDataProtector`のインスタンスを作成するには、まず、特定の目的で構築された通常の[IDataProtector](xref:security/data-protection/consumer-apis/overview)のインスタンスが必要です。 `IDataProtector` インスタンスが使用可能になったら、`IDataProtector.ToTimeLimitedDataProtector` 拡張メソッドを呼び出して、組み込みの有効期限があるプロテクターを取得します。

`ITimeLimitedDataProtector` は、次の API サーフェイスと拡張メソッドを公開します。

* CreateProtector (文字列の目的): ITimeLimitedDataProtector-この API は、ルートの時間限定プロテクターから[目的のチェーン](xref:security/data-protection/consumer-apis/purpose-strings)を作成するために使用できるという点で、既存の `IDataProtectionProvider.CreateProtector` に似ています。

* Protect (byte [] plaintext, DateTimeOffset 有効期限): byte []

* Protect(byte[] plaintext, TimeSpan lifetime) : byte[]

* Protect(byte[] plaintext) : byte[]

* Protect (文字列プレーンテキスト、DateTimeOffset 有効期限): 文字列

* Protect (文字列プレーンテキスト、TimeSpan 有効期間): 文字列

* Protect (文字列プレーンテキスト): 文字列

プレーンテキストのみを受け取る中核的な `Protect` メソッドに加えて、ペイロードの有効期限を指定できる新しいオーバーロードがあります。 有効期限は、(`DateTimeOffset`を使用して) 絶対日付として指定することも、(現在のシステム時刻から `TimeSpan`を介して) 相対時刻として指定することもできます。 有効期限がないオーバーロードが呼び出された場合、ペイロードは期限切れにならないと見なされます。

* 保護解除 (byte [] protectedData、out DateTimeOffset 有効期限): byte []

* Unprotect(byte[] protectedData) : byte[]

* 保護解除 (文字列 protectedData、out DateTimeOffset 有効期限): 文字列

* 保護解除 (string protectedData): 文字列

`Unprotect` メソッドは、保護されていない元のデータを返します。 ペイロードの有効期限がまだ切れていない場合、絶対有効期限は、元の保護されていないデータと共にオプションの out パラメーターとして返されます。 ペイロードの有効期限が切れた場合、保護を解除するメソッドのすべてのオーバーロードは、System.security.cryptography.cryptographicexception> をスローします。

>[!WARNING]
> 長期間または永続的な永続化を必要とするペイロードを保護するために、これらの Api を使用することは推奨されません。 "保護されたペイロードを1か月後に完全に回復できないようにすることはできますか。" 優れた目安として機能します。答えが「いいえ」の場合、開発者は別の Api を検討する必要があります。

次のサンプルでは、[非 DI コードパス](xref:security/data-protection/configuration/non-di-scenarios)を使用して、データ保護システムをインスタンス化しています。 このサンプルを実行するには、最初に AspNetCore パッケージへの参照を追加したことを確認します。

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
