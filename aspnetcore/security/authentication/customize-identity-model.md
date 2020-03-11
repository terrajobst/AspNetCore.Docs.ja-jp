---
title: ASP.NET Core での Identity モデルのカスタマイズ
author: ajcvickers
description: この記事では、ASP.NET Core Id の基になる Entity Framework Core データモデルをカスタマイズする方法について説明します。
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651458"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="c2d2e-103">ASP.NET Core での Identity モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="c2d2e-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="c2d2e-104">[Arthur ヴィッカース](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="c2d2e-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="c2d2e-105">ASP.NET Core Id は、ASP.NET Core アプリでユーザーアカウントを管理および格納するためのフレームワークを提供します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="c2d2e-106">認証メカニズムとして個々の**ユーザーアカウント**を選択すると、id がプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="c2d2e-107">既定では、Id によって、Entity Framework (EF) のコアデータモデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="c2d2e-108">この記事では、Id モデルをカスタマイズする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="c2d2e-109">Identity と EF Core のマイグレーション</span><span class="sxs-lookup"><span data-stu-id="c2d2e-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="c2d2e-110">モデルを調べる前に、 [EF Core の移行](/ef/core/managing-schemas/migrations/)を使用して id がどのように動作し、データベースを作成および更新するかを理解しておくと便利です。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="c2d2e-111">最上位レベルでは、プロセスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="c2d2e-112">[コードでデータモデル](/ef/core/modeling/)を定義または更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="c2d2e-113">このモデルをデータベースに適用できる変更に変換するには、移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="c2d2e-114">移行によって意図が正しく表現されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="c2d2e-115">モデルと同期するようにデータベースを更新するには、移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="c2d2e-116">手順 1. ~ 4. を繰り返して、モデルをさらに調整し、データベースを同期させます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="c2d2e-117">移行を追加して適用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="c2d2e-118">Visual Studio を使用している場合は、[**パッケージマネージャーコンソール**(PMC)] ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="c2d2e-119">詳細については、「 [EF CORE PMC ツール](/ef/core/miscellaneous/cli/powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="c2d2e-120">コマンドラインを使用している場合は、.NET Core CLI ます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="c2d2e-121">詳細については、「 [EF Core .net コマンドラインツール](/ef/core/miscellaneous/cli/dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="c2d2e-122">アプリの実行時に、エラーページの **[移行の適用]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="c2d2e-123">ASP.NET Core には、開発時エラーページハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="c2d2e-124">ハンドラーは、アプリの実行時に移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="c2d2e-125">実稼働アプリでは、通常、移行から SQL スクリプトを生成し、制御されたアプリとデータベースの配置の一部としてデータベースの変更をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="c2d2e-126">Id を使用する新しいアプリが作成されると、上記の手順 1. と 2. は既に完了しています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="c2d2e-127">つまり、初期データモデルが既に存在し、初期移行がプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="c2d2e-128">初期移行は、引き続きデータベースに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="c2d2e-129">最初の移行は、次のいずれかの方法を使用して適用できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="c2d2e-130">PMC で `Update-Database` を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="c2d2e-131">コマンドシェルで `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="c2d2e-132">アプリの実行時に、エラーページの **[移行の適用]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="c2d2e-133">モデルが変更されたときに、上記の手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="c2d2e-134">Identity モデル</span><span class="sxs-lookup"><span data-stu-id="c2d2e-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="c2d2e-135">エンティティの種類</span><span class="sxs-lookup"><span data-stu-id="c2d2e-135">Entity types</span></span>

<span data-ttu-id="c2d2e-136">Id モデルは、次のエンティティ型で構成されています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="c2d2e-137">エンティティの種類</span><span class="sxs-lookup"><span data-stu-id="c2d2e-137">Entity type</span></span>|<span data-ttu-id="c2d2e-138">説明</span><span class="sxs-lookup"><span data-stu-id="c2d2e-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="c2d2e-139">ユーザーを表します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="c2d2e-140">ロールを表します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="c2d2e-141">ユーザーが所有するクレームを表します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="c2d2e-142">ユーザーの認証トークンを表します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="c2d2e-143">ユーザーをログインに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="c2d2e-144">ロール内のすべてのユーザーに付与されるクレームを表します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="c2d2e-145">ユーザーとロールを関連付ける結合エンティティ。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="c2d2e-146">エンティティ型のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="c2d2e-146">Entity type relationships</span></span>

<span data-ttu-id="c2d2e-147">[エンティティ型](#entity-types)は、次の方法で相互に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="c2d2e-148">各 `User` には、多くの `UserClaims`を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="c2d2e-149">各 `User` には、多くの `UserLogins`を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="c2d2e-150">各 `User` には、多くの `UserTokens`を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="c2d2e-151">各 `Role` には、多数の `RoleClaims`を関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="c2d2e-152">各 `User` には多数の `Roles`を関連付けることができ、各 `Role` は多くの `Users`に関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="c2d2e-153">これは、データベース内の結合テーブルを必要とする多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="c2d2e-154">結合テーブルは、`UserRole` エンティティによって表されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="c2d2e-155">既定のモデル構成</span><span class="sxs-lookup"><span data-stu-id="c2d2e-155">Default model configuration</span></span>

<span data-ttu-id="c2d2e-156">Identity は、モデルを構成して使用するために[Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)から継承する多くの*コンテキストクラス*を定義します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="c2d2e-157">この構成は、コンテキストクラスの[Onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)メソッドで[EF CORE Code First Fluent API](/ef/core/modeling/)を使用して行います。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="c2d2e-158">既定の構成は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="c2d2e-159">モデルのジェネリック型</span><span class="sxs-lookup"><span data-stu-id="c2d2e-159">Model generic types</span></span>

<span data-ttu-id="c2d2e-160">Id は、上記の各エンティティ型に対して、既定の[共通言語ランタイム](/dotnet/standard/glossary#clr)(CLR) 型を定義します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="c2d2e-161">これらの型には、すべて*id*が付加されています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="c2d2e-162">これらの型を直接使用するのではなく、アプリケーション独自の型の基底クラスとして型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="c2d2e-163">Id で定義される `DbContext` クラスはジェネリックであり、さまざまな CLR 型をモデルの1つ以上のエンティティ型に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="c2d2e-164">また、これらのジェネリック型を使用して、`User` 主キー (PK) のデータ型を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="c2d2e-165">ロールのサポートで Id を使用する場合は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> クラスを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="c2d2e-166">例 :</span><span class="sxs-lookup"><span data-stu-id="c2d2e-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="c2d2e-167">ロールのない Id (要求のみ) を使用することもできます。その場合は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> クラスを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="c2d2e-168">モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="c2d2e-168">Customize the model</span></span>

<span data-ttu-id="c2d2e-169">モデルのカスタマイズの開始点は、適切なコンテキスト型から派生することです。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="c2d2e-170">「[モデルのジェネリック型](#model-generic-types)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="c2d2e-171">このコンテキストの種類は `ApplicationDbContext` と呼ばれ、ASP.NET Core テンプレートによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="c2d2e-172">このコンテキストは、次の2つの方法でモデルを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="c2d2e-173">ジェネリック型パラメーターのエンティティ型とキー型を提供します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="c2d2e-174">これらの型のマッピングを変更するために `OnModelCreating` をオーバーライドしています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="c2d2e-175">`OnModelCreating`をオーバーライドする場合は、最初に `base.OnModelCreating` を呼び出す必要があります。次に、オーバーライドする構成を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="c2d2e-176">EF Core は、一般に、構成のための最後の1つの wins ポリシーを持っています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="c2d2e-177">たとえば、エンティティ型の `ToTable` メソッドが最初に1つのテーブル名で呼び出され、次に別のテーブル名を使用している場合は、2番目の呼び出しのテーブル名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="c2d2e-178">カスタムユーザーデータ</span><span class="sxs-lookup"><span data-stu-id="c2d2e-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="c2d2e-179">[カスタムユーザーデータ](xref:security/authentication/add-user-data)は、`IdentityUser`から継承することによってサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="c2d2e-180">この型には、通常、`ApplicationUser`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="c2d2e-181">`ApplicationUser` 型をコンテキストの汎用引数として使用します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="c2d2e-182">`ApplicationDbContext` クラスの `OnModelCreating` をオーバーライドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="c2d2e-183">EF Core は、`CustomTag` プロパティを慣例に従ってマップします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="c2d2e-184">ただし、新しい `CustomTag` 列を作成するには、データベースを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="c2d2e-185">列を作成するには、移行を追加し、「 [id と EF Core の移行](#identity-and-ef-core-migrations)」の説明に従ってデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="c2d2e-186">*Pages/Shared/_LoginPartial*を更新し、`IdentityUser` を `ApplicationUser`に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="c2d2e-187">*区分/id/IdentityHostingStartup*または `Startup.ConfigureServices` を更新し、`IdentityUser` を `ApplicationUser`に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="c2d2e-188">ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="c2d2e-189">詳細については、<xref:security/authentication/scaffold-identity> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="c2d2e-190">そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="c2d2e-191">Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="c2d2e-192">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-192">For more information, see:</span></span>

* [<span data-ttu-id="c2d2e-193">Identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c2d2e-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="c2d2e-194">カスタムユーザーデータを Id に追加、ダウンロード、および削除する</span><span class="sxs-lookup"><span data-stu-id="c2d2e-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="c2d2e-195">主キーの型の変更</span><span class="sxs-lookup"><span data-stu-id="c2d2e-195">Change the primary key type</span></span>

<span data-ttu-id="c2d2e-196">データベースを作成した後に PK 列のデータ型を変更すると、多くのデータベースシステムで問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="c2d2e-197">PK を変更するには、通常、テーブルを削除してから再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="c2d2e-198">そのため、データベースの作成時に、初期移行でキーの種類を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="c2d2e-199">PK の種類を変更するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="c2d2e-200">PK の変更前にデータベースを作成した場合は、`Drop-Database` (PMC) または `dotnet ef database drop` (.NET Core CLI) を実行して削除します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="c2d2e-201">データベースの削除を確認した後、`Remove-Migration` (PMC) または `dotnet ef migrations remove` (.NET Core CLI) を使用して最初の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="c2d2e-202"><xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>から派生するように `ApplicationDbContext` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="c2d2e-203">`TKey`の新しいキーの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="c2d2e-204">たとえば、`Guid` キーの種類を使用するには、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="c2d2e-205">前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> と <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> を指定して、新しいキーの型を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="c2d2e-206">前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> と <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> を指定して、新しいキーの型を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="c2d2e-207">汎用ユーザーを使用するには `Startup.ConfigureServices` を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="c2d2e-208">カスタム `ApplicationUser` クラスが使用されている場合は、`IdentityUser`から継承するようにクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="c2d2e-209">例 :</span><span class="sxs-lookup"><span data-stu-id="c2d2e-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="c2d2e-210">カスタム `ApplicationUser` クラスを参照するように `ApplicationDbContext` を更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="c2d2e-211">`Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="c2d2e-212">主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="c2d2e-213">ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="c2d2e-214">詳細については、<xref:security/authentication/scaffold-identity> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="c2d2e-215">そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="c2d2e-216">Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="c2d2e-217">主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="c2d2e-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> メソッドは、主キーのデータ型を示す `TKey` 型を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="c2d2e-219">カスタム `ApplicationRole` クラスが使用されている場合は、`IdentityRole<TKey>`から継承するようにクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="c2d2e-220">例 :</span><span class="sxs-lookup"><span data-stu-id="c2d2e-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="c2d2e-221">カスタム `ApplicationRole` クラスを参照するように `ApplicationDbContext` を更新します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="c2d2e-222">たとえば、次のクラスは、カスタム `ApplicationUser` とカスタム `ApplicationRole`を参照します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="c2d2e-223">`Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="c2d2e-224">主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="c2d2e-225">ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="c2d2e-226">詳細については、<xref:security/authentication/scaffold-identity> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="c2d2e-227">そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="c2d2e-228">Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="c2d2e-229">`Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="c2d2e-230">主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="c2d2e-231">`Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="c2d2e-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> メソッドは、主キーのデータ型を示す `TKey` 型を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="c2d2e-233">ナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c2d2e-233">Add navigation properties</span></span>

<span data-ttu-id="c2d2e-234">リレーションシップのモデル構成の変更は、他の変更を行うよりも困難になることがあります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="c2d2e-235">新しい追加のリレーションシップを作成するのではなく、既存のリレーションシップを置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="c2d2e-236">特に、変更されたリレーションシップでは、既存のリレーションシップと同じ外部キー (FK) プロパティを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="c2d2e-237">たとえば、`Users` と `UserClaims` 間のリレーションシップは、既定では次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="c2d2e-238">このリレーションシップの FK は、`UserClaim.UserId` プロパティとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="c2d2e-239">`HasMany` と `WithOne` は、ナビゲーションプロパティを使用せずにリレーションシップを作成するための引数なしで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="c2d2e-240">関連する `UserClaims` をユーザーから参照できるようにするナビゲーションプロパティを `ApplicationUser` に追加します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="c2d2e-241">`IdentityUserClaim<TKey>` の `TKey` は、ユーザーの PK に対して指定された型です。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="c2d2e-242">この場合、既定値が使用されているため、`TKey` が `string` ます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="c2d2e-243">`UserClaim` エンティティ型の PK 型では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="c2d2e-244">ナビゲーションプロパティが存在するので、`OnModelCreating`で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="c2d2e-245">リレーションシップが以前とまったく同じように構成されていることに注意してください。 `HasMany`の呼び出しで指定されたナビゲーションプロパティを使用した場合のみです。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="c2d2e-246">ナビゲーションプロパティは、データベースではなく EF モデルにのみ存在します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="c2d2e-247">リレーションシップの FK は変更されていないため、この種のモデルの変更では、データベースを更新する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="c2d2e-248">これは、変更を行った後に移行を追加することによって確認できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="c2d2e-249">`Up` メソッドと `Down` メソッドは空です。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="c2d2e-250">すべてのユーザー ナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c2d2e-250">Add all User navigation properties</span></span>

<span data-ttu-id="c2d2e-251">次の例では、上のセクションをガイダンスとして使用して、ユーザーのすべてのリレーションシップに対して一方向のナビゲーションプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="c2d2e-252">ユーザーおよびロールのナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c2d2e-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="c2d2e-253">次の例では、上のセクションをガイダンスとして使用して、ユーザーとロールのすべてのリレーションシップに対してナビゲーションプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="c2d2e-254">注:</span><span class="sxs-lookup"><span data-stu-id="c2d2e-254">Notes:</span></span>

* <span data-ttu-id="c2d2e-255">この例には、ユーザーからロールへの多対多リレーションシップを移動するために必要な `UserRole` join エンティティも含まれています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="c2d2e-256">`IdentityXxx` 型の代わりに `ApplicationXxx` 型が現在使用されていることを反映するように、ナビゲーションプロパティの型を変更することを忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="c2d2e-257">ジェネリック `ApplicationContext` 定義の `ApplicationXxx` を使用することを忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="c2d2e-258">すべてのナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c2d2e-258">Add all navigation properties</span></span>

<span data-ttu-id="c2d2e-259">次の例では、上のセクションをガイダンスとして使用して、すべてのエンティティ型のすべてのリレーションシップに対してナビゲーションプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="c2d2e-260">複合キーの使用</span><span class="sxs-lookup"><span data-stu-id="c2d2e-260">Use composite keys</span></span>

<span data-ttu-id="c2d2e-261">前のセクションでは、Id モデルで使用されるキーの種類の変更について説明しています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="c2d2e-262">複合キーを使用するように Id キーモデルを変更することはサポートされていないか、推奨されません。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="c2d2e-263">Id で複合キーを使用するには、Identity manager コードがモデルとどのように連携するかを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="c2d2e-264">このカスタマイズについては、このドキュメントでは説明しません。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="c2d2e-265">テーブル名/列名とファセットの変更</span><span class="sxs-lookup"><span data-stu-id="c2d2e-265">Change table/column names and facets</span></span>

<span data-ttu-id="c2d2e-266">テーブルと列の名前を変更するには、`base.OnModelCreating`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="c2d2e-267">次に、構成を追加して、既定値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="c2d2e-268">たとえば、すべての Id テーブルの名前を変更するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="c2d2e-269">これらの例では、既定の Id の種類を使用します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-269">These examples use the default Identity types.</span></span> <span data-ttu-id="c2d2e-270">`ApplicationUser`などのアプリの種類を使用する場合は、既定の種類の代わりにその種類を構成します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="c2d2e-271">次の例では、いくつかの列名を変更します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="c2d2e-272">一部の種類のデータベース列は、特定の*ファセット*を使用して構成できます (たとえば、許容される最大 `string` 長さ)。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="c2d2e-273">次の例では、モデル内のいくつかの `string` プロパティの列の最大長を設定します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="c2d2e-274">別のスキーマへのマップ</span><span class="sxs-lookup"><span data-stu-id="c2d2e-274">Map to a different schema</span></span>

<span data-ttu-id="c2d2e-275">スキーマは、データベースプロバイダーによって動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="c2d2e-276">SQL Server の場合、既定では*dbo*スキーマのすべてのテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="c2d2e-277">テーブルは、別のスキーマで作成できます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="c2d2e-278">例 :</span><span class="sxs-lookup"><span data-stu-id="c2d2e-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="c2d2e-279">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="c2d2e-279">Lazy loading</span></span>

<span data-ttu-id="c2d2e-280">このセクションでは、Id モデルでの遅延読み込みプロキシのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="c2d2e-281">遅延読み込みは、ナビゲーションプロパティが読み込まれていることを確認せずに使用できるため便利です。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="c2d2e-282">エンティティ型は、 [EF Core のドキュメント](/ef/core/querying/related-data#lazy-loading)で説明されているように、いくつかの方法で遅延読み込みに適したものにすることができます。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="c2d2e-283">わかりやすくするために、レイジー読み込みプロキシを使用します。これには次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="c2d2e-284">[Microsoft EntityFrameworkCore プロキシ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージのインストール。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="c2d2e-285">[Adddbcontext\<tcontext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)内 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="c2d2e-286">`public virtual` ナビゲーションプロパティを持つパブリックエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="c2d2e-287">次の例は `Startup.ConfigureServices`で `UseLazyLoadingProxies` を呼び出す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="c2d2e-288">エンティティ型へのナビゲーションプロパティの追加に関するガイダンスについては、前の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2d2e-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2d2e-289">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c2d2e-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
