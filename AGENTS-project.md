# Agent-Harness プロジェクトルール

## 適用範囲と共通ルール

このファイルは、このrepository固有の目的、構成、正本、開発手順、Git管理境界を定め、
repository全体に適用する。作業前に共通ルールの[`AGENTS.md`](AGENTS.md)も全文読み、
AI Agentの記憶、報告書、Skill／Command改善、調査、安全性、Gitに関する共通ルールを
併せて適用する。

より深いdirectoryに`AGENTS.md`またはtool固有の指示fileがある場合は、その範囲では
より近い指示を優先する。project固有の事実はこのfile、projectをまたぐ運用規則は
`AGENTS.md`を正本とし、同じ規則を両方へ複製しない。

## プロジェクトの目的

このrepositoryは、AI coding環境とagent基盤に関する日本語の調査資料、および
GPQA Diamond BM25章検索knowledge prompt評価基盤のsource、test、開発文書を管理する。
現在有効なGPQA評価基盤の要求と設計は`arch/`、調査や活動の成果は`reports/AI/`を
それぞれ正本とする。

## リポジトリ構成とGit管理境界

- `AGENTS.md`: projectをまたぐAI Agent共通ルール。
- `AGENTS-project.md`: このrepository固有のprojectルール。
- `README.md`: repositoryの概要、directory構成、報告書索引、開発環境の入口。
- `arch/`: Git管理するsoftware開発文書の正本。要求、設計、ADR、test方針、
  traceability、開発計画を置く。
- `src/gpqa_eval/`: GPQA評価基盤のPython 3.12 package。
- `tests/`: `src/gpqa_eval/`に対応するtest suite。
- `scripts/`: setupなど、projectで共有する補助script。
- `skills/`: Git管理する、AI Agentが依頼内容に応じて利用するSkill。
- `commands/`: Git管理する、利用者が明示実行するAI Agent向けSkill／Command。
- `reports/AI/`: Git管理する日本語の調査・活動報告と付随SVG。報告書索引は
  `README.md`を正本とする。
- `docs/`: Git管理外とするlocal参考資料。原文のscopeと限界を保ち、明示的な依頼なしに
  変更しない。2026-08-10時点で`docs/paper/`の2 PDFはroot Gitに追跡済みであり、policyとの
  既存不一致として扱う。新しい例外の根拠にしない。
- `data/`: 全体をGit管理外とするlocal data。PDF、TXT、metadata、READMEなどfile種別や
  内容を問わず、`data/`以下をroot Gitへ追加しない。ignore例外やforce addを行わない。
  2026-08-10時点で`data/public-release-kit/`は追跡済みかつignore例外があり、policyとの
  既存不一致として扱う。解消は対象と影響を確認する別taskで行う。
- `memory/`: Git管理外のAI Agent長期記憶。具体的な運用は`AGENTS.md`に従う。
- `runs/`: Git管理外の評価実行artifact。private入力・出力とaggregate結果の境界は
  `arch/`の現行設計に従う。
- `GPQA/`: Git管理外のGPQA公式repository参照用clone。独立したGit repositoryとして
  read-onlyで扱う。
- `continue/`、`codex/`、`claude-code/`、`cline/`、`opencode/`: Git管理外の調査用
  upstream clone。各directoryは独立したGit repositoryである。

この一覧を固定された事実だと仮定しない。作業開始時に実在するfile、root Gitの追跡状態、
ignore規則、nested Git境界を確認する。READMEと実態が食い違う場合は、実在するfileと
Git差分を証拠として不一致を報告し、どちらかを暗黙に正しいものとして扱わない。

## プロジェクト作業の開始手順

`AGENTS.md`の共通手順に加え、次を行う。

1. `README.md`、`arch/README.md`、対象milestoneに関係するSOW、Development Plan、SRS、
   Design、ADR、Test Strategy、Traceabilityを読む。
2. 対象に近い既存報告書と、`README.md`の報告書索引を確認する。
3. projectの公式な環境、command、対象要求ID、受入条件、action item、更新が必要な正本を
   特定する。
4. `data/`、`docs/`、`memory/`、`runs/`、参照用cloneをroot Gitへ追加しないことを確認する。

## ソフトウェア開発文書

人間と生成AIが同じ要求、制約、設計判断、完了条件に基づいてsource codeを開発できるよう、
現在有効なsoftware開発情報は`arch/`以下を正本として管理する。`reports/AI/`は調査結果や
検討過程を説明する成果物であり、採択後の要求や設計を`arch/`の代わりにしない。Issue、
会話、作業logだけを唯一の根拠にしない。

### 文書構成

- `arch/README.md`: 開発文書の索引、各文書のstatus、読む順序、対象systemと実装の対応。
- `arch/sow/SOW.md`: 目的、背景、stakeholder、scope、非scope、成果物、制約、前提、成功条件。
- `arch/management/DEVELOPMENT-PLAN.md`: 開発方針、phase、milestone、dependency、現在の
  action itemを一体で管理する開発計画の正本。
- `arch/srs/SRS.md`: `REQ-`と`NFR-`の安定したIDを持つ機能要件、非機能要件、優先度、
  根拠、受入条件。
- `arch/design/DESIGN.md`: system context、component責務、data／control flow、interface、
  data model、failure handling、security boundary、deployment、要求との対応。
- `arch/adr/ADR-NNN-<descriptive-name>.md`: 重要なarchitecture判断ごとのstatus、context、
  決定、選択肢、採否理由、影響。採択済みADRは新しいADRでsupersedeする。
- `arch/testing/TEST-STRATEGY.md`: 必要なtest level、oracle、環境、fixture、quality gate、
  実行責任。
- `arch/testing/TEST-PLAN.md`: 大きな機能またはreleaseで個別計画が必要な場合のscope、
  test case、環境、entry／exit条件、証拠の保存先。
- `arch/verification/TRACEABILITY.md`: 要求ID、設計、ADR、実装path、test、検証証拠の
  双方向対応。
- `arch/glossary/GLOSSARY.md`: domain用語、略語、同義語、避ける表現。必要な場合に追加する。
- `arch/security/THREAT-MODEL.md`: asset、trust boundary、threat、mitigation、残存risk。
- `arch/operations/RUNBOOK.md`: 継続運用がある場合の設定、監視、backup、復旧、rollback、
  incident対応。

対象に不要な文書やdirectoryを空の雛形として一括作成しない。最小限として
`arch/README.md`、SOW、Development Plan、SRS、Design、Test Strategy、Traceabilityを
scopeに合わせて用意し、重要判断が生じた時点でADRを追加する。securityとoperationsは
該当するriskまたは運用責任がある場合に追加する。

### 開発計画とaction item

小規模から中規模の開発では、計画、milestone、action itemを
`arch/management/DEVELOPMENT-PLAN.md`の1fileへまとめる。情報を一覧してdependencyと
次の行動を判断しやすくし、複数文書間のstatusずれを避けるため、これを既定とする。
少なくとも次を含める。

- 開発目標、非目標、計画上の前提と制約。
- phaseまたはworkstream、その順序、dependency、並行実行できる範囲。
- `MS-NNN`形式のstable IDを持つmilestone。成果、scope、deliverable、dependency、
  exit criteria、責任者、statusを記載する。target dateは人間が合意した場合だけ記載する。
- `ACT-NNN`形式のstable IDを持つ現在のaction item。対応milestone、要求ID、具体的な次の
  行動、責任者、優先度、status、blocker、Issueまたは成果物へのlinkを記載する。
- milestone達成を妨げるrisk、外部dependency、pending decisionとowner。
- 完了したmilestoneのexit criteria、test結果、承認または成果物へのlink。

action itemには、完了条件が明確で、1人または1 agentが担当できる次の行動を書く。
曖昧な目標、会話log、細かな実行履歴を蓄積しない。Issue trackerがある場合は詳細な作業と
日々のstatusをIssueの正本とし、Development Planにはmilestoneとの対応、優先度、dependency、
要約statusだけを同期する。同じtask説明を二重管理しない。

独立したworkstream、更新cadence、文書量、access権などの理由で一体管理が継続的に困難な
場合だけ分割する。分割時も`DEVELOPMENT-PLAN.md`を全体方針、phase、dependency、索引として
維持し、`arch/management/MILESTONES.md`と`arch/management/ACTION-ITEMS.md`へ移したIDを
変更しない。`arch/README.md`と相互linkを更新し、同じ項目を複数fileへ複製しない。

### 文書の状態と責任

- 各文書にはtitle、`draft | review | approved | superseded`のstatus、更新日、責任者または
  承認者を記載する。既存形式がある場合はそれを優先する。
- AI Agentは文書を調査、起草、更新できるが、人間の確認なしに`approved`へ変更しない。
  scope、受入条件、公開interface、data migration、security boundary、重大なarchitecture判断は
  人間の承認対象とする。
- 未決事項は`TBD`、仮定は`Assumption`、外部判断待ちは`Pending decision`として明示する。
  実装を左右する未決事項は、推測で確定せず着手前に利用者へ確認する。
- SRSとDesignには現在有効な状態を記載する。経緯と却下理由はADR、計画とmilestoneは
  Development Plan、詳細taskと日々の進捗はIssue tracker、調査過程は報告書へ分離する。

### 開発時の更新手順

1. 実装前に対象milestone、action item、要求ID、受入条件を特定する。
2. 要求変更はcodeより先または同じ変更単位でSRSへ反映し、Design、Test Plan、
   Traceabilityも更新する。
3. component境界、public interface、永続data、dependency方針、security boundaryなどの
   重要判断を変更する場合はADRを作成またはsupersedeし、Designへ現在の結論を反映する。
4. 実装とtestを要求IDへ対応付ける。testを追加できない場合はTraceabilityへgapとriskを
   記録する。
5. 文書とcodeの矛盾を見つけた場合は、差異、影響、提案を示して人間へ確認し、決定後に
   正本と実装を同じ変更で整合させる。
6. 完了時は要求、設計、実装、test、証拠のlinkを検査する。exit criteriaと検証証拠を
   満たしたaction itemとmilestoneだけを完了にする。

## 開発原則

- 後方互換性を維持しない。互換layer、fallback、migrationを追加するのではなく、
  廃止された経路を削除する。
- 現在の要求を完全に満たす最も単純な実装を選ぶ。将来を見越した抽象化、設定、
  間接化を避ける。
- systemを段階的に成長させる。最初にend-to-endで動作する最小版を作り、すでに動作する
  productの上へ新しい機能を一つずつ追加する。未完成の複雑さと引き換えに、動作する
  productを損なわない。
- componentをmodularに保ち、関心事を明確に分離する。
- 十分に確立され継続的に保守されているlibraryが全体の複雑性を下げるか信頼性を高める
  場合は優先する。明確な理由なく一般的な機能を再実装しない。
- 独自実装やpackage追加の前に、projectにすでにあるdependencyを活用する。documentationと
  type定義を確認せず、libraryに必要な機能がないと決めつけない。
- architecture判断は長期的な視点で行う。現時点だけ動作し、後で置き換える前提の暫定策を
  受け入れない。
- 公開済みの報告書pathやlinkを、明示的な理由なく変更しない。

## 参照用cloneの扱い

- `GPQA/`、`continue/`、`codex/`、`claude-code/`、`cline/`、`opencode/`は原則read-onlyの
  証拠sourceとして扱う。
- 利用者が明示的に依頼しない限り、checkout、branch、submodule、source、lockfile、設定を
  変更しない。
- upstream更新前に、既存報告書が参照するcommitと調査の再現性への影響を確認する。
- root repositoryのGit操作と、参照用clone内のGit操作を混同しない。

## プロジェクト固有の検証

`AGENTS.md`の共通検証に加え、変更内容に応じて次を確認する。

- READMEの報告書一覧と`reports/AI/`の実fileに漏れや削除済みlinkがない。
- 要求または設計に影響する変更では、`arch/`の正本とcodeが一致し、要求ID、Design、ADR、
  test、検証証拠の対応に欠落や重複がない。
- Development Planのmilestone、action item、要求ID、Issue、成果物のlinkとstatusが一致し、
  完了項目にはexit criteriaを満たす証拠がある。
- 完了milestoneには`AGENTS.md`が定める作業報告書があり、exit criteria、検証結果、
  traceability、未解決事項、Git provenance、次のaction itemが記載されている。
- `arch/`を作成または変更した場合は、`arch/README.md`の索引、文書status、supersede関係が
  実fileと一致する。
- `data/`、`docs/`、`memory/`、`runs/`、参照用cloneに新しいroot Git追跡対象が生じていない。
  既存のtracked例外を検出した場合は、policy適合と誤認せず対象pathを報告する。
- Python実装または設定を変更した場合は、Python 3.12のlock済み環境で
  `.venv/bin/pytest -q`を実行する。環境準備は`scripts/setup-venv.sh`を正本とする。
