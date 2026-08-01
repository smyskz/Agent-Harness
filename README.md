# Agent Harness：生成AIエージェントの調査資料

このリポジトリは、AIコーディング環境とエージェント基盤について、公開ドキュメントと固定したソースコードのスナップショットを照合した日本語の調査資料を収録しています。機能比較だけでなく、実装、プロンプト、拡張機構、権限・隔離、運用設計、科学分野のRAG用データベースも扱います。

## リポジトリ構成

- `reports/AI/`: 調査結果をまとめたMarkdown報告書
- `continue/`、`codex/`、`claude-code/`、`cline/`、`opencode/`: 調査時に参照する各製品のローカルクローン。容量と履歴の重複を避けるためGit管理の対象外

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
