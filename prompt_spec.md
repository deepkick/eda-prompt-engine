# EDA Prompt Engine 仕様書（prompt_spec.md）

本仕様書は、本リポジトリが提供する「自動EDAレポート生成プロンプト」の
**入出力仕様・制約・運用ルール**を定義する。

---

## 1. 目的

- `pandas.DataFrame.describe()` 相当の統計量（テキスト）から、
  **監査可能で再現性のある EDA レポート**を生成する。
- Prompt を文章ではなく **設計資産（プロダクトアーティファクト）**として運用する。

---

## 2. 対象入力（Input Contract）

### 2.1 入力形式（最低要件）
- describe() の数値統計を含むテキスト
- 少なくとも以下の統計行が含まれることが望ましい：
  - `count`, `mean`, `std`, `min`, `25%`, `50%`, `75%`, `max`

### 2.2 期待する表現例
- `survived ... count 891 ... mean 0.38 ... max 1.0` のように、列ごとの統計が並ぶ形式
- 行区切り・スペース区切りは厳密でなくてよい（LLM側で許容）

### 2.3 既知の制約
- describe() だけでは dtype / unique 数 / value_counts が不明。
  → **確定不能な事項は「統計量だけでは確定できない」と明示し、不足情報を要求する**。

---

## 3. 出力形式（Output Contract）

### 3.1 共通原則
- **事実と推測を分離**する
- **数値根拠を必ず添える**
- 断定できないことは断定しない（不足情報を要求）

### 3.2 v4 の標準構造（推奨）
v4 を安定版とし、以下のセクション構造を標準とする：

0. 型分類（ルールベース）
1. 重要な事実（最大5点）
2. 欠損分析
3. 逸脱候補（Outlier / Rare）
4. 推測（最大3点）
5. 次の最小実験セット（3つのみ）

※ v1〜v3 は「進化の履歴」として保持し、標準は v4 を採用。

---

## 4. 制約（Guardrails）

### 4.1 禁止事項
- 統計量に含まれない情報の断定
- 外部知識の混入（例：Titanic一般知識による断定）
- 禁止された曖昧語の使用（該当バージョンのみ）
  - 例：「可能性が高い」「おそらく」など

### 4.2 ガバナンス方針（推奨）
- 逸脱値は **削除禁止**（デフォルト）
- まずはロバスト化（log/clip/robust scaler）で影響度を評価する

---

## 5. 評価（Quality / Regression）

### 5.1 回帰チェック
- `evaluations/gpt52/titanic/regression_checklist.md` を基準とする。
- Prompt変更時は、少なくとも `tests/titanic_summary.txt` を使って再実行し、
  重大回帰（構造崩れ・幻覚・誤ルール適用）がないことを確認する。

### 5.2 ベースライン
- モデル：ChatGPT 5.2（UI デフォルト）
- テストケース：Titanic describe summary
- 保存場所：`evaluations/gpt52/titanic/eda_vX.md`

---

## 6. バージョニング運用（Versioning Policy）

- Prompt は **上書き禁止**。変更は新しいバージョン（eda_v5.md 等）として追加する。
- CHANGELOG に「なぜ変えたか（目的）」と「何が改善したか（観測）」を記録する。
- 最新安定版ポインタ `prompts/eda_latest.md` を更新する（後述）。

---

## 7. 成果物の保存（Evaluation Log Policy）

- 各バージョンについて、同一入力に対する LLM 出力を保存する。
- 保存先ルール：
  - `evaluations/<model>/<testcase>/eda_vX.md`
- 各出力には `Test Observations` を記載し、プロンプトの意図どおり動いたかを短く記録する。
