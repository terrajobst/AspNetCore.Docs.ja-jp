---
title: ASP.NET Core でのデータ保護 Api の概要
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用して、アプリのデータを保護および復号化する方法について説明します。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653834"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core でのデータ保護 Api の概要

<a name="security-data-protection-getting-started"></a>

データの保護は、次の手順で最も簡単に行うことができます。

1. データ保護プロバイダーからデータプロテクターを作成します。

2. 保護するデータを指定して `Protect` メソッドを呼び出します。

3. プレーンテキストに戻すデータを使用して `Unprotect` メソッドを呼び出します。

ASP.NET Core や SignalRなど、ほとんどのフレームワークとアプリモデルでは、データ保護システムが既に構成されており、依存関係の挿入によってアクセスするサービスコンテナーに追加されています。 次のサンプルでは、依存関係の挿入のためのサービスコンテナーの構成、データ保護スタックの登録、DI を使用したデータ保護プロバイダーの受信、保護機能の作成、データの復号化の保護を行う方法を示します。

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

保護機能を作成するときは、1つまたは複数の[目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)を指定する必要があります。 目的の文字列は、コンシューマー間の分離を提供します。 たとえば、目的の文字列が "green" で作成されたプロテクターは、"紫" の目的で保護機能によって提供されるデータの保護を解除することはできません。

>[!TIP]
> `IDataProtectionProvider` と `IDataProtector` のインスタンスは、複数の呼び出し元に対してスレッドセーフです。 コンポーネントが `CreateProtector`の呼び出しによって `IDataProtector` への参照を取得すると、その参照を使用して `Protect` と `Unprotect`の複数の呼び出しに使用されることを意図しています。
>
>保護されたペイロードを検証または解読できない場合、`Unprotect` を呼び出すと System.security.cryptography.cryptographicexception> がスローされます。 コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証クッキーを読み取るコンポーネントは、このエラーを処理し、要求を完全に失敗させるのではなく、cookie がまったくないかのように要求を処理することがあります。 この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。
