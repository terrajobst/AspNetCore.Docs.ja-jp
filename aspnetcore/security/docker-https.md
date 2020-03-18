---
title: HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする
author: rick-anderson
description: HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: f2a615093e7b1190962bd1c6ecbcc63f65bfbe7e
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511588"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="2bb39-103">HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする</span><span class="sxs-lookup"><span data-stu-id="2bb39-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="2bb39-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2bb39-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2bb39-105">[既定では、](/aspnet/core/security/enforcing-ssl)ASP.NET Core で HTTPS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2bb39-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="2bb39-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS)は、信頼、id、および暗号化のための[証明書](https://en.wikipedia.org/wiki/Public_key_certificate)に依存します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="2bb39-107">このドキュメントでは、HTTPS で事前に構築されたコンテナーイメージを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="2bb39-108">開発シナリオについては、「 [HTTPS 経由の Docker を使用した ASP.NET Core アプリケーションの開発](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb39-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="2bb39-109">このサンプルでは、docker [17.06](https://docs.docker.com/release-notes/docker-ce)以降の[docker クライアント](https://www.docker.com/products/docker)が必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2bb39-110">前提条件</span><span class="sxs-lookup"><span data-stu-id="2bb39-110">Prerequisites</span></span>

<span data-ttu-id="2bb39-111">このドキュメントの一部の手順では、 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download)以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="2bb39-112">証明書</span><span class="sxs-lookup"><span data-stu-id="2bb39-112">Certificates</span></span>

<span data-ttu-id="2bb39-113">ドメインの[運用ホスト](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)には、[証明機関](https://wikipedia.org/wiki/Certificate_authority)からの証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="2bb39-114">[Let's Encrypt](https://letsencrypt.org/)は、無料の証明書を提供する証明機関です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="2bb39-115">このドキュメントでは、`localhost`で事前構築されたイメージをホストするために[自己署名の開発証明書](https://en.wikipedia.org/wiki/Self-signed_certificate)を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="2bb39-116">手順は、実稼働証明書の使用に似ています。</span><span class="sxs-lookup"><span data-stu-id="2bb39-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="2bb39-117">実稼働証明書の場合:</span><span class="sxs-lookup"><span data-stu-id="2bb39-117">For production certs:</span></span>

* <span data-ttu-id="2bb39-118">`dotnet dev-certs` ツールは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2bb39-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="2bb39-119">手順で使用した場所に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2bb39-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="2bb39-120">任意の場所を使用できますが、証明書をサイトディレクトリ内に格納することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="2bb39-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="2bb39-121">次のセクションに記載されている手順では、Docker の `-v` コマンドラインオプションを使用して、コンテナーに証明書をマウントします。</span><span class="sxs-lookup"><span data-stu-id="2bb39-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="2bb39-122">*Dockerfile*で `COPY` コマンドを使用してコンテナーイメージに証明書を追加することもできますが、この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="2bb39-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="2bb39-123">証明書をイメージにコピーすることは、次の理由から推奨されません。</span><span class="sxs-lookup"><span data-stu-id="2bb39-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="2bb39-124">開発者の証明書を使用したテストで同じイメージを使用するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="2bb39-125">実稼働証明書を使用してホストする場合、同じイメージを使用するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="2bb39-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="2bb39-126">証明書の公開には大きなリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="2bb39-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="2bb39-127">HTTPS を使用した既成のコンテナーイメージの実行</span><span class="sxs-lookup"><span data-stu-id="2bb39-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="2bb39-128">オペレーティングシステムの構成については、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="2bb39-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="2bb39-129">Linux コンテナーを使用した Windows</span><span class="sxs-lookup"><span data-stu-id="2bb39-129">Windows using Linux containers</span></span>

<span data-ttu-id="2bb39-130">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2bb39-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2bb39-131">上記のコマンドの `{ password here }` をパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb39-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2bb39-132">HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2bb39-133">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb39-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="2bb39-134">macOS または Linux</span><span class="sxs-lookup"><span data-stu-id="2bb39-134">macOS or Linux</span></span>

<span data-ttu-id="2bb39-135">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2bb39-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2bb39-136">`dotnet dev-certs https --trust` は、macOS と Windows でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2bb39-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="2bb39-137">ディストリビューションでサポートされている方法で、Linux 上の証明書を信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb39-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="2bb39-138">ブラウザーで証明書を信頼する必要があると考えられます。</span><span class="sxs-lookup"><span data-stu-id="2bb39-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="2bb39-139">上記のコマンドの `{ password here }` をパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb39-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2bb39-140">HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2bb39-141">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb39-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="2bb39-142">Windows コンテナーを使用した windows</span><span class="sxs-lookup"><span data-stu-id="2bb39-142">Windows using Windows containers</span></span>

<span data-ttu-id="2bb39-143">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2bb39-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2bb39-144">上記のコマンドの `{ password here }` をパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb39-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2bb39-145">HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb39-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2bb39-146">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb39-146">The password must match the password used for the certificate.</span></span>
