---
title: ASP.NET Core MVC のキャッシュ タグ ヘルパー
author: pkellner
description: キャッシュ タグ ヘルパーを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: db9e1a968588410f11e5f137dfdd4542df505ebc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653306"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="3956b-103">ASP.NET Core MVC のキャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="3956b-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="3956b-104">著者: [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="3956b-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="3956b-105">キャッシュ タグ ヘルパーは、ASP.NET Core アプリの内容を内部 ASP.NET Core キャッシュ プロバイダーにキャッシュすることによって、アプリのパフォーマンスを改善する機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="3956b-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="3956b-106">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3956b-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="3956b-107">次の Razor マークアップは、現在の日付をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="3956b-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="3956b-108">タグ ヘルパーを含むページに対する最初の要求で、現在の日付が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="3956b-109">キャッシュの有効期限 (既定値は 20 分) が切れるか、キャッシュされた日付がキャッシュから削除されるまで、それ以降の要求ではキャッシュされた値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="3956b-110">キャッシュ タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="3956b-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="3956b-111">enabled</span><span class="sxs-lookup"><span data-stu-id="3956b-111">enabled</span></span>

| <span data-ttu-id="3956b-112">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-112">Attribute Type</span></span>  | <span data-ttu-id="3956b-113">例</span><span class="sxs-lookup"><span data-stu-id="3956b-113">Examples</span></span>        | <span data-ttu-id="3956b-114">既定値</span><span class="sxs-lookup"><span data-stu-id="3956b-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="3956b-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="3956b-115">Boolean</span></span>         | <span data-ttu-id="3956b-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="3956b-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="3956b-117">`enabled` によってキャッシュ タグ ヘルパーで囲まれた内容をキャッシュするかどうかが決定されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="3956b-118">既定では、 `true`です。</span><span class="sxs-lookup"><span data-stu-id="3956b-118">The default is `true`.</span></span> <span data-ttu-id="3956b-119">`false` に設定すると、作成された出力はキャッシュ**されません**。</span><span class="sxs-lookup"><span data-stu-id="3956b-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="3956b-120">例:</span><span class="sxs-lookup"><span data-stu-id="3956b-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="3956b-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="3956b-121">expires-on</span></span>

| <span data-ttu-id="3956b-122">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-122">Attribute Type</span></span>   | <span data-ttu-id="3956b-123">例</span><span class="sxs-lookup"><span data-stu-id="3956b-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="3956b-124">`expires-on` によって、特定の日時でキャッシュされた項目の有効期限を設定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="3956b-125">次の例では、キャッシュ タグ ヘルパーの内容を 2025 年 1 月 29 日午後 5 時 2 分までキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="3956b-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="3956b-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="3956b-126">expires-after</span></span>

| <span data-ttu-id="3956b-127">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-127">Attribute Type</span></span> | <span data-ttu-id="3956b-128">例</span><span class="sxs-lookup"><span data-stu-id="3956b-128">Example</span></span>                      | <span data-ttu-id="3956b-129">既定値</span><span class="sxs-lookup"><span data-stu-id="3956b-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="3956b-130">20 分</span><span class="sxs-lookup"><span data-stu-id="3956b-130">20 minutes</span></span> |

<span data-ttu-id="3956b-131">`expires-after` では、最初の要求があってから内容をキャッシュしている時間の長さが設定されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="3956b-132">例:</span><span class="sxs-lookup"><span data-stu-id="3956b-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3956b-133">Razor ビュー エンジンでは、`expires-after` の規定値が 20 分に設定されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="3956b-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="3956b-134">expires-sliding</span></span>

| <span data-ttu-id="3956b-135">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-135">Attribute Type</span></span> | <span data-ttu-id="3956b-136">例</span><span class="sxs-lookup"><span data-stu-id="3956b-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="3956b-137">ここで設定した時間だけキャッシュ エントリの値に対するアクセスがなかった場合、キャッシュ エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="3956b-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="3956b-138">例:</span><span class="sxs-lookup"><span data-stu-id="3956b-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="3956b-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="3956b-139">vary-by-header</span></span>

| <span data-ttu-id="3956b-140">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-140">Attribute Type</span></span> | <span data-ttu-id="3956b-141">例</span><span class="sxs-lookup"><span data-stu-id="3956b-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="3956b-142">String</span><span class="sxs-lookup"><span data-stu-id="3956b-142">String</span></span>         | <span data-ttu-id="3956b-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="3956b-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="3956b-144">`vary-by-header` には、変化したときにキャッシュの更新をトリガーする、コンマで区切ったヘッダー値のリストを指定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="3956b-145">次の例では、ヘッダー値 `User-Agent` を監視します。</span><span class="sxs-lookup"><span data-stu-id="3956b-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="3956b-146">この例は、Web サーバーに提示されるすべての異なる `User-Agent` の内容をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="3956b-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="3956b-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="3956b-147">vary-by-query</span></span>

| <span data-ttu-id="3956b-148">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-148">Attribute Type</span></span> | <span data-ttu-id="3956b-149">例</span><span class="sxs-lookup"><span data-stu-id="3956b-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="3956b-150">String</span><span class="sxs-lookup"><span data-stu-id="3956b-150">String</span></span>         | <span data-ttu-id="3956b-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="3956b-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="3956b-152">`vary-by-query` には、リストのいずれかのキーの値が変化したときにキャッシュの更新をトリガーする、クエリ文字列 (<xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*>) の <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> のコンマ区切り値リストを指定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="3956b-153">次の例では、`Make` と `Model` の値を監視します。</span><span class="sxs-lookup"><span data-stu-id="3956b-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="3956b-154">この例は、Web サーバーに提示されるすべての異なる `Make` と `Model` の内容をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="3956b-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="3956b-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="3956b-155">vary-by-route</span></span>

| <span data-ttu-id="3956b-156">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-156">Attribute Type</span></span> | <span data-ttu-id="3956b-157">例</span><span class="sxs-lookup"><span data-stu-id="3956b-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="3956b-158">String</span><span class="sxs-lookup"><span data-stu-id="3956b-158">String</span></span>         | <span data-ttu-id="3956b-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="3956b-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="3956b-160">`vary-by-route` には、ルート データのパラメーター値が変化したときにキャッシュの更新をトリガーする、コンマで区切ったルート パラメーター名のリストを指定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="3956b-161">例:</span><span class="sxs-lookup"><span data-stu-id="3956b-161">Example:</span></span>

<span data-ttu-id="3956b-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3956b-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="3956b-163">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3956b-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="3956b-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="3956b-164">vary-by-cookie</span></span>

| <span data-ttu-id="3956b-165">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-165">Attribute Type</span></span> | <span data-ttu-id="3956b-166">例</span><span class="sxs-lookup"><span data-stu-id="3956b-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="3956b-167">String</span><span class="sxs-lookup"><span data-stu-id="3956b-167">String</span></span>         | <span data-ttu-id="3956b-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="3956b-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="3956b-169">`vary-by-cookie` には、Cookie 値が変化したときにキャッシュの更新をトリガーする、コンマで区切った Cookie 名のリストを指定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="3956b-170">次の例では、ASP.NET Core ID に関連付けられている Cookie を監視します。</span><span class="sxs-lookup"><span data-stu-id="3956b-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="3956b-171">ユーザーが認証されると、Id Cookie の変更によってキャッシュの更新がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3956b-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="3956b-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="3956b-172">vary-by-user</span></span>

| <span data-ttu-id="3956b-173">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-173">Attribute Type</span></span>  | <span data-ttu-id="3956b-174">例</span><span class="sxs-lookup"><span data-stu-id="3956b-174">Examples</span></span>        | <span data-ttu-id="3956b-175">既定値</span><span class="sxs-lookup"><span data-stu-id="3956b-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="3956b-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="3956b-176">Boolean</span></span>         | <span data-ttu-id="3956b-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="3956b-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="3956b-178">`vary-by-user` では、サインインしているユーザー (またはコンテキストのプリンシパル) が変化したときにキャッシュをリセットするかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="3956b-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="3956b-179">現在のユーザーは要求コンテキストのプリンシパルとも呼ばれ、`@User.Identity.Name` を参照することにより Razor ビューで表示できます。</span><span class="sxs-lookup"><span data-stu-id="3956b-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="3956b-180">次の例では、現在ログインしているユーザーを監視して、キャッシュの更新をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3956b-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3956b-181">この属性を使って、ユーザーがサインインしてからサインアウトするまでキャッシュの内容を保持します。</span><span class="sxs-lookup"><span data-stu-id="3956b-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="3956b-182">値を `true` に設定すると、認証サイクルによって認証されたユーザーのキャッシュが無効にされます。</span><span class="sxs-lookup"><span data-stu-id="3956b-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="3956b-183">ユーザーが認証されると新しい一意の Cookie 値が生成されるため、キャッシュは無効になります。</span><span class="sxs-lookup"><span data-stu-id="3956b-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="3956b-184">Cookie が存在しない場合、または Cookie の有効期限が切れている場合は、キャッシュは匿名状態で保持されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="3956b-185">ユーザーが認証**されない**場合、キャッシュは保持されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="3956b-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="3956b-186">vary-by</span></span>

| <span data-ttu-id="3956b-187">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-187">Attribute Type</span></span> | <span data-ttu-id="3956b-188">例</span><span class="sxs-lookup"><span data-stu-id="3956b-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="3956b-189">String</span><span class="sxs-lookup"><span data-stu-id="3956b-189">String</span></span>         | `@Model` |

<span data-ttu-id="3956b-190">`vary-by` を使うと、キャッシュ対象のデータをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3956b-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="3956b-191">属性の文字列値によって参照されているオブジェクトが変化すると、キャッシュ タグ ヘルパーの内容が更新されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="3956b-192">多くの場合、モデル値の文字列を連結したものがこの属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="3956b-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="3956b-193">このため、事実上連結されている値のいずれかが更新されると、キャッシュは無効になります。</span><span class="sxs-lookup"><span data-stu-id="3956b-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="3956b-194">次の例では、ビューをレンダリングするコントローラー メソッドは 2 つのルート パラメーター `myParam1` と `myParam2` の整数値を合計し、1 つのモデル プロパティとしてその合計値を返すものとします。</span><span class="sxs-lookup"><span data-stu-id="3956b-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="3956b-195">この合計が変化すると、キャッシュ タグ ヘルパーの内容がレンダリングされて、再びキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="3956b-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="3956b-196">アクション:</span><span class="sxs-lookup"><span data-stu-id="3956b-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="3956b-197">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3956b-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="3956b-198">priority</span><span class="sxs-lookup"><span data-stu-id="3956b-198">priority</span></span>

| <span data-ttu-id="3956b-199">属性の種類</span><span class="sxs-lookup"><span data-stu-id="3956b-199">Attribute Type</span></span>      | <span data-ttu-id="3956b-200">例</span><span class="sxs-lookup"><span data-stu-id="3956b-200">Examples</span></span>                               | <span data-ttu-id="3956b-201">既定値</span><span class="sxs-lookup"><span data-stu-id="3956b-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="3956b-202">`High`、`Low`、`NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="3956b-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="3956b-203">`priority` により、組み込みのキャッシュ プロバイダーにキャッシュ削除ガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3956b-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="3956b-204">Web サーバーは、メモリ不足になると、`Low` キャッシュ エントリを最初に削除します。</span><span class="sxs-lookup"><span data-stu-id="3956b-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="3956b-205">例:</span><span class="sxs-lookup"><span data-stu-id="3956b-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3956b-206">`priority` 属性によって、特定レベルのキャッシュ リテンション期間が保証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="3956b-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="3956b-207">`CacheItemPriority` は提案するだけです。</span><span class="sxs-lookup"><span data-stu-id="3956b-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="3956b-208">この属性を `NeverRemove` に設定しても、キャッシュされた項目が常に保持されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="3956b-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="3956b-209">詳細については、「[その他のリソース](#additional-resources)」セクションにあるトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3956b-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="3956b-210">キャッシュ タグ ヘルパーは、[メモリ キャッシュ サービス](xref:performance/caching/memory)に依存します。</span><span class="sxs-lookup"><span data-stu-id="3956b-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="3956b-211">サービスが追加されていない場合、キャッシュ タグ ヘルパーによってサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3956b-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3956b-212">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3956b-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
