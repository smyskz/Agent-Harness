# Agent Harness：生成AIエージェントの調査資料

このリポジトリは、AIコーディング環境とエージェント基盤について、公開ドキュメントと固定したソースコードのスナップショットを照合した日本語の調査資料を収録しています。機能比較だけでなく、実装、プロンプト、拡張機構、権限・隔離、運用設計、科学分野のRAG用データベースも扱います。

## リポジトリ構成

ここでいう「ルートGit」は、このAgent-Harness repositoryのGit管理を指します。

| directory | 用途 | ルートGitでの扱い |
|---|---|---|
| [`AGENTS.md`](AGENTS.md)、[`AGENTS-project.md`](AGENTS-project.md) | projectをまたぐAI Agent共通ruleと、このrepository固有のrule | **管理対象**。memory・報告書・Skill／Command改善は`AGENTS.md`、project構成・正本・検証は`AGENTS-project.md`を正本とする |
| [`arch/`](arch/README.md) | 人間と生成AIが共有する、要求、開発計画、設計、ADR、テスト、traceabilityなどのソフトウェア開発文書 | **管理対象**。現在有効なsoftware要求・設計の正本 |
| [`skills/`](skills/diagram-design.md) | AI Agentが依頼内容に応じて利用するSkill | **管理対象**。新規Skillは通常名、既存Skillの改善候補は`-update`名で管理する |
| [`commands/`](commands/README.md) | 利用者が明示実行するAI Agent向けCommand／Skill | **管理対象**。原本と`-update`候補を区別し、通常のGit review対象とする |
| `reports/AI/` | 調査結果をまとめた日本語のMarkdown報告書と付随SVG | **管理対象**。新規報告書の追加・削除・改名時はこのREADME索引も更新する |
| `data/` | 実験評価に使うデータ | **すべて管理対象外**。file種別や内容を問わず`data/`以下をルートGitへ追加せず、`.gitignore`の例外も設けない |
| `docs/` | 論文などのlocal参考資料。paperは`docs/paper/`へ置く | **管理対象外**。`.gitignore`で除外する |
| `memory/` | AI Agent作業から蒸留したlocal長期記憶 | **管理対象外**。`.gitignore`で除外する |
| `continue/`、`codex/`、`claude-code/`、`cline/`、`opencode/` | 調査時に参照する各製品のlocal clone | **ルートGitでは管理対象外**。各directoryは独立したGit repository |

「管理対象」は自動的に追跡済みになるという意味ではありません。新規fileはstageして
commitした時点で追跡されます。「管理対象外」または「管理対象外とする方針」のpathは
原則としてルート`.gitignore`で除外します。表に明記した既存のtracked例外は、別taskで
解消されるまで実態として区別します。独立Git repositoryでの変更やcommitは、ルートGitの
履歴には含まれません。

各報告書には調査日、対象バージョンまたはコミット、根拠、確認できた範囲と制約を記載しています。製品仕様や評価は調査時点のスナップショットであり、最新状態とは異なる場合があります。

## AIコーディング環境ごとの調査

| 報告書 | 概要 |
|---|---|
| [Continue](reports/AI/continue.md) | VS Code／JetBrains、CLI、複数のモデル役割、MCP、コンテキスト、Autocomplete／Next Editを備えたOSS基盤を、保守終了の状況も含めて整理しています。 |
| [OpenAI Codex](reports/AI/codex.md) | Rust製のOSS中核、CLI・IDE・デスクトップ・クラウド・SDK、sandboxと承認、AGENTS.md、Skills、Plugins、MCP、subagent orchestrationを調査しています。 |
| [Claude Code](reports/AI/claude-code.md) | terminal-firstのClaude統合環境について、公開リポジトリと非公開の本体実装を区別しながら、CLAUDE.md、Skills、Plugins、Hooks、subagents、Agent Teamsを整理しています。 |
| [Cline](reports/AI/cline.md) | IDE、CLI、SDK、HubにまたがるOSS agent platformとして、browser、checkpoint、MCP、Rules、Skills、Hooks、Plugins、Agent Teamsとsurface間の差を調査しています。 |
| [OpenCode](reports/AI/opencode.md) | provider-neutralなOSS coding agentとして、TUI・Web・Desktop・IDE・SDKを支えるlocal server、model選択、LSP、agents、plugins、権限設定と安全上の注意点をまとめています。 |

## 横断比較と関連するエージェント基盤

| 報告書 | 概要 |
|---|---|
| [AIコーディング環境の機能比較](reports/AI/comparison.md) | Continue、Codex、Claude Code、Cline、OpenCodeを、公開範囲、surface、model、agent、拡張機構、MCP、隔離・承認、CI／自動化の観点で比較しています。 |
| [Coding Agentの性能差](reports/AI/coding-agent-performance-comparison.md) | Codex、Claude Code、Continue、OpenCodeについて、model単体ではなく、公開実装、prompt、tool、context管理、sandbox、benchmarkと利用者レビューからharnessの性能差を検討しています。 |
| [QM（yc-software/qm）](reports/AI/QM_yc-software.md) | 複数のagent harnessを組織で運用する共有基盤／control planeとしてQMを調査し、ContinueやOpenCodeとの役割の違い、scope、権限、sandboxを整理しています。 |
| [Open WebUI Computer](reports/AI/openwebui-computer.md) | 実機やcontainerをbrowser workstationとして公開し、既存のcoding-agent CLIを接続する基盤について、構成、永続性、gateway、無人実行とsecurity boundaryを調査しています。 |

## 設計・知識基盤

| 報告書 | 概要 |
|---|---|
| [モデル切り替えによるアルゴリズム性能改善](reports/AI/gpt-5.6-model-switching-for-algorithm-optimization.md) | GPT-5.6 Sol／Terra／Lunaとgpt-oss-120bを、統制・実装・大量実行・候補探索へ分担させるworkflow、評価契約、昇格条件を提案しています。 |
| [AGENTS.mdの開発原則](reports/AI/agents-md-development-principles.md) | AGENTS.mdの校正版と、単純な実装、段階的な成長、責務分離、依存関係の活用、長期的な設計という原則を日本語で解説しています。 |
| [科学RAG向け知識データベース](reports/AI/rag-scientific-knowledge-databases.md) | 化学・生物・物理の公開データベースを、RAGでの採用証拠、API・一括取得、license、版固定、provenance、検索方式との適合性から比較しています。 |

## 推奨する読み方

AIコーディング環境の全体像をつかむ場合は、まず[機能比較](reports/AI/comparison.md)を読み、関心のある製品の個別報告へ進んでください。実装やpromptが結果へ与える影響を重視する場合は、続けて[性能比較](reports/AI/coding-agent-performance-comparison.md)を参照してください。

組織や複数の実行環境を含む基盤を検討する場合は[QM](reports/AI/QM_yc-software.md)と[Open WebUI Computer](reports/AI/openwebui-computer.md)、アルゴリズム改善の役割分担を設計する場合は[モデル切り替え報告](reports/AI/gpt-5.6-model-switching-for-algorithm-optimization.md)、科学RAGを設計する場合は[知識データベース調査](reports/AI/rag-scientific-knowledge-databases.md)から読むのが適しています。
