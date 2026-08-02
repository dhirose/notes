## メモ

### 空ディレクトリのGit管理

慣習的に.keepや.gitkeepと命名したファイルをコミットする

### ファイル、フォルダを無視する

.gitignoreファイルを作成して、無視したいファイル名、フォルダ名を記載する。

正規表現が使える。

先頭に「!」を付ければ、特定のファイルだけ無視しないようにできる。

.gitignoreファイルは忘れずにコミットしておく。

※.gitignoreは既に追跡されている(コミット)ファイルに対しては除外できない。

### git-flow

ブランチの運用ルール

①mainブランチ：安定しているブランチかつリリースタグで管理されている

②developブランチ：新規機能のfeatureやリリース用のreleaseブランチがここから作成される

③releaseブランチ：リリース用のブランチであり、mainブランチとdevelopブランチにマージされる

④featureブランチ：新規機能ブランチであり、developブランチから作成される

⑤hotfixブランチ：バグ修正の為のブランチで、mainブランチから作成され、そのあとはmainブランチとdevelopブランチにマージされる

https://zenn.dev/no4_dev/articles/3360a6078d8e8c

デフォルトブランチはdevelopに変更する。

https://fwywd.com/tech/github-switch-default-branch

### ブランチの命名規則

スラッシュをブランチ名に含めると階層化できる

releaseブランチ：「release/v*.*.*」や「r/v*.*.*」とするとよい

featureブランチ：「feature/***」や「f/***」とするとよい

hotfixブランチ：「hotfix/***」や「h/***」とするとよい

### セルフホストランナー

セルフホストランナー

### アンチパターン

- フォースプッシュ
    - チームみんなが使うようなブランチではしてはいけない

## よくあるエラー

コミット時に、ステージングしていない時に出る

```powershell
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑1 +0 ~1 -0 !]> git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   practice14.txt

no changes added to commit (use "git add" and/or "git commit -a")
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑1 +0 ~1 -0 !]>
```

（コミットまで完了して）マージしていないブランチを削除しようとした時に出る

「git branch -D feature」で削除できる

```powershell
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git branch -d feature
error: the branch 'feature' is not fully merged
hint: If you are sure you want to delete it, run 'git branch -D feature'
hint: Disable this message with "git config advice.forceDeleteBranch false"
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]>
```

# コマンド集

| 分類1 | 分類2 | コマンド | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| git | config | git config --list | 設置値の取得 |  |
|  |  | git config --global user.name "d-hirose” | ユーザー名の設定 |  |
|  |  | git config --global user.email "hiroki.940920@gmail.com” | パスワードの設定 |  |
|  | init | git init | リポジトリ作成 |  |
|  | add | git add [ファイル名] | ファイルをステージングする |  |
|  |  | git add . | カレントディレクトリのファイル全てをステージングする。（サブディレクトリ含め再帰的に事項される） |  |
|  | commit | git commit -m “コミットメッセージ” | 引数にコミットメッセージを付けてコミットする |  |
|  |  | git commit | エディタを開いてメッセージを付けてコミットする |  |
|  | remote | git remote origin [URL] | リモートリポジトリを追加する |  |
|  | branch | git branch -m [新しいブランチ名] | 今のブランチ名を変更する |  |
|  |  | git branch -m [古いブランチ名] [新しいブランチ名] | ブランチ名を変更する |  |
|  |  | git branch | ローカルのブランチの一覧を取得する |  |
|  |  | git branch [ブランチ名] | ブランチを作成する |  |
|  | push | git push origin [ブランチ名] | プッシュする |  |
|  |  | git push -u origin [ブランチ名] | 自動的にリモートブランチを指定する引数を省略してくれるようにする設定。次回からは「git push」でプッシュができる |  |
|  | clone | git clone [URL] | リモートリポジトリをローカルにクローンする |  |
|  | switch | git switch [ブランチ名] | ブランチを切り替える |  |
|  |  | git switch -c [ブランチ名] | ブランチを作成して、切り替える |  |
|  | merge | git merge [ブランチ名] | 現在いるブランチに指定したブランチをマージする |  |
|  | status | git status | リポジトリの状態を確認する |  |
|  | diff | git diff | ワーキングツリーとインデックスエリア（ステージング）の差分を確認する |  |
|  |  | git diff —cached | インデックスエリア（ステージング）とローカルリポジトリの差分を確認する |  |
|  |  | git diff —staged | 〃 |  |
|  | log | git log | コミットログを確認する |  |
|  |  | git log —oneline | コミットログを省略して確認する |  |
|  |  | git log -p [ファイル名] | 指定したファイルのコミットログを確認する |  |
| Linux |  | touch | ファイルを作成する |  |
|  |  | mkdir [ディレクトリ名] | ディレクトリ作成 |  |
|  |  |  |  |  |

# 用語集

- init
    - 初期化。Gitではリポジトリを作成する行為。
- clone
    - リポジトリのコピーを作成する。
- fork(フォーク)
    - GitでなくGitHubの機能。他人のGitHubのリポジトリをGitHub上でクローン出来る。
- fetch
    - リモートリポジトリの変更をローカルリポジトリに持ってくる。
- rebase(リベース)
    - マージの一種。マージの際に過去のコミットを修正する。
- checkout
    - ブランチを切り替える行為。
- tag
    - コミット（リビジョン）に名前を付ける。プロダクトのリリース時に名前を付けて管理する。
- staging
    - ワーキングツリーからステージングエリアに変更を登録する。
- stash（スタッシュ）
    - コミット前の変更を一時避難する行為。
- conflict
    - 競合。同じファイルの同じ行を修正した際に起こる。
- rebase
- amend
- Fast-forward
- フォースプッシュ

# 理解に役立ったサイト

https://zenn.dev/shgs/articles/c5e2314c649263

後でまとめるメモ