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
    └── reviewer.md        # レビュー用プロンプト
```

> **Note:** `src/` `tests/` `scripts/` はGitで空フォルダを管理するため `.gitkeep` を配置しています。実装開始時に削除してください。

---

## はじめ方

> 👤 **対象読者：人間**

### 新しいツールを作り始めるとき

1. GitHubで新しいリポジトリを作成する（例：`my-tool`）
   - Visibility：PublicまたはPrivate
   - 「Initialize with README」はチェックしない

2. Claude Codeに以下を指示する：

```
https://github.com/KSaito-hm/ai-dev-template のREADMEを読んで作業を開始してください。
新しいリポジトリのURLは https://github.com/YOUR_NAME/my-tool です。
```

あとはClaude Codeが自律的に進めます。

### 既存のツールの開発を再開するとき

Claude Codeに以下を指示する：

```
https://github.com/KSaito-hm/ai-dev-template のREADMEを読んで作業を開始してください。
リポジトリのURLは https://github.com/YOUR_NAME/my-tool です。
```

---

## Claude Codeの起動手順

> 🤖 **対象読者：Claude Code（このREADMEを読んだら最初に実行すること）**

以下を順番に実行してください。

### STEP 1：作業対象リポジトリを確認する

人間から作業対象リポジトリのURLが伝えられているか確認してください。

- **URLが伝えられていない場合** → 「作業対象のリポジトリURLを教えてください」と質問する
- **URLが伝えられている場合** → STEP 2へ進む

### STEP 2：作業対象リポジトリの状態を確認する

作業対象リポジトリがローカルにクローン済みかどうかを確認してください。

- **クローン済みの場合** → STEP 4へ進む
- **未クローンの場合** → STEP 3へ進む

### STEP 3：新規セットアップ（初回のみ）

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
cp -r ../ai-dev-template/docs ./
cp -r ../ai-dev-template/prompts ./
cp -r ../ai-dev-template/scripts ./
cp -r ../ai-dev-template/src ./
cp -r ../ai-dev-template/tests ./
cp ../ai-dev-template/.gitignore ./
```

**④ Git Flowの初期ブランチを作成する**

```bash
git add .
git commit -m "initial commit: apply project template"
git push origin main
git checkout -b develop
git push -u origin develop
git checkout main
```

**⑤ テンプレートのクローンを削除する**

```bash
cd ..
rm -rf ai-dev-template
cd [作業対象リポジトリ名]
```

### STEP 4：プロジェクトの背景を把握する

`docs/00_context.md` を読んでください。  
内容が空の場合は、人間に「プロジェクトの背景と目的を教えてください」と質問してください。

### STEP 5：現在のタスク状況を確認する

`docs/04_tasks.md` を読んでください。

### STEP 6：人間に作業内容を確認する

把握した内容をもとに、次のアクションを人間に提案・確認してください。

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
| `develop` | 開発の統合ブランチ | 機能完成後にここへマージ |
| `feature/xxx` | 機能単位の作業ブランチ | developから切る |
| `release/x.x` | リリース前の調整ブランチ | developから切る |

### ブランチの流れ

```
main
 └─ develop
      ├─ feature/xxx   ← 作業はここで行う
      ├─ feature/yyy
      └─ release/1.0   ← リリース前調整 → mainへマージ
```

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

ブランチ操作は**必ず承認者・レビュアーに確認してから実行**してください。

| 操作 | Claude Codeの動き |
|---|---|
| ブランチを切る | ブランチ名を提案し、**プロジェクトオーナー**の確認を取ってから実行する |
| コミット | メッセージを提案し、**承認者**の確認を取ってから実行する |
| developへのマージ | マージ内容を提示し、**レビュアー**のレビュー・承認後に実行する |
| mainへのマージ | **提案のみ行う。プロジェクトオーナーの承認後に実行する** |

**禁止事項**

- 承認なしにブランチを切ること
- 承認なしにコミット・マージを実行すること
- mainブランチへ直接コミットすること

---

## Claude Codeの作業ルール

> 🤖 **対象読者：Claude Code（常に守ること）**

- **編集対象のファイルを必ず明示すること**
- **差分ベースで変更すること**（全文再生成禁止）
- **不明点は実装前に質問すること**
- **複数ファイルの同時更新は禁止**（1ファイルずつ確認を取ること）

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
