---
name: diagram-design
description: 技術・製品・業務の内容を、architecture、IT current-state、flowchart、sequence、state machine、ER、timeline、swimlane、quadrant、radar、loop、nested、tree、org chart、layers、Venn、pyramid、bar／line chart、Gantt、scatter、process、data platform図などの編集品質のSVGまたはself-contained HTMLとして設計・作成・改訂するときに使用する。
---

# Diagram Design

情報を飾るためではなく、読者が文章や表だけの場合より速く正確に理解できる図を作る。
図を作る価値がない場合は、無理に図解せず、文章、表、箇条書きを提案する。

## 成果物の契約

- 利用者がformatとpathを指定した場合は、それを優先する。
- 指定がなく、独立した図を作る場合は、embedded CSSとinline SVGを含む単一の`.html`を
  既定とする。build stepとJavaScriptを必要としないものにする。
- Markdown報告書、設計書、slideなどへ組み込む場合は、その文書の規約に合わせて独立した
  `.svg`またはinline SVGを作る。HTML wrapperを強制しない。
- 報告書先頭のexecutive-summary SVGでは、報告の最重要項目を一つだけ主題に選び、成果、
  根拠、影響または次の判断を一目で読める構成にする。複数の指標を同じ重みで並べる固定
  dashboardにしない。
- 原則として外部image、外部JavaScript、runtime依存を使わない。fontを外部取得する場合は
  network非接続時のfallback fontを指定し、portable性が必要ならsystem fontまたは埋込みを
  選ぶ。
- chartの数値、状態、関係、時系列を推測で作らない。根拠がない値は、mockupであることを
  明示する依頼の場合だけplaceholderとして使う。

## 1. 図にする内容を確定する

作図前に、次を短いdiagram briefとして内部で整理する。利用者の依頼とrepositoryから
一意に判断できる項目を聞き直さない。意味や結果が変わる不足だけを確認する。

1. 読者と利用場面を特定する。
2. 図を見た読者に残すべき一文を`main message`として定める。
3. 根拠となるsource、data、要求、設計、既存図を特定する。
4. node、group、relation、順序、数量、annotationを列挙する。
5. 最重要の1～2要素と、補助情報を分ける。
6. 出力format、path、縦横比、light／dark、必要なbrand適合を確認する。

同じ意味を常に一緒に運ぶnodeは統合する。位置だけで関係が明らかなconnectionや、なくても
理解が変わらないlabelは削除する。情報密度は10段階で4程度を目安とし、nodeが9個、
connectionが12本を超える場合は、要約図と詳細図へ分割するか、主題に不要な要素を除く。

## 2. Diagram typeを選ぶ

表現したい関係に最も合うtypeを一つ選ぶ。複数typeを混ぜた独自grammarを安易に作らず、
主要な軸を決める。

| 表現する内容 | Type | 守るgrammar |
|---|---|---|
| systemのcomponent、boundary、connection | Architecture | containerで境界を示し、方向とprotocolを必要なconnectionだけに付ける |
| modernization前のlegacy landscape | IT current-state | phase、部門またはzoneで現状を分類し、課題とfuture stateを混同しない |
| 条件分岐を含む判断手順 | Flowchart | start／end、action、decisionを形で区別し、branchへ条件を付ける |
| actor間の時系列message | Sequence | 時間を上から下へ進め、lifeline、message、return、guardを区別する |
| stateとtransition | State machine | state、event、guard、action、initial／terminalを明示する |
| entity、field、relationship | ER / data model | PK／FK、cardinality、optional性を必要な範囲で示す |
| 日時順のevent | Timeline | 一つの時間軸、基準日、intervalの意味を明示する |
| 担当者をまたぐprocess | Swimlane | laneを責任主体に対応させ、handoffを境界上で読めるようにする |
| 2軸上のpositioningまたは優先度 | Quadrant | 軸名、方向、尺度、象限の意味を明示する |
| 3～5指標のprofile比較 | Radar / spider | scaleを統一し、seriesを5以下、focal seriesを1つにする |
| 最終stepが最初へ戻るreinforcing cycle | Loop / flywheel | 方向付きcycleと、蓄積するhubまたはfeedbackを区別する |
| containmentによるscope階層 | Nested | 親の意味が子を包含するようにし、深さを6以下にする |
| parent-child関係 | Tree | rootを一つにし、siblingの粒度を揃え、深さを4以下にする |
| ownership、reporting、routing | Org chart | 人・team・agentの責任線を示し、node 12、深さ4を目安にする |
| abstractionやstackの上下関係 | Layer stack | 各layerの責務と依存方向を揃え、6 layer以下にする |
| setの重なり | Venn | setを3以下にし、intersectionの意味を直接labelする |
| rank、成熟度、conversionの絞り込み | Pyramid / funnel | 上下方向の意味と量の変化を説明し、6段以下にする |
| category間の数量比較 | Bar chart | 単位とsourceを示し、原則zero baseline、bar 8本以下にする |
| 時間に沿う連続的なtrend | Line chart | 時間軸、単位、欠損、seriesを明示し、series 5本以下にする |
| taskとphaseの期間 | Gantt | 日付尺度、dependency、milestoneを区別し、task 12件以下にする |
| 2変数の分布や相関 | Scatter plot | 両軸の単位、sample、encodingを示し、point 30件以下を目安にする |
| cluster上のend-to-end stack | High-level | phase banner、deployment zone、横断的concernを分け、end-to-endで俯瞰できる順序にする |
| 複数actorが順番に進める業務 | Process | stage、owner、input／output、handoffを一方向に読む |
| bronze／silver／gold等のdata tier | Medallion | tierごとの品質、変換、access policyを区別する |
| pipeline stepごとのroleとdata移動 | Data flow | source、transformation、sinkと、各stepの責任主体を示す |
| data platformのsource→core→consumer | DP integration | integration boundaryとinterfaceを中心にtopologyを示す |
| roleまたはcomponent別の権限 | DP security matrix | row／column、permission symbol、deny／unknown、legendを明示する |

3列程度の表で同じ内容をより明確に伝えられる場合は表を使う。before／afterだけなら比較表、
一つのshapeだけなら一文、terminal向けの簡易説明ならtext diagramを優先する。

## 3. Layoutを設計する

- 先にreading orderとgroupを決め、座標はその後に置く。
- 座標、node寸法、gap、paddingは原則4の倍数に揃える。stroke幅、opacity、細いradiusは
  例外としてよい。
- node間は20～48、内側paddingは8～16を目安とし、labelが収まる前にboxを小さくしない。
- 同じ種類のnodeは同じshapeを使い、focal、store、external、optional、securityなど意味が
  異なるものだけfill、stroke、dash、tagを変える。
- shadow、強いglow、大きすぎる角丸、cyan／purpleの安易なdark-mode表現、同じ大きさの
  generic card gridを既定にしない。border、余白、typography、alignmentで階層を作る。
- legendが必要ならdiagram領域の外側、原則として下部の横長stripへ置く。使っていない
  symbolをlegendへ載せない。
- SVGに`viewBox`を設定し、固定pixel寸法だけに依存させない。`role="img"`、内容を表す
  `<title>`と`<desc>`を付け、読み順とcontrastを確認する。

## 4. Connectorを設計する

1. connectorをnodeより先に描き、nodeが線を自然にmaskするz-orderにする。
2. 始点と終点が同じxまたはyに揃う場合だけ直線を使う。off-axisの接続は、半径6～8程度の
   quarter arcを持つ直角connectorにする。意味のない斜線を使わない。
3. connector同士を同じpathへ重ねない。parallel pathは12程度離し、交差が避けられない場合は
   bridge／hopでどちらの線が続くか示す。
4. 同じbox edgeへ複数の線を接続する場合は、attach pointを12以上離してfan outする。
   connectorを途中で重ねて一本に見せない。
5. sourceでもdestinationでもないboxの背後を通さず、周囲へ迂回する。幾何的に避けられない
   transitだけはdashed lineにし、labelとarrowheadが途中のboxをendpointに見せないようにする。
6. arrow labelには`paper`色のopaque maskを置き、線から6～10離す。縦線のlabelは横へ置き、
   vertical writing-modeを使わない。
7. line styleの意味を一定にする。通常は`muted`、primary flowは`accent`、external/APIは`link`、
   optional／async／returnはdashedとし、必要な場合だけlegendで説明する。

直角connectorはarchitectureやprocess系の既定であり、type固有grammarを壊してまで適用しない。
Sequenceは水平messageと垂直lifeline、State machineは意味のある曲線transition、Loopは同じ半径の
clockwise arcとhubへのspoke、Medallionはtier間のarcを使ってよい。DP security matrixには
connectorを追加しない。例外の場合も、重なり、曖昧な方向、node背後の通過を残さない。

## 5. Type固有の上限を適用する

- Sequenceはlifeline 5以下、combined fragmentは原則1、nestingは1段にする。
- Swimlaneはlane 5以下、ERはentity 8以下、Vennはcircle 3以下にする。
- Quadrantはitem 12以下、Radarはaxis 5以下、series 5以下にする。
- Treeとorg chartは深さ4以下、org chartはnode 12以下にする。
- Layersとpyramidは6段以下、Ganttはtask 12以下にする。
- Barは8本以下、Lineはseries 5以下、Scatterはpoint 30以下にする。
- annotation calloutは2個以下にする。

上限を超える場合は小さく詰め込まず、overviewとdetailへ分ける。1枚だけを求められた場合は、
main messageに直接関係する内容へ削る。

定量図では、axisの切断、実時間を偽る等間隔、根拠のないspline、見かけのtrendを強めるscaleを
使わない。Radarへ異なる単位を載せる場合は、正規化方法とscaleを示す。GanttとTimelineは
日付の距離をdataに忠実にするか、not-to-scaleであることを明記する。

## 6. Optional primitiveを使う

- Editorial calloutは、図から少し離した短い注釈とleader lineで、判断または注意点を補う。
  本文の代わりに大量の説明を載せない。
- Sketchy variantは、essayや概念説明で手描き感が意味を持つ場合だけ使う。filterはshapeへ
  限定し、textを歪ませない。正確なarchitecture、security、data modelでは既定にしない。
- Terminal variantはCLIやdeveloper toolを主題にする場合だけ使い、brand適合が必要な図へ
  無条件に適用しない。
- Iconは識別を速める場合だけ使う。装飾として増やさず、同じ体系とstroke／fill規則に揃える。
  外部iconを取り込む前にlicenseとtrademark条件を確認する。

## 7. 実装する

1. briefをnode、group、connector、annotationの一覧へ落とす。
2. 選んだdiagram typeのgrammarに合わせてrough layoutを決める。
3. 不要なnode、line、labelを除き、focal elementを1～2個に絞る。
4. semantic tokenとtypographyを適用する。
5. connector、group boundary、node、label、annotation、legendの順でSVGを組み立てる。
6. HTMLの場合はCSSとSVGを一つのfileへ含め、JavaScriptなしで表示できるようにする。
7. repositoryの命名、配置、link、Git管理境界に従う。既存fileを改訂する場合は、適用される
   Skill／Command改善規則も確認する。

## 8. 出力前に検証する

### 内容

- [ ] main messageを一文で説明でき、図のfocal elementと一致する。
- [ ] sourceにない数値、関係、状態、日付を作っていない。
- [ ] 文章または表より図の方が理解を助ける。
- [ ] 選んだtypeのgrammarを一貫して使っている。
- [ ] 削除または統合できるnode、line、labelが残っていない。

### 視覚

- [ ] 最小表示幅と想定表示幅の両方で、文字切れ、重なり、過密、不要な余白がない。
- [ ] title、主経路、補助情報の順に視線が移り、accentは2要素以下である。
- [ ] contrast、font size、色以外の識別手段が十分である。
- [ ] legendとannotationがnodeやconnectorへ衝突していない。
- [ ] genericなcard grid、glow、shadow、過剰な角丸で意味を代替していない。

### SVGとconnector

- [ ] `viewBox`、`title`、`desc`があり、XML／HTMLとしてparseできる。
- [ ] off-axis connectorは直角にroutingされ、斜め線になっていない。
- [ ] connectorの重なり、共有attach point、非endpoint nodeの背後通過がない。
- [ ] arrow labelのmaskと6～10のgapがあり、線を隠していない。
- [ ] arrowheadがtarget borderで終わり、marker／clipPath／filterのID参照が壊れていない。
- [ ] node、座標、gapがgridへ揃い、stroke、marker、text、marginがviewBoxからclipされない。

可能なら実際にbrowserでrenderし、desktop幅と狭い幅のscreenshotを目視する。format validatorの
成功だけで視覚品質を合格にしない。修正後は再renderし、変更箇所だけでなく全体を確認する。

## 9. Exportは依頼された場合だけ行う

- `.svg`を求められた場合は、diagramのSVG nodeだけを独立fileにし、XML namespaceと
  `viewBox`を保持する。fontが外部依存なら、その制約またはfallbackを伝える。
- `.png`を求められた場合は、元HTMLをbrowserでrenderし、最初のdiagram SVGのbounding boxを
  透過背景でcaptureする。既定は2x相当とし、利用者指定のscaleを優先する。
- 一つのHTMLに複数SVGがある場合は対象を推測しない。どの図をexportするか確認する。
- export toolやbrowser runtimeを自動installしない。不足している場合は必要条件を示して止める。
- source HTMLをexport処理のために書き換えず、HTML生成時にPNG／SVGを勝手に併産しない。
