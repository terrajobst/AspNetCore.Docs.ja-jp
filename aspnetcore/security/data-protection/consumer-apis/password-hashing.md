---
title: ASP.NET Core でのパスワードのハッシュ
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用してパスワードをハッシュする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651434"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core でのパスワードのハッシュ

データ保護コードベースには、暗号化キー派生関数を含むパッケージ*AspNetCore*が含まれています。 このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。 完全に独立して使用できます。 ソースは、便宜上、データ保護コードベースと共に存在します。

パッケージには現在、 [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッド `KeyDerivation.Pbkdf2` が用意されています。 この API は .NET Framework の既存の[Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。

1. `KeyDerivation.Pbkdf2` メソッドでは、複数の PRFs (現在 `HMACSHA1`、`HMACSHA256`、および `HMACSHA512`) の使用がサポートされていますが、`Rfc2898DeriveBytes` の型では `HMACSHA1`のみがサポートされています。

2. `KeyDerivation.Pbkdf2` メソッドは、現在のオペレーティングシステムを検出し、そのルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。 (Windows 8 では、`Rfc2898DeriveBytes`のスループットが約10倍になります)。

3. `KeyDerivation.Pbkdf2` メソッドでは、呼び出し元がすべてのパラメーター (salt、PRF、およびイテレーション数) を指定する必要があります。 `Rfc2898DeriveBytes` 型は、これらの既定値を提供します。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

実際のユースケースについては、ASP.NET Core Id の `PasswordHasher` の種類の[ソースコード](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)を参照してください。
