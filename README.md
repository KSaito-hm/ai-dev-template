# AI開発テンプレート

AIと協働してツール開発を行うためのテンプレートリポジトリです。

---

## このテンプレートの目的

> 👤 **対象読者：人間（プロジェクトオーナー）**

AIとの開発でよくある問題を解決します。

- 会話が長くなるとAIの挙動が不安定になる
- 仕様がチャット上にしか存在せず、再現性がない
- 途中からAIを切り替えると文脈が失われる

**解決策：ドキュメント駆動 × 差分ベース開発**

仕様はすべてファイルで管理し、AIには「ファイルを読んで・編集する」役割を与えます。

---

## ディレクトリ構成

> 👤 **対象読者：人間 / 🤖 Claude Code**

```
/
├── README.md              # このファイル
├── CLAUDE.md              # Claude Code の行動ルール（毎セッション自動読み込み）
├── docs/
│   ├── 00_context.md      # プロジェクトの背景・前提条件
│   ├── 01_requirements.md # 要件定義
│   ├── 02_architecture.md # 構成設計
│   ├── 03_design.md       # 詳細設計
│   ├── 04_tasks.md        # タスク管理
│   ├── ADR/               # 設計判断の記録
│   └── assets/            # 設計リファレンス・図・画像などの素材
├── src/                   # ツール本体のコード
├── tests/
│   └── fixtures/          # テスト用インプットデータ
├── scripts/               # セットアップ・自動化などの補助スクリプト
└── prompts/
    ├── architect.md       # 設計フェーズ用プロンプト
    ├── designer.md        # 詳細設計フェーズ用プロンプト
    ├── implementer.md     # 実装フェーズ用プロンプト
    └── reviewer.md        # レビュー用プロンプト
```

> **Note:** `src/` `tests/` `scripts/` はGitで空フォルダを管理するため `.gitkeep` を配置しています。実装開始時に削除してください。

---

## はじめ方

> 👤 **対象読者：人間**

### 新しいツールを作り始めるとき

1. GitHubで新しいリポジトリを作成する（「Initialize with README」はチェックしない）

2. Claude Codeに以下を指示する：

```
https://github.com/KSaito-hm/ai-dev-template のREADMEを読んで作業を開始してください。
新しいリポジトリのURLは https://github.com/YOUR_NAME/my-tool です。
```

### 既存の開発を再開するとき

```
https://github.com/KSaito-hm/ai-dev-template のREADMEを読んで作業を再開してください。
リポジトリのURLは https://github.com/YOUR_NAME/my-tool です。
```

---

## Claude Codeの起動手順

> 🤖 **対象読者：Claude Code（このREADMEを読んだら最初に実行すること）**

人間から作業対象リポジトリのURLが伝えられているか確認し、状況に応じて以下のいずれかを実行してください。

---

### ケース A：新プロジェクトのセットアップ（初回のみ）

新しいリポジトリのURLが伝えられ、ローカルにまだ存在しない場合。

**① テンプレートをクローンする**

```bash
git clone https://github.com/KSaito-hm/ai-dev-template.git
```

**② 作業対象リポジトリをクローンする**

```bash
git clone [作業対象リポジトリのURL]
cd [作業対象リポジトリ名]
```

**③ テンプレートのファイルをコピーする**

```bash
cp ../ai-dev-template/CLAUDE.md ./
cp -r ../ai-dev-template/docs ./
cp -r ../ai-dev-template/prompts ./
cp -r ../ai-dev-template/scripts ./
cp -r ../ai-dev-template/src ./
cp -r ../ai-dev-template/tests ./
cp ../ai-dev-template/.gitignore ./
```

> ⚠️ **`CLAUDE.md` を必ず最初にコピーすること。** これにより、このリポジトリでの作業時に Claude Code がルールを自動読み込みします。

**④ `docs/00_context.md` を記述してからコミットする**

> ⚠️ **コミット前に必ず記述すること。** テンプレートを再適用すると内容が上書きされます。

`docs/00_context.md` を開き、プロジェクトの背景・目的・技術スタック・制約を人間に確認しながら記述してください。記述完了後：

```bash
git add .
git commit -m "initial commit: apply project template"
git push origin main
```

**⑤ Git Flowの初期ブランチを作成する**

```bash
git checkout -b develop
git push -u origin develop
git checkout main
```

**⑥ テンプレートのクローンを削除する**

```bash
cd ..
rm -rf ai-dev-template
cd [作業対象リポジトリ名]
```

**⑦ 次のアクションを人間に提案する**

`docs/04_tasks.md` を読み、T-001〜T-004（ドキュメント整備）から着手することを提案してください。

---

### ケース B：既存プロジェクトの作業再開

作業対象リポジトリがローカルにクローン済みの場合。

**① `docs/00_context.md` を読む**

内容が空の場合は、人間に「プロジェクトの背景と目的を教えてください」と質問してください。

**② `docs/04_tasks.md` を読む**

現在のフォーカスタスクと進捗を確認してください。

**③ 現在のブランチを確認する**

```bash
git branch && git log --oneline -5
```

**④ 人間に現状と次のアクションを報告する**

把握した内容をもとに、次のアクション候補を提案・確認してください。

---

## 開発フロー

> 👤 **対象読者：人間（開発開始時に確認）**

以下の順序を**必ず守ってください**。

```
① docs/00_context.md      プロジェクトの背景・目的を記述
② docs/01_requirements.md 要件を定義する
③ docs/02_architecture.md 全体構成を設計する
④ docs/03_design.md       詳細設計を行う
⑤ docs/04_tasks.md        タスクに分解する
⑥ 実装
⑦ レビュー・承認
```

各フェーズの開始時は、`prompts/` 配下の対応するプロンプトをClaude Codeに渡してください。

---

## ブランチ戦略（Git Flow）

> 👤 **対象読者：人間 / 🤖 Claude Code**

本プロジェクトはGit Flowを採用します。

### ブランチ構成

| ブランチ | 役割 | 備考 |
|---|---|---|
| `main` | リリース済みの完成物 | 直接コミット禁止 |
| `develop` | 開発の統合ブランチ | 直接コミット禁止 |
| `feature/T-xxx` | タスク単位の作業ブランチ | developから切る。xxxはタスクID |
| `release/x.x.x` | リリース前の調整ブランチ | developから切る。semver命名 |
| `hotfix/T-xxx` | 緊急修正ブランチ | mainから切り、main+developへマージ |

### ブランチの流れ

```
main ──────────────────────────────────── v1.0.0
 └─ develop
      ├─ feature/T-101   ← 作業はここで行う（タスクIDと一致）
      ├─ feature/T-102
      └─ release/v1.0.0  ← リリース前調整 → main へマージ
```

### ブランチ命名規則

- 機能・タスク：`feature/T-xxx`（例：`feature/T-101`）
- リリース：`release/x.x.x`（例：`release/v1.0.0`）
- 緊急修正：`hotfix/T-xxx`（例：`hotfix/T-999`）

---

## ロール定義

> 👤 **対象読者：人間 / 🤖 Claude Code**

本プロジェクトでは以下のロールを定義します。各ロールは人間またはClaude Codeが担当できます。

| ロール | 役割 |
|---|---|
| **プロジェクトオーナー** | 最終承認・方針決定 |
| **レビュアー** | コード・ドキュメントのレビュー |
| **承認者** | マージ・リリースの承認 |

セッション開始時に「このセッションでの各ロールの担当者」を確認してください。

---

## ブランチ操作ポリシー

> 🤖 **対象読者：Claude Code（ブランチ操作時に必ず守ること）**

| 操作 | Claude Codeの動き |
|---|---|
| feature ブランチを切る | タスクID を確認し `feature/T-xxx` で作成する |
| feature ブランチへのコミット | 自由に行ってよい（確認不要） |
| feature → develop マージ | マージ内容を提示し、**人間の確認後**に `--no-ff` でマージする |
| develop → main マージ | release ブランチを経由する。**プロジェクトオーナーの承認後**に実行する |

**禁止事項**

- `develop` / `main` への直接コミット
- `--no-ff` なしの feature マージ
- 人間の確認なしの develop / main へのマージ

---

## Claude Codeの作業ルール

> 🤖 **対象読者：Claude Code（常に守ること）**

> ⚠️ **詳細なルールは `CLAUDE.md` を参照すること。Claude Code は毎セッション開始時に `CLAUDE.md` を自動で読み込みます。**

- **編集対象のファイルを必ず明示すること**
- **差分ベースで変更すること**（全文再生成禁止）
- **不明点は実装前に質問すること**
- **複数ファイルを同時変更する場合は変更計画を事前に提示すること**

### フェーズごとの担当プロンプト

各フェーズの開始時は、`prompts/` 配下の対応するプロンプトを読んでから作業してください。

| フェーズ | 使用するプロンプト |
|------|------------|
| 要件定義・構成設計 | `prompts/architect.md` |
| 詳細設計 | `prompts/designer.md` |
| 実装 | `prompts/implementer.md` |
| レビュー | `prompts/reviewer.md` |

---

## 変更管理ルール

> 🤖 **対象読者：Claude Code（変更発生時に必ず守ること）**

仕様変更が発生したとき：

1. `docs/01_requirements.md` を先に更新する
2. 影響するファイルを順番に更新する（architecture → design → tasks）
3. 実装は最後

**禁止事項**

- チャット上だけで仕様を決定する
- ドキュメントを更新せずに実装を進める
- 人間の確認なしに複数ファイルを変更する

---

## 設計判断の記録（ADR）

> 👤 **対象読者：人間 / 🤖 Claude Code**

大きな技術的判断をしたときは `docs/ADR/` にファイルを追加してください。

```
docs/ADR/
└── 001_why-we-chose-xxx.md
```

**フォーマット：**

```markdown
# ADR-001: タイトル

## 状況
何を決める必要があったか

## 決定
何を選んだか

## 理由
なぜそれを選んだか

## トレードオフ
捨てたものは何か
```
