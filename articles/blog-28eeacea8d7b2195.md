---
title: "GPT-6.1 Terra徹底解説：料金・使い方・Claude Opus 5.5との違い"
emoji: "🤖"
type: tech
topics: ["ai", "openai", "claude"]
published: true
---

![GPT-6.1 Terra徹底解説：料金・使い方・Claude Opus 5.5との違い](https://ay3s.com/images/covers/gpt-61-terra-guide-vs-claude-opus-55.webp)

10月26日、OpenAI が GPT-6.1 Terra をリリースしました。API 名は `gpt-6.1-terra` です。

10月初めに [GPT-6.1 Sol](https://ay3s.com/ja/articles/gpt-61-sol-speed-and-pricing/) の記事を書いた時点では、6.1 シリーズは Sol の1モデルだけでした。そこに Terra が加わり、しかも価格はちょうど Sol の半分です。

この記事では Terra について、どんな位置づけなのか、スペックと価格、どこで使えるのか、API の呼び出し方、ハマりどころを整理し、最後に Claude Opus 5.5 と比較して、どう使い分けるべきかをまとめます。

## まず Terra が OpenAI のラインナップのどこにいるのか

OpenAI のモデルは現在、サイズ別に4つの階層に分かれています。安い順に並べると次のとおりです。

| 階層 | 現行モデル | 位置づけ |
|---|---|---|
| Luna | GPT-6 Luna | 最も安価。抽出、分類、バッチ処理向け |
| **Terra** | **GPT-6.1 Terra** | 2番目に安価。高速で、画像理解、コンピューター操作、文章作成が得意 |
| Sol | GPT-6.1 Sol | 日常使いの主力 |
| Astra | GPT-6 Astra | 最も高性能で、最も高価 |

Terra は GPT-5.6 Terra の後継です。GPT-6.0 Terra は存在せず、5.6 から一気に 6.1 へ飛んでいます。

OpenAI 公式の一言紹介は、**「高速で効率的なモデル。視覚的推論、正確なコンピューター操作、明快で自然な文章作成に優れる」**というものです。システムカードでは「大量の専門業務向け」と説明されています。

公式ガイドに挙げられている典型的な用途は、かなり具体的です。

- 商品写真と商品カタログを照合する
- 2枚の技術図面の違いを比較する
- アプリの画面上でボタンをクリックし、フォームに入力する
- 与えられた資料をもとに原稿を書く、手直しする
- 範囲がはっきりした小規模なコード修正

一方で OpenAI は、**最も複雑なプログラミングタスクには引き続き GPT-6.1 Sol か GPT-6 Astra を推奨する**ともはっきり書いています。Terra は Sol を置き換えるためのモデルではなく、「量が多く、内容が明確で、結果を検証できる」仕事を速く安くこなすためのモデルです。

## スペック

| 項目 | GPT-6.1 Terra |
|---|---|
| コンテキスト | 1,050,000 tokens（入力は最大 922K、出力は最大 128K） |
| 入力 / 出力 | テキストと画像を入力可能、出力はテキストのみ |
| 知識のカットオフ | 2026年6月1日 |
| 推論強度 | none、low、medium（デフォルト）、high、xhigh、max |
| API | Responses、Chat Completions、Batch |
| ツール | 関数呼び出し、構造化出力、Web 検索、ファイル検索、コードインタープリター、コンピューター操作（computer use）、MCP など |
| 非対応 | ファインチューニング、Predicted Outputs、音声と動画 |

見落としがちなポイントがひとつあります。**Terra は `none`、つまり推論を完全にオフにする設定に対応しています**。これは GPT-6.1 Sol にも GPT-6 Astra にもありません。手早い書き換えや抽出だけで済むタスクなら、推論をオフにすることで、時間もコストもさらに削れます。

## 価格：ちょうど Sol の半分

100万 tokens あたり、米ドル：

| モデル | 入力 | キャッシュヒット | 出力 |
|---|---|---|---|
| GPT-6 Luna | 0.10 | 0.01 | 0.50 |
| **GPT-6.1 Terra** | **1.00** | **0.05** | **5.00** |
| GPT-6.1 Sol | 2.00 | 0.10 | 10.00 |
| GPT-6 Astra | 10.00 | 1.00 | 50.00 |
| GPT-5.6 Terra（前世代） | 2.00 | 0.20 | 12.00 |

課金ルールで注意すべき点をいくつか挙げておきます。

- **長いコンテキストの割増**：1回のリクエストで入力が 272K tokens を超えると、そのリクエスト全体の入力が2倍、出力が1.5倍で課金されます。Terra の場合は 2.00 / 0.10 / 7.50 になります。
- **サービスティア**：Batch と Flex は半額、Fast（旧称 Priority）は2倍の価格です。Terra には Ultrafast ティアはありません。
- **キャッシュ書き込み**：保持期間30分のキャッシュ書き込みは 1.25 ドル、12時間は 2.50 ドルです。
- **Web 検索**は別途、1,000回あたり 10 ドルかかります。

前世代の GPT-5.6 Terra と比べると、入力は半額、出力は 12 ドルから 5 ドルに下がり、6割近く安くなっています。「50% 安くなった」と書いている記事が多いですが、それは入力についてしか言っていません。

## どこで使えるか

**API**：モデル名は `gpt-6.1-terra` で、日付付きのスナップショット版はありません。

**ChatGPT**：ChatGPT Work と Codex にのみ登場し、**通常のチャットでは使えません**。Plus、Pro、Business、Enterprise、Edu のいずれでも利用でき、モデル一覧から手動で選びます。全体の利用上限は変わっていません。Enterprise と Edu のメンバーは、管理者がこのモデルと Work / Codex の両方を有効にしている必要があります。

**Codex**：Codex CLI 0.159.0 以上が必要です。

```bash
codex -m gpt-6.1-terra
```

セッション内で `/model` を使って切り替えることもできます。Codex デスクトップ版と Work の Web 版では、「詳細設定」内のモデル一覧から選びます。

**その他のプラットフォーム**：GitHub Copilot と Microsoft Foundry はどちらもリリース当日に対応しました。GitHub によれば、Terra は「GPT-5.6 Terra よりも明らかに多くのターミナル系および C# のプログラミングタスクを解決した」とのことです。また、Amazon Bedrock でも同時に提供が始まったというサードパーティの報道がありますが、公式ページでは確認できていません。

## ベンチマークをどう読むか

OpenAI は発表の中で、次のように述べています。

- 推論強度 medium で、コンピューター操作タスクのスコアが GPT-5.6 Terra より **15ポイント高く、コストは約3分の1**
- GPT-6 Astra と同等の水準に達しつつ、token あたりの価格は Astra の10分の1
- タスクの平均完了速度は Astra や 6.1 Sol の **3倍以上**

発表のグラフに載っている具体的な数値は、私の環境では公式ページを直接開いて確認できなかったため、以下はメディアが書き起こしたものを引用しています。参考程度に見てください。

| ベンチマーク（推論強度） | GPT-6.1 Terra | GPT-5.6 Terra | GPT-6.1 Sol | GPT-6 Astra |
|---|---|---|---|---|
| Agents' Last Exam（max） | 60.0% | 50.7% | 58.6% | 59.3% |
| Terminal-Bench 4.0（max） | 50.3% | 21.5% | 55.9% | 58.2% |
| OSWorld 2.1（max、オフラインサブセット） | 74.2% | 57.1% | 約 75.9% | 約 75.8% |
| MMMU-Pro | 85.5% | — | — | — |

注目すべき点が2つあります。

- **Terminal-Bench 4.0 は推論強度に大きく左右される**：Terra は low だと 24.8%、medium で 35.8%、max にしてようやく 50.3% です。Terra でプログラミングをさせるなら、推論強度を低くしすぎないようにしましょう。
- **「15ポイント高い」という数字が合わない**：グラフの数値では、Terra の medium が 57.6%、GPT-5.6 Terra の最高強度が 50.7% で、差は 6.9 ポイントしかありません。公式の15ポイントは同じ推論強度同士で比べた数字かもしれませんが、現時点では検証できません。

独立系の評価機関 Artificial Analysis の総合インテリジェンス指数では、Terra は max 強度で 51（6.1 Sol が 55、Astra が 57、Claude Opus 5.5 が 58）です。別のソースでは 48 と報じられており、こちらも参考値として見るのがよいでしょう。

## 使い方：API のサンプルとハマりどころ

最もシンプルな呼び出しはこうです。

```python
from openai import OpenAI

client = OpenAI()
resp = client.responses.create(
    model="gpt-6.1-terra",
    reasoning={"effort": "medium"},
    input="以下の議事録をもとに、200字以内のフォローアップメールを書いてください：……",
)
print(resp.output_text)
```

古いモデルから移行する際に、特につまずきやすいのは次の点です。

**1. 推論がオンのときは `temperature` と `top_p` を渡せない**

推論強度が `none` 以外なら、`temperature`、`top_p`、`top_logprobs` を外す必要があります。Chat Completions ではさらに `logprobs` を、Responses では `include` から `message.output_text.logprobs` を外します。開発者の検証によると、これらのパラメータを残したままだと、そのまま HTTP 400 が返ってくるそうです。

**2. `minimal` はなくなった**

これまで `minimal` を使っていた場合は `low` に変更してください。`minimal` を渡すと、こちらも 400 エラーになります。

**3. ツール呼び出しは Responses API 経由が必須**

Chat Completions でも通常の会話はできますが、関数呼び出し、Web 検索、コンピューター操作といったツールは Responses API でしか使えません。Chat Completions でツールを呼んでいる既存プロジェクトは、移行が必要です。

**4. 会話の途中で推論強度を変えるときは、リクエストパラメータを直接いじらない**

公式は `configuration_update` 入力アイテムで調整することを推奨しています。リクエストレベルの `reasoning.effort` は変えずにおけば、プロンプトキャッシュが途切れません。

**5. キャッシュのパラメータ名が変わった**

GPT-5.5 以前のモデルから移行する場合、`prompt_cache_retention` を `prompt_cache_options.ttl` に置き換える必要があります。たとえば `"30m"` のように指定します。

OpenAI の推奨は、`medium` から始めて、本当に必要な場合だけ上げていくことです。移行前には、代表的な画像とワークフローを使い、同じ推論強度で Terra と既存モデルをそれぞれ動かして比較しておきましょう。

## コンピューター操作に使うなら、必ず人の確認を挟む

Terra はコンピューター操作を売りにしていますが、システムカードにはしっかり目を通しておきたいデータがいくつかあります。

- 重大な問題は 6.1 Sol や Astra より少ない：約4.9万件の Codex シミュレーションタスクのうち、深刻度3以上のフラグは Terra が22件、6.1 Sol が24件、Astra が26件
- ただし深刻度の低い問題はむしろ多い：不確実性の隠蔽、制約の回避、データの持ち出し、破壊的な操作といったカテゴリのフラグ率は、いずれも 6.1 Sol や Astra より高い
- **29.6% のテストで、制約条件の本来の意図に反するやり方でタスクを完了している**
- 13.75% のテストで、「仲間の AI」らしき存在からの指示に従って、許可されていない操作を実行している。6.1 Sol ではわずか 1.25%

なので私のアドバイスはシンプルです。Terra にボタンを押させたりフォームを埋めさせたりするのは構いませんが、**削除、支払い、送信、提出のような取り消せない操作には、必ず人による確認のステップを入れてください**。

## Claude Opus 5.5 と比べて何が違うのか

まずはっきりさせておくと、**この2つは同じクラスのモデルではありません**。Terra は OpenAI で2番目に安い階層、Claude Opus 5.5 は Anthropic の汎用フラッグシップです。同じクラスで比べるなら、Opus 5.5 に相当するのは GPT-6.1 Sol か GPT-6 Astra で、価格帯が Terra に近いのは Claude Sonnet 5.5（入力 2 ドル、出力 10 ドル）です。

とはいえ、実際この2つを比べたい人は多いので、主な違いを並べておきます。

| | GPT-6.1 Terra | Claude Opus 5.5 |
|---|---|---|
| リリース | 2026-10-26 | 2026-09-22 |
| 位置づけ | 高速・低価格、画像理解、コンピューター操作、文章作成 | 汎用フラッグシップ、長時間のプログラミングと知的作業 |
| 価格（100万 tokens あたり） | 入力 1 ドル、出力 5 ドル | 入力 4 ドル、出力 20 ドル |
| キャッシュヒット | 0.05 ドル | 0.20 ドル |
| 長いコンテキスト | 272K 超でリクエスト全体が割増 | 1M まで一律料金、割増なし |
| コンテキスト / 出力 | 約 1M / 128K | 1M / 128K |
| 推論 | 完全にオフにできる（none） | 常にオン、デフォルトは medium |
| 知識のカットオフ | 2026年6月 | 2026年6月 |
| 利用できる場所 | API、ChatGPT Work、Codex、Copilot | API、Claude の各プラン、Claude Code、Bedrock、Vertex など |

両社ともスコアを公表しているベンチマークは次のとおりです。

| ベンチマーク | GPT-6.1 Terra | Claude Opus 5.5 |
|---|---|---|
| Terminal-Bench 4.0 | 50.3%（max） | 66.4%（xhigh） |
| Terminal-Bench-Science 0.1 | 58.3%（max） | 58.7% |
| OSWorld 2.1 | 74.2%（オフラインサブセット） | 81.8%（一部タスク） |
| Agents' Last Exam | 60.0% | 63.2%（OpenAI のグラフ内の数値） |

この表は慎重に読む必要があります。両者で推論強度もタスクのサブセットも異なり、しかも Terra の数値は書き起こしなので、厳密な比較はできません。さらに、同じ項目なのに両社の公表値が一致しないこともあります。たとえば Opus 5.5 の Terminal-Bench-Science は、Anthropic 自身は 58.7% としていますが、OpenAI のグラフでは 63.3%（「フォールバックあり」と注記）になっています。

ざっくり言える結論は、**ターミナル系のプログラミングタスクでは Opus 5.5 が明らかに強く、科学計算とコンピューター操作では大きな差はない。そして価格は Terra がわずか4分の1**、ということです。

## 結局どう選ぶか

| シナリオ | 選ぶモデル | 理由 |
|---|---|---|
| 大量の原稿作成・手直し、資料からの要約作成 | Terra | 安くて速く、推論もオフにできる |
| 画像理解、図面の比較、スクリーンショットの読み取り | Terra | 公式が打ち出している得意分野で、価格も安い |
| 目的が明確で、結果を検証できるコンピューター操作 | Terra | 主要ユースケース。ただし取り消せない操作には人の確認を |
| 複雑なプログラミング、長時間のリファクタリング、多段階のデバッグ | Opus 5.5 または GPT-6.1 Sol | Terminal-Bench の差が大きく、OpenAI 自身もそう推奨している |
| 非常に長いコンテキストを渡したい（272K 超） | Opus 5.5 | 1M までは割増なし。Terra は 272K を超えるとリクエスト全体が割増 |
| 予算は厳しいが、Claude を使いたい | Sonnet 5.5 | Terra と価格が近い同クラスの競合 |

この考え方でいくと、コスパがいいのは両者の組み合わせです。プログラミングや大規模プロジェクトは Opus 5.5 に任せる。私がスマホアプリを作ったときもこの使い方で、その過程は[同じ Claude Opus 5.5 なのに、なぜアプリまで作れる人がいるのか](https://ay3s.com/ja/articles/same-ai-model-different-results/)に書きました。そして大量の文章整理やスクリーンショットの読み取りといった作業は、Terra のような安くて速いモデルに任せる、という形です。

モデルも価格も変化が速いので、本格的に導入する前には、OpenAI と Anthropic の公式ドキュメントで最新情報を確認してください。

---

参考資料：

- [OpenAI：GPT-6.1 Terra モデルページ](https://developers.openai.com/api/docs/models/gpt-6.1-terra)
- [OpenAI：API の料金](https://developers.openai.com/api/docs/pricing)
- [OpenAI：Using GPT-6 移行ガイド](https://developers.openai.com/api/docs/guides/latest-model)
- [OpenAI：モデル選択ガイド](https://developers.openai.com/api/docs/guides/model-selection)
- [OpenAI：GPT-6.1 Terra システムカード補遺](https://deploymentsafety.openai.com/gpt-6-1-terra/addendum-to-gpt-6-astra-system-card-gpt-6.1-terra)
- [GitHub Changelog：GPT-6.1 Terra が Copilot で利用可能に](https://github.blog/changelog/2026-10-26-gpt-6-1-terra-in-github-copilot/)
- [Anthropic：Claude Opus 5.5](https://www.anthropic.com/claude-opus-5-5)
- [Anthropic：モデル概要](https://platform.claude.com/docs/en/about-claude/models/overview)、[料金](https://platform.claude.com/docs/en/about-claude/pricing)

あなたもモデル選定をしていたり、GPT-5.6 から GPT-6.1 への移行を検討していたりするなら、ぜひ情報交換しましょう：[linktr.ee/weiyii](https://linktr.ee/weiyii)

画像: [NASA / Apollo 17 crew](https://commons.wikimedia.org/wiki/File:The_Earth_seen_from_Apollo_17.jpg) / [Public domain](https://commons.wikimedia.org/wiki/File:The_Earth_seen_from_Apollo_17.jpg)

[原文](https://ay3s.com/ja/articles/gpt-61-terra-guide-vs-claude-opus-55/)
