---
description: arch以下の要求・設計・ADR・test・traceability・開発計画を整合性監査し、仕様提案と選択肢を1問ずつ提示して利用者の回答を正本文書へ反映し、全質問の最後にbaseline承認を確認する
---

# Architecture baselineを対話的にreviewする

`arch/`以下のsoftware開発文書を正本として監査し、実装を左右する矛盾、欠落、曖昧さを
利用者と1件ずつ解消する。各質問では推奨案と代替案、影響を提示し、回答を関係文書へ
反映してから整合性を再検証する。baseline approvalは全仕様質問を解決した後の最終質問にする。

## 入力

- 任意の対象scope、feature、milestone、要求ID、またはreview対象文書。
- 省略時は`arch/README.md`が示す現在有効なbaseline全体。
- 任意の制約、既知の懸念、優先順位、回答済みdecision。

対象と承認権限が不明な場合は、文書を変更する前に利用者へ確認する。対象を限定された場合も、
直接影響するSRS、Design、ADR、Test Strategy、Traceability、Development Planは監査対象に含める。

## 守る原則

- repository rootと適用される`AGENTS.md`を最初に確認する。
- `arch/`を現在仕様の正本とし、会話、Issue、`reports/AI/`、既存codeを暗黙に優先しない。
- 利用者の未コミット変更を保持し、対象外の修正、整形、stage解除を行わない。
- 事実、既存仕様、提案、仮定、未決事項を区別する。
- 生成AIがhuman approvalを代行しない。明示回答なしに`approved`へ変更しない。
- 採択済みADRを履歴ごと書き換えない。判断変更は新ADRでsupersedeする。
- 実装、API call、外部送信、commit、pushは、別途明示的に依頼された場合だけ行う。
- secret、raw private data、著作物本文をterminal、報告書、commitへ出さない。

## 1. 開始時監査

1. repository root、branch、`git status --short`、近接指示を確認する。
2. `README.md`、`arch/README.md`、対象に関係する正本文書、近接する活動報告、関連する
   `memory/` entryの名前と説明を確認する。
3. `arch/README.md`の索引、実在file、文書status、owner、approver、更新日を一覧化する。
4. 対象system、今回変更してよいscope、非scope、成果物、承認者を確認する。
5. 既存の未コミット変更と今回の対象が重なる場合は、差分と影響を示してから進める。

`arch/`または必要な正本が存在しない場合は、対象scopeに必要な最小構成と作成理由を提案する。
利用者の確認前に空の雛形を一括作成しない。

利用者が途中でfeedbackを入れられるよう、監査中は短い進捗を継続的に共有する。共有するのは、
確認済みの事実、検出した矛盾、現在の仮定、次に確認する箇所、判断が必要な理由とする。
内部の非公開chain-of-thoughtを逐語的に出力しない。

## 2. 文書間の整合性監査

次の観点を確認し、各issueへ`GAP-NNN`の一時ID、severity、影響文書、実装blockerかどうかを付ける。

| 正本 | 確認する内容 |
|---|---|
| SOW | 目的、stakeholder、scope／非scope、成果物、制約、成功条件がSRSと一致するか |
| SRS | `REQ-`／`NFR-` ID、priority、根拠、観測可能な受入条件、failure時の挙動が明確か |
| ADR | 重要判断が1 ADR 1 decisionになり、status、選択肢、影響、supersede関係が正しいか |
| Design | component境界、interface、data/control flow、data model、failure handling、security boundaryが要求を実現するか |
| Test Strategy／Plan | test level、fixture、test oracle、entry／exit条件、quality gate、実行責任が要求を判定できるか |
| Traceability | 各要求がDesign、ADR、test、実装path、evidenceへ双方向に一意対応するか |
| Development Plan | milestone、action item、dependency、owner、status、exit criteriaが現在状態と一致するか |
| Threat Model／Runbook | asset、trust boundary、脅威、mitigation、残存risk、運用責任がscopeに対して十分か |
| arch/README | 文書索引、読む順序、status、現在決定、実装path、supersede関係が実ファイルと一致するか |

表現の違いだけをissueにしない。同じ用語が異なる意味を持つ場合、同じ挙動に異なる上限や既定値が
ある場合、受入条件で判定できない場合、failureの継続範囲が不明な場合を優先する。

## 3. 質問候補を組み立てる

質問前にissueを次の順に並べる。

1. scope、成功条件、公開interface、data／security boundary。
2. 通常flow、failure handling、停止・継続範囲、retry／cost上限。
3. default、parameter、model／provider差、runtime入力として残せる値。
4. test oracle、統計母集団、quality gate、受入条件。
5. traceability、milestone、action item、実装開始条件。
6. baseline approval。これは必ず最後にする。

同じ根本decisionで解消できるissueは1問へまとめるが、独立した判断を1問へ詰め込まない。
文書調査で確定できる事実を利用者へ質問せず、根拠を示して修正案へ含める。

## 4. 1問ずつ対話する

一度に質問するのは1件だけとし、次の形式を使う。

```text
Q-NNN: <短いdecision名>

現在の仕様:
<文書path、要求ID、現在値>

確認が必要な理由:
<矛盾、欠落、実装・test・費用・安全性への影響>

推奨: A — <推奨案>

A. <案>（推奨）
   影響: <利点、制約、変更文書>
B. <代替案>
   影響: <利点、制約、変更文書>
C. <保留、parameter化、別案。妥当な場合だけ>
   影響: <未決のまま残す範囲>

A／B／C、または具体的な修正案で回答してください。
```

- 推奨案を先頭に置き、根拠を簡潔に示す。見かけだけの選択肢を作らない。
- optionの差がtest、互換性、費用、security、統計へ与える影響を明記する。
- 利用者が説明を求めた場合は同じ質問を保持し、解説後に回答を再度求める。
- `C1`などのsub-optionや自由記述を受け付け、回答を勝手に近い選択肢へ丸めない。
- 回答をdecision ledgerへ記録し、影響文書と受入条件を特定してから次の質問へ進む。
- 新しいfeedbackが過去回答と矛盾する場合は、古い回答を暗黙に維持せず差異を再確認する。

## 5. 回答を正本へ反映する

回答ごと、または密接なdecisionの小さなbatchごとに次の順で更新する。

1. 要求変更をSRSの要求、priority、受入条件へ反映する。
2. 重要なarchitecture判断はADRを作成またはsupersedeし、Designへ現在の結論を反映する。
3. Test Strategy／Planのfixture、oracle、failure case、quality gateを更新する。
4. Traceabilityの要求、Design、ADR、planned test、実装path、evidenceを同期する。
5. Development Planのmilestone、action item、dependency、blocker、exit criteriaを同期する。
6. `arch/README.md`の索引、status、現在決定、読む順序を同期する。
7. `arch/`を変更した活動記録として`reports/AI/<topic>.md`と内容固有のSVGを作成または更新し、
   root `README.md`の索引を更新する。現在仕様の正本を活動報告へ重複させない。

承認済みbaselineの意味を変更した場合は、影響文書を`review`へ戻し、更新日とpending approvalを
記録する。typoやlinkだけの非意味的修正でstatusを変える必要があるかは、project規則と影響を見て判断する。

更新後は、採択内容、変更path、まだ未決の点を短く共有する。次の質問は整合性再検査後に提示する。

## 6. 各batch後に検証する

- SRSの`REQ-`／`NFR-`に欠番の誤解、重複、未定義参照がない。
- 各要求IDがTraceabilityへ1行だけ存在し、余分な要求行がない。
- Design、ADR、test、planで既定値、上限、status、failure categoryが一致する。
- ADR番号、filename、title、status、supersede linkが一致する。
- `arch/README.md`のfile、status、読む順序が実体と一致する。
- 完了milestone／action itemにexit criteriaを満たすevidenceがある。
- 変更したMarkdownの相対linkが存在し、活動報告とSVGの参照が成立する。
- 却下済み用語、旧path、古い既定値、`TBD`の取り残しを検索する。
- `git diff --check`を実行する。
- `data/`、private artifact、secret、対象外の利用者変更が差分へ入っていない。

検証失敗は隠さず、そのfailureが質問追加、文書修正、実装blockerのどれに当たるかを示す。

## 7. 最終approval gate

仕様質問と整合性検証が完了した後だけ、次を提示する。

- 採択したdecision一覧と変更文書。
- 意図的にruntime入力へ残した値。
- 残存`TBD`、仮定、外部dependency、非scope。
- 実装、test、migration、費用、securityへの影響。
- 実装開始条件と、次のmilestone／action item。
- 実施した検証と未実施の検証。

最後の質問は次の形式にする。

```text
Q-APPROVAL: Architecture baselineを承認しますか？

A. 承認する（推奨。実装開始条件を満たす場合）
B. reviewのまま維持する
C. 指定したdecisionへ戻って修正する
```

`A`の明示回答を得た場合だけ、対象文書を`approved`へ変更し、承認日、承認者、evidenceを記録する。
review milestone／action itemはexit criteriaを満たす場合だけ完了にする。未決の重大issueがある場合は
`A`を推奨せず、承認不能な理由を示す。

## 8. 完了報告

- 変更した文書と主要decisionを報告する。
- 質問IDと回答の対応、解消したgap、残存riskを報告する。
- link、ID、status、traceability、SVG、`git diff --check`の検証結果を報告する。
- code、test、API call、commit、pushの実施有無を明記する。
- commitを依頼された場合は今回のpathだけをstageし、活動報告／README索引を含む論理的な変更単位で
  commitする。依頼されていなければ未stageのまま引き渡す。
