---
title: ASP.NET Core での証明書の認証を構成します。
author: blowdart
description: ASP.NET Core で IIS と HTTP.sys の証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538718"
---
# <a name="overview"></a><span data-ttu-id="631a0-103">概要</span><span class="sxs-lookup"><span data-stu-id="631a0-103">Overview</span></span>

<span data-ttu-id="631a0-104">`Microsoft.AspNetCore.Authentication.Certificate` ような実装を含む[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)ASP.NET Core 用です。</span><span class="sxs-lookup"><span data-stu-id="631a0-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="631a0-105">証明書の認証は、ASP.NET Core にこれまで到達する前に、長い、TLS レベルで行われます。</span><span class="sxs-lookup"><span data-stu-id="631a0-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="631a0-106">証明書を検証し、しにその証明書を解決できるイベントを提供する認証ハンドラーをより正確には、これは、`ClaimsPrincipal`します。</span><span class="sxs-lookup"><span data-stu-id="631a0-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="631a0-107">[ホストを構成する](#configure-your-host-to-require-certificates)証明書の認証は IIS、Kestrel、Azure Web Apps、またはあらゆる要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="631a0-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="631a0-108">作業開始</span><span class="sxs-lookup"><span data-stu-id="631a0-108">Get started</span></span>

<span data-ttu-id="631a0-109">HTTPS 証明書を取得、それを適用し、 [、ホストを構成する](#configure-your-host-to-require-certificates)証明書を要求します。</span><span class="sxs-lookup"><span data-stu-id="631a0-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="631a0-110">Web アプリへの参照を追加、`Microsoft.AspNetCore.Authentication.Certificate`パッケージ。</span><span class="sxs-lookup"><span data-stu-id="631a0-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="631a0-111">次に、`Startup.Configure`メソッドを呼び出します`app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`オプションをご提供のデリゲート`OnCertificateValidated`要求と共に送信されるクライアント証明書の補助、検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="631a0-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="631a0-112">その情報を有効にする、`ClaimsPrincipal`に設定し、`context.Principal`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="631a0-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="631a0-113">認証が失敗したかどうか、このハンドラーを返します、`403 (Forbidden)`応答ではなく、`401 (Unauthorized)`想像どおり、します。</span><span class="sxs-lookup"><span data-stu-id="631a0-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="631a0-114">理由は、初期の TLS 接続中に、認証が発生することです。</span><span class="sxs-lookup"><span data-stu-id="631a0-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="631a0-115">ハンドラーに到達するまででは遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="631a0-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="631a0-116">証明書のいずれかに匿名接続から接続をアップグレードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="631a0-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="631a0-117">追加も`app.UseAuthentication();`で、`Startup.Configure`メソッド。</span><span class="sxs-lookup"><span data-stu-id="631a0-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="631a0-118">それ以外の場合、HttpContext.User は設定されません`ClaimsPrincipal`証明書から作成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="631a0-119">例えば:</span><span class="sxs-lookup"><span data-stu-id="631a0-119">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="631a0-120">前の例では、既定の証明書認証を追加する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="631a0-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="631a0-121">ハンドラーは、一般的な証明書のプロパティを使用して、ユーザー プリンシパルを作成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="631a0-122">証明書の検証を構成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-122">Configure certificate validation</span></span>

<span data-ttu-id="631a0-123">`CertificateAuthenticationOptions`ハンドラーがいくつか組み込みの検証は、最小の検証証明書でを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="631a0-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="631a0-124">これらの各設定は、既定で有効です。</span><span class="sxs-lookup"><span data-stu-id="631a0-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="631a0-125">AllowedCertificateTypes = さまざま、またはすべてのチェーン (チェーン |さまざま)</span><span class="sxs-lookup"><span data-stu-id="631a0-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="631a0-126">このチェックは、適切な証明書の種類のみが許可されていることを検証します。</span><span class="sxs-lookup"><span data-stu-id="631a0-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="631a0-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="631a0-127">ValidateCertificateUse</span></span>

<span data-ttu-id="631a0-128">このチェックは、クライアントによって提示された証明書が、クライアント認証用のキーの使用 (EKU)、またはなしの Eku をすべての拡張を検証します。</span><span class="sxs-lookup"><span data-stu-id="631a0-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="631a0-129">ように、仕様と答えると、EKU が指定されていない場合、すべての Eku が有効と見なされます。</span><span class="sxs-lookup"><span data-stu-id="631a0-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="631a0-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="631a0-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="631a0-131">このチェックでは、その有効期間内で証明書が検証します。</span><span class="sxs-lookup"><span data-stu-id="631a0-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="631a0-132">要求ごとに、ハンドラーにより、ことが現在のセッション中に表示されたときに有効であった証明書が期限切れです。</span><span class="sxs-lookup"><span data-stu-id="631a0-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="631a0-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="631a0-133">RevocationFlag</span></span>

<span data-ttu-id="631a0-134">チェーンの証明書は失効のチェックを指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="631a0-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="631a0-135">失効のチェックが実行されるは、証明書がルート証明書にチェーンされたときにのみです。</span><span class="sxs-lookup"><span data-stu-id="631a0-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="631a0-136">revocationMode</span><span class="sxs-lookup"><span data-stu-id="631a0-136">RevocationMode</span></span>

<span data-ttu-id="631a0-137">失効チェックを実行する方法を指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="631a0-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="631a0-138">オンラインのチェックを指定すると、証明機関には接続中に長時間の遅延が発生することができます。</span><span class="sxs-lookup"><span data-stu-id="631a0-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="631a0-139">失効のチェックが実行されるは、証明書がルート証明書にチェーンされたときにのみです。</span><span class="sxs-lookup"><span data-stu-id="631a0-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="631a0-140">特定のパスでのみ証明書を必要とするアプリを構成できますか。</span><span class="sxs-lookup"><span data-stu-id="631a0-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="631a0-141">これは、ことはできません。</span><span class="sxs-lookup"><span data-stu-id="631a0-141">This isn't possible.</span></span> <span data-ttu-id="631a0-142">HTTPS メッセージ交換の開始日は、そのサーバーが要求しているフィールドに基づくスコープにことはできませんので、その接続で、最初の要求が受信されるまで、証明書の交換を実行してください。</span><span class="sxs-lookup"><span data-stu-id="631a0-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="631a0-143">イベントのハンドラー</span><span class="sxs-lookup"><span data-stu-id="631a0-143">Handler events</span></span>

<span data-ttu-id="631a0-144">ハンドラーでは、2 つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="631a0-144">The handler has two events:</span></span>

* <span data-ttu-id="631a0-145">`OnAuthenticationFailed` &ndash; 例外が発生した認証時に、対応することができる場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="631a0-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="631a0-146">`OnCertificateValidated` &ndash; 証明書が検証される検証に合格し、既定のプリンシパルが作成された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="631a0-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="631a0-147">このイベントを使用すると、独自の検証を実行し、補強またはプリンシパルを置換できます。</span><span class="sxs-lookup"><span data-stu-id="631a0-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="631a0-148">例が含まれます。</span><span class="sxs-lookup"><span data-stu-id="631a0-148">For examples include:</span></span>
  * <span data-ttu-id="631a0-149">サービスに証明書が既知のかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="631a0-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="631a0-150">独自のプリンシパルを作成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-150">Constructing your own principal.</span></span> <span data-ttu-id="631a0-151">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="631a0-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="631a0-152">受信証明書は、追加の検証を満たしていない場合は、呼び出す`context.Fail("failure reason")`エラー理由を使用します。</span><span class="sxs-lookup"><span data-stu-id="631a0-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="631a0-153">実際の機能は、おそらくたいデータベースやその他のユーザー ストアに接続する依存関係の挿入に登録されているサービスを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="631a0-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="631a0-154">デリゲートに渡されたコンテキストを使用して、サービスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="631a0-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="631a0-155">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="631a0-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="631a0-156">概念的には、証明書の検証は、承認問題です。</span><span class="sxs-lookup"><span data-stu-id="631a0-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="631a0-157">チェックを追加すると、たとえば、発行者または内部ではなく、承認のポリシーで拇印`OnCertificateValidated`は差し支えありません。</span><span class="sxs-lookup"><span data-stu-id="631a0-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="631a0-158">証明書を要求するように、ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="631a0-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="631a0-159">Kestrel</span></span>

<span data-ttu-id="631a0-160">*Program.cs*、次のように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="631a0-160">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a><span data-ttu-id="631a0-161">IIS</span><span class="sxs-lookup"><span data-stu-id="631a0-161">IIS</span></span>

<span data-ttu-id="631a0-162">IIS マネージャーで次の手順を完了するには。</span><span class="sxs-lookup"><span data-stu-id="631a0-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="631a0-163">サイトを選択、**接続**タブ。</span><span class="sxs-lookup"><span data-stu-id="631a0-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="631a0-164">ダブルクリックして、 **SSL 設定**オプション、**機能ビュー**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="631a0-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="631a0-165">チェック、 **SSL が必要** チェック ボックスを選択し、**を必要と**オプション ボタンをクリック、**クライアント証明書**セクション。</span><span class="sxs-lookup"><span data-stu-id="631a0-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS でクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="631a0-167">Azure とカスタム web プロキシ</span><span class="sxs-lookup"><span data-stu-id="631a0-167">Azure and custom web proxies</span></span>

<span data-ttu-id="631a0-168">参照してください、[ホストし、ドキュメントを配置](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)ミドルウェアを転送する証明書を構成するためです。</span><span class="sxs-lookup"><span data-stu-id="631a0-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
