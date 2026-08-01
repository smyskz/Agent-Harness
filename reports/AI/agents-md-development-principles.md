# AGENTS.md の開発原則：校正版と解説

- 作成日: 2026-08-04（Asia/Tokyo）
- 対象: ユーザー提示の `AGENTS.md` 抜粋
- 校正範囲: コピー時に混入した文字・行番号の除去、箇条書きの空白と改行の修正
- 内容上の変更: なし

## 校正済み `AGENTS.md`

以下が、提示された文章の意味を変えずに誤字・脱字と体裁を修正したバージョンである。

```markdown
# AGENTS.md

- Do not preserve backward compatibility. Remove obsolete paths instead of
  adding compatibility layers, fallbacks, or migrations.
- Choose the simplest implementation that fully meets the current
  requirements. Avoid speculative abstractions, configuration, and
  indirection.
- Grow the system in layers. Start from the smallest version that works end
  to end, and add each new capability on top of a product that already
  works. Never trade a working product for unfinished complexity.
- Keep components modular and concerns clearly separated.
- Prefer established, well-maintained libraries when they reduce overall
  complexity or improve reliability. Do not reimplement common
  functionality without a clear reason.
- Lean on the dependencies already in the project before writing your own
  implementation or adding packages. Do not assume a library lacks a
  capability without checking its documentation and types.
- Make architectural decisions for the long term. Do not accept a stopgap
  that only works for now and is meant to be replaced later.
```

## 修正した箇所

提示された文章には、本文とは考えにくい `Q`、`4`、`5`、`6` などの文字や行番号、および `4 SF` が混入していたため削除した。また、`-Keep` を正しい Markdown の箇条書きである `- Keep` に修正した。

途中で分断されていた文は、意味と語順を変更せずに接続した。英語表現そのものは文法的に成立しているため、別表現への書き換えは行っていない。

## 全体の意図

この `AGENTS.md` は、AI コーディングエージェントに対し、現在の要件を満たす単純な実装を、将来も育てられる健全な構造で完成させるよう求めている。中心となる考え方は次の3点に集約できる。

1. 古い経路や未使用の仕組みを残さず、コードベースを簡潔に保つ。
2. 動作する最小単位から始め、常に end-to-end で動く状態を維持する。
3. 小さく実装しても、後で捨てることを前提とした場当たり的な設計にはしない。

## 各原則の解説

### 1. 後方互換性を維持しない

> Do not preserve backward compatibility. Remove obsolete paths instead of adding compatibility layers, fallbacks, or migrations.

古い API、設定形式、処理経路を維持するための互換レイヤー、フォールバック、移行処理を追加せず、不要になった経路を削除する方針である。

たとえば関数の引数形式を変更した場合、旧形式も受け付ける条件分岐を追加するのではなく、呼び出し側を新形式へ更新して旧形式を取り除く。この方針はコードを簡潔に保つ一方、既存利用者に対する破壊的変更を許容する。そのため、公開 API や永続データに互換性保証が必要なプロジェクトでは、適用範囲を別途明確にする必要がある。

### 2. 現在の要件を満たす最も単純な実装を選ぶ

> Choose the simplest implementation that fully meets the current requirements. Avoid speculative abstractions, configuration, and indirection.

「将来必要になるかもしれない」という推測だけを根拠に、抽象クラス、設定項目、プラグイン機構、中間レイヤーなどを先回りして追加しないという指示である。

ここでいう「最も単純」は、単にコード量が最少という意味ではない。現在の要件を完全に満たし、理解・検証・保守に必要な全体の複雑性が最も低い実装を意味する。

### 3. 動くシステムを段階的に成長させる

> Grow the system in layers. Start from the smallest version that works end to end, and add each new capability on top of a product that already works. Never trade a working product for unfinished complexity.

最初に小さくても端から端まで動く製品を作り、その動作を保ちながら機能を一層ずつ追加する方針である。

たとえば、最初から大規模な並列ジョブ基盤を作るのではなく、まず1件の処理を完了できるようにし、その後に複数件処理、永続化、再試行、並列化を順番に追加する。高度な仕組みを途中まで導入した結果、製品全体が動かない状態になることを避ける。

### 4. コンポーネントをモジュール化し、責務を分離する

> Keep components modular and concerns clearly separated.

UI、ビジネスロジック、データアクセス、外部サービス連携など、変更理由の異なる責務を明確に分けるよう求めている。

ただし、これは不要なインターフェースやレイヤーを増やすという意味ではない。「推測による抽象化を避ける」という第2原則と合わせ、現在存在する責務を自然な単位で分離するのが意図である。

### 5. 実績があり、保守されているライブラリを利用する

> Prefer established, well-maintained libraries when they reduce overall complexity or improve reliability. Do not reimplement common functionality without a clear reason.

認証、入力検証、日付処理、暗号、再試行、構文解析などの一般的な機能を、明確な理由なく再実装しないという方針である。成熟したライブラリによってシステム全体が単純になる、または信頼性が向上する場合は、そのライブラリを優先する。

一方、依存関係や設定の追加がかえって複雑性を増やす場合まで、無条件に外部ライブラリを採用する指示ではない。判断基準は、プロジェクト全体としての複雑性と信頼性である。

### 6. 新規実装やパッケージ追加の前に既存依存関係を調べる

> Lean on the dependencies already in the project before writing your own implementation or adding packages. Do not assume a library lacks a capability without checking its documentation and types.

新しいコードを自作したり別のパッケージを追加したりする前に、プロジェクトがすでに利用している依存関係で実現できないか確認するよう求めている。

確認は記憶や推測だけで済ませず、対象バージョンのドキュメントと型定義を調べる必要がある。これにより、機能の重複、不要な依存関係、既存ライブラリと新規実装の不整合を避けられる。

### 7. 長期的に妥当なアーキテクチャを選ぶ

> Make architectural decisions for the long term. Do not accept a stopgap that only works for now and is meant to be replaced later.

「今だけ動けばよく、後で作り直す」ことを前提とする場当たり的な設計を避ける方針である。機能の範囲は小さくしても、その範囲内では継続的に拡張できる構造として完成させる。

## 原則間の関係

「最も単純な実装」と「長期的なアーキテクチャ」は、一見すると矛盾するように見える。しかし、この文書では次のように両立する。

| 考え方 | 意味 |
|---|---|
| 最小実装 | 現在実装する機能の範囲を小さくする |
| 段階的成長 | 動作する製品に能力を一つずつ追加する |
| 長期的設計 | 小さな機能でも、後で捨てる前提の構造にはしない |
| 非互換方針 | 不要な旧経路を残して構造を複雑にしない |

したがって、要求されているのは「小さく作るが、使い捨てにはしない」実装である。将来の機能を予測して仕組みを増やすのではなく、現在必要な機能を正しい責務分離のもとで完成させ、その動作する土台の上に次の機能を追加する。

## 実装・レビュー時の確認事項

- 廃止されたコードパスや設定を互換性のためだけに残していないか。
- 新しい抽象化や設定に、現在の要件から説明できる具体的な必要性があるか。
- 変更後も、製品が end-to-end で動作する状態を維持しているか。
- 異なる責務が一つのコンポーネントに混在していないか。
- 自作やパッケージ追加の前に、既存依存関係のドキュメントと型を確認したか。
- 後で全面的に置き換えることを前提とした暫定設計になっていないか。

## 適用上の注意

この方針は、後方互換性を明示的に保証しないプロジェクトに適している。外部利用者向け API、既存の永続データ、複数バージョンのクライアントなどを持つシステムでは、互換性を維持しないことで利用者に影響が生じる可能性がある。

そのようなプロジェクトで使用する場合は、「後方互換性を維持しない」という原則を無条件に適用せず、互換性保証の対象と期間、破壊的変更の通知方法、データ移行の責任をプロジェクト要件として明示する必要がある。
