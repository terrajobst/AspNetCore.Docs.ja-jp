---
title: ASP.NET Core のコンシューマー Api の概要
author: rick-anderson
description: ASP.NET Core データ保護ライブラリ内で使用できるさまざまなコンシューマー Api の簡単な概要を説明します。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654584"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core のコンシューマー Api の概要

`IDataProtectionProvider` インターフェイスと `IDataProtector` インターフェイスは、コンシューマーがデータ保護システムを使用するための基本的なインターフェイスです。 これらは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)パッケージに格納されています。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

プロバイダーインターフェイスは、データ保護システムのルートを表します。 データの保護または保護解除に直接使用することはできません。 代わりに、コンシューマーは `IDataProtectionProvider.CreateProtector(purpose)`を呼び出すことによって `IDataProtector` への参照を取得する必要があります。ここで、目的は、目的のコンシューマーユースケースを説明する文字列です。 このパラメーターの目的と適切な値を選択する方法の詳細については、「[目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)」を参照してください。

## <a name="idataprotector"></a>IDataProtector

プロテクターインターフェイスは `CreateProtector`への呼び出しによって返されます。このインターフェイスは、コンシューマーが保護と保護解除の操作を実行するために使用できるインターフェイスです。

データを保護するには、`Protect` メソッドにデータを渡します。 基本インターフェイスは byte []-> byte [] を変換するメソッドを定義しますが、文字列 > 文字列を変換するオーバーロード (拡張メソッドとして提供される) もあります。 2つのメソッドによって提供されるセキュリティは同じです。開発者は、ユースケースに最も便利なオーバーロードのいずれかを選択する必要があります。 選択されたオーバーロードに関係なく、Protect メソッドによって返される値は保護されるようになりました (読んだりと校正)。アプリケーションは信頼されていないクライアントに送信できます。

以前に保護されていたデータの一部を保護解除するには、保護されたデータを `Unprotect` メソッドに渡します。 (開発者の便宜のために、byte [] ベースのオーバーロードと文字列ベースのオーバーロードがあります)。この同じ `IDataProtector`に対して以前に `Protect` を呼び出したときに保護されたペイロードが生成された場合、`Unprotect` メソッドは元の保護されていないペイロードを返します。 保護されたペイロードが改ざんされている場合、または別の `IDataProtector`によって生成された場合、`Unprotect` メソッドは System.security.cryptography.cryptographicexception> をスローします。

同一および異なる `IDataProtector` の概念は、目的の概念に結び付けています。 2つの `IDataProtector` インスタンスが同じルート `IDataProtectionProvider` から生成されたものの、`IDataProtectionProvider.CreateProtector`の呼び出しで別の目的の文字列を使用して生成された場合、それらは[異なるプロテクター](xref:security/data-protection/consumer-apis/purpose-strings)と見なされ、もう一方のインスタンスによって生成されたペイロードの保護を解除することはできません。

## <a name="consuming-these-interfaces"></a>これらのインターフェイスの使用

DI 対応のコンポーネントの場合、使用目的は、コンポーネントがコンストラクター内で `IDataProtectionProvider` パラメーターを受け取り、コンポーネントがインスタンス化されるときに DI システムが自動的にこのサービスを提供することです。

> [!NOTE]
> 一部のアプリケーション (コンソールアプリケーションや ASP.NET 4.x アプリケーションなど) が DI に対応していない可能性があるため、ここで説明するメカニズムを使用することはできません。 これらのシナリオでは、DI を使用せずに `IDataProtection` プロバイダーのインスタンスを取得する方法の詳細については、 [Di 非対応のシナリオ](xref:security/data-protection/configuration/non-di-scenarios)に関するドキュメントを参照してください。

次の例では、3つの概念を示します。

1. [データ保護システム](xref:security/data-protection/configuration/overview)をサービスコンテナーに追加します。

2. DI を使用して `IDataProtectionProvider`のインスタンスを受信する

3. `IDataProtectionProvider` から `IDataProtector` を作成し、それを使用してデータを保護および保護解除します。

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

AspNetCore パッケージには、開発者の便宜として拡張メソッド `IServiceProvider.GetDataProtector` が含まれています。 サービスプロバイダーから `IDataProtectionProvider` を取得し、`IDataProtectionProvider.CreateProtector`を呼び出すことによって、単一の操作としてカプセル化します。 次の例では、その使用方法を示します。

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> `IDataProtectionProvider` と `IDataProtector` のインスタンスは、複数の呼び出し元に対してスレッドセーフです。 コンポーネントが `CreateProtector`の呼び出しによって `IDataProtector` への参照を取得すると、その参照を使用して `Protect` と `Unprotect`の複数の呼び出しに使用されることを意図しています。 保護されたペイロードを検証または解読できない場合、`Unprotect` を呼び出すと System.security.cryptography.cryptographicexception> がスローされます。 コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証クッキーを読み取るコンポーネントは、このエラーを処理し、要求を完全に失敗させるのではなく、cookie がまったくないかのように要求を処理することがあります。 この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。
