---
title: ASP.NET Core SignalR での認証と承認
author: bradygaster
description: ASP.NET Core SignalR で認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/17/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: 258b6d92896d38b79116278abb7c70b6063e8131
ms.sourcegitcommit: ce2bfb01f2cc7dd83f8a97da0689d232c71bcdc4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531173"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core SignalR での認証と承認

作成者: [Andrew Stanton-Nurse](https://twitter.com/anurse)

[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/)[(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub">SignalR ハブに接続しているユーザーを認証する</a>

SignalR を[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用すると、ユーザーを各接続に関連付けることができます。 ハブでは、 [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティから認証データにアクセスできます。 認証を使用すると、ユーザーに関連付けられているすべての接続で、ハブがメソッドを呼び出すことができます。 詳細については、「 [Manage users and groups In SignalR](xref:signalr/groups)」を参照してください。 複数の接続を1人のユーザーに関連付けることができます。

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
> SignalR および ASP.NET Core 認証ミドルウェアを登録する順序が重要になります。 SignalR が `HttpContext` にユーザーを持つようにするには、`UseSignalR` の前に常に `UseAuthentication` を呼び出します。

::: moniker-end

### <a name="cookie-authentication"></a>Cookie 認証

ブラウザーベースのアプリでは、Cookie 認証を使用して、既存のユーザーの資格情報を SignalR 接続に自動的に渡すことができます。ブラウザー クライアントを使用する場合、追加の構成は必要ありません。ユーザーがアプリにログインしている場合、SignalR 接続は自動的にこの認証を継承します。

Cookie はアクセス トークンを送信するためのブラウザー固有の方法ですが、ブラウザー以外のクライアントは送信できます。[.NET クライアント](xref:signalr/dotnet-client)を使用する場合は、`.WithUrl` 呼び出しで `Cookies` プロパティを構成して、Cookie を提供できます。ただし、.NET クライアントから Cookie 認証を使用するには、アプリが Cookie の認証データを交換するための API を提供する必要があります。

### <a name="bearer-token-authentication"></a>ベアラートークン認証

クライアントは、Cookie を使用する代わりにアクセス トークンを提供できます。サーバーはトークンを検証し、それを使用してユーザーを識別します。この検証は、接続が確立された場合にのみ実行されます。接続の有効期間中は、トークンの失効を確認するためにサーバーが自動的に再検証されることはありません。


サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。

JavaScript クライアントでは、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプションを使用してトークンを指定できます。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET クライアントには、トークンの構成に使用できる同様の [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) プロパティがあります。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 指定したアクセス トークン関数は、SignalR によって行われる**すべて**の HTTP 要求の前に呼び出されます。接続をアクティブな状態に保つために、トークンを更新する必要がある場合 (接続中に期限切れになる可能性があるため)、この関数内でトークンを更新し、それを返します。

標準の Web API では、ベアラー トークンは HTTP ヘッダーで送信されます。ただし、SignalR では、一部のトランスポートを使用するときに、これらのヘッダーをブラウザーで設定することはできません。WebSocket および Server-Sent Events を使用する場合、トークンはクエリ文字列パラメーターとして送信されます。サーバーでこれをサポートするには、追加の構成が必要です。


[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> クエリ文字列は、ブラウザー API の制限により、WebSocket および Server-Sent Events と接続するときにブラウザーで使用されます。HTTPS を使用する場合、クエリ文字列の値は TLS 接続によって保護されます。ただし、多くのサーバーはクエリ文字列の値をログに記録します。詳細については、「[ASP.NET Core SignalR のセキュリティに関する考慮事項](xref:signalr/security)」を参照してください。SignalR は、トークンをサポートする環境 (.NET や Java クライアントなど) では、ヘッダーを使用してトークンを送信します。

### <a name="cookies-vs-bearer-tokens"></a>Cookie とベアラートークン 

Cookie は、ブラウザーに固有のものです。他の種類のクライアントから送信すると、ベアラー トークンの送信と比較して複雑さが増します。そのため、アプリがブラウザー クライアントからのユーザーの認証のみを必要とする場合を除き、Cookie 認証は推奨されません。ベアラー トークン認証は、ブラウザー クライアント以外のクライアントを使用する場合に推奨される方法です。

### <a name="windows-authentication"></a>Windows 認証

アプリで [Windows 認証](xref:security/authentication/windowsauth)が構成されている場合、SignalR はその ID を使用してハブをセキュリティで保護することができます。ただし、個々のユーザーにメッセージを送信するには、カスタム ユーザー ID プロバイダーを追加する必要があります。Windows 認証システムは、"Name Identifier" クレームを提供しません。SignalR は、クレームを使用してユーザー名を決定します。

`IUserIdProvider` を実装する新しいクラスを追加し、識別子として使用するクレームの 1 つをユーザーから取得します。たとえば、"Name" クレーム (フォーム `[Domain]\[Username]` の Windows ユーザー名) を使用するには、次のクラスを作成します。


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

### <a name="use-claims-to-customize-identity-handling"></a>クレームによる ID 処理のカスタマイズ

ユーザーを認証するアプリは、ユーザー クレームから SignalR ユーザー ID を派生させることができます。SignalR がユーザー ID を作成する方法を指定するには、`IUserIdProvider` を実装し、実装を登録します。


このサンプル コードでは、クレームを使用して、識別プロパティとしてユーザーのメール アドレスを選択する方法を示します。 

> [!NOTE]
> 選択する値は、システム内のすべてのユーザーの間で一意である必要があります。 そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

アカウント登録によって、種類が `ClaimsTypes.Email` のクレームが ASP.NET identity データベースに追加されます。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

`Startup.ConfigureServices` 内でこのコンポーネントを登録します。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>ハブおよびハブのメソッドへのアクセスをユーザーに承認する

既定では、ハブ内のすべてのメソッドを認証されていないユーザーが呼び出すことができます。認証を要求するには、[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 属性をハブに適用します。


[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

`[Authorize]` 属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。たとえば、`MyAuthorizationPolicy` というカスタム承認ポリシーがある場合、次のコードを使用して、そのポリシーに一致するユーザーだけがハブにアクセスできるようにすることができます。

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization">承認ハンドラーによるハブ メソッドの承認のカスタマイズ</a>

SignalR は、ハブ メソッドが承認を必要とする場合に、承認ハンドラーにカスタム リソースを提供します。リソースは `HubInvocationContext` のインスタンスです。`HubInvocationContext` には、`HubCallerContext`、呼び出されるハブ メソッドの名前、およびハブ メソッドへの引数が含まれます。

Azure Active Directory による複数の組織でのサインインを可能にするチャット ルームの例を考えてみましょう。Microsoft アカウントを持つユーザーはだれでもチャットにサインインできますが、所有している組織のメンバーだけがユーザーの許可を禁止したり、ユーザーのチャット履歴を表示したりできるようにする必要があります。さらに、特定のユーザーに対し、特定の機能を制限したい場合があります。ASP.NET Core 3.0 の更新された機能を使用すると、これはすべて可能です。`DomainRestrictedRequirement` がカスタム `IAuthorizationRequirement` としてどのように機能するかに注意してください。`HubInvocationContext` リソース パラメーターが渡されるようになったので、内部ロジックはハブが呼び出されているコンテキストを検査し、ユーザーが個々のハブ メソッドを実行できるようにすることを決定します。


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

`Startup.ConfigureServices` 内で、新しいポリシーを追加し、`DomainRestricted` ポリシーを作成するためのパラメーターとしてカスタム `DomainRestrictedRequirement` 要件を指定します。


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

前の例では、`DomainRestrictedRequirement` クラスは、その要件の `IAuthorizationRequirement` と独自の `AuthorizationHandler` の両方です。関心の分離のために、この 2 つのコンポーネントを別々のクラスに分割することができます。この例のアプローチの利点は、要件とハンドラーが同じであるため、起動時に `AuthorizationHandler` を挿入する必要がないことです。


::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core でのベアラートークン認証](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [リソースベースの承認](xref:security/authorization/resourcebased)
