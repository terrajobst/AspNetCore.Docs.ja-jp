---
title: ASP.NET Core で証明書認証を構成する
author: blowdart
description: IIS と http.sys の ASP.NET Core で証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 280daa86510d4445c791b6952653122961f13aeb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654170"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="533d8-103">ASP.NET Core で証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="533d8-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="533d8-104">`Microsoft.AspNetCore.Authentication.Certificate` には、ASP.NET Core の[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)と同様の実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="533d8-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="533d8-105">証明書の認証は、TLS レベルで実行されますが、その前に ASP.NET Core になります。</span><span class="sxs-lookup"><span data-stu-id="533d8-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="533d8-106">より正確には、これは証明書を検証し、その証明書を `ClaimsPrincipal`に解決できるイベントを提供する認証ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="533d8-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="533d8-107">証明書認証用に[ホストを構成](#configure-your-host-to-require-certificates)します。これは、IIS、Kestrel、Azure Web Apps など、使用している他のすべてのものになります。</span><span class="sxs-lookup"><span data-stu-id="533d8-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="533d8-108">プロキシとロードバランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="533d8-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="533d8-109">証明書認証は、主に、プロキシまたはロードバランサーがクライアントとサーバー間のトラフィックを処理しない場合に使用されるステートフルシナリオです。</span><span class="sxs-lookup"><span data-stu-id="533d8-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="533d8-110">プロキシまたはロードバランサーが使用されている場合、証明書の認証はプロキシまたはロードバランサーの場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="533d8-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="533d8-111">認証を処理します。</span><span class="sxs-lookup"><span data-stu-id="533d8-111">Handles the authentication.</span></span>
* <span data-ttu-id="533d8-112">認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="533d8-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="533d8-113">プロキシとロードバランサーを使用する環境での証明書認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。</span><span class="sxs-lookup"><span data-stu-id="533d8-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="533d8-114">作業開始</span><span class="sxs-lookup"><span data-stu-id="533d8-114">Get started</span></span>

<span data-ttu-id="533d8-115">HTTPS 証明書を取得して適用し、証明書を要求するように[ホストを構成](#configure-your-host-to-require-certificates)します。</span><span class="sxs-lookup"><span data-stu-id="533d8-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="533d8-116">Web アプリで、`Microsoft.AspNetCore.Authentication.Certificate` パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="533d8-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="533d8-117">次に、`Startup.ConfigureServices` メソッドで、`services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` をオプションと共に呼び出し、要求と共に送信されるクライアント証明書に対して補助的な検証を行うための `OnCertificateValidated` のデリゲートを提供します。</span><span class="sxs-lookup"><span data-stu-id="533d8-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="533d8-118">その情報を `ClaimsPrincipal` に変換し、`context.Principal` プロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="533d8-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="533d8-119">認証が失敗した場合、このハンドラーは `401 (Unauthorized)`ではなく `403 (Forbidden)` 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="533d8-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="533d8-120">これは、最初の TLS 接続中に認証が行われるということです。</span><span class="sxs-lookup"><span data-stu-id="533d8-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="533d8-121">ハンドラーに到達するまでには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="533d8-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="533d8-122">匿名接続から証明書を使用して接続をアップグレードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="533d8-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="533d8-123">また、`Startup.Configure` メソッドに `app.UseAuthentication();` を追加します。</span><span class="sxs-lookup"><span data-stu-id="533d8-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="533d8-124">それ以外の場合、`HttpContext.User` は証明書から作成された `ClaimsPrincipal` には設定されません。</span><span class="sxs-lookup"><span data-stu-id="533d8-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="533d8-125">例 :</span><span class="sxs-lookup"><span data-stu-id="533d8-125">For example:</span></span>

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

<span data-ttu-id="533d8-126">前の例では、証明書認証を追加する既定の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="533d8-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="533d8-127">ハンドラーは、共通の証明書のプロパティを使用してユーザープリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="533d8-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="533d8-128">証明書の検証の構成</span><span class="sxs-lookup"><span data-stu-id="533d8-128">Configure certificate validation</span></span>

<span data-ttu-id="533d8-129">`CertificateAuthenticationOptions` ハンドラーには、証明書に対して実行する必要のある検証の最小値である組み込みの検証がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="533d8-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="533d8-130">これらの各設定は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="533d8-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="533d8-131">AllowedCertificateTypes = チェーン、自己署名、またはすべて (チェーン |自己署名済み)</span><span class="sxs-lookup"><span data-stu-id="533d8-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="533d8-132">[既定値]\: [すべての要求]</span><span class="sxs-lookup"><span data-stu-id="533d8-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="533d8-133">このチェックでは、適切な証明書の種類のみが許可されていることが検証されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="533d8-134">アプリが自己署名証明書を使用している場合は、このオプションを `CertificateTypes.All` または `CertificateTypes.SelfSigned`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="533d8-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="533d8-135">ValidateCertificateUse</span></span>

<span data-ttu-id="533d8-136">[既定値]\: [すべての要求]</span><span class="sxs-lookup"><span data-stu-id="533d8-136">Default value: `true`</span></span>

<span data-ttu-id="533d8-137">このチェックでは、クライアントが提示した証明書にクライアント認証の拡張キー使用法 (EKU) があること、またはまったく Eku がないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="533d8-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="533d8-138">仕様として、EKU が指定されていない場合は、すべての Eku が有効と見なされます。</span><span class="sxs-lookup"><span data-stu-id="533d8-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="533d8-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="533d8-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="533d8-140">[既定値]\: [すべての要求]</span><span class="sxs-lookup"><span data-stu-id="533d8-140">Default value: `true`</span></span>

<span data-ttu-id="533d8-141">このチェックでは、証明書が有効期間内であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="533d8-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="533d8-142">要求が発生するたびに、ハンドラーは、提示されたときに有効だった証明書が現在のセッション中に期限切れにならないようにします。</span><span class="sxs-lookup"><span data-stu-id="533d8-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="533d8-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="533d8-143">RevocationFlag</span></span>

<span data-ttu-id="533d8-144">[既定値]\: [すべての要求]</span><span class="sxs-lookup"><span data-stu-id="533d8-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="533d8-145">チェーン内のどの証明書の失効を確認するかを指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="533d8-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="533d8-146">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="533d8-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="533d8-147">RevocationMode</span></span>

<span data-ttu-id="533d8-148">[既定値]\: [すべての要求]</span><span class="sxs-lookup"><span data-stu-id="533d8-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="533d8-149">失効確認の実行方法を指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="533d8-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="533d8-150">オンラインチェックを指定すると、証明機関への接続中に長い遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="533d8-151">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="533d8-152">特定のパスでのみ証明書を要求するようにアプリを構成することはできますか。</span><span class="sxs-lookup"><span data-stu-id="533d8-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="533d8-153">これはできません。</span><span class="sxs-lookup"><span data-stu-id="533d8-153">This isn't possible.</span></span> <span data-ttu-id="533d8-154">証明書の交換は、HTTPS メッセージ交換の開始時に行われます。これは、その接続で最初の要求を受信する前にサーバーによって行われるため、要求フィールドに基づいてスコープを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="533d8-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="533d8-155">ハンドラーイベント</span><span class="sxs-lookup"><span data-stu-id="533d8-155">Handler events</span></span>

<span data-ttu-id="533d8-156">ハンドラーには、次の2つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="533d8-156">The handler has two events:</span></span>

* <span data-ttu-id="533d8-157">`OnAuthenticationFailed` &ndash;、認証中に例外が発生した場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-157">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="533d8-158">証明書が検証され、検証が渡され、既定のプリンシパルが作成された後に呼び出される `OnCertificateValidated` &ndash;。</span><span class="sxs-lookup"><span data-stu-id="533d8-158">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="533d8-159">このイベントを使用すると、独自の検証を実行したり、プリンシパルを補強したり置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="533d8-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="533d8-160">例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="533d8-160">For examples include:</span></span>
  * <span data-ttu-id="533d8-161">証明書がサービスに対して認識されているかどうかを確認しています。</span><span class="sxs-lookup"><span data-stu-id="533d8-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="533d8-162">独自のプリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="533d8-162">Constructing your own principal.</span></span> <span data-ttu-id="533d8-163">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="533d8-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="533d8-164">受信証明書が追加の検証に合わない場合は、失敗の理由で `context.Fail("failure reason")` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="533d8-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="533d8-165">実際の機能の場合は、データベースまたはその他の種類のユーザーストアに接続する依存関係の挿入に登録されているサービスを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="533d8-166">デリゲートに渡されたコンテキストを使用してサービスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="533d8-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="533d8-167">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="533d8-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="533d8-168">概念的には、証明書の検証は承認に関する問題です。</span><span class="sxs-lookup"><span data-stu-id="533d8-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="533d8-169">たとえば、`OnCertificateValidated`内部ではなく、承認ポリシーの発行者や拇印などのチェックを追加することは、まったく可能です。</span><span class="sxs-lookup"><span data-stu-id="533d8-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="533d8-170">証明書を要求するようにホストを構成する</span><span class="sxs-lookup"><span data-stu-id="533d8-170">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="533d8-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="533d8-171">Kestrel</span></span>

<span data-ttu-id="533d8-172">*Program.cs*で、次のように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="533d8-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="533d8-173"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="533d8-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="533d8-174">IIS</span><span class="sxs-lookup"><span data-stu-id="533d8-174">IIS</span></span>

<span data-ttu-id="533d8-175">IIS マネージャーで、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="533d8-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="533d8-176">**[接続]** タブからサイトを選択します。</span><span class="sxs-lookup"><span data-stu-id="533d8-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="533d8-177">**[機能ビュー]** ウィンドウで、 **[SSL 設定]** オプションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="533d8-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="533d8-178">**[SSL が必要]** チェックボックスをオンにし、 **[クライアント証明書]** セクションの **[必須]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="533d8-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS でのクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="533d8-180">Azure とカスタム web プロキシ</span><span class="sxs-lookup"><span data-stu-id="533d8-180">Azure and custom web proxies</span></span>

<span data-ttu-id="533d8-181">証明書転送ミドルウェアを構成する方法については、[ホストを参照し、ドキュメントを展開](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)してください。</span><span class="sxs-lookup"><span data-stu-id="533d8-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="533d8-182">Azure Web Apps で証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="533d8-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="533d8-183">Azure では、転送構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="533d8-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="533d8-184">これは既に証明書転送ミドルウェアで設定されています。</span><span class="sxs-lookup"><span data-stu-id="533d8-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="533d8-185">これを行うには、CertificateForwardingMiddleware が存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="533d8-186">カスタム web プロキシで証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="533d8-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="533d8-187">`AddCertificateForwarding` メソッドを使用して、次のように指定します。</span><span class="sxs-lookup"><span data-stu-id="533d8-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="533d8-188">クライアントヘッダー名。</span><span class="sxs-lookup"><span data-stu-id="533d8-188">The client header name.</span></span>
* <span data-ttu-id="533d8-189">(`HeaderConverter` プロパティを使用して) 証明書を読み込む方法。</span><span class="sxs-lookup"><span data-stu-id="533d8-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="533d8-190">カスタム web プロキシでは、証明書はカスタム要求ヘッダー (`X-SSL-CERT`など) として渡されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="533d8-191">これを使用するには `Startup.ConfigureServices`で証明書の転送を構成します。</span><span class="sxs-lookup"><span data-stu-id="533d8-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="533d8-192">`Startup.Configure` メソッドは、ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="533d8-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="533d8-193">`UseCertificateForwarding` は、`UseAuthentication` と `UseAuthorization`を呼び出す前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="533d8-194">別のクラスを使用して、検証ロジックを実装できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="533d8-195">この例では同じ自己署名証明書が使用されているため、使用できるのは証明書のみであることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="533d8-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="533d8-196">クライアント証明書とサーバー証明書の両方の拇印が一致しているかどうかを検証します。それ以外の場合は、証明書を使用して、認証に十分なものになります。</span><span class="sxs-lookup"><span data-stu-id="533d8-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="533d8-197">これは、`AddCertificate` メソッドの内部で使用されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="533d8-198">中間証明書または子証明書を使用している場合は、ここでサブジェクトまたは発行者を検証することもできます。</span><span class="sxs-lookup"><span data-stu-id="533d8-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="533d8-199">証明書と HttpClientHandler を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="533d8-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="533d8-200">HttpClientHandler は、HttpClient クラスのコンストラクターに直接追加できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-200">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="533d8-201">HttpClient のインスタンスを作成するときは注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="533d8-201">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="533d8-202">HttpClient は、要求ごとに証明書を送信します。</span><span class="sxs-lookup"><span data-stu-id="533d8-202">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="533d8-203">IHttpClientFactory から証明書と名前付き HttpClient を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="533d8-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="533d8-204">次の例では、ハンドラーから ClientCertificates プロパティを使用して、クライアント証明書を HttpClientHandler に追加しています。</span><span class="sxs-lookup"><span data-stu-id="533d8-204">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="533d8-205">このハンドラーは、HttpClient の名前付きインスタンスで ConfigurePrimaryHttpMessageHandler メソッドを使用して使用できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-205">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="533d8-206">これは、ConfigureServices メソッドの Startup クラスに設定されています。</span><span class="sxs-lookup"><span data-stu-id="533d8-206">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="533d8-207">IHttpClientFactory を使用すると、ハンドラーと証明書を使用して名前付きインスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-207">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="533d8-208">インスタンスを取得するには、Startup クラスで定義されているクライアントの名前を持つ CreateClient メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="533d8-208">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="533d8-209">HTTP 要求は、クライアントを使用して必要に応じて送信できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="533d8-210">正しい証明書がサーバーに送信されると、データが返されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="533d8-211">証明書または間違った証明書が送信されない場合は、HTTP 403 ステータスコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="533d8-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="533d8-212">PowerShell での証明書の作成</span><span class="sxs-lookup"><span data-stu-id="533d8-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="533d8-213">証明書の作成は、このフローを設定するうえで最も難しい部分です。</span><span class="sxs-lookup"><span data-stu-id="533d8-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="533d8-214">ルート証明書は、`New-SelfSignedCertificate` PowerShell コマンドレットを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="533d8-215">証明書を作成するときは、強力なパスワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="533d8-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="533d8-216">ここで示すように、`KeyUsageProperty` パラメーターと `KeyUsage` パラメーターを追加することが重要です。</span><span class="sxs-lookup"><span data-stu-id="533d8-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="533d8-217">ルート CA の作成</span><span class="sxs-lookup"><span data-stu-id="533d8-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="533d8-218">`-DnsName` パラメーター値は、アプリの配置ターゲットと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="533d8-219">たとえば、開発用の "localhost" などです。</span><span class="sxs-lookup"><span data-stu-id="533d8-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="533d8-220">信頼されたルートにインストールする</span><span class="sxs-lookup"><span data-stu-id="533d8-220">Install in the trusted root</span></span>

<span data-ttu-id="533d8-221">ルート証明書は、ホストシステムで信頼されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="533d8-222">証明機関によって作成されていないルート証明書は、既定では信頼されません。</span><span class="sxs-lookup"><span data-stu-id="533d8-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="533d8-223">次のリンクを使用して、Windows でこれを実現する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="533d8-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="533d8-224">中間証明機関の証明書</span><span class="sxs-lookup"><span data-stu-id="533d8-224">Intermediate certificate</span></span>

<span data-ttu-id="533d8-225">ルート証明書から中間証明書を作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="533d8-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="533d8-226">これはすべてのユースケースで必須ではありませんが、多くの証明書を作成したり、証明書のグループをアクティブ化または無効にしたりする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="533d8-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="533d8-227">`TextExtension` パラメーターは、証明書の基本的な制約のパスの長さを設定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="533d8-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="533d8-228">その後、中間証明書を Windows ホストシステムの信頼された中間証明書に追加できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="533d8-229">中間証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="533d8-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="533d8-230">子証明書は、中間証明書から作成できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="533d8-231">これは end エンティティであり、さらに子証明書を作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="533d8-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="533d8-232">ルート証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="533d8-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="533d8-233">子証明書は、ルート証明書から直接作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="533d8-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="533d8-234">ルート中間証明書の例-証明書</span><span class="sxs-lookup"><span data-stu-id="533d8-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="533d8-235">ルート証明書、中間証明書、または子証明書を使用する場合は、必要に応じて拇印または PublicKey を使用して証明書を検証できます。</span><span class="sxs-lookup"><span data-stu-id="533d8-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
