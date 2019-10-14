---
title: パフォーマンス診断ツール
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスの問題を診断するための便利なツールです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: d273897b9ad26d57eb94b196b58f14019a96d07d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815623"
---
# <a name="performance-diagnostic-tools"></a>パフォーマンスの診断ツール

作成者: [Mike Rousos](https://github.com/mjrousos)

この記事では、ASP.NET Core でのパフォーマンスの問題を診断するためのツールを紹介します。

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio 診断ツール

[プロファイリングと診断ツール](/visualstudio/profiling)パフォーマンスの問題の調査を開始点としては、Visual Studio に組み込まれています。 これらのツールとは、強力で、Visual Studio 開発環境から使用すると便利です。 ツールは、ASP.NET Core アプリでの CPU 使用率、メモリ使用量とパフォーマンス イベントの分析を使用できます。 組み込みの中では開発時に簡単にプロファイルします。

詳細については[Visual Studio ドキュメント](/visualstudio/profiling/profiling-overview)します。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview)アプリの詳細なパフォーマンス データを提供します。 Application Insights は、自動的に反応率、エラー率、依存関係の応答時間に関するデータを収集します。 Application Insights では、アプリにカスタム イベントとメトリックが特定のログ記録をサポートします。

Azure Application Insights では、監視対象のアプリで洞察を提供する複数の方法を提供します。

- [アプリケーション マップ](/azure/application-insights/app-insights-app-map)– 分散アプリのすべてのコンポーネント間でスポット パフォーマンスのボトルネックや障害ホット スポットに役立ちます。
- [Azure メトリックス エクスプ ローラー](/azure/azure-monitor/platform/metrics-getting-started)はプロット グラフ、傾向を視覚的に関連付けることができる Microsoft Azure ポータルのコンポーネントで急増し急減メトリック内の値を調査します。
- [Application Insights ポータルの [パフォーマンス] ブレード](/azure/application-insights/app-insights-tutorial-performance):

  - 監視対象のアプリでは、さまざまな操作のパフォーマンスの詳細を示しています。
  - すべての部分と依存関係を長い期間に影響を確認する 1 つの操作へのドリル ダウンできます。
  - パフォーマンス トレースはオンデマンドで収集するには、ここでは、Profiler を起動できます。

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler)定期的かつオンデマンドでの .NET アプリのプロファイリングを使用します。  Azure ポータルの表示では、呼び出し履歴とホット パスのパフォーマンス トレースをキャプチャします。 PerfView を使用して、詳細な分析のトレース ファイルをダウンロードすることもできます。

Application Insights は、さまざまな環境で使用できます。

- Azure で動作するように最適化します。
- 運用、開発、およびステージング環境で機能します。
- ローカルで動作[Visual Studio](/azure/application-insights/app-insights-visual-studio)または他のホスト環境にします。

詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview)は .NET のパフォーマンスの問題を診断するためには、具体的には、.NET チームによって作成されたパフォーマンス分析ツールです。 PerfView は、CPU 使用率、メモリおよび GC の動作、パフォーマンス イベント、およびウォール クロック時間の分析を使用できます。

PerfView および開始する方法の詳細については、 [PerfView のチュートリアル ビデオ](https://channel9.msdn.com/Series/PerfView-Tutorial)またはツールで使用可能なユーザー ガイドを参照してまたは[github](https://github.com/Microsoft/perfview)します。

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows パフォーマンス ツールキット](/windows-hardware/test/wpt/)(WPT) は、2 つのコンポーネントで構成されています。Windows Performance Recorder (WPR) と Windows パフォーマンス アナライザー (WPA)。 ツールは、Windows オペレーティング システムおよびアプリの詳細なパフォーマンス プロファイルを生成します。 WPT がデータを視覚化するための豊富な方法が、そのデータの収集は PerfView のよりも低い。

## <a name="perfcollect"></a>PerfCollect

PerfView が .NET シナリオ用の便利なパフォーマンス分析ツールの中でしか実行できません Windows のため、これを使用して、Linux 環境で実行されている ASP.NET Core アプリからトレースを収集することはできません。

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)ネイティブ Linux のプロファイリング ツールを使用する bash スクリプトは、([Perf](https://perf.wiki.kernel.org/index.php/Main_Page)と[LTTng](https://lttng.org/)) PerfView で分析できる Linux 上のトレースを収集します。 PerfCollect は、PerfView を直接使用することはできません、Linux 環境でパフォーマンスの問題を表示する場合に便利です。 代わりに、PerfCollect すると、PerfView を使用して Windows コンピューター上で、分析し、.NET Core アプリからトレースを収集できます。

インストールして PerfCollect の使用方法の詳細については、使用可能な[github](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)します。

## <a name="other-third-party-performance-tools"></a>その他のサード パーティ製のパフォーマンス ツール

次に、.NET Core アプリケーションのパフォーマンス調査に役立ついくつかのサードパーティ製のパフォーマンス ツールの一覧を表示します。

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace、dotMemory jetbrains
- Intel から VTune
