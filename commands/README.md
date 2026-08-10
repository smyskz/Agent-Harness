# AI Agent command索引

`commands/`には、利用者が内容を確認して明示的に実行するAI Agent向けCommandまたは
Skillを置く。各Command／Skillはrepository rootの[`AGENTS.md`](../AGENTS.md)と
[`AGENTS-project.md`](../AGENTS-project.md)に従い、既存変更、Git境界、承認条件を確認して
から使用する。

## Command一覧

| File | Status | 対象と動作 |
|---|---|---|
| [`initialize-project-agent-rules.md`](initialize-project-agent-rules.md) | original | repositoryの実態を調査し、共通`AGENTS.md`を複製せずproject固有の`AGENTS-project.md`を生成・統合する明示実行型Command |
| [`review-architecture-baseline.md`](review-architecture-baseline.md) | original | `arch/`正本を整合性監査し、推奨案と選択肢を1問ずつ確認して更新し、最後にbaseline承認を求める |
| [`archive-old-ai-reports.md`](archive-old-ai-reports.md) | original | 締切月より前の報告書を`reports/AI/YYYY-MM/`へ整理する月単位の原本 |
| [`archive-old-ai-reports-update.md`](archive-old-ai-reports-update.md) | unverified update candidate | 締切日以前の報告書を`reports/AI/YYYY/MM/DD/`へ整理する日単位の改善候補 |

`-update`は採択済みを意味しない。原本と同じ条件で比較検証し、明確な改善が確認された場合だけ
候補を採択する。未検証、同点、悪化の場合は原本を維持する。

## Report archival commandの安全条件

- 移動前に候補、移動先、日付根拠、Git管理状態、path衝突を提示する。
- 利用者の承認前にfileを移動しない。
- 未追跡fileは、明示的に対象へ含める承認がなければ移動しない。
- 報告本文、README索引、相対link、付随asset、旧path参照を一つの変更単位で検証する。
- commitやpushは、利用者から明示的に依頼された場合だけ行う。

## 更新規則

既存の原本を直接改善せず、同じbasenameの`-update.md`を完全な候補文書として更新する。
`-update-update.md`や番号付き派生fileは作成しない。採否、比較条件、失敗、回帰、未解決事項は
作業結果として報告する。
