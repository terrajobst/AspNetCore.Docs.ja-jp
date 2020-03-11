---
title: ASP.NET Core Id の構成
author: AdrienTorris
description: ASP.NET Core Id の既定値について理解し、カスタム値を使用するように Id プロパティを構成する方法について説明します。
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652694"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="cc13d-103">ASP.NET Core Id の構成</span><span class="sxs-lookup"><span data-stu-id="cc13d-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="cc13d-104">ASP.NET Core Id では、パスワードポリシー、ロックアウト、cookie の構成などの設定に既定値を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="cc13d-105">これらの設定は、`Startup` クラスでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="cc13d-106">Id オプション</span><span class="sxs-lookup"><span data-stu-id="cc13d-106">Identity options</span></span>

<span data-ttu-id="cc13d-107">Id システムを構成するために使用できるオプションを[指定します](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)。</span><span class="sxs-lookup"><span data-stu-id="cc13d-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="cc13d-108">`IdentityOptions` は `AddIdentity` または `AddDefaultIdentity`を呼び出し**た後**に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="cc13d-109">要求 Id</span><span class="sxs-lookup"><span data-stu-id="cc13d-109">Claims Identity</span></span>

<span data-ttu-id="cc13d-110">[ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)は、次の表に示すプロパティを使用して[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions)を指定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="cc13d-111">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-111">Property</span></span> | <span data-ttu-id="cc13d-112">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-112">Description</span></span> | <span data-ttu-id="cc13d-113">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="cc13d-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="cc13d-115">ロール要求に使用されるクレームの種類を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="cc13d-116">ClaimTypes。 Role</span><span class="sxs-lookup"><span data-stu-id="cc13d-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="cc13d-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="cc13d-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="cc13d-118">セキュリティスタンプ要求に使用される要求の種類を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="cc13d-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="cc13d-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="cc13d-120">ユーザー識別子要求に使用されるクレームの種類を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="cc13d-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="cc13d-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="cc13d-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="cc13d-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="cc13d-123">ユーザー名要求に使用される要求の種類を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="cc13d-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="cc13d-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="cc13d-125">アウト</span><span class="sxs-lookup"><span data-stu-id="cc13d-125">Lockout</span></span>

<span data-ttu-id="cc13d-126">ロックアウトは、 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)メソッドで設定されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="cc13d-127">上記のコードは、`Login` Id テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="cc13d-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="cc13d-128">ロックアウトオプションは `StartUp.ConfigureServices`で設定されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="cc13d-129">上記のコードでは、 [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) [オプション](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)を既定値に設定しています。</span><span class="sxs-lookup"><span data-stu-id="cc13d-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="cc13d-130">成功した認証は失敗したアクセス試行数がリセットされ、時計をリセットします。</span><span class="sxs-lookup"><span data-stu-id="cc13d-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="cc13d-131">[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)は、テーブルに示されているプロパティを使用して、そのプロパティを指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)</span><span class="sxs-lookup"><span data-stu-id="cc13d-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="cc13d-132">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-132">Property</span></span> | <span data-ttu-id="cc13d-133">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-133">Description</span></span> | <span data-ttu-id="cc13d-134">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="cc13d-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="cc13d-136">新しいユーザーをロックアウトできるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="cc13d-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="cc13d-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="cc13d-138">ロックアウトが発生したときにユーザーがロックアウトされる時間。</span><span class="sxs-lookup"><span data-stu-id="cc13d-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="cc13d-139">5 分</span><span class="sxs-lookup"><span data-stu-id="cc13d-139">5 minutes</span></span> |
| [<span data-ttu-id="cc13d-140">Max失敗した Accessattempts</span><span class="sxs-lookup"><span data-stu-id="cc13d-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="cc13d-141">ロックアウトが有効になっている場合に、ユーザーがロックアウトされるまでのアクセス試行の失敗回数。</span><span class="sxs-lookup"><span data-stu-id="cc13d-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="cc13d-142">5</span><span class="sxs-lookup"><span data-stu-id="cc13d-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="cc13d-143">Password</span><span class="sxs-lookup"><span data-stu-id="cc13d-143">Password</span></span>

<span data-ttu-id="cc13d-144">既定では、Id を使用するには、パスワードに大文字と小文字、数字、英数字以外の文字を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="cc13d-145">パスワードの長さは6文字以上である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="cc13d-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)は `Startup.ConfigureServices`で設定できます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="cc13d-147">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)は、テーブルに表示されるプロパティを使用して指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)</span><span class="sxs-lookup"><span data-stu-id="cc13d-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="cc13d-148">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-148">Property</span></span> | <span data-ttu-id="cc13d-149">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-149">Description</span></span> | <span data-ttu-id="cc13d-150">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="cc13d-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="cc13d-152">では、パスワードに0-9 を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="cc13d-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="cc13d-154">パスワードの最小長。</span><span class="sxs-lookup"><span data-stu-id="cc13d-154">The minimum length of the password.</span></span> | <span data-ttu-id="cc13d-155">6</span><span class="sxs-lookup"><span data-stu-id="cc13d-155">6</span></span> |
| [<span data-ttu-id="cc13d-156">RequireLowercase 文字</span><span class="sxs-lookup"><span data-stu-id="cc13d-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="cc13d-157">パスワードに小文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="cc13d-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="cc13d-159">パスワードには英数字以外の文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="cc13d-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="cc13d-161">ASP.NET Core 2.0 以降にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="cc13d-162">パスワードに含まれる個別の文字数が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="cc13d-163">1</span><span class="sxs-lookup"><span data-stu-id="cc13d-163">1</span></span> |
| [<span data-ttu-id="cc13d-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="cc13d-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="cc13d-165">パスワードには大文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="cc13d-166">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-166">Property</span></span> | <span data-ttu-id="cc13d-167">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-167">Description</span></span> | <span data-ttu-id="cc13d-168">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="cc13d-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="cc13d-170">では、パスワードに0-9 を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="cc13d-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="cc13d-172">パスワードの最小長。</span><span class="sxs-lookup"><span data-stu-id="cc13d-172">The minimum length of the password.</span></span> | <span data-ttu-id="cc13d-173">6</span><span class="sxs-lookup"><span data-stu-id="cc13d-173">6</span></span> |
| [<span data-ttu-id="cc13d-174">RequireLowercase 文字</span><span class="sxs-lookup"><span data-stu-id="cc13d-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="cc13d-175">パスワードに小文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="cc13d-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="cc13d-177">パスワードには英数字以外の文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="cc13d-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="cc13d-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="cc13d-179">パスワードには大文字が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="cc13d-180">サインイン</span><span class="sxs-lookup"><span data-stu-id="cc13d-180">Sign-in</span></span>

<span data-ttu-id="cc13d-181">次のコードは、`SignIn` 設定 (既定値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="cc13d-182">[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions)は、表に示されているプロパティを使用して指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)</span><span class="sxs-lookup"><span data-stu-id="cc13d-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="cc13d-183">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-183">Property</span></span> | <span data-ttu-id="cc13d-184">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-184">Description</span></span> | <span data-ttu-id="cc13d-185">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="cc13d-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="cc13d-187">サインインするには、確認済みの電子メールが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="cc13d-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="cc13d-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="cc13d-189">サインインするには、確認済みの電話番号が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="cc13d-190">トークン</span><span class="sxs-lookup"><span data-stu-id="cc13d-190">Tokens</span></span>

<span data-ttu-id="cc13d-191">[トークンオプション。トークン](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)は、テーブルに示されているプロパティを使用して[tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions)を指定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="cc13d-192">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="cc13d-193">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="cc13d-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="cc13d-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="cc13d-195">認証子を使用して2要素サインインを検証するために使用される `AuthenticatorTokenProvider` を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="cc13d-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="cc13d-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="cc13d-197">電子メール変更の確認メールで使用されるトークンを生成するために使用される `ChangeEmailTokenProvider` を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="cc13d-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="cc13d-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="cc13d-199">電話番号を変更するときに使用するトークンを生成するために使用される `ChangePhoneNumberTokenProvider` を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="cc13d-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="cc13d-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="cc13d-201">アカウントの確認メールで使用されるトークンを生成するために使用されるトークンプロバイダーを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="cc13d-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="cc13d-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="cc13d-203">パスワードリセット電子メールで使用されるトークンを生成するために使用される[IUserTwoFactorTokenProvider\<tuser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1)を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="cc13d-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="cc13d-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="cc13d-205">プロバイダーの名前として使用されるキーを使用して[ユーザートークンプロバイダー](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)を構築するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="cc13d-206">User</span><span class="sxs-lookup"><span data-stu-id="cc13d-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="cc13d-207">[ユーザー設定。ユーザー](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)は、テーブルに示されているプロパティを使用して[useroptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions)を指定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="cc13d-208">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cc13d-208">Property</span></span> | <span data-ttu-id="cc13d-209">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-209">Description</span></span> | <span data-ttu-id="cc13d-210">既定値</span><span class="sxs-lookup"><span data-stu-id="cc13d-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="cc13d-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="cc13d-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="cc13d-212">ユーザー名に使用できる文字。</span><span class="sxs-lookup"><span data-stu-id="cc13d-212">Allowed characters in the username.</span></span> | <span data-ttu-id="cc13d-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="cc13d-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="cc13d-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="cc13d-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="cc13d-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="cc13d-215">0123456789</span></span><br><span data-ttu-id="cc13d-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="cc13d-216">-.\_@+</span></span> |
| [<span data-ttu-id="cc13d-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="cc13d-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="cc13d-218">各ユーザーが一意の電子メールを持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="cc13d-219">Cookie の設定</span><span class="sxs-lookup"><span data-stu-id="cc13d-219">Cookie settings</span></span>

<span data-ttu-id="cc13d-220">`Startup.ConfigureServices`でアプリの cookie を構成します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc13d-221">`AddIdentity` または `AddDefaultIdentity`を呼び出し**た後**に[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__)を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13d-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="cc13d-222">詳細については、「 [Cookieauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc13d-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="cc13d-223">パスワードの Hasher オプション</span><span class="sxs-lookup"><span data-stu-id="cc13d-223">Password Hasher options</span></span>

<span data-ttu-id="cc13d-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> は、パスワードハッシュのオプションを取得して設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="cc13d-225">オプション</span><span class="sxs-lookup"><span data-stu-id="cc13d-225">Option</span></span> | <span data-ttu-id="cc13d-226">説明</span><span class="sxs-lookup"><span data-stu-id="cc13d-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="cc13d-227">新しいパスワードをハッシュするときに使用する互換性モード。</span><span class="sxs-lookup"><span data-stu-id="cc13d-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="cc13d-228">既定値は <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="cc13d-229">*形式マーカー*と呼ばれるハッシュされたパスワードの最初のバイトは、パスワードのハッシュに使用されるハッシュアルゴリズムのバージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="cc13d-230">ハッシュに対してパスワードを確認する場合、<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 方法では、最初のバイトに基づいて正しいアルゴリズムが選択されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="cc13d-231">クライアントは、パスワードのハッシュに使用されたアルゴリズムのバージョンに関係なく認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="cc13d-232">互換性モードを設定すると、*新しいパスワード*のハッシュに影響します。</span><span class="sxs-lookup"><span data-stu-id="cc13d-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="cc13d-233">PBKDF2 を使用してパスワードをハッシュするときに使用されるイテレーションの数。</span><span class="sxs-lookup"><span data-stu-id="cc13d-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="cc13d-234">この値は、<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> が <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>に設定されている場合にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc13d-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="cc13d-235">値は正の整数である必要があり、既定値は `10000`です。</span><span class="sxs-lookup"><span data-stu-id="cc13d-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="cc13d-236">次の例では、<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> が `Startup.ConfigureServices`で `12000` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="cc13d-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
