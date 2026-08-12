---
name: initialize-project-agent-rules
description: ユーザーの明示操作により、対象Git repositoryを調査してプロジェクトrootのAGENTS.mdを新規作成または安全に統合し、共通AGENTS.mdとの重複を分離する。repository構成、Git管理境界、正本文書、build／test手順、開発原則、project固有の検証を記載するときに使用する。
---

# Initialize Project Agent Rules

## 実行契約

ユーザーが`$initialize-project-agent-rules`を明示した場合だけ実行する。このSkillは
プロジェクトrootの`AGENTS.md`を管理し、AI Agent共通ルールそのものをproject fileへ
複製しない。

共通ルールの参照先を先に確定する。プロジェクトrootの`AGENTS.md`が参照するglobal rule、
利用者が指定したglobal rule、または配布元の共通ルールのいずれも確認できない場合は、
既存fileから共通規則を削除せず、必要な参照先を利用者へ確認する。

本ファイル末尾に記載の`project-AGENTS.template.md`を構成の雛形として使う。
templateをそのままcopyせず、確認したrepository固有の値で具体化する。

## 共通ルールとprojectルールの境界

次を共通ルールとして扱い、プロジェクトrootの`AGENTS.md`へ詳細を複製しない。

- `memory/`を使うAI Agentの長期記憶とknowledge graphの運用。
- 日本語報告書、executive-summary SVG、milestone完了報告の作成契約。
- Skill／Command原本と`-update`改善候補を分けるSkillOpt運用。
- projectをまたいで同一に適用する調査、証拠、安全性、Git操作の規則。

次をprojectルールとしてプロジェクトrootの`AGENTS.md`へ記載する。

- projectの目的、scope、成果物。
- 実在するrepository構成と、各pathの役割・root Gitでの扱い。
- 要求、設計、ADR、計画、test、用語、運用情報などの正本と優先関係。
- source、test、script、data、生成artifact、nested repositoryの境界。
- projectで採用するbuild、format、lint、test、releaseの正規手順。
- project固有の開発原則、承認対象、作業開始手順、検証条件。
- projectに固有のread-only参照先、外部system、security boundary。

プロジェクトrootの`AGENTS.md`には共通`AGENTS.md`への参照と優先順位を短く記載する。共通rule内の
directory名をrepository構成表へ掲載する必要がある場合も、Git管理状態と役割だけを
記載し、運用手順は共通ruleへlinkする。

## 手順

1. 現在地、Git root、現在branch、HEAD、worktree、既存の指示file階層をread-onlyで
   確認する。Git repositoryでない場合は変更せず停止する。
2. 未コミット変更を利用者の作業として記録し、今回の対象と区別する。stash、破棄、
   stage解除、無関係な整形を行わない。
3. `README.md`、既存のプロジェクトrootの`AGENTS.md`、より深いdirectoryの`AGENTS.md`、開発文書索引、
   package manifest、lockfile、CI設定、 source／test／script directory、報告書索引を読む。
4. tracked file、ignored path、untracked path、nested Git repositoryを別々に調べる。
   directory名だけからGit管理状態を推測しない。ignore例外と、policyに反するtracked fileも
   検出して明示する。
5. 共通ルールの実在する参照先、対象toolがそれを読む方法、project ruleとの優先順位を
   確定する。参照先を確認できない場合は、既存の共通規則を移動・削除しない。
6. プロジェクトrootの`AGENTS.md`の各規則を「共通」「project固有」 「より近いdirectoryの規則」へ
   分類する。意味が不明な規則や、分類により効力が変わる規則は推測で移動しない。
7. 本file末尾の`project-AGENTS.template.md`を使い、project名、目的、共通ルール参照、構成、
   正本、公式command、承認対象、検証を確認済みの値で具体化する。placeholderを残さない。
8. 開発原則は、既存project ruleに同等以上の規則がない限りtemplateの日本語版を含める。
   既存原則と意味が重なる場合は一つに統合し、矛盾を残さない。
9. 既存のプロジェクトrootの`AGENTS.md`がある場合は無条件に上書きしない。project固有の規則と、 利用者が追加し
   た未知の規則を保持した統合差分を作る。共通`AGENTS.md`への参照と 同等内容を確認した後だ
   け、重複するmemory、報告書、Skill／Command改善規則を project fileから削除する。
10. より深いdirectoryの`AGENTS.md`、tool固有指示、common ruleは、利用者が同時に依頼した
    場合を除いて変更しない。
11. 生成後に、共通ruleの参照先、project内link、placeholder、記載path、Git管理状態、
    nested Git境界、公式commandを再検証する。`git diff --check`と、対象repositoryの
    文書検証を実行する。
12. project ruleが実態と一致しない項目は自動的に正当化しない。policy違反、未決事項、
    未実施検証として報告し、必要な修正を今回の依頼範囲に含めるか利用者へ確認する。
13. 作成・変更file、共通ruleから分離した項目、保持したproject規則、検証結果、既存の
    不整合を報告する。commitやpushは、利用者が明示的に依頼した場合だけ行う。

## 出力契約

- 各プロジェクトrootの`AGENTS.md`はproject固有の情報だけで自己完結し、共通`AGENTS.md`を参照する。
- repository構成は少なくともpath、役割、root Gitでの扱いを区別する。
- projectの正本、開始時に読む文書、公式command、承認対象、検証条件を明示する。
- 不明な値は事実のように補完しない。実装を左右しない場合は`TBD`または
  `Pending decision`とし、実装を左右する場合は変更前に利用者へ確認する。
- 共通ruleの全文、rawな調査log、現在の一時的なtask状態をプロジェクトrootの`AGENTS.md`へ
  複製しない。

## 禁止事項

- userの未コミット変更、既存rule、nested repositoryを無条件に上書きしない。
- Git管理状態をREADME、directory名、`.gitignore`の1つだけから断定しない。
- 共通ruleが未確認のまま、重複して見える既存ruleを削除しない。
- projectの要求、設計、build／test手順、承認を推測で確定しない。
- user-facing Skillを暗黙呼び出し可能にしない。
- branch作成、commit、push、公開、削除を、このSkillの暗黙の権限として実行しない。

## project-AGENTS.template.md

```
# {{PROJECT_NAME}} プロジェクトルール

## 適用範囲と共通ルール

このファイルは、{{PROJECT_SCOPE}}に適用するproject固有ルールである。
作業前に{{COMMON_RULES_REFERENCE}}を読み、共通ルールと併せて適用する。
競合時は、systemおよび利用者の指示、対象に最も近い指示、このproject rule、
共通ruleの順に優先する。

## プロジェクトの目的

{{PROJECT_PURPOSE}}

## リポジトリ構成とGit管理境界

| Path | 役割 | root Gitでの扱い |
|---|---|---|
{{REPOSITORY_STRUCTURE_ROWS}}

構成を固定された事実だと仮定しない。作業開始時に実在するfile、追跡状態、ignore規則、
nested Git境界を確認し、文書と実態の不一致を報告する。

## 正本と優先関係

{{SOURCE_OF_TRUTH_RULES}}

同じ情報の正本を複数作らない。未決事項は`TBD`、仮定は`Assumption`、外部判断待ちは
`Pending decision`として明示し、人間の確認なしに承認済みへ変更しない。

## 作業開始手順

1. 共通ruleと、対象に近いproject指示を読む。
2. {{STARTUP_DOCUMENTS}}
3. 現在branch、worktree、対象path、Git管理境界を確認する。
4. 対象要求、受入条件、更新する正本、公式な検証commandを特定する。

## 開発原則

- 後方互換性を維持しない。互換layer、fallback、migrationを追加するのではなく、
  廃止された経路を削除する。
- 現在の要求を完全に満たす最も単純な実装を選ぶ。将来を見越した抽象化、設定、
  間接化を避ける。
- systemを段階的に成長させる。最初にend-to-endで動作する最小版を作り、動作する
  productの上へ新しい機能を一つずつ追加する。未完成の複雑さと引き換えに、動作する
  productを損なわない。
- componentをmodularに保ち、関心事を明確に分離する。
- 十分に確立され継続的に保守されているlibraryが全体の複雑性を下げるか信頼性を高める
  場合は優先し、明確な理由なく一般的な機能を再実装しない。
- 独自実装やpackage追加の前に、projectにすでにあるdependencyを活用する。documentationと
  type定義を確認せず、libraryに必要な機能がないと決めつけない。
- architecture判断は長期的な視点で行う。現時点だけ動作し、後で置き換える前提の暫定策を
  受け入れない。

## 実装・レビュー時の確認事項

- 廃止されたコードパスや設定を互換性のためだけに残していないか。
- 新しい抽象化や設定に、現在の要件から説明できる具体的な必要性があるか。
- 変更後も、製品が end-to-end で動作する状態を維持しているか。
- 異なる責務が一つのコンポーネントに混在していないか。
- 自作やパッケージ追加の前に、既存依存関係のドキュメントと型を確認したか。
- 後で全面的に置き換えることを前提とした暫定設計になっていないか。

## プロジェクト固有の開発手順

{{PROJECT_DEVELOPMENT_WORKFLOW}}

## 公式command

{{OFFICIAL_COMMANDS}}

## 承認が必要な変更

{{HUMAN_APPROVAL_BOUNDARIES}}

## プロジェクト固有の検証

{{PROJECT_VALIDATION_RULES}}
```
