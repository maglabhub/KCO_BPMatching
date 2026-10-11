---
version: 1.0.0
---
# Project constitution / BPMatching 進化型憲章

## Overview / 概要

このBPMatchingプロジェクトの憲章は、**段階的な成熟度**に基づいて設計されています。
プロジェクトの進行に応じて、検証の厳密性と要件の複雑度が段階的に増していきます。

---

## Phase 1: Foundations / 基礎段階

### 目的
要件・設計・実装の基本的トレーサビリティを確立し、最小限の品質ゲートを通す段階。

### PRINC-001: Minimum viable evidence / 最小限の根拠
> 要件と実装の関連を追跡可能にする。「言っただけ」では通らない。

#### RULE-001: Requirements are traced
**Metric:** `trace.errors`  
**Limit:** 0

すべての要件（REQ-BPM-*）が設計（DES-BPM-*）および実装（CODE-BPM-*）と結びついていることを検証。
ダングリング要件（どのコードからも参照されていない要件）を許さない。

### PRINC-002: Tests exist and run / テストは存在し実行される
> テストは「書かれた」だけでなく「実行される」こと。検証されないものは品質ではない。

#### RULE-002: All unit tests pass
**Metric:** `commands.failures`  
**Limit:** 0

BP matchingの主要モジュール（matcher, scoring, optimizer等）について、
ユニットテストが定義され、すべてパスすることを検証。
テストスイートの実行は `npm test` で確実に成功する必要があります。

---

## Phase 2: Maturity / 成熟段階

### 目的
アルゴリズムの正確性を設計で保証し、アーキテクチャの整合性を維持する段階。
Red-Green-Refactorサイクルの完全実装と、意思決定の記録化を強制。

### PRINC-003: Design discipline / 設計の規律
> 実装は設計に従う。アーキテクチャの選択は明示的に記録される。

#### RULE-003: ADRs (Architecture Decision Records) for major decisions
**Metric:** `design.errors`  
**Limit:** 0

BP matchingの重要な設計判断について、ADRを必須化。例：
- **ADR-0002:** マッチング戦略の選定（完全一致 vs. 部分一致 vs. スコアリング）
- **ADR-0003:** スコアリング関数の設計（線形 vs. 機械学習ベース）
- **ADR-0004:** パフォーマンス最適化戦略（メモ化、キャッシング、並列処理）
- **ADR-0005:** 依存ライブラリの選定（外部ML/graph libraries）

#### RULE-004: Architecture review gates
**Metric:** `graph.violations`  
**Limit:** 0

Code Graphによる依存性分析。BP matchingの内部モジュール間の循環依存を許さず、
層構造（matcher層 → scoring層 → optimizer層）を保証。

### PRINC-004: Quality depth / 品質の深化
> テスト駆動開発（TDD）を完全実装。テストと実装が同じペースで進む。

#### RULE-005: Test-driven development (TDD) cycles
**Metric:** `tests.annotatedIds`  
**Limit:** 0

すべての新機能は、以下のサイクルで実装：
1. `tdd red` - 失敗するテストを記録（テスト: TEST-BPM-*）
2. `tdd green` - テストをパスさせる最小実装
3. `tdd refactor` - コードをリファクタリング

要件（REQ-BPM-*）ごとに、対応するテスト（TEST-BPM-*）が存在し、
赤→緑→リファクタのサイクル証拠が `.musubix/evidence/tdd.json` に記録されていること。

#### RULE-006: Code coverage minimum (80%)
**Metric:** `tests.executedIds`  
**Limit:** 0

BP matchingの実装コード（src/matching.ts, src/scoring.ts等）について、
テストカバレッジが80%以上であることを検証。
カバレッジレポートは `npm run coverage` で確認可能。

---

## Phase 3: Release Ready / リリース準備段階

### 目的
本番環境での実装品質を保証。セキュリティ、パフォーマンス、非機能要件を検証。

### PRINC-005: Security and formal verification / セキュリティと形式検証
> 入力検証、エラーハンドリング、予測不可能な挙動がないこと。

#### RULE-007: Type checking strict mode
**Metric:** `commands.failures`  
**Limit:** 0

TypeScript strict mode (`tsconfig.json` に `"strict": true`) での型チェック合格。
コードの安全性を静的解析で保証。

例：
```bash
npm run typecheck
```

#### RULE-008: Security scanning passes
**Metric:** `commands.failures`  
**Limit:** 0

セキュリティスキャン（例：`npm audit`）でhigh以上の脆弱性がゼロであること。
また、BP matching特有の入力バリデーション（不正なグラフ形式、無限ループの可能性等）
が実装されていることを確認。

### PRINC-006: Non-functional requirements / 非機能要件
> アルゴリズムのパフォーマンスと可用性。

#### RULE-009: Performance benchmarks met
**Metric:** `commands.failures`  
**Limit:** 0

BP matchingの処理時間が要件を満たすこと（例：1000ノードのグラフで1秒以内など）。
ベンチマークテスト（`npm run benchmark`）がすべてパス。

#### RULE-010: Integration tests pass
**Metric:** `commands.failures`  
**Limit:** 0

BP matching全体の統合テスト（matcher → scoring → optimizer → output）が
すべてパスすることを確認。実世界シナリオを想定したテストケースを含む。

---

## Transition Rules / 段階遷移ルール

### Phase 1 → Phase 2 への遷移条件
- PRINC-001, PRINC-002 のすべてのルール（RULE-001, 002）が `gate --changed` をパス
- BP matchingの基本設計（アルゴリズム、データ構造）が確定
- 最初の3つのADRが承認済み

### Phase 2 → Phase 3 への遷移条件
- PRINC-001, 002, 003, 004 のすべてのルール（RULE-001～006）が `gate --changed` をパス
- TDD Red-Green-Refactorサイクルが5件以上完了
- テストカバレッジが80%に達成
- コードレビューで重大な欠陥指摘なし

### Phase 3 (Release Ready) への到達基準
- すべてのPRINC（001～006）とすべてのRULE（001～010）が検証パス
- 本番環境への合理的な自信がある
- ドキュメント、ADR、テストが整備完了

---

## Metrics and Evidence / メトリクスと証拠

各ルールが使用するメトリクスと、それが記録される場所：

| ルール | メトリクス | 証拠ファイル | 検証コマンド |
|--------|-----------|-----------|-----------|
| RULE-001 | trace.errors | `.musubix/evidence/trace.json` | `npx musubix3 trace check` |
| RULE-002 | commands.failures | `.musubix/evidence/quality.json` | `npm test` + `gate` |
| RULE-003 | design.errors | `.musubix/features/BPMatching/design.md` | `npx musubix3 design validate` |
| RULE-004 | graph.violations | `.musubix/evidence/quality.json` | `npx musubix3 graph gate` |
| RULE-005 | tests.annotatedIds | `.musubix/evidence/tdd.json` | `npx musubix3 tdd validate` |
| RULE-006 | tests.executedIds | coverage report | `npm run coverage` |
| RULE-007 | commands.failures | `.musubix/evidence/quality.json` | `npm run typecheck` |
| RULE-008 | commands.failures | `.musubix/evidence/quality.json` | `npm audit` |
| RULE-009 | commands.failures | benchmark results | `npm run benchmark` |
| RULE-010 | commands.failures | integration test report | `npm run test:integration` |

---

## Summary / まとめ

このBPMatching憲章は、以下の3つの段階を通じて、
**段階的に品質を深化させるアプローチ**を採用しています：

1. **Phase 1 (Foundations)**: 要件追跡とテスト実行の基盤構築
2. **Phase 2 (Maturity)**: 設計規律とTDDの完全実装、アーキテクチャ整合性確保
3. **Phase 3 (Release Ready)**: セキュリティ・パフォーマンス・非機能要件の検証

各段階は独立してゲート検証可能であり、プロジェクトの進捗に応じて
段階的に厳格化することで、**持続可能かつスケーラブルな品質体系**を実現します。

---

**最終更新**: 2026-10-11  
**作成者**: GitHub Copilot  
**関連ADR**: ADR-0001（Copilot-native specification development）
