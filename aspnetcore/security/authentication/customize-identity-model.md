---
title: ASP.NET Core での Identity モデルのカスタマイズ
author: ajcvickers
description: この記事では、ASP.NET Core Identity の基になる Entity Framework Core のデータ モデルをカスタマイズする方法について説明します。
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500472"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="74186-103">ASP.NET Core での Identity モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="74186-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="74186-104">によって[Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="74186-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="74186-105">ASP.NET Core Identity は、管理および ASP.NET Core アプリでユーザー アカウントを格納するためのフレームワークを提供します。</span><span class="sxs-lookup"><span data-stu-id="74186-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="74186-106">Id が、プロジェクトに追加時に**個々 のユーザー アカウント**認証メカニズムとして選択されます。</span><span class="sxs-lookup"><span data-stu-id="74186-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="74186-107">既定では、Id はなりますの Entity Framework (EF) を使用して、コア データ モデル。</span><span class="sxs-lookup"><span data-stu-id="74186-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="74186-108">この記事では、Id モデルをカスタマイズする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="74186-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="74186-109">Identity と EF Core のマイグレーション</span><span class="sxs-lookup"><span data-stu-id="74186-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="74186-110">Id がどのように機能するかを把握するのに役立ちますが、モデルを調べる前に、 [EF コア移行](/ef/core/managing-schemas/migrations/)を作成してデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="74186-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="74186-111">最上位のレベルでは、手順は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="74186-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="74186-112">定義または更新を[コードでデータ モデル](/ef/core/modeling/)します。</span><span class="sxs-lookup"><span data-stu-id="74186-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="74186-113">このモデルに変更をデータベースに適用できる変換への移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="74186-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="74186-114">移行が正しく、開発者の意図を表すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="74186-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="74186-115">モデルと同期するデータベースの更新への移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="74186-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="74186-116">手順 1. ~ 4. をさらに、モデルを調整し、データベースの同期を維持します。</span><span class="sxs-lookup"><span data-stu-id="74186-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="74186-117">追加して、移行を適用するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="74186-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="74186-118">**パッケージ マネージャー コンソール**(PMC) ウィンドウの場合、Visual Studio を使用します。</span><span class="sxs-lookup"><span data-stu-id="74186-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="74186-119">詳細については、次を参照してください。 [EF Core の優れた PMC ツール](/ef/core/miscellaneous/cli/powershell)します。</span><span class="sxs-lookup"><span data-stu-id="74186-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="74186-120">.NET Core CLI のコマンドラインを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="74186-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="74186-121">詳細については、次を参照してください。 [EF Core .NET コマンド ライン ツール](/ef/core/miscellaneous/cli/dotnet)します。</span><span class="sxs-lookup"><span data-stu-id="74186-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="74186-122">クリックすると、**適用移行**アプリの実行時にエラー ページ ボタンをします。</span><span class="sxs-lookup"><span data-stu-id="74186-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="74186-123">ASP.NET Core では、開発時のエラー ページ ハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="74186-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="74186-124">ハンドラーは、アプリの実行時に、移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="74186-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="74186-125">通常、実稼働アプリは、移行からの SQL スクリプトを生成し、管理されたアプリとデータベースの配置の一部としてデータベースの変更をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="74186-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="74186-126">Id を使用して新しいアプリが作成されると、手順 1. と上記の 2 が完了しました既に。</span><span class="sxs-lookup"><span data-stu-id="74186-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="74186-127">つまり、初期のデータ モデルが既に存在して、最初の移行がプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="74186-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="74186-128">最初の移行は、データベースに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="74186-129">次の方法のいずれかでは、最初の移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="74186-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="74186-130">実行`Update-Database`PMC でします。</span><span class="sxs-lookup"><span data-stu-id="74186-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="74186-131">実行`dotnet ef database update`コマンド シェルでします。</span><span class="sxs-lookup"><span data-stu-id="74186-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="74186-132">をクリックして、**移行を適用**アプリの実行時にエラー ページにボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="74186-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="74186-133">モデルに変更が加えられるとは、上記の手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="74186-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="74186-134">Identity モデル</span><span class="sxs-lookup"><span data-stu-id="74186-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="74186-135">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="74186-135">Entity types</span></span>

<span data-ttu-id="74186-136">Id モデルは、次のエンティティ型で構成されます。</span><span class="sxs-lookup"><span data-stu-id="74186-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="74186-137">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="74186-137">Entity type</span></span>|<span data-ttu-id="74186-138">説明</span><span class="sxs-lookup"><span data-stu-id="74186-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="74186-139">ユーザーを表します。</span><span class="sxs-lookup"><span data-stu-id="74186-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="74186-140">ロールを表します。</span><span class="sxs-lookup"><span data-stu-id="74186-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="74186-141">ユーザーが所有するクレームを表します。</span><span class="sxs-lookup"><span data-stu-id="74186-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="74186-142">ユーザーの認証トークンを表します。</span><span class="sxs-lookup"><span data-stu-id="74186-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="74186-143">ユーザーをログインに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="74186-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="74186-144">ロール内のすべてのユーザーに付与されるクレームを表します。</span><span class="sxs-lookup"><span data-stu-id="74186-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="74186-145">ユーザーとロールを関連付ける結合エンティティを指定します。</span><span class="sxs-lookup"><span data-stu-id="74186-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="74186-146">エンティティ型のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="74186-146">Entity type relationships</span></span>

<span data-ttu-id="74186-147">[エンティティ型](#entity-types)次の方法で相互に関連します。</span><span class="sxs-lookup"><span data-stu-id="74186-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="74186-148">各`User`多く持つことができます`UserClaims`します。</span><span class="sxs-lookup"><span data-stu-id="74186-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="74186-149">各`User`多く持つことができます`UserLogins`します。</span><span class="sxs-lookup"><span data-stu-id="74186-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="74186-150">各`User`多く持つことができます`UserTokens`します。</span><span class="sxs-lookup"><span data-stu-id="74186-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="74186-151">各`Role`関連付けられた多く持つことができます`RoleClaims`します。</span><span class="sxs-lookup"><span data-stu-id="74186-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="74186-152">各`User`関連付けられた多く持つことができます`Roles`、および各`Role`さまざまな使用に関連付けることができます`Users`します。</span><span class="sxs-lookup"><span data-stu-id="74186-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="74186-153">これは、データベース内の結合テーブルを必要とする多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="74186-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="74186-154">結合テーブルがによって表される、`UserRole`エンティティ。</span><span class="sxs-lookup"><span data-stu-id="74186-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="74186-155">既定のモデルの構成</span><span class="sxs-lookup"><span data-stu-id="74186-155">Default model configuration</span></span>

<span data-ttu-id="74186-156">多くの id が定義*コンテキスト クラス*から継承した[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)を構成し、モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="74186-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="74186-157">この構成を行うを使用して、 [EF Core Code First Fluent API](/ef/core/modeling/)で、 [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)コンテキスト クラスのメソッド。</span><span class="sxs-lookup"><span data-stu-id="74186-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="74186-158">既定の構成は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="74186-158">The default configuration is:</span></span>

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

### <a name="model-generic-types"></a><span data-ttu-id="74186-159">ジェネリック型のモデル</span><span class="sxs-lookup"><span data-stu-id="74186-159">Model generic types</span></span>

<span data-ttu-id="74186-160">Identity は、既定値を定義します。[共通言語ランタイム](/dotnet/standard/glossary#clr)上のエンティティの種類ごとの (CLR) 型の一覧にします。</span><span class="sxs-lookup"><span data-stu-id="74186-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="74186-161">これらの型はすべてを先頭*Identity*:</span><span class="sxs-lookup"><span data-stu-id="74186-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="74186-162">これらの型を直接使用するのではなく アプリの種類の型の基本クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="74186-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="74186-163">`DbContext` Id によって定義されているクラスはジェネリックでは、モデルのエンティティ型の 1 つ以上の別の CLR 型を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="74186-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="74186-164">これらのジェネリック型が許可することも、`User`主キー (PK) データの種類を変更します。</span><span class="sxs-lookup"><span data-stu-id="74186-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="74186-165">ロールについては、サポートでの Id を使用する場合、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>クラスを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="74186-166">例:</span><span class="sxs-lookup"><span data-stu-id="74186-166">For example:</span></span>

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

<span data-ttu-id="74186-167">この場合、ロール (要求のみ) がない Id を使用することも、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601>クラスを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

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

## <a name="customize-the-model"></a><span data-ttu-id="74186-168">モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="74186-168">Customize the model</span></span>

<span data-ttu-id="74186-169">モデルのカスタマイズの開始点では、適切なコンテキストの型から派生します。</span><span class="sxs-lookup"><span data-stu-id="74186-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="74186-170">参照してください、[ジェネリック型をモデル化](#model-generic-types)セクション。</span><span class="sxs-lookup"><span data-stu-id="74186-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="74186-171">このコンテキスト型が慣例的に呼び出される`ApplicationDbContext`と ASP.NET Core テンプレートによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="74186-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="74186-172">コンテキストを使用して、2 つの方法でモデルを構成します。</span><span class="sxs-lookup"><span data-stu-id="74186-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="74186-173">エンティティとジェネリック型パラメーターのキーの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="74186-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="74186-174">オーバーライドする`OnModelCreating`これらの型のマッピングを変更します。</span><span class="sxs-lookup"><span data-stu-id="74186-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="74186-175">オーバーライドするときに`OnModelCreating`、`base.OnModelCreating`最初に呼び出す必要があります。 オーバーライドする側の構成を次に呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="74186-176">EF Core には、最後の 1 つの wins のポリシー構成を一般があります。</span><span class="sxs-lookup"><span data-stu-id="74186-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="74186-177">たとえば場合、`ToTable`エンティティ型のメソッドが 1 つのテーブルの名前の最初と呼ばれ、もう一度後で別のテーブルの名前を持つ 2 番目の呼び出し内のテーブル名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="74186-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="74186-178">カスタム ユーザー データ</span><span class="sxs-lookup"><span data-stu-id="74186-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="74186-179">[カスタム ユーザー データ](xref:security/authentication/add-user-data)から継承することではサポートされて`IdentityUser`します。</span><span class="sxs-lookup"><span data-stu-id="74186-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="74186-180">この型名前を指定するが通例`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="74186-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="74186-181">使用して、`ApplicationUser`に関しては、汎用引数として型。</span><span class="sxs-lookup"><span data-stu-id="74186-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

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

<span data-ttu-id="74186-182">オーバーライドする必要はありません`OnModelCreating`で、`ApplicationDbContext`クラス。</span><span class="sxs-lookup"><span data-stu-id="74186-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="74186-183">EF Core のマップ、`CustomTag`慣例プロパティ。</span><span class="sxs-lookup"><span data-stu-id="74186-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="74186-184">データベースを新たに作成する更新が必要、`CustomTag`列。</span><span class="sxs-lookup"><span data-stu-id="74186-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="74186-185">列を作成するには、移行を追加および」の説明に従って、データベースを更新[Id と EF Core の移行](#identity-and-ef-core-migrations)します。</span><span class="sxs-lookup"><span data-stu-id="74186-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="74186-186">Update *Pages/Shared/_LoginPartial.cshtml*と置換`IdentityUser`で`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="74186-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="74186-187">Update *Areas/Identity/IdentityHostingStartup.cs*または`Startup.ConfigureServices`と置換`IdentityUser`で`ApplicationUser`します。</span><span class="sxs-lookup"><span data-stu-id="74186-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="74186-188">ASP.NET Core 2.1 以降では、Identity は、Razor クラス ライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="74186-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="74186-189">詳細については、「 <xref:security/authentication/scaffold-identity> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74186-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="74186-190">したがって、上記のコードへの呼び出しが必要です。<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>します。</span><span class="sxs-lookup"><span data-stu-id="74186-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="74186-191">Id ファイルをプロジェクトに追加する Identity scaffolder を使用した場合への呼び出しを削除`AddDefaultUI`します。</span><span class="sxs-lookup"><span data-stu-id="74186-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="74186-192">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="74186-192">For more information, see:</span></span>

* [<span data-ttu-id="74186-193">Identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="74186-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="74186-194">追加、ダウンロード、および Id にカスタム ユーザー データの削除</span><span class="sxs-lookup"><span data-stu-id="74186-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="74186-195">主キーの型の変更</span><span class="sxs-lookup"><span data-stu-id="74186-195">Change the primary key type</span></span>

<span data-ttu-id="74186-196">データベースが作成された後に、PK 列のデータ型への変更は多くのデータベース システムで問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="74186-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="74186-197">通常、主キーを変更するには、削除して、テーブルを再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="74186-198">そのため、データベースが作成されたときに、最初の移行でキーの種類を指定してください。</span><span class="sxs-lookup"><span data-stu-id="74186-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="74186-199">主キーの種類を変更する次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="74186-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="74186-200">データベースが作成された場合、PK 変更する前に実行`Drop-Database`(PMC) または`dotnet ef database drop`(.NET Core CLI) を削除します。</span><span class="sxs-lookup"><span data-stu-id="74186-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="74186-201">データベースの削除を確定した後で初期の移行を削除`Remove-Migration`(PMC) または`dotnet ef migrations remove`(.NET Core CLI)。</span><span class="sxs-lookup"><span data-stu-id="74186-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="74186-202">更新プログラム、`ApplicationDbContext`クラスから派生する<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>します。</span><span class="sxs-lookup"><span data-stu-id="74186-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="74186-203">新しいキーの種類を指定`TKey`します。</span><span class="sxs-lookup"><span data-stu-id="74186-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="74186-204">たとえば、使用するため、`Guid`キーの種類。</span><span class="sxs-lookup"><span data-stu-id="74186-204">For example, to use a `Guid` key type:</span></span>

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

    <span data-ttu-id="74186-205">上記のコードでは、ジェネリック クラスで<xref:Microsoft.AspNetCore.Identity.IdentityUser%601>と<xref:Microsoft.AspNetCore.Identity.IdentityRole%601>新しいキーの種類を使用するように指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="74186-206">上記のコードでは、ジェネリック クラスで<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601>と<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601>新しいキーの種類を使用するように指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="74186-207">`Startup.ConfigureServices` ジェネリック ユーザーを使用する更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

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

4. <span data-ttu-id="74186-208">場合、カスタム`ApplicationUser`クラスが使用されているから継承するクラスを更新`IdentityUser`します。</span><span class="sxs-lookup"><span data-stu-id="74186-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="74186-209">例:</span><span class="sxs-lookup"><span data-stu-id="74186-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="74186-210">Update`ApplicationDbContext`カスタムを参照する`ApplicationUser`クラス。</span><span class="sxs-lookup"><span data-stu-id="74186-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

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

    <span data-ttu-id="74186-211">Id サービスを追加するときに、カスタムのデータベース コンテキスト クラスを登録`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="74186-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="74186-212">分析することで、プライマリ キーのデータ型が推論される、 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="74186-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="74186-213">ASP.NET Core 2.1 以降では、Identity は、Razor クラス ライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="74186-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="74186-214">詳細については、「 <xref:security/authentication/scaffold-identity> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74186-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="74186-215">したがって、上記のコードへの呼び出しが必要です。<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>します。</span><span class="sxs-lookup"><span data-stu-id="74186-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="74186-216">Id ファイルをプロジェクトに追加する Identity scaffolder を使用した場合への呼び出しを削除`AddDefaultUI`します。</span><span class="sxs-lookup"><span data-stu-id="74186-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="74186-217">分析することで、プライマリ キーのデータ型が推論される、 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="74186-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="74186-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>メソッドは、`TKey`主キーのデータ型を表す型。</span><span class="sxs-lookup"><span data-stu-id="74186-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="74186-219">場合、カスタム`ApplicationRole`クラスが使用されているから継承するクラスを更新`IdentityRole<TKey>`します。</span><span class="sxs-lookup"><span data-stu-id="74186-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="74186-220">例:</span><span class="sxs-lookup"><span data-stu-id="74186-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="74186-221">Update`ApplicationDbContext`カスタムを参照する`ApplicationRole`クラス。</span><span class="sxs-lookup"><span data-stu-id="74186-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="74186-222">たとえば、次のクラスは参照カスタム`ApplicationUser`およびカスタム`ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="74186-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="74186-223">Id サービスを追加するときに、カスタムのデータベース コンテキスト クラスを登録`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="74186-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="74186-224">分析することで、プライマリ キーのデータ型が推論される、 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="74186-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="74186-225">ASP.NET Core 2.1 以降では、Identity は、Razor クラス ライブラリとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="74186-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="74186-226">詳細については、「 <xref:security/authentication/scaffold-identity> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74186-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="74186-227">したがって、上記のコードへの呼び出しが必要です。<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>します。</span><span class="sxs-lookup"><span data-stu-id="74186-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="74186-228">Id ファイルをプロジェクトに追加する Identity scaffolder を使用した場合への呼び出しを削除`AddDefaultUI`します。</span><span class="sxs-lookup"><span data-stu-id="74186-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="74186-229">Id サービスを追加するときに、カスタムのデータベース コンテキスト クラスを登録`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="74186-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="74186-230">分析することで、プライマリ キーのデータ型が推論される、 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="74186-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="74186-231">Id サービスを追加するときに、カスタムのデータベース コンテキスト クラスを登録`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="74186-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="74186-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>メソッドは、`TKey`主キーのデータ型を表す型。</span><span class="sxs-lookup"><span data-stu-id="74186-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="74186-233">ナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="74186-233">Add navigation properties</span></span>

<span data-ttu-id="74186-234">リレーションシップのモデルの構成を変更すると、その他の変更よりも難しくことができます。</span><span class="sxs-lookup"><span data-stu-id="74186-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="74186-235">新しい追加のリレーションシップを作成するのではなく、既存のリレーションシップを置き換えるに注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="74186-236">具体的には、変更されたリレーションシップでは、既存のリレーションシップとして同じ外部キー (FK) プロパティを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="74186-237">間のリレーションシップなど`Users`と`UserClaims`、次のように、既定では。</span><span class="sxs-lookup"><span data-stu-id="74186-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

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

<span data-ttu-id="74186-238">このリレーションシップの FK として指定された、`UserClaim.UserId`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="74186-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="74186-239">`HasMany` `WithOne`はナビゲーション プロパティのないリレーションシップを作成する引数を指定しないと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="74186-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="74186-240">ナビゲーション プロパティを追加`ApplicationUser`に関連付けできる`UserClaims`ユーザーから参照できます。</span><span class="sxs-lookup"><span data-stu-id="74186-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="74186-241">`TKey`の`IdentityUserClaim<TKey>`はユーザーの PK に指定された型です。</span><span class="sxs-lookup"><span data-stu-id="74186-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="74186-242">この場合、`TKey`は`string`既定値が使用されているためです。</span><span class="sxs-lookup"><span data-stu-id="74186-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="74186-243">**いない**の PK 型、`UserClaim`エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="74186-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="74186-244">ナビゲーション プロパティが存在するようになりましたことで構成する必要があります`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="74186-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

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

<span data-ttu-id="74186-245">呼び出しで指定されたナビゲーション プロパティでのみが、以前とまったく同じ関係が構成されていることに注意してください。`HasMany`します。</span><span class="sxs-lookup"><span data-stu-id="74186-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="74186-246">ナビゲーション プロパティは、データベースではなく、EF モデルにのみ存在します。</span><span class="sxs-lookup"><span data-stu-id="74186-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="74186-247">リレーションシップの外部キーが変更されていないため、この種のモデルの変更は更新するデータベースを必要としません。</span><span class="sxs-lookup"><span data-stu-id="74186-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="74186-248">これは、変更を行った後の移行を追加することで確認できます。</span><span class="sxs-lookup"><span data-stu-id="74186-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="74186-249">`Up`と`Down`メソッドは空です。</span><span class="sxs-lookup"><span data-stu-id="74186-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="74186-250">すべてのユーザー ナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="74186-250">Add all User navigation properties</span></span>

<span data-ttu-id="74186-251">次の例では、ガイダンスとして、上のセクションを使用して、ユーザーのすべてのリレーションシップの一方向のナビゲーション プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="74186-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

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

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="74186-252">ユーザーおよびロールのナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="74186-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="74186-253">次の例では、ガイダンスとして、上のセクションを使用して、ユーザーおよびロールのすべての関係のナビゲーション プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="74186-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

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

<span data-ttu-id="74186-254">メモ:</span><span class="sxs-lookup"><span data-stu-id="74186-254">Notes:</span></span>

* <span data-ttu-id="74186-255">この例では、`UserRole`結合エンティティで、ユーザーからロールへの多対多リレーションシップを移動するが必要です。</span><span class="sxs-lookup"><span data-stu-id="74186-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="74186-256">反映するようにナビゲーション プロパティの型を変更して`ApplicationXxx`型は、の代わりに使用されているようになりました`IdentityXxx`型。</span><span class="sxs-lookup"><span data-stu-id="74186-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="74186-257">使用してください、`ApplicationXxx`ジェネリック`ApplicationContext`定義します。</span><span class="sxs-lookup"><span data-stu-id="74186-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="74186-258">すべてのナビゲーション プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="74186-258">Add all navigation properties</span></span>

<span data-ttu-id="74186-259">次の例では、ガイダンスとして、上のセクションを使用して、すべてのエンティティ型ですべての関係のナビゲーション プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="74186-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

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

### <a name="use-composite-keys"></a><span data-ttu-id="74186-260">複合キーの使用</span><span class="sxs-lookup"><span data-stu-id="74186-260">Use composite keys</span></span>

<span data-ttu-id="74186-261">前のセクションでは、Id モデルで使用されるキーの型の変更が示されています。</span><span class="sxs-lookup"><span data-stu-id="74186-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="74186-262">複合キーを使用するキーの Id モデルを変更すると、サポートされていないかお勧めします。</span><span class="sxs-lookup"><span data-stu-id="74186-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="74186-263">Id を持つ複合キーを使用するには、Identity manager のコードが、モデルと対話する方法を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74186-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="74186-264">このカスタマイズでは、このドキュメントの範囲外です。</span><span class="sxs-lookup"><span data-stu-id="74186-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="74186-265">テーブル名/列名とファセットの変更</span><span class="sxs-lookup"><span data-stu-id="74186-265">Change table/column names and facets</span></span>

<span data-ttu-id="74186-266">テーブルと列の名前を変更するには、呼び出す`base.OnModelCreating`します。</span><span class="sxs-lookup"><span data-stu-id="74186-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="74186-267">次に、既定値のいずれかを上書きする構成を追加します。</span><span class="sxs-lookup"><span data-stu-id="74186-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="74186-268">たとえば、Identity のすべてのテーブルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="74186-268">For example, to change the name of all the Identity tables:</span></span>

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

<span data-ttu-id="74186-269">これらの例では、既定の Id 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="74186-269">These examples use the default Identity types.</span></span> <span data-ttu-id="74186-270">などのアプリの種類を使用して場合`ApplicationUser`、既定値の型ではなく、その型を構成します。</span><span class="sxs-lookup"><span data-stu-id="74186-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="74186-271">次の例では、一部の列名を変更します。</span><span class="sxs-lookup"><span data-stu-id="74186-271">The following example changes some column names:</span></span>

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

<span data-ttu-id="74186-272">特定のデータベース列のいくつかの種類を構成することができます*ファセット*(たとえば、最大`string`許容長)。</span><span class="sxs-lookup"><span data-stu-id="74186-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="74186-273">次の例では、いくつかの列の最大長を設定する`string`モデル内のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="74186-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

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

### <a name="map-to-a-different-schema"></a><span data-ttu-id="74186-274">別のスキーマへのマップ</span><span class="sxs-lookup"><span data-stu-id="74186-274">Map to a different schema</span></span>

<span data-ttu-id="74186-275">スキーマは、データベース プロバイダーにわたる動作が異なることができます。</span><span class="sxs-lookup"><span data-stu-id="74186-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="74186-276">既定値は、SQL Server のすべてのテーブルを作成する、 *dbo*スキーマ。</span><span class="sxs-lookup"><span data-stu-id="74186-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="74186-277">別のスキーマ内のテーブルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="74186-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="74186-278">例えば:</span><span class="sxs-lookup"><span data-stu-id="74186-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="74186-279">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="74186-279">Lazy loading</span></span>

<span data-ttu-id="74186-280">このセクションでは、Id モデルで遅延読み込みプロキシのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="74186-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="74186-281">ナビゲーション プロパティが読み込まれている確認せずに使用することができるので、遅延読み込みの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="74186-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="74186-282">エンティティの種類にできるに適したいくつかの方法で遅延読み込み」の説明に従って、 [EF Core ドキュメント](/ef/core/querying/related-data#lazy-loading)します。</span><span class="sxs-lookup"><span data-stu-id="74186-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="74186-283">わかりやすくするために、必要があります、遅延読み込みプロキシを使用します。</span><span class="sxs-lookup"><span data-stu-id="74186-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="74186-284">インストール、 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="74186-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="74186-285">呼び出し<xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*>内[AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)します。</span><span class="sxs-lookup"><span data-stu-id="74186-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="74186-286">パブリック エンティティ型`public virtual`ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="74186-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="74186-287">次の例では、通話`UseLazyLoadingProxies`で`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="74186-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="74186-288">エンティティ型にナビゲーション プロパティを追加する方法のガイダンスについては、前の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74186-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74186-289">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="74186-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
