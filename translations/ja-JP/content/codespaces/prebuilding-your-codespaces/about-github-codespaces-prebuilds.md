---
title: GitHub Codespaces のプレビルドについて
shortTitle: About prebuilds
intro: Codespaces プレビルドは、大規模または複雑なリポジトリの新しい codespace の作成を高速化するのに役立ちます。
versions:
  fpt: '*'
  ghec: '*'
topics:
  - Codespaces
product: '{% data reusables.gated-features.codespaces %}'
redirect_from:
  - /codespaces/prebuilding-your-codespaces/about-codespaces-prebuilds
ms.openlocfilehash: f08b4a1b691f5eaa55fe9126e1d74feeeff2188e
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2022
ms.locfileid: '147548105'
---
## 概要

codespace をプレビルドすると、生産性が高まり、codespace にすばやくアクセスできます。特に、リポジトリが大規模であるか、複雑であり、新しい codespace の開始に現状、2 分以上かかる場合に有効です。 これは、プロジェクト用の codespace を作成する前に、ソース コード、エディター拡張機能、プロジェクトの依存関係、コマンド、構成が既にダウンロード、インストール、適用されているためです。 プレビルドは、codespace のための "準備ができている" テンプレートと考えてください。 

既定では、リポジトリに変更をプッシュするたびに、{% data variables.product.prodname_github_codespaces %} は {% data variables.product.prodname_actions %} を使って、プレビルドを自動的に更新します。

リポジトリの特定のブランチ、特定の開発コンテナー構成ファイル、ご利用のリージョンでプレビルドを使用できる場合、codespace を作成するときに、マシンの種類の一覧に、"{% octicon "zap" aria-label="The zap icon" %} プレビルド準備完了" というラベルが表示されます。 プレビルドがまだ作成中の場合、"{% octicon "history" aria-label="The history icon" %} プレビルド進行中" ラベルが表示されます。 詳細については、「[codespace を作成する](/codespaces/developing-in-codespaces/creating-a-codespace#creating-a-codespace)」を参照してください。

![コンピューターの種類を選択するためのダイアログ ボックス](/assets/images/help/codespaces/choose-custom-machine-type.png)

## プレビルドのプロセス

プレビルドを作成するには、プレビルドの構成を設定します。 構成を保存すると、{% data variables.product.prodname_actions %} ワークフローが実行され、必要な各ビルド (プレビルドごとに 1 つのワークフロー) が作成されます。 ワークフローは、構成のプレビルドを更新する必要がある場合にも実行されます。 これは、スケジュールされた間隔、プレビルドが有効なリポジトリへのプッシュ時、または開発コンテナーの構成を変更するときに発生する可能性があります。 詳細については、「[プレビルドの構成](/codespaces/prebuilding-your-codespaces/configuring-prebuilds#configuring-a-prebuild)」を参照してください。  

プレビルドの構成ワークフローが実行されると、{% data variables.product.prodname_dotcom %} により一時的な codespace が作成され、`devcontainer.json` ファイル内の任意の `onCreateCommand` および `updateContentCommand` コマンドまでの設定操作が実行されます。 プレビルドの作成時に `postCreateCommand` コマンドは実行されません。 これらのコマンドについて詳しくは、{% data variables.product.prodname_vscode_shortname %} ドキュメントの「[`devcontainer.json` リファレンス](https://code.visualstudio.com/docs/remote/devcontainerjson-reference#_devcontainerjson-properties)」を参照してください。 生成されたコンテナーのスナップショットが取得され、格納されます。

プレビルドから codespace を作成すると、{% data variables.product.prodname_dotcom %} によりストレージから既存のコンテナー スナップショットがダウンロードされ、それを新しい仮想マシンにデプロイし、開発コンテナー構成で指定された残りのコマンドを完了します。 リポジトリの複製など、多くの操作が既に実行されているため、プレビルドから codespace を作成する方が、プレビルドなしで作成するよりも大幅に速くなる可能性があります。 これは、リポジトリが大きい場合や `onCreateCommand` コマンドの実行に時間がかかる場合に当てはまります。

## {% data variables.product.prodname_codespaces %} のプレビルドに対する支払いについて

{% data reusables.codespaces.billing-for-prebuilds-default %} {% data variables.product.prodname_codespaces %} ストレージの価格について詳しくは、「[{% data variables.product.prodname_github_codespaces %} の支払いについて](/billing/managing-billing-for-github-codespaces/about-billing-for-github-codespaces)」を参照してください。 

{% data reusables.codespaces.billing-for-prebuilds-reducing %} 

プレビルドを使用して作成された codespace の使用は、通常の codespace と同じレートで課金されます。

## プレビルドが有効なブランチへの変更のプッシュについて

既定では、プレビルドの構成があるブランチにプッシュするたびに、{% data variables.product.prodname_dotcom %} で管理される Actions ワークフローが実行され、プレビルドが更新されます。 関連するリポジトリの開発コンテナー構成に影響を与える変更が行われたのでない限り、プレビルド ワークフローには、特定のプレビルド構成に対して一度に 1 つのワークフロー実行というコンカレンシー制限があります。 詳細については、「[開発コンテナーの概要](/codespaces/setting-up-your-project-for-codespaces/introduction-to-dev-containers)」をご覧ください。 実行が既に進行中の場合は、現在の実行が完了した後、最後にキューに登録されたワークフロー実行が次に実行されます。 

プッシュのたびにプレビルドが更新されるように設定すると、リポジトリへのプッシュが非常に多い場合、プレビルドは少なくとも、プレビルド ワークフローの実行に必要なだけ更新されます。 つまり、ワークフローの実行が完了するまでに通常 1 時間かかるとすると、実行が成功する場合は約 1 時間ごと、ブランチの開発コンテナー構成を変更するプッシュがあった場合はさらに頻繁に、リポジトリに対してプレビルドが作成されます。

たとえば、プレビルドが構成されたブランチに対し、プッシュが 5 回立て続けに行われるとします。 この状況では、次のようになります。

* 1 番目のプッシュに対するワークフローの実行が開始され、プレビルドが更新されます。
* 残りの 4 つのプッシュが開発コンテナーの構成に影響しない場合、これらに対するワークフロー実行は "保留中" 状態でキューに入れられます。 
  
  残りの 4 つのプッシュのいずれかが開発コンテナーの構成を変更する場合は、サービスはそれをスキップせず、プレビルド作成ワークフローを直ちに実行して、成功した場合はそれに応じてプレビルドを更新します。 

* 最初の実行が完了すると、プッシュ 2、3、4 に対するワークフローの実行は取り消されて、最後にキューに入れられたワークフロー (プッシュ 5 に対する) が実行され、プレビルドが更新されます。 
