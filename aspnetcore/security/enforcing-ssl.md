---
title: ASP.NET Core に HTTPS を適用する
author: rick-anderson
description: ASP.NET Core web アプリで HTTPS/TLS を要求する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 8474b8f12d3e08e47a4cc9b20543d2a9e6e91998
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011071"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="5f982-103">ASP.NET Core に HTTPS を適用する</span><span class="sxs-lookup"><span data-stu-id="5f982-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="5f982-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f982-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5f982-105">このドキュメントでは次の方法について説明します:</span><span class="sxs-lookup"><span data-stu-id="5f982-105">This document shows how to:</span></span>

* <span data-ttu-id="5f982-106">すべての要求に HTTPS を必要とさせる。</span><span class="sxs-lookup"><span data-stu-id="5f982-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="5f982-107">すべての HTTP 要求を HTTPS にリダイレクトさせる。</span><span class="sxs-lookup"><span data-stu-id="5f982-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="5f982-108">API がないと、クライアントが最初の要求で機微なデータを送信できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="5f982-109">API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="5f982-109">API projects</span></span>
>
> <span data-ttu-id="5f982-110">機密情報を受け取る Web Api では[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5f982-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="5f982-111">`RequireHttpsAttribute` はブラウザーを HTTP から HTTPS へリダイレクトするために HTTP ステータス コードを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="5f982-112">API クライアントはこれを理解しない場合や、HTTP から HTTPS へのリダイレクトに従わない場合があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="5f982-113">このようなクライアントは、HTTP 経由で情報を送信することがあります。</span><span class="sxs-lookup"><span data-stu-id="5f982-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="5f982-114">Web API は次のいずれかの対策を講じるべきです:</span><span class="sxs-lookup"><span data-stu-id="5f982-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="5f982-115">HTTP をリッスンしない。</span><span class="sxs-lookup"><span data-stu-id="5f982-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="5f982-116">ステータス コード 400 (無効な要求) で接続を閉じ、要求に応答しない。</span><span class="sxs-lookup"><span data-stu-id="5f982-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="5f982-117">HSTS と API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="5f982-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="5f982-118">HSTS は一般にブラウザー専用の命令であるため、既定の API プロジェクトには[Hsts](#hsts)は含まれません。</span><span class="sxs-lookup"><span data-stu-id="5f982-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="5f982-119">電話やデスクトップアプリなどの他の呼び出し元は、命令に従い**ません**。</span><span class="sxs-lookup"><span data-stu-id="5f982-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="5f982-120">ブラウザー内でも、HTTP 経由の API に対して認証された単一の呼び出しにより、セキュリティで保護されていないネットワークに対するリスクが生じます。</span><span class="sxs-lookup"><span data-stu-id="5f982-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="5f982-121">セキュリティで保護された方法は、HTTPS 経由でのみリッスンして応答するように API プロジェクトを構成することです。</span><span class="sxs-lookup"><span data-stu-id="5f982-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="5f982-122">API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="5f982-122">API projects</span></span>
>
> <span data-ttu-id="5f982-123">機密情報を受け取る Web Api では[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5f982-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="5f982-124">`RequireHttpsAttribute` はブラウザーを HTTP から HTTPS へリダイレクトするために HTTP ステータス コードを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="5f982-125">API クライアントはこれを理解しない場合や、HTTP から HTTPS へのリダイレクトに従わない場合があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="5f982-126">このようなクライアントは、HTTP 経由で情報を送信することがあります。</span><span class="sxs-lookup"><span data-stu-id="5f982-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="5f982-127">Web API は次のいずれかの対策を講じるべきです:</span><span class="sxs-lookup"><span data-stu-id="5f982-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="5f982-128">HTTP をリッスンしない。</span><span class="sxs-lookup"><span data-stu-id="5f982-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="5f982-129">ステータス コード 400 (無効な要求) で接続を閉じ、要求に応答しない。</span><span class="sxs-lookup"><span data-stu-id="5f982-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="5f982-130">HTTPS を要求する</span><span class="sxs-lookup"><span data-stu-id="5f982-130">Require HTTPS</span></span>

<span data-ttu-id="5f982-131">Web アプリの運用 ASP.NET Core では次のものを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5f982-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="5f982-132">HTTP 要求を https<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>にリダイレクトする https リダイレクトミドルウェア ()。</span><span class="sxs-lookup"><span data-stu-id="5f982-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="5f982-133">HSTS ミドルウェア ([Usehsts](#http-strict-transport-security-protocol-hsts)) を介して、HTTP Strict Transport Security Protocol (hsts) ヘッダーをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="5f982-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="5f982-134">リバースプロキシ構成で展開されたアプリは、プロキシが接続セキュリティ (HTTPS) を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="5f982-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="5f982-135">プロキシが HTTPS リダイレクトも処理する場合は、HTTPS リダイレクトミドルウェアを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5f982-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="5f982-136">また、プロキシサーバーが HSTS ヘッダーの書き込みも処理する場合 ( [IIS 10.0 (1709) 以降のネイティブ HSTS のサポート](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)など)、アプリでは Hsts ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5f982-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="5f982-137">詳細については、「[プロジェクト作成時の HTTPS/HSTS のオプトアウト](#opt-out-of-httpshsts-on-project-creation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="5f982-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="5f982-138">UseHttpsRedirection</span></span>

<span data-ttu-id="5f982-139">次のコードは`UseHttpsRedirection` 、 `Startup`クラス内でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5f982-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="5f982-140">前の強調表示されているコード:</span><span class="sxs-lookup"><span data-stu-id="5f982-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="5f982-141">既定の[HttpsRedirectionOptions statuscode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="5f982-142">`ASPNETCORE_HTTPS_PORT` 環境変数または [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature) でオーバーライドされない限り、既定の [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="5f982-143">永続的なリダイレクトではなく、一時的なリダイレクトを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5f982-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="5f982-144">リンクキャッシュを使用すると、開発環境で不安定な動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="5f982-145">アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、「運用環境[で永続的なリダイレクトを構成](#configure-permanent-redirects-in-production)する」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="5f982-146">[Hsts](#http-strict-transport-security-protocol-hsts)を使用して、セキュリティで保護されたリソース要求のみをアプリケーションに送信する (運用環境のみ) ことをクライアントに通知することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5f982-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="5f982-147">ポートの構成</span><span class="sxs-lookup"><span data-stu-id="5f982-147">Port configuration</span></span>

<span data-ttu-id="5f982-148">ミドルウェアがセキュリティで保護されていない要求を HTTPS にリダイレクトするには、ポートが使用可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="5f982-149">使用可能なポートがない場合:</span><span class="sxs-lookup"><span data-stu-id="5f982-149">If no port is available:</span></span>

* <span data-ttu-id="5f982-150">HTTPS へのリダイレクトは行われません。</span><span class="sxs-lookup"><span data-stu-id="5f982-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="5f982-151">ミドルウェアは、"リダイレクト用の https ポートを特定できませんでした" という警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="5f982-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="5f982-152">次のいずれかの方法を使用して、HTTPS ポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="5f982-153">[HttpsRedirectionOptions](#options)を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="5f982-154">`https_port` [ホスト設定](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="5f982-155">ホスト構成。</span><span class="sxs-lookup"><span data-stu-id="5f982-155">In host configuration.</span></span>
  * <span data-ttu-id="5f982-156">`ASPNETCORE_HTTPS_PORT`環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="5f982-157">次のようにして、 *appsettings*にトップレベルのエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="5f982-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="5f982-158">[ASPNETCORE_URLS 環境変数](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)を使用して、セキュリティで保護されたスキームのポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="5f982-159">環境変数によってサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="5f982-159">The environment variable configures the server.</span></span> <span data-ttu-id="5f982-160">ミドルウェアは、を使用<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>して HTTPS ポートを間接的に検出します。</span><span class="sxs-lookup"><span data-stu-id="5f982-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="5f982-161">リバースプロキシの展開では、この方法は使用できません。</span><span class="sxs-lookup"><span data-stu-id="5f982-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="5f982-162">`https_port` [ホスト設定](xref:fundamentals/host/web-host#https-port)を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="5f982-163">ホスト構成。</span><span class="sxs-lookup"><span data-stu-id="5f982-163">In host configuration.</span></span>
  * <span data-ttu-id="5f982-164">`ASPNETCORE_HTTPS_PORT`環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="5f982-165">次のようにして、 *appsettings*にトップレベルのエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="5f982-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="5f982-166">[ASPNETCORE_URLS 環境変数](xref:fundamentals/host/web-host#server-urls)を使用して、セキュリティで保護されたスキームのポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="5f982-167">環境変数によってサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="5f982-167">The environment variable configures the server.</span></span> <span data-ttu-id="5f982-168">ミドルウェアは、を使用<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>して HTTPS ポートを間接的に検出します。</span><span class="sxs-lookup"><span data-stu-id="5f982-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="5f982-169">リバースプロキシの展開では、この方法は使用できません。</span><span class="sxs-lookup"><span data-stu-id="5f982-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="5f982-170">開発では、 *launchsettings. json*に HTTPS URL を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="5f982-171">IIS Express が使用されている場合は、HTTPS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5f982-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="5f982-172">[Kestrel](xref:fundamentals/servers/kestrel) [サーバーまたは http.sys サーバー](xref:fundamentals/servers/httpsys)の公開エッジデプロイの HTTPS URL エンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="5f982-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="5f982-173">アプリケーションで使用される**HTTPS ポートは1つ**だけです。</span><span class="sxs-lookup"><span data-stu-id="5f982-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="5f982-174">ミドルウェアは、を使用<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>してポートを検出します。</span><span class="sxs-lookup"><span data-stu-id="5f982-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="5f982-175">リバースプロキシ構成でアプリを実行する場合、 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>は使用できません。</span><span class="sxs-lookup"><span data-stu-id="5f982-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="5f982-176">このセクションで説明する他の方法のいずれかを使用して、ポートを設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="5f982-177">エッジデプロイ</span><span class="sxs-lookup"><span data-stu-id="5f982-177">Edge deployments</span></span> 

<span data-ttu-id="5f982-178">Kestrel または http.sys が公開エッジサーバーとして使用されている場合、Kestrel または http.sys が両方でリッスンするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="5f982-179">クライアントがリダイレクトされるセキュリティで保護されたポート (通常、運用環境では443、開発では 5001)。</span><span class="sxs-lookup"><span data-stu-id="5f982-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="5f982-180">セキュリティで保護されていないポート (通常、運用環境では80、開発では 5000)。</span><span class="sxs-lookup"><span data-stu-id="5f982-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="5f982-181">セキュリティで保護されていない要求をアプリケーションが受信し、セキュリティで保護されたポートにクライアントをリダイレクトするには、セキュリティで保護されていないポートにクライアントがアクセスできる必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="5f982-182">詳細については、「 [Kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)」または<xref:fundamentals/servers/httpsys>「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="5f982-183">展開シナリオ</span><span class="sxs-lookup"><span data-stu-id="5f982-183">Deployment scenarios</span></span>

<span data-ttu-id="5f982-184">クライアントとサーバーの間のファイアウォールでは、トラフィック用の通信ポートも開いている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="5f982-185">リバースプロキシ構成で要求が転送される場合は、HTTPS リダイレクトミドルウェアを呼び出す前に、転送された[ヘッダーミドルウェア](xref:host-and-deploy/proxy-load-balancer)を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="5f982-186">転送されたヘッダー `Request.Scheme`ミドルウェアは、 `X-Forwarded-Proto`ヘッダーを使用してを更新します。</span><span class="sxs-lookup"><span data-stu-id="5f982-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="5f982-187">ミドルウェアは、リダイレクト Uri とその他のセキュリティポリシーを正しく動作させることを許可します。</span><span class="sxs-lookup"><span data-stu-id="5f982-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="5f982-188">転送ヘッダーミドルウェアが使用されていない場合、バックエンドアプリは正しいスキームを受信せず、リダイレクトループで終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="5f982-189">一般的なエンドユーザーエラーメッセージは、リダイレクトされた回数が多すぎることを示しています。</span><span class="sxs-lookup"><span data-stu-id="5f982-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="5f982-190">Azure App Service にデプロイする場合は、チュートリアルの[ガイダンスに従ってください。既存のカスタム SSL 証明書を Azure Web Apps にバインドする](/azure/app-service/app-service-web-tutorial-custom-ssl)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5f982-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="5f982-191">オプション</span><span class="sxs-lookup"><span data-stu-id="5f982-191">Options</span></span>

<span data-ttu-id="5f982-192">次の強調表示されたコードは、 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection)を呼び出してミドルウェアオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="5f982-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="5f982-193">を`AddHttpsRedirection`呼び出す必要があるのは、 `HttpsPort`また`RedirectStatusCode`はの値を変更することだけです。</span><span class="sxs-lookup"><span data-stu-id="5f982-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="5f982-194">前の強調表示されているコード:</span><span class="sxs-lookup"><span data-stu-id="5f982-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="5f982-195">[HttpsRedirectionOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*)をに<xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>設定します。これは既定値です。</span><span class="sxs-lookup"><span data-stu-id="5f982-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="5f982-196"><xref:Microsoft.AspNetCore.Http.StatusCodes> へ`RedirectStatusCode`の割り当てには、クラスのフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="5f982-197">HTTPS ポートを5001に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-197">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="5f982-198">既定値は443です。</span><span class="sxs-lookup"><span data-stu-id="5f982-198">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="5f982-199">運用環境での永続的なリダイレクトの構成</span><span class="sxs-lookup"><span data-stu-id="5f982-199">Configure permanent redirects in production</span></span>

<span data-ttu-id="5f982-200">ミドルウェアは、既定ですべてのリダイレクトを使用して[Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)を送信します。</span><span class="sxs-lookup"><span data-stu-id="5f982-200">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="5f982-201">アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、非開発環境の条件付きチェックでミドルウェアオプションの構成をラップします。</span><span class="sxs-lookup"><span data-stu-id="5f982-201">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f982-202">*Startup.cs*でサービスを構成する場合:</span><span class="sxs-lookup"><span data-stu-id="5f982-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5f982-203">*Startup.cs*でサービスを構成する場合:</span><span class="sxs-lookup"><span data-stu-id="5f982-203">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="5f982-204">HTTPS リダイレクトミドルウェアの代替アプローチ</span><span class="sxs-lookup"><span data-stu-id="5f982-204">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="5f982-205">HTTPS リダイレクトミドルウェア (`UseHttpsRedirection`) を使用する代わりに、URL リライトミドルウェア (`AddRedirectToHttps`) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f982-205">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="5f982-206">`AddRedirectToHttps`また、リダイレクトの実行時に状態コードとポートを設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f982-206">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="5f982-207">さらに詳しい情報は、[URL 書き換えミドルウェア](xref:fundamentals/url-rewriting) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="5f982-208">追加のリダイレクト規則を必要とせずに https にリダイレクトする場合は、このトピック`UseHttpsRedirection`で説明する https リダイレクトミドルウェア () を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5f982-208">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="5f982-209">HTTP Strict Transport Security Protocol (HSTS)</span><span class="sxs-lookup"><span data-stu-id="5f982-209">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="5f982-210">[Owasp](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)では、 [HTTP Strict Transport Security (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)は、応答ヘッダーを使用して web アプリによって指定されるオプトインセキュリティ拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="5f982-210">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="5f982-211">[HSTS をサポートするブラウザー](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support)がこのヘッダーを受け取ると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5f982-211">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="5f982-212">ブラウザーには、HTTP 経由の通信を送信できないようにするドメインの構成が格納されます。</span><span class="sxs-lookup"><span data-stu-id="5f982-212">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="5f982-213">ブラウザーは、HTTPS 経由のすべての通信を強制的に実行します。</span><span class="sxs-lookup"><span data-stu-id="5f982-213">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="5f982-214">ブラウザーによって、ユーザーが信頼されていない証明書や無効な証明書を使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="5f982-214">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="5f982-215">ブラウザーは、ユーザーがこのような証明書を一時的に信頼できるようにするプロンプトを無効にします。</span><span class="sxs-lookup"><span data-stu-id="5f982-215">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="5f982-216">HSTS はクライアントによって適用されるため、いくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-216">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="5f982-217">クライアントは HSTS をサポートしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-217">The client must support HSTS.</span></span>
* <span data-ttu-id="5f982-218">HSTS で HSTS ポリシーを確立するには、少なくとも1つの HTTPS 要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="5f982-218">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="5f982-219">アプリケーションは、すべての HTTP 要求を確認し、HTTP 要求をリダイレクトまたは拒否する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-219">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="5f982-220">ASP.NET Core 2.1 以降では、 `UseHsts`拡張メソッドを使用して hsts を実装します。</span><span class="sxs-lookup"><span data-stu-id="5f982-220">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="5f982-221">次のコードは`UseHsts` 、アプリが[開発モード](xref:fundamentals/environments)でない場合にを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5f982-221">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="5f982-222">`UseHsts`HSTS の設定はブラウザーによって高度なキャッシュが可能であるため、開発では推奨されません。</span><span class="sxs-lookup"><span data-stu-id="5f982-222">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="5f982-223">既定では`UseHsts` 、はローカルループバックアドレスを除外します。</span><span class="sxs-lookup"><span data-stu-id="5f982-223">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="5f982-224">初めて HTTPS を実装する運用環境では、 <xref:System.TimeSpan>メソッドのいずれかを使用して、初期の[HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*)を小さい値に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-224">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="5f982-225">HTTPS インフラストラクチャを HTTP に戻す必要がある場合に備えて、値を時間から1日以内に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-225">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="5f982-226">HTTPS 構成の持続性を確認したら、HSTS の最長有効期間の値を増やします。一般的に使用される値は1年です。</span><span class="sxs-lookup"><span data-stu-id="5f982-226">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="5f982-227">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5f982-227">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="5f982-228">Strict-Transport-Security ヘッダーのプリロードパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-228">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="5f982-229">プリロードは[RFC hsts 仕様](https://tools.ietf.org/html/rfc6797)の一部ではありませんが、web ブラウザーでは、新規インストール時に hsts サイトを事前に読み込むことがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5f982-229">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="5f982-230">詳細については、「[https://hstspreload.org/](https://hstspreload.org/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-230">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="5f982-231">HSTS ポリシーをホストサブドメインに適用する[includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5f982-231">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="5f982-232">厳密な-Transport-Security ヘッダーの最長有効期間パラメーターを60日に明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-232">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="5f982-233">設定されていない場合、既定値は30日です。</span><span class="sxs-lookup"><span data-stu-id="5f982-233">If not set, defaults to 30 days.</span></span> <span data-ttu-id="5f982-234">詳細については、「[最長有効期間」ディレクティブ](https://tools.ietf.org/html/rfc6797#section-6.1.1)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-234">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="5f982-235">除外`example.com`するホストの一覧にを追加します。</span><span class="sxs-lookup"><span data-stu-id="5f982-235">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="5f982-236">`UseHsts`次のループバックホストを除外します。</span><span class="sxs-lookup"><span data-stu-id="5f982-236">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="5f982-237">`localhost` は、次のとおりです。IPv4 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="5f982-237">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="5f982-238">`127.0.0.1` は、次のとおりです。IPv4 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="5f982-238">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="5f982-239">`[::1]` は、次のとおりです。IPv6 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="5f982-239">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="5f982-240">プロジェクト作成時の HTTPS/HSTS のオプトアウト</span><span class="sxs-lookup"><span data-stu-id="5f982-240">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="5f982-241">接続セキュリティがネットワークの公開エッジで処理されるバックエンドサービスのシナリオによっては、各ノードで接続セキュリティを構成する必要がない場合があります。</span><span class="sxs-lookup"><span data-stu-id="5f982-241">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="5f982-242">Visual Studio のテンプレートまたは[dotnet new](/dotnet/core/tools/dotnet-new)コマンドから生成された Web アプリでは、 [HTTPS リダイレクト](#require-https)と[hsts](#http-strict-transport-security-protocol-hsts)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="5f982-242">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="5f982-243">これらのシナリオを必要としないデプロイでは、テンプレートからアプリを作成するときに HTTPS/HSTS をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="5f982-243">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="5f982-244">HTTPS/HSTS をオプトアウトするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5f982-244">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f982-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f982-245">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5f982-246">**[HTTPS 用に構成]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="5f982-246">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![HTTPS 用に構成 チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション ダイアログボックスが表示されます。](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![HTTPS 用に構成 チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション ダイアログボックスが表示されます。](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f982-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f982-249">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="5f982-250">`--no-https` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f982-250">Use the `--no-https` option.</span></span> <span data-ttu-id="5f982-251">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5f982-251">For example</span></span>

```console
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="5f982-252">Windows および macOS で ASP.NET Core HTTPS 開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="5f982-252">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="5f982-253">.NET Core SDK には、HTTPS 開発証明書が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f982-253">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="5f982-254">証明書は、最初の実行エクスペリエンスの一部としてインストールされます。</span><span class="sxs-lookup"><span data-stu-id="5f982-254">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="5f982-255">たとえば、は`dotnet --info`次のような出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="5f982-255">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="5f982-256">.NET Core SDK をインストールすると、ローカル ユーザーの証明書ストアに ASP.NET Core HTTPS 開発証明書がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="5f982-256">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="5f982-257">証明書はインストールされていますが、信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="5f982-257">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="5f982-258">証明書を信頼するには、1回限りの手順を`dev-certs`実行して dotnet ツールを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f982-258">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="5f982-259">次のコマンドにより、`dev-certs` ツールに関するヘルプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f982-259">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="5f982-260">Docker 用の開発者証明書を設定する方法</span><span class="sxs-lookup"><span data-stu-id="5f982-260">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="5f982-261">[こちらの GitHub のイシュー](https://github.com/aspnet/AspNetCore.Docs/issues/6199)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f982-261">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="5f982-262">Windows Subsystem for Linux からの HTTPS 証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="5f982-262">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="5f982-263">Windows Subsystem for Linux (WSL) は、HTTPS 自己署名証明書を生成します。WSL 証明書を信頼するように Windows 証明書ストアを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5f982-263">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="5f982-264">次のコマンドを実行して、WSL によって生成された証明書をエクスポートします。`dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="5f982-264">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="5f982-265">WSL ウィンドウで、次のコマンドを実行します。`ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="5f982-265">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="5f982-266">上記のコマンドは、Linux が Windows の信頼された証明書を使用するように環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="5f982-266">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="5f982-267">追加情報</span><span class="sxs-lookup"><span data-stu-id="5f982-267">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="5f982-268">Apache を使用した Linux でのホスト ASP.NET Core:HTTPS の構成</span><span class="sxs-lookup"><span data-stu-id="5f982-268">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="5f982-269">Nginx を使用した Linux でのホスト ASP.NET Core:HTTPS の構成</span><span class="sxs-lookup"><span data-stu-id="5f982-269">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="5f982-270">IIS で SSL を設定する方法</span><span class="sxs-lookup"><span data-stu-id="5f982-270">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="5f982-271">OWASP HSTS ブラウザーサポート</span><span class="sxs-lookup"><span data-stu-id="5f982-271">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
