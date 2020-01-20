---
title: ASP.NET Core Blazor の認証と承認
author: guardrex
description: Blazor の認証と承認のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 2ce2cff8d3ab77f21181070b6f1e48c50561036c
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160289"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>ASP.NET Core Blazor の認証と承認

作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

ASP.NET Core は、Blazor アプリのセキュリティの構成と管理をサポートしています。

Blazor サーバー アプリと Blazor WebAssembly アプリのセキュリティ シナリオは異なります。 Blazor サーバー アプリはサーバー上で動作するため、承認チェックでは以下のことを判断できます。

* ユーザーに表示される UI オプション (たとえば、ユーザーが利用できるメニュー エントリ)。
* アプリとコンポーネントの領域に対するアクセス規則。

Blazor WebAssembly アプリはクライアント上で動作します。 承認は、表示する UI オプションを決定するために "*のみ*" 使用されます。 クライアント側のチェックはユーザーによって変更またはバイパスされる可能性があるため、Blazor WebAssembly アプリでは承認アクセス規則を適用できません。

## <a name="authentication"></a>認証

Blazor は、既存の ASP.NET Core 認証メカニズムを使用してユーザーの ID を証明します。 詳細なメカニズムは、Blazor アプリのホスティング方法、Blazor サーバーか Blazor WebAssembly かによって異なります。

### <a name="opno-locblazor-server-authentication"></a>Blazor サーバー認証

Blazor サーバー アプリは、SignalR を使用して作成されたリアルタイム接続を介して動作します。 [SignalR ベースのアプリの認証](xref:signalr/authn-and-authz)は、接続が確立したときに処理されます。 認証は、Cookie または他のベアラー トークンに基づいています。

プロジェクトの作成時に、Blazor サーバー プロジェクト テンプレートで認証を設定できます。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。

**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。

ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。

* **認証なし**
* **個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。
  * ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c)。
* **職場または学校アカウント**
* **Windows 認証**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム                                                                 | `{AUTHENTICATION}` の値 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 認証なし                                                                        | `None`                   |
| 個人<br>ASP.NET Core ID を使用するアプリに格納されているユーザー。                        | `Individual`             |
| 個人<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。 | `IndividualB2C`          |
| 職場または学校アカウント<br>単一のテナントに対する組織認証。            | `SingleOrg`              |
| 職場または学校アカウント<br>複数のテナントに対する組織認証です。           | `MultiOrg`               |
| Windows 認証                                                                   | `Windows`                |

このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。 詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor WebAssembly 認証

Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、認証チェックがバイパスされる可能性があります。 JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。

アプリのプロジェクト ファイルに、[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) のパッケージ参照を追加します。

Blazor WebAssembly アプリ用のカスタム `AuthenticationStateProvider` サービスの実装については、以降のセクションで説明します。

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider サービス

Blazor サーバー アプリには、ASP.NET Core の `HttpContext.User` から認証状態データを取得する組み込みの `AuthenticationStateProvider` サービスが含まれています。 このようにして、認証状態は既存の ASP.NET Core サーバー側認証メカニズムと統合されています。

`AuthenticationStateProvider` は、認証状態を取得するために `AuthorizeView` コンポーネントと `CascadingAuthenticationState` コンポーネントによって使用される基となるサービスです。

通常、`AuthenticationStateProvider` は直接使用しません。 この記事で後述する [AuthorizeView コンポーネント](#authorizeview-component)または [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) のアプローチを使用します。 `AuthenticationStateProvider` を直接使用することの主な欠点は、基となる認証状態データが変更されてもコンポーネントに自動的に通知されないことです。

次の例に示すように、`AuthenticationStateProvider` サービスから現在のユーザーの <xref:System.Security.Claims.ClaimsPrincipal> データを提供できます。

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

`user.Identity.IsAuthenticated` が `true` である場合、ユーザーが <xref:System.Security.Claims.ClaimsPrincipal> であるため、要求を列挙し、役割のメンバーシップを評価できます。

依存関係の注入 (DI) とサービスの詳細については、<xref:blazor/dependency-injection> と <xref:fundamentals/dependency-injection> を参照してください。

## <a name="implement-a-custom-authenticationstateprovider"></a>カスタム AuthenticationStateProvider の実装

Blazor WebAssembly アプリを構築している場合、またはアプリの仕様でカスタム プロバイダーが必要な場合は、プロバイダーを実装して `GetAuthenticationStateAsync` をオーバーライドします。

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
    public class CustomAuthStateProvider : AuthenticationStateProvider
    {
        public override Task<AuthenticationState> GetAuthenticationStateAsync()
        {
            var identity = new ClaimsIdentity(new[]
            {
                new Claim(ClaimTypes.Name, "mrfibuli"),
            }, "Fake authentication type");

            var user = new ClaimsPrincipal(identity);

            return Task.FromResult(new AuthenticationState(user));
        }
    }
}
```

`CustomAuthStateProvider` サービスは `Startup.ConfigureServices` に登録されています。

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider` を使用すると、すべてのユーザーはユーザー名 `mrfibuli` で認証されます。

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>認証状態をカスケード パラメーターとして公開する

ユーザーによってトリガーされたアクションを実行する場合など、認証状態データが手続き型ロジックに必要な場合は、型 `Task<AuthenticationState>` のカスケード パラメーターを定義して認証状態データを取得します。

```razor
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Components.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Components.Authorization`) を追加します。

`user.Identity.IsAuthenticated` が `true` である場合、要求を列挙し、役割のメンバーシップを評価できます。

*App.razor* ファイル内の `AuthorizeRouteView` および `CascadingAuthenticationState` コンポーネントを使用して、`Task<AuthenticationState>` カスケード パラメーターを設定します。

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

## <a name="authorization"></a>承認

ユーザーが認証されると、ユーザーが実行できる操作を制御する "*承認*" 規則が適用されます。

通常、アクセスは以下の条件に基づいて許可または拒否されます。

* ユーザーが認証されている (サインインしている)。
* ユーザーに "*ロール*" が割り当てられている。
* ユーザーに "*要求*" がある。
* "*ポリシー*" が満たされている。

これらの各概念は、ASP.NET Core MVC または Razor Pages アプリと同じです。 ASP.NET Core のセキュリティの詳細については、[ASP.NET Core のセキュリティと ID](xref:security/index) の記事を参照してください。

## <a name="authorizeview-component"></a>AuthorizeView コンポーネント

`AuthorizeView` コンポーネントでは、ユーザーに表示を許可するかどうかに応じて UI が選択的に表示されます。 このアプローチは、ユーザーに対してデータを "*表示する*" だけで済み、手続き型ロジックでユーザーの ID を使用する必要がない場合に便利です。

このコンポーネントでは、型 `AuthenticationState` の `context` 変数が公開されており、これを使用して、サインインしたユーザーに関する情報にアクセスできます。

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

ユーザーが認証されていない場合は、表示用に異なるコンテンツを指定することもできます。

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

`<Authorized>` および `<NotAuthorized>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。

UI オプションまたはアクセスを制御するロールやポリシーなどの承認条件については、「[承認](#authorization)」セクションを参照してください。

承認条件が指定されていない場合、`AuthorizeView` では既定のポリシーが使用され、以下が扱われます。

* 承認済みの認証された (サインインした) ユーザー。
* 未承認の認証されていない (サインアウトした) ユーザー。

### <a name="role-based-and-policy-based-authorization"></a>ロールベースとリソースベースの承認

`AuthorizeView` コンポーネントは、"*ロールベース*" または "*ポリシーベース*" の承認をサポートしています。

ロールベースの承認の場合は、`Roles` パラメーターを使用します。

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

詳細については、「<xref:security/authorization/roles>」を参照してください。

ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

要求ベースの承認は、ポリシーベースの承認の特殊なケースです。 たとえば、ユーザーが特定の要求持つことを必須にするポリシーを定義できます。 詳細については、「<xref:security/authorization/policies>」を参照してください。

これらの API は、Blazor サーバー アプリまたは Blazor WebAssembly アプリのどちらでも使用できます。

`Roles` も `Policy` も指定されていない場合、`AuthorizeView` には既定のポリシーが使用されます。

### <a name="content-displayed-during-asynchronous-authentication"></a>非同期認証中に表示されるコンテンツ

Blazor では、認証状態を "*非同期的に*" 決定することができます。 このアプローチの主なシナリオは、認証のために外部エンドポイントに要求を送信する Blazor WebAssembly アプリです。

認証が進行中の間、`AuthorizeView` には既定でコンテンツが表示されません。 認証が行われている間にコンテンツを表示するには、`<Authorizing>` 要素を使用します。

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

通常、このアプローチは Blazor サーバー アプリには適用されません。 Blazor サーバー アプリでは、状態が確立されるとすぐに認証状態が認識されます。 `Authorizing` コンテンツは Blazor サーバー アプリの `AuthorizeView` コンポーネントで提供できますが、コンテンツは表示されません。

## <a name="authorize-attribute"></a>[Authorize] 属性

`[Authorize]` 属性は Razor コンポーネント内で使用できます。

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> Blazor WebAssembly アプリ コンポーネントで、このセクション内の例に `Microsoft.AspNetCore.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Authorization`) を追加します。

> [!IMPORTANT]
> Blazor ルーター経由で到達した `@page` コンポーネントにのみ `[Authorize]` を使用してください。 承認はルーティングの一面としてのみ実行され、ページ内にレンダリングされた子コンポーネントに対しては実行され "*ません*"。 ページ内の特定部分の表示を承認するには、代わりに `AuthorizeView` を使用します。

`[Authorize]` 属性は、ロールベースまたはポリシーベースの承認もサポートしています。 ロールベースの承認の場合は、`Roles` パラメーターを使用します。

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

`Roles` も `Policy` も指定されていない場合、`[Authorize]` には既定のポリシーが使用されます。これは既定で以下が扱われます。

* 承認済みの認証された (サインインした) ユーザー。
* 未承認の認証されていない (サインアウトした) ユーザー。

## <a name="customize-unauthorized-content-with-the-router-component"></a>Router コンポーネントを使用して承認されていないコンテンツをカスタマイズする

`Router` コンポーネントを `AuthorizeRouteView` コンポーネントとともに使用すると、以下の場合にアプリがカスタム コンテンツを指定できます。

* コンテンツが見つからない。
* ユーザーはコンポーネントに適用されている `[Authorize]` 条件に失敗します。 `[Authorize]` 属性については、「[`[Authorize]` 属性](#authorize-attribute)」セクションを参照してください。
* 非同期認証が実行中です。

既定の Blazor サーバー プロジェクト テンプレートでは、*App.razor* ファイルがカスタム コンテンツの設定方法を示しています。

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

`<NotFound>`、`<NotAuthorized>`、および `<Authorizing>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。

`<NotAuthorized>` 要素が指定されていない場合、`AuthorizeRouteView` には次のフォールバック メッセージが使用されます。

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>認証状態の変更に関する通知

基となる認証状態データが変わったとアプリが判断した場合 (たとえば、ユーザーがサインアウトした、または別のユーザーがロールを変更したなど)、カスタムの `AuthenticationStateProvider` はオプションで `AuthenticationStateProvider` 基底クラスのメソッド `NotifyAuthenticationStateChanged` を呼び出すことができます。 これにより、新しいデータを使用して再表示するように、認証状態データ (`AuthorizeView` など) がコンシューマーに通知されます。

## <a name="procedural-logic"></a>手続き型ロジック

手続き型ロジックの一部としてアプリで承認規則をチェックする必要がある場合、型 `Task<AuthenticationState>` のカスケード パラメーターを使用してユーザーの <xref:System.Security.Claims.ClaimsPrincipal> を取得します。 ポリシーを評価するために、`Task<AuthenticationState>` を `IAuthorizationService` などの他のサービスと組み合わせることができます。

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Authorization` と `Microsoft.AspNetCore.Components.Authorization` の名前空間を追加します。
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Blazor WebAssembly アプリでの承認

Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。 JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。

**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**

## <a name="troubleshoot-errors"></a>エラーのトラブルシューティング

一般的なエラー:

* **承認には、型 Task\<AuthenticationState> のカスケード パラメーターが必要です。これを実行するには CascadingAuthenticationState の使用を検討します。**

* `authenticationStateTask` **に対して**`null` 値を受け取ります

認証が有効な Blazor サーバー テンプレートを使用してプロジェクトが作成されなかった可能性があります。 次のように、*App.razor* などの UI ツリーの一部に `<CascadingAuthenticationState>` をラップします。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` には `Task<AuthenticationState>` カスケード パラメーターが用意されており、次に基となる `AuthenticationStateProvider` DI サービスから受け取ります。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
