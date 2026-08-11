---
name: map-repository-architecture
description: ユーザーが`$map-repository-architecture`を明示した場合だけ、指定したlocalまたはremote repositoryの全体architectureを静的に調査し、同一modelから機械可読JSONとself-containedな単一HTMLページを作成する。component、relation、runtime flow、data、deployment、trust boundary、根拠、推論、未確認事項を俯瞰可能にするときに使用する。
---

# Repository Architecture Mapを作成する

## 実行契約

ユーザーが`$map-repository-architecture`を明示した場合だけ実行する。architectureに関する
一般的な質問へ暗黙に適用しない。指定repositoryをread-onlyの調査対象とし、依頼されたHTMLと
JSON以外のsource、設定、文書、Git状態を変更しない。commit、push、Issue／PR作成、外部公開は
別途明示的に依頼された場合だけ行う。

「全体architecture」は、宣言したscope内で検出できる主要component、boundary、interface、
data store、runtime flow、build／deployment構成をcomponent levelで網羅することを意味する。
全fileの内容を転載することや、証拠のない関係を埋めて完全に見せることを意味しない。読めなかった
範囲、除外、推論、競合する証拠を成果物へ明示する。

## 入力

- `repository`: local pathまたはremote Git URL。省略時は現在のGit rootを使う。
- `ref`: 任意のbranch、tag、commit。省略時、local repositoryは現在のworktreeを対象にする。
- `output_base`: 拡張子を除いた出力path。`<output_base>.html`と`<output_base>.json`を作る。
- `scope`: 任意のsubdirectory、package、service、または除外条件。
- `include_untracked`: 既定`false`。local worktreeの未追跡fileを対象に含める場合だけ`true`にする。
- `detail`: `overview | standard | deep`。既定は`standard`とする。

`output_base`が省略された場合は、targetと出力先の関係を次の順で決める。

1. targetが現在のwritable Git rootなら、既存規約に合うreport／documentation directoryを探す。
2. 適切なdirectoryが一意なら、`<directory>/<repository-name>-architecture-map`を提案する。
3. targetがremote、read-only、nested repository、または出力規約が曖昧なら、書き込み前に
   `output_base`を確認する。

既存の`.html`または`.json`と衝突する場合は上書きしない。両方のpath、Git管理状態、差分への
影響を示し、上書きまたは別名について確認する。一方だけが存在する場合も片方だけを更新しない。

## 1. Snapshotと境界を確定する

1. 現在地、targetの実path、Git root、branch、HEAD、`git status --short`、submodule、nested Git
   repository、適用される`AGENTS.md`等の指示を確認する。
2. localで`ref`がない場合は現在のworktreeを対象とし、開始commitとdirty状態を記録する。
   trackedなworktree変更は含め、未追跡fileは`include_untracked: true`の場合だけ含める。
3. `ref`が指定された場合は、targetのcheckoutやbranchを変更せず、一時cloneまたは一時worktreeで
   固定snapshotを読む。成果物には解決後の完全commit SHAを記録する。
4. remote URLが明示された場合だけnetwork取得する。認証tokenをURL、log、成果物へ含めない。
   cloneは一時directoryに作り、target repositoryへsubmoduleやvendor copyとして追加しない。
5. nested repositoryとsubmoduleは別boundaryとして列挙する。明示的にscopeへ含められていない
   nested repositoryの内部を再帰調査しない。
6. non-Git directoryの場合はfilesystem snapshotとして扱い、Git provenanceを推測しない。

調査開始時に、対象snapshot、含めるroot、除外、未追跡fileの扱い、予定出力pathを簡潔に示す。

## 2. Repository inventoryを作る

Git repositoryでは`git ls-files`を基本inventoryとする。non-Git directoryではignore規則を尊重する
file検索を使う。最初にtree全体をsurveyし、次のarchitecture signalを優先して読む。

- root／近接するAgent指示、README、architecture文書、ADR、runbook、threat model。
- workspace／package manifest、lockfile、module定義、build tool設定。
- executable entrypoint、route、controller、CLI、worker、scheduler、plugin registration。
- domain／application／infrastructure module、public interface、schema、migration、event definition。
- database、cache、queue、object storage、filesystem、外部APIのadapterと設定。
- Dockerfile、Compose、Kubernetes、Terraform等のdeployment／infrastructure定義。
- CI/CD、release、test構成。testは境界の補助証拠とし、production runtimeと混同しない。

`.git/`、cache、build output、generated bundle、vendor、dependency directory、binary、大規模data、
coverage、temporary artifactは本文を読まない。trackedでarchitecture上の意味がある生成物は、存在と
生成元だけを記録する。`.env`、credential、private key、token、個人情報を開かず、値を成果物へ
含めない。secretらしいfileは`redacted-sensitive`として除外理由だけを記録する。

巨大repositoryでは全fileを無条件に読み込まない。manifestとentrypointからcomponent候補を作り、
import、registration、configuration、deployment定義を辿って境界を確定する。読んだfile数、対象の
tracked file数、除外categoryを記録し、samplingを全件確認と表現しない。

## 3. 根拠からarchitecture modelを組み立てる

次の順序でmodelを作る。

1. repository、workspace、package、process、deployment、trust、data ownershipのboundaryを特定する。
2. entrypointからruntime componentを特定し、責務とpublic interfaceを一文で記述する。
3. code、config、schemaからrelationを抽出する。package dependencyだけをruntime callと断定しない。
4. user request、batch、event、startup、failure recovery等の主要flowをrelation IDの列として記述する。
5. datastore、queue、external system、authentication、network boundaryを通常componentと区別する。
6. documentationの主張とsource／configの実態を照合し、矛盾は片方へ寄せず`unresolved`へ残す。
7. componentとrelationを統合し、同じ責務をfile単位の多数のnodeへ分割しない。

すべての重要な主張へrepository-relativeなevidenceを付け、次を区別する。

| `basis` | 意味 |
|---|---|
| `source-confirmed` | executable source、schema、test対象のinterfaceで直接確認した |
| `config-confirmed` | manifest、deployment、runtime設定で直接確認した |
| `docs-stated` | 文書に記載されているが実装との一致を独立確認できていない |
| `inferred` | 複数のsignalから推論した。断定せず理由と反証条件を残す |

confidenceは`high | medium | low`とする。line範囲またはsymbolを取得できる場合は併記し、pathだけで
主張が再検証できない状態を避ける。証拠がないnodeやedgeを見た目の整合のために作らない。

## 4. JSONを正本として作る

先に`<output_base>.json`のobjectを完成させ、HTMLを同じobjectから生成する。UTF-8、2-space
indent、stable key order、末尾改行で保存する。IDは同じsnapshotから再生成しても変わりにくい
kebab-caseとし、`cmp-`、`rel-`、`flow-`、`bnd-`、`gap-`をprefixに使う。

最低限、次のshapeを満たす。

```json
{
  "schemaVersion": "repository-architecture-map/v1",
  "generatedAt": "YYYY-MM-DDTHH:mm:ssZ",
  "repository": {
    "name": "example",
    "root": ".",
    "vcs": "git",
    "snapshotMode": "worktree",
    "branch": "main",
    "commit": "FULL_SHA",
    "dirty": false,
    "includeUntracked": false,
    "languages": [],
    "frameworks": []
  },
  "scope": {
    "includedRoots": ["."],
    "excluded": [],
    "nestedRepositories": [],
    "trackedFileCount": 0,
    "inspectedFileCount": 0,
    "limitations": []
  },
  "pathMappings": [
    {
      "path": "src/api",
      "componentIds": ["cmp-example-api"],
      "role": "runtime-source"
    },
    {
      "path": "db",
      "componentIds": ["cmp-example-store"],
      "role": "schema"
    }
  ],
  "components": [
    {
      "id": "cmp-example-api",
      "name": "Example API",
      "type": "api",
      "layer": "application",
      "responsibility": "公開requestを受け付ける",
      "paths": ["src/api"],
      "entrypoints": ["src/api/main.ts"],
      "technologies": ["TypeScript"],
      "interfaces": ["HTTP"],
      "evidence": [
        {
          "path": "src/api/main.ts",
          "lines": "12-48",
          "symbol": "bootstrap",
          "basis": "source-confirmed",
          "supports": "HTTP entrypointを登録する"
        }
      ],
      "confidence": "high"
    },
    {
      "id": "cmp-example-store",
      "name": "Example Store",
      "type": "datastore",
      "layer": "infrastructure",
      "responsibility": "永続recordを保持する",
      "paths": ["db/schema.sql"],
      "entrypoints": [],
      "technologies": ["PostgreSQL"],
      "interfaces": ["SQL"],
      "evidence": [
        {
          "path": "db/schema.sql",
          "lines": "1-32",
          "symbol": "records",
          "basis": "source-confirmed",
          "supports": "永続recordのschemaを定義する"
        }
      ],
      "confidence": "high"
    }
  ],
  "relations": [
    {
      "id": "rel-example-api-to-store",
      "from": "cmp-example-api",
      "to": "cmp-example-store",
      "type": "writes",
      "direction": "outbound",
      "protocol": "SQL",
      "data": ["record"],
      "synchronous": true,
      "evidence": [
        {
          "path": "src/api/repository.ts",
          "lines": "18-41",
          "symbol": "saveRecord",
          "basis": "source-confirmed",
          "supports": "APIからSQL writeを実行する"
        }
      ],
      "confidence": "medium"
    }
  ],
  "flows": [
    {
      "id": "flow-example-request",
      "name": "Example request",
      "trigger": "HTTP request",
      "relationIds": ["rel-example-api-to-store"],
      "outcome": "recordを保存する",
      "evidence": [
        {
          "path": "src/api/routes.ts",
          "lines": "10-36",
          "symbol": "createRecord",
          "basis": "source-confirmed",
          "supports": "requestから保存処理を呼び出す"
        }
      ],
      "confidence": "medium"
    }
  ],
  "boundaries": [],
  "unresolved": [],
  "provenance": {
    "commands": [],
    "sourceDocuments": [],
    "generatedBy": "map-repository-architecture"
  }
}
```

component `type`には`entrypoint | ui | api | service | domain | worker | library | datastore | queue |
external | infrastructure | build | test`を基本として使う。relation `type`には`calls | publishes |
consumes | reads | writes | depends-on | contains | deploys | configures | tests`を使い、必要な追加typeは
成果物内で定義する。`unresolved`にはID、question、impact、evidence、resolutionConditionを入れる。
`pathMappings`はarchitecture上のsource／config／schema／deployment pathをcomponentへ対応付ける。
architectureに関係するrootがcomponentへ対応しない場合は、`scope.excluded`または`limitations`へ
理由を記載する。

absolute path、home directory、credential、source本文、巨大なdependency一覧をJSONへ含めない。
direct runtime dependencyとarchitecture上重要なtoolだけをcomponentまたはtechnologyとして扱い、
transitive dependencyは集計に留める。

## 5. 単一HTMLページを生成する

`<output_base>.html`を、外部CDN、外部font、外部image、build stepを必要としないself-containedな
HTMLとして作る。CSS、SVG、必要最小限のJavaScriptをinlineにする。JavaScriptは検索、filter、
detail展開のprogressive enhancementだけに使い、無効でも主要architectureと根拠を読めるようにする。

同じJSON objectを`<script id="architecture-data" type="application/json">`へ埋め込む。`</script>`注入を
防ぐため`<`等をJSON escapeし、DOM textとして安全にparseする。別JSONのexact UTF-8 bytesから
SHA-256を計算し、HTMLのvisible provenanceと`data-json-sha256`へ記載する。
hash、component／relation／flow／gap件数は生成programから直接HTMLへ渡し、手作業で転記しない。

ページは最低限、次を一つのreading orderで含める。

1. repository名、snapshot、scope、最重要のarchitecture summary。
2. system contextと主要component／relationを示すmain SVG。
3. component catalogとresponsibility、interface、technology、evidence。
4. 主要runtime flow、data flow、async boundary、failure／recovery flow。
5. deployment、process、trust、data ownership、nested repositoryのboundary。
6. datastore、external system、direct dependency、build／test／releaseの要約。
7. evidence coverage、docsとsourceの矛盾、未確認事項、除外、再検証条件。
8. commit、dirty状態、調査日時、JSON hash、実行したread-only commandのprovenance。

main SVGでは主要componentを9個程度までにまとめ、詳細componentはcatalogへ置く。off-axis connectorは
直角にroutingし、線の重なり、共有attach point、非endpoint nodeの背後通過を避ける。色だけで
component typeやconfidenceを表さず、label、stroke、shape、legendも使う。`viewBox`、`title`、`desc`を
付ける。利用可能な`diagram-design` Skillがあれば、そのlayoutと視覚QAを適用してよいが、成果物を
そのSkillの存在へ依存させない。

genericなcard dashboardをarchitectureの代わりにしない。最初にsystem boundaryと主経路が読める
構成にし、component数やlanguage比率などの集計は補助情報として扱う。

## 6. JSONとHTMLを検証する

書き込み前にmodelを検査し、両fileを一つの変更単位で作成する。少なくとも次を確認する。
parse、reference、deep-equality、hash、renderは、引数でpathを渡す小さなvalidator scriptまたは
独立した単純なcommandとして実行する。raw JSONをshell commandへ埋め込んだ長い一行の検証を作らない。
shell quoting等でvalidator自体が実行できなかった場合は、成果物の不合格とは区別しつつ、validatorを
修正して全checkを再実行するまで検証成功と報告しない。

### JSON integrity

- JSONがparseでき、`schemaVersion`と必須top-level keyが存在する。
- 全IDが一意でprefixとkebab-caseに従う。
- relationの`from`／`to`、flowの`relationIds`、boundaryのmember IDにdangling referenceがない。
- `pathMappings.componentIds`が実在し、architecture上の主要rootがmappingまたは明示的な除外を持つ。
- evidence pathが対象snapshotに存在し、line範囲またはsymbolが主張を直接支える。
- `inferred`と`docs-stated`を`source-confirmed`として扱っていない。
- absolute path、secret、credential値、source本文が含まれていない。
- file件数、component／relation／flow件数がinventoryと矛盾しない。

### HTML integrity

- HTMLがparseでき、外部runtimeまたはnetwork requestなしで開ける。
- embedded JSONがparseでき、別JSONとdeep-equalである。
- HTMLに表示したJSON SHA-256が別JSONのexact bytesと一致する。
- component、relation、flow、gapの表示件数がJSONと一致する。
- main SVGのID参照、marker、label、connector、viewBoxに破損やclipがない。
- desktop幅と狭い幅でrenderし、文字切れ、重なり、横方向の不可読な縮小がない。
- JavaScriptを無効にしてもsummary、main map、catalog、evidence、limitationsが読める。

### Scope and repository integrity

- target repositoryのsource、設定、Git branch、index、submodule、nested repositoryを変更していない。
- output以外の既存未コミット変更を変更、stage、復元していない。
- outputをGit管理する場合は、projectのindex、link、Git境界の更新要否を確認する。
- 検証不能な項目は成功扱いにせず、理由と再検証方法をHTMLとJSONの両方へ残す。

## 7. 完了報告

次を簡潔に報告する。

- target repository、snapshot mode、branch／commit、dirty状態、scope。
- HTMLとJSONのpath、JSON SHA-256、component／relation／flow／unresolved件数。
- 確認した主要entrypoint、boundary、data store、deployment、外部system。
- 実施したparse、reference、deep-equality、hash、render検証と結果。
- 除外、low-confidence inference、docsとの矛盾、未確認事項。
- targetへ変更を加えていないこと、outputのGit管理状態、commit／pushの未実施または結果。

SHA-256と件数は、成功した最終validatorの出力からそのまま取得する。記憶や手入力で転記しない。

追加のMarkdown報告書、source変更、dependency install、build／test実行を自動で行わない。利用者が
別途求めた場合だけ、対象と影響を確認して実施する。
