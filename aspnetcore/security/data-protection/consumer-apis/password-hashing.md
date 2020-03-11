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
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="43883-103">ASP.NET Core でのパスワードのハッシュ</span><span class="sxs-lookup"><span data-stu-id="43883-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="43883-104">データ保護コードベースには、暗号化キー派生関数を含むパッケージ*AspNetCore*が含まれています。</span><span class="sxs-lookup"><span data-stu-id="43883-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="43883-105">このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。</span><span class="sxs-lookup"><span data-stu-id="43883-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="43883-106">完全に独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="43883-106">It can be used completely independently.</span></span> <span data-ttu-id="43883-107">ソースは、便宜上、データ保護コードベースと共に存在します。</span><span class="sxs-lookup"><span data-stu-id="43883-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="43883-108">パッケージには現在、 [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッド `KeyDerivation.Pbkdf2` が用意されています。</span><span class="sxs-lookup"><span data-stu-id="43883-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="43883-109">この API は .NET Framework の既存の[Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="43883-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="43883-110">`KeyDerivation.Pbkdf2` メソッドでは、複数の PRFs (現在 `HMACSHA1`、`HMACSHA256`、および `HMACSHA512`) の使用がサポートされていますが、`Rfc2898DeriveBytes` の型では `HMACSHA1`のみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="43883-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="43883-111">`KeyDerivation.Pbkdf2` メソッドは、現在のオペレーティングシステムを検出し、そのルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。</span><span class="sxs-lookup"><span data-stu-id="43883-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="43883-112">(Windows 8 では、`Rfc2898DeriveBytes`のスループットが約10倍になります)。</span><span class="sxs-lookup"><span data-stu-id="43883-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="43883-113">`KeyDerivation.Pbkdf2` メソッドでは、呼び出し元がすべてのパラメーター (salt、PRF、およびイテレーション数) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43883-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="43883-114">`Rfc2898DeriveBytes` 型は、これらの既定値を提供します。</span><span class="sxs-lookup"><span data-stu-id="43883-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="43883-115">実際のユースケースについては、ASP.NET Core Id の `PasswordHasher` の種類の[ソースコード](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43883-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
