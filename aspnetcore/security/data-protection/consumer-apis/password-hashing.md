---
title: ASP.NET Core でのパスワードのハッシュ
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用してパスワードをハッシュする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: bd4b8fcf6a5a16a86ada97bbd3519f872d1417b7
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602456"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core でのパスワードのハッシュ

データ保護コードベースには、暗号化キー派生関数を含むパッケージ*AspNetCore*が含まれています。 このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。 完全に独立して使用できます。 ソースは、便宜上、データ保護コードベースと共に存在します。

パッケージには、現在、 `KeyDerivation.Pbkdf2` [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッドが用意されています。 この API は .NET Framework の既存の[Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。

1. この`KeyDerivation.Pbkdf2`メソッドは、複数の prfs ( `HMACSHA1`現在`HMACSHA256`、、 `HMACSHA512`および) の使用`Rfc2898DeriveBytes`をサポートし`HMACSHA1`ていますが、型はのみをサポートしています。

2. メソッド`KeyDerivation.Pbkdf2`は、現在のオペレーティングシステムを検出し、ルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。 (Windows 8 では、の`Rfc2898DeriveBytes`スループットの約10倍が提供されます)。

3. メソッド`KeyDerivation.Pbkdf2`では、呼び出し元がすべてのパラメーター (salt、PRF、およびイテレーション数) を指定する必要があります。 型`Rfc2898DeriveBytes`は、これらの既定値を提供します。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

実際のユースケースについては`PasswordHasher` 、ASP.NET Core id の種類の[ソースコード](https://github.com/aspnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)を参照してください。
