---
title: ASP.NET Core のホストと展開
author: rick-anderson
description: ホスティング環境を設定し、ASP.NET Core アプリを展開する方法を学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 464d19bd63e1f0f06bd7d218e7644afde04a5672
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644150"
---
# <a name="host-and-deploy-aspnet-core"></a>ASP.NET Core のホストと展開

::: moniker range=">= aspnetcore-2.2"

通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。

* ホスティング サーバー上のフォルダーに発行されたアプリを展開します。
* 要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。
* リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。

## <a name="publish-to-a-folder"></a>フォルダーに発行する

[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。 Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。

### <a name="folder-contents"></a>フォルダーの内容

*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。

.NET Core アプリは、*自己完結型展開*または*フレームワーク依存展開*として発行できます。 アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。 アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。 既定の展開モデルはフレームワークに依存します。 詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。

*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。 詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。

## <a name="set-up-a-process-manager"></a>プロセス マネージャーを設定する

ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。 起動と再起動を自動化するには、プロセス マネージャーが必要です。 ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows サービス](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>リバース プロキシを設定する

アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。 リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。

いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。 詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。

## <a name="proxy-server-and-load-balancer-scenarios"></a>プロキシ サーバーとロード バランサーのシナリオ

プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。 詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Visual Studio と MSBuild を使用してデプロイを自動化する

多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。 たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。 Visual Studio では Web 展開で MSBuild を使用します。この MSBuild は、展開時に他の多くの作業を行うためにカスタマイズすることができます。 詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。

[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。 Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。 詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。

## <a name="publish-to-azure"></a>Azure に発行する

Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。 その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。

## <a name="publish-with-msdeploy-on-windows"></a>Windows での MSDeploy を使用した発行

Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。

## <a name="internet-information-services-iis"></a>インターネット インフォメーション サービス (IIS)

*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。

## <a name="host-in-a-web-farm"></a>Web ファームでのホスト

Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。

## <a name="host-on-docker"></a>Docker でのホスト

詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。

## <a name="perform-health-checks"></a>正常性チェックを実行する

アプリとその依存関係に対して正常性チェックを実行するには、正常性チェックのミドルウェアを使用します。 詳細については、「<xref:host-and-deploy/health-checks>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:test/troubleshoot>
* [ASP.NET ホスティング](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。

* ホスティング サーバー上のフォルダーに発行されたアプリを展開します。
* 要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。
* リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。

## <a name="publish-to-a-folder"></a>フォルダーに発行する

[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。 Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。

### <a name="folder-contents"></a>フォルダーの内容

*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。

.NET Core アプリは、*自己完結型展開*または*フレームワーク依存展開*として発行できます。 アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。 アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。 既定の展開モデルはフレームワークに依存します。 詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。

*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。 詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。

## <a name="set-up-a-process-manager"></a>プロセス マネージャーを設定する

ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。 起動と再起動を自動化するには、プロセス マネージャーが必要です。 ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows サービス](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>リバース プロキシを設定する

アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。 リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。

いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。 詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。

## <a name="proxy-server-and-load-balancer-scenarios"></a>プロキシ サーバーとロード バランサーのシナリオ

プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。 詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Visual Studio と MSBuild を使用してデプロイを自動化する

多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。 たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。 Visual Studio では Web 展開で MSBuild を使用します。この MSBuild は、展開時に他の多くの作業を行うためにカスタマイズすることができます。 詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。

[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。 Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。 詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。

## <a name="publish-to-azure"></a>Azure に発行する

Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。 その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。

## <a name="publish-with-msdeploy-on-windows"></a>Windows での MSDeploy を使用した発行

Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。

## <a name="internet-information-services-iis"></a>インターネット インフォメーション サービス (IIS)

*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。

## <a name="host-in-a-web-farm"></a>Web ファームでのホスト

Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。

## <a name="host-on-docker"></a>Docker でのホスト

詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:test/troubleshoot>
* [ASP.NET ホスティング](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
