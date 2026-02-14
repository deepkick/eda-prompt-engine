# EDA Prompt Engine

describe() 形式の統計量を入力として、
構造化された EDA レポートを生成するための
Prompt バージョン管理リポジトリです。

本リポジトリでは、Prompt を「文章」ではなく
**設計資産（プロダクトアーティファクト）**として扱います。

---

## リポジトリ構成

- `prompts/`
  - バージョン管理された Prompt 仕様（v1〜v4）
- `tests/`
  - テスト入力データ（例：titanic_summary.txt）
- `evaluations/`
  - モデル出力および回帰チェック関連ファイル
- `prompt_spec.md`
  - Prompt の入出力仕様書
- `CHANGELOG.md`
  - 各バージョン変更理由

---

## 本プロジェクトの目的

- Prompt の進化を履歴として残す
- 設計バグを検出・修正する
- 回帰（品質劣化）を防止する
- LLM を推論エンジンとして安定利用する

---

## 使用方法（手動実行）

1. `prompts/eda_vX.md` の内容をコピー
2. 新規チャットで LLM に貼り付け
3. `tests/titanic_summary.txt` を入力
4. 出力を `evaluations/` に保存
5. `regression_checklist.md` に基づき検証

---

## 現在の安定版

- 最新安定版：v4
- テストケース：Titanic describe summary
- モデル：ChatGPT 5.2（UI デフォルト）

---

## 今後の拡張

- 追加テストケース（Housing, Wine, edge cases）
- モデル差分比較
- API連携による自動回帰テスト
- Prompt DSL の形式仕様化
