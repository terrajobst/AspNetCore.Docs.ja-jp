---
title: ASP.NET Core SignalR での認証と承認
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 091cc9b2adc1f6a8fac79519884695d1c1725d2a
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880415"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR での認証と承認

作成者: [Andrew Stanton-Nurse](https://twitter.com/anurse)

[サンプル コードの表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a>SignalR hub に接続しているユーザーを認証する

SignalR を[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用して、ユーザーを各接続に関連付けることができます。 ハブでは、 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティから認証データにアクセスできます。 認証を使用すると、ユーザーに関連付けられているすべての接続で、ハブがメソッドを呼び出すことができます。 詳細については、「 [SignalRでのユーザーとグループの管理](xref:signalr/groups)」を参照してください。 複数の接続を1人のユーザーに関連付けることができます。

SignalR と ASP.NET Core 認証を使用する `Startup.Configure` の例を次に示します。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> SignalR と ASP.NET Core 認証ミドルウェアを登録する順序が重要になります。 SignalR が `HttpContext`にユーザーを持つようにするには、`UseSignalR` する前に必ず `UseAuthentication` を呼び出してください。

::: moniker-end

### <a name="cookie-authentication"></a>Cookie 認証

ブラウザーベースのアプリでは、cookie 認証を使用して、既存のユーザーの資格情報を SignalR 接続に自動的にフローさせることができます。 ブラウザークライアントを使用する場合、追加の構成は必要ありません。 ユーザーがアプリにログインしている場合は、SignalR 接続によって自動的にこの認証が継承されます。

Cookie はアクセストークンを送信するためのブラウザー固有の方法ですが、ブラウザー以外のクライアントは送信できます。 [.Net クライアント](xref:signalr/dotnet-client)を使用する場合は、`.WithUrl` 呼び出しで `Cookies` プロパティを構成して、cookie を提供できます。 ただし、.NET クライアントから cookie 認証を使用するには、アプリが cookie の認証データを交換するための API を提供する必要があります。

### <a name="bearer-token-authentication"></a>ベアラートークン認証

クライアントは、cookie を使用する代わりにアクセストークンを提供できます。 サーバーはトークンを検証し、それを使用してユーザーを識別します。 この検証は、接続が確立された場合にのみ実行されます。 接続の有効期間中は、トークンの失効を確認するためにサーバーが自動的に再検証されることはありません。

サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。

JavaScript クライアントでは、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプションを使用してトークンを指定できます。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET クライアントには、トークンの構成に使用できる同様の[れる場合](xref:signalr/configuration#configure-bearer-authentication)プロパティがあります。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 指定したアクセストークン関数は、SignalRによって行われる**すべて**の HTTP 要求の前に呼び出されます。 接続をアクティブな状態に保つために、トークンを更新する必要がある場合 (接続中に期限切れになる可能性があるため)、この関数内からトークンを取得し、更新されたトークンを返します。

標準の web Api では、ベアラートークンは HTTP ヘッダーで送信されます。 ただし、一部のトランスポートを使用している場合、SignalR はブラウザーでこれらのヘッダーを設定できません。 Websocket およびサーバー送信イベントを使用する場合、トークンはクエリ文字列パラメーターとして送信されます。 サーバーでこれをサポートするには、追加の構成が必要です。

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> クエリ文字列は、ブラウザー API の制限により、Websocket およびサーバーから送信されたイベントと接続するときにブラウザーで使用されます。 HTTPS を使用する場合、クエリ文字列の値は TLS 接続によって保護されます。 ただし、多くのサーバーはクエリ文字列の値をログに記録します。 詳細については、「 [ASP.NET Core SignalR」の「セキュリティに関する考慮事項](xref:signalr/security)」を参照してください。 SignalR は、ヘッダーを使用して、トークンをサポートする環境 (.NET や Java クライアントなど) でトークンを送信します。

### <a name="cookies-vs-bearer-tokens"></a>Cookie とベアラートークン 

Cookie は、ブラウザーに固有のものです。 他の種類のクライアントから送信すると、ベアラートークンの送信と比較して複雑さが増します。 そのため、アプリがブラウザークライアントからのユーザーの認証のみを必要とする場合を除き、cookie 認証は推奨されません。 ベアラートークン認証は、ブラウザークライアント以外のクライアントを使用する場合に推奨される方法です。

### <a name="windows-authentication"></a>Windows 認証

アプリで[Windows 認証](xref:security/authentication/windowsauth)が構成されている場合、SignalR はその id を使用してハブをセキュリティで保護することができます。 ただし、個々のユーザーにメッセージを送信するには、カスタム ユーザー ID プロバイダーを追加する必要があります。 Windows 認証システムは、"Name Identifier" クレームを提供しません。 SignalR は、要求を使用してユーザー名を決定します。

`IUserIdProvider` を実装する新しいクラスを追加し、識別子として使用するクレームの 1 つをユーザーから取得します。 たとえば、"Name" クレーム (フォーム `[Domain]\[Username]` の Windows ユーザー名) を使用するには、次のクラスを作成します。

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

`ClaimTypes.Name` ではなく、`User` の任意の値 (Windows SID 識別子など) を使用できます。

> [!NOTE]
> 選択する値は、システム内のすべてのユーザーの間で一意である必要があります。 そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。

`Startup.ConfigureServices` メソッド内でこのコンポーネントを登録します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

.NET クライアントでは、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)プロパティを設定することによって Windows 認証を有効にする必要があります。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows 認証は、Microsoft Internet Explorer または Microsoft Edge を使用している場合にのみブラウザークライアントでサポートされます。

### <a name="use-claims-to-customize-identity-handling"></a>要求を使用して id 処理をカスタマイズする

ユーザーを認証するアプリは、ユーザー要求からユーザー Id を SignalR 派生させることができます。 SignalR がユーザー Id を作成する方法を指定するには `IUserIdProvider` を実装し、実装を登録します。

このサンプルコードでは、要求を使用して、識別プロパティとしてユーザーの電子メールアドレスを選択する方法を示します。 

> [!NOTE]
> 選択する値は、システム内のすべてのユーザーの間で一意である必要があります。 そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

アカウント登録によって、種類が `ClaimsTypes.Email` の要求が ASP.NET identity データベースに追加されます。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

`Startup.ConfigureServices` 内でこのコンポーネントを登録します。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>ハブおよびハブのメソッドへのアクセスをユーザーに承認する

既定では、ハブ内のすべてのメソッドを認証されていないユーザーが呼び出すことができます。 認証を要求するには、[承認](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性をハブに適用します。

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

`[Authorize]` 属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。 たとえば、`MyAuthorizationPolicy` というカスタム承認ポリシーがある場合、次のコードを使用して、そのポリシーに一致するユーザーだけがハブにアクセスできるようにすることができます。

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

個々のハブメソッドにも、`[Authorize]` 属性を適用できます。 現在のユーザーがメソッドに適用されているポリシーと一致しない場合は、呼び出し元にエラーが返されます。

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>承認ハンドラーを使用してハブメソッドの承認をカスタマイズする

ハブメソッドが承認を必要とする場合、SignalR は承認ハンドラーにカスタムリソースを提供します。 リソースは `HubInvocationContext` のインスタンスです。 `HubInvocationContext` には、`HubCallerContext`、呼び出されるハブメソッドの名前、およびハブメソッドへの引数が含まれます。

Azure Active Directory による複数の組織でのサインインを可能にするチャットルームの例を考えてみましょう。 Microsoft アカウントを持つユーザーはだれでもチャットにサインインできますが、所有している組織のメンバーだけがユーザーの許可を禁止したり、ユーザーのチャット履歴を表示したりできるようにする必要があります。 さらに、特定のユーザーの特定の機能を制限することもできます。 ASP.NET Core 3.0 の更新された機能を使用すると、これは完全に可能です。 `DomainRestrictedRequirement` がカスタム `IAuthorizationRequirement`としてどのように機能するかに注意してください。 `HubInvocationContext` リソースパラメーターが渡されるようになったので、内部ロジックはハブが呼び出されているコンテキストを検査し、ユーザーが個々のハブメソッドを実行できるようにすることを決定します。

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

`Startup.ConfigureServices` 内で、新しいポリシーを追加し、`DomainRestrictedRequirement` ポリシーを作成するためのパラメーターとしてカスタム `DomainRestricted` 要件を指定します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

前の例では、`DomainRestrictedRequirement` クラスは、その要件の `IAuthorizationRequirement` と独自の `AuthorizationHandler` の両方です。 この2つのコンポーネントを別々のクラスに分割して、懸念事項を分けることができます。 この例のアプローチの利点は、要件とハンドラーが同じであるため、起動時に `AuthorizationHandler` を挿入する必要がないことです。

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core でのベアラートークン認証](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [リソースベースの承認](xref:security/authorization/resourcebased)
