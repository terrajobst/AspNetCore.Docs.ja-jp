---
title: ASP.NET Core の短期データ保護プロバイダー
author: rick-anderson
description: ASP.NET Core 短期データ保護プロバイダーの実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654014"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>ASP.NET Core の短期データ保護プロバイダー

<a name="data-protection-implementation-key-storage-ephemeral"></a>

アプリケーションに使い捨て `IDataProtectionProvider`が必要なシナリオがあります。 たとえば、開発者が1回限りのコンソールアプリケーションを試している場合や、アプリケーション自体が一時的なものである場合があります (スクリプトまたは単体テストプロジェクト)。 これらのシナリオをサポートするために、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)パッケージには `EphemeralDataProtectionProvider`型が含まれています。 この型は、キーリポジトリがメモリ内にのみ保持され、バッキングストアに書き出されない `IDataProtectionProvider` の基本的な実装を提供します。

`EphemeralDataProtectionProvider` の各インスタンスは、独自の一意のマスターキーを使用します。 したがって、`EphemeralDataProtectionProvider` をルートとする `IDataProtector` が保護されたペイロードを生成する場合、そのペイロードは、同じ `EphemeralDataProtectionProvider` インスタンスをルートとする同等の `IDataProtector` (同じ[目的](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes)のチェーンが指定されている) によってのみ保護を解除できます。

次のサンプルでは、`EphemeralDataProtectionProvider` をインスタンス化し、それを使用してデータを保護および保護解除する方法を示します。

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
