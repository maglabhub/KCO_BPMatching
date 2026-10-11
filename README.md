# KCO_BPMatching

KCO_BPMatching は、MUSUBIX を参考にした「仕様駆動開発（Specification-Driven Development, SDD）」のセットアップを持つリポジトリです。実際には、`musubix3` を依存として導入し、`.musubix/` と `.github/skills/` を使って要件・設計・検証の基盤を整備する構成になっています。

現時点では、BPMatching の本体実装まではまだ初期化段階であり、README は最小構成です。主要な構成は、プロジェクトの方針・要件・設計・証跡を `musubix3` の規約で管理するための骨格です。

## 1. このリポジトリの特徴

- 依存パッケージとして `musubix3` を使用
- `.musubix/` 配下に、要件・設計・憲章・証跡を管理する構造がある
- `.github/skills/` 配下に GitHub Copilot 向けのスキル群がある
- 実際の BP matching 実装はまだ未構成、現時点では scaffold / 基盤整備が中心

package.json の内容を見ると、依存関係は以下の通りです。

```json
{
  "name": "my-musubix-project",
  "version": "1.0.0",
  "dependencies": {
    "musubix3": "^0.1.20"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

これは、現時点で「BP matching の本体コード」よりも「musubix3 による仕様開発基盤の準備」が主目的であることを示しています。

## 2. ディレクトリ構成

実際のリポジトリ構成は次のとおりです。

```text
KCO_BPMatching/
├── README.md
├── SECURITY.md
├── package.json
├── package-lock.json
├── .gitignore
├── .musubix/
│   ├── config.json
│   ├── constitution.md
│   ├── policy-baseline.json
│   ├── features/
│   │   ├── BPMatching/
│   │   │   └── README.md
│   │   └── example/
│   ├── decisions/
│   │   └── ADR-0001.md
│   └── evidence/
│       └── quality.json
├── .github/
│   └── skills/
│       ├── sdd-change/
│       ├── sdd-design/
│       ├── sdd-formal-codegraph/
│       ├── sdd-implementation/
│       ├── sdd-issue-report/
│       ├── sdd-knowledge/
│       ├── sdd-quality/
│       ├── sdd-requirements/
│       └── sdd-traceability/
├── node_modules/
│   └── musubix3/
└── .musubix/cache/  (生成物、通常は除外される)
```

## 3. 主要ディレクトリの役割

### 3.1 `.musubix/`

このディレクトリがリポジトリの中核です。`musubix3` の設計思想に従い、要件・設計・検証の証跡を一元管理します。

#### `.musubix/config.json`

```json
{
  "schemaVersion": 1,
  "language": "auto",
  "qualityProfile": "custom",
  "requiredChecks": [
    "requirements",
    "design",
    "constitution",
    "trace",
    "graph",
    "commands"
  ],
  "thresholds": {
    "design": 1,
    "implementation": 1,
    "tests": 1
  }
}
```

この設定では、最低限として以下を強制しています。

- requirements: 要件があるか
- design: 設計があるか
- constitution: 憲章が守られているか
- trace: 要件と設計と実装の関連が追跡可能か
- graph: コード依存関係が妥当か
- commands: 実際に検証コマンドが実行されているか

#### `.musubix/constitution.md`

憲章は、プロジェクトが守るべき原則を定義します。実際には次の 2 つが定義されています。

```md
## PRINC-001: Evidence before claims / 主張には根拠を
### RULE-001: Trace mandatory requirements / 必須要求を追跡する
Metric: trace.errors
Limit: 0

## PRINC-002: Run real checks / 実際に検証する
### RULE-002: No failed verification commands / 検証失敗を許可しない
Metric: commands.failures
Limit: 0
### RULE-003: Do not pass skipped checks / 未実行を成功扱いしない
Metric: commands.skipped
Limit: 0
```

要するに、

- 「言っただけ」では通らない
- 「検証されていないもの」を品質とみなさない
- 手を抜いたチェックを通してはいけない

という原則です。

#### `.musubix/features/`

ここには機能単位の設計・要件の材料が置かれます。実際には `BPMatching` と `example` が存在しています。

`BPMatching` はメイン機能の想定配置です。

```text
.musubix/features/BPMatching/
└── README.md
```

現時点では README だけの状態で、以下のようなコンテンツを後に作る想定が強いです。

- requirements.md
- design.md
- implementation.md
- tests.md

つまり、BP matching の実装前提を、仕様として書き起こす場所です。

#### `.musubix/decisions/`

設計判断の記録が残ります。

- ADR-0001.md

内容は、

- 「Copilot-native specification development を採用する」
- 「GitHub Copilot を中心に計画・編集・検証を行う」
- 「並列 AI ランタイムを導入しない」

という方針です。

#### `.musubix/evidence/`

検証の証跡が格納されます。現時点では `quality.json` があるものの、内容は以下です。

```json
{
  "schemaVersion": 1,
  "status": "skipped",
  "generatedAt": null,
  "checks": [],
  "reason": "No checks have run. Configure commands and run musubix3 gate."
}
```

即ち、現在の状態は「チェックが未実行」であり、品質ゲートがまだ走っていません。これは、実装がまだ未整備であることを示唆します。

## 4. `.github/skills/` の意義

このディレクトリには、GitHub Copilot CLI で利用可能なスキルが入っています。実際に含まれているスキルは以下です。

- `sdd-change`
- `sdd-design`
- `sdd-formal-codegraph`
- `sdd-implementation`
- `sdd-issue-report`
- `sdd-knowledge`
- `sdd-quality`
- `sdd-requirements`
- `sdd-traceability`

それぞれの役割は、以下のような流れです。

- 要件の明確化
- 設計の明文化
- 実装の進行
- 依存関係の解析
- 変更の追跡
- 品質確認
- 議論の文書化

この構成により、Copilot が単なるコード生成機ではなく、

- 要件を受け取る
- 設計を議論する
- 実装と証跡を管理する
- 品質ゲートを通す

役割を持てるようになっています。

## 5. MUSUBIX との対応関係

MUSUBIX では、一般的に以下のような構成が取られます。

```text
MUSUBIX/
├── .musubix/
├── .github/
├── docs/
├── packages/
├── scripts/
├── storage/
├── examples/
└── tests/
```

一方 KCO_BPMatching は、現時点で MUSUBIX の「マニフェスト/骨格」をそのまま採用した状態です。

| 観点 | MUSUBIX | KCO_BPMatching |
|------|--------|---------------|
| 主目的 | 汎用 SDD フレームワーク | BPMatching 向け実装の土台 |
| 中核 | `.musubix/` + `.github/skills/` | `.musubix/` + `.github/skills/` |
| 実装本体 | `packages/` など | 現時点では未形成 |
| 実運用状態 | 実際のプロジェクト例 | 初期化/スキャフォールド段階 |

つまり、このリポジトリは「MUSUBIX の仕組みを使って BPMatching を開発するための準備段階」にあります。

## 6. 現在の状態と今後の完了イメージ

現時点の事実として、以下が確認できます。

- `musubix3` が依存としてインストールされている
- `.musubix/` の設定が存在する
- `.github/skills/` が導入されている
- `quality.json` は `skipped` の状態
- package.json の `test` スクリプトは未設定のまま

したがって、このリポジトリはまだ「実装開始前」または「初期セットアップ完了」段階です。

完成形のイメージは以下のような構造です。

```text
KCO_BPMatching/
├── .musubix/
│   ├── features/
│   │   └── BPMatching/
│   │       ├── requirements.md
│   │       ├── design.md
│   │       ├── implementation.md
│   │       └── tests.md
│   └── evidence/
│       ├── quality.json
│       ├── trace.json
│       └── graph.json
├── src/
│   ├── matching.ts
│   ├── types.ts
│   └── utils.ts
├── tests/
│   └── matching.test.ts
├── README.md
├── package.json
└── .github/
```

この構造になれば、BP matching の要件・設計・実装・テストが追跡可能になり、musubix3 の品質ゲートを通せる状態になります。

## 7. まとめ

KCO_BPMatching は、単なる「コードリポジトリ」ではなく、MUSUBIX で定められた仕様駆動開発の流れに沿って設計されたプロジェクト骨格です。

特に注目すべき点は次の 3 つです。

1. `.musubix/` が要件・設計・証跡の中核である
2. `.github/skills/` が Copilot を設計・実装・検証の支援役としている
3. BP matching 本体コードはまだ未作成で、今後の実装を進めるための土台になっている

つまり、KCO_BPMatching は「実装の最中」ではなく、「実装を安全に進めるための仕様・品質基盤を構築した初期段階」だと理解するのが最も適切です。

今後、要件書や設計書、アルゴリズム実装、テストコードが追加されていくことで、MUSUBIX の原理に沿った強い検証可能な BP matching 実装へと進んでいく構成です。

---

この README は、実際に確認できた `package.json`, `.musubix/`, `.github/skills/`, `ADR-0001.md`, `quality.json` を根拠に整理した内容です。

必要であれば次に、

- README を英語版にする
- BP matching 実装用の設計書テンプレートを追加する
- `.musubix/features/BPMatching/requirements.md` と `design.md` を生成する

といった作業も続けられます。
