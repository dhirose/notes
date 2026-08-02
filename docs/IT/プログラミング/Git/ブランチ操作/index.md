# ブランチ操作
## ブランチ操作（branch）

### 確認

#### リモートブランチの一覧を表示する

```powershell
git branch -r ## (--remotes)
```

#### ローカル、リモートブランチの一覧を表示する

```powershell
git branch -a ## (--all)
```

#### 最新のコミット情報とブランチの一覧を表示する

```powershell
git branch -v ## (--version)
```

#### バージョンと紐づくリモートブランチの情報を表示する

```powershell
git branch -vv ## (--verbose)
```

### 削除

#### マージ済みのブランチを表示する

```powershell
git branch --merged
```

#### マージされていないブランチを表示する

```powershell
git branch --no-merged
```

#### マージ済みのブランチを削除する

```powershell
git branch -d <ブランチ名> ## (--delete)
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git branch -d develop
Deleted branch develop (was 55213b4).
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git branch
  feature
* main
```

#### マージされていないブランチを削除する

```powershell
git branch -D <ブランチ名>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git branch -D feature
Deleted branch feature (was aa74e8d).
```

### 更新

#### 既存のブランチ名を別のブランチ名に指定して変更する

```powershell
git branch -m <変更前ブランチ> <変更後ブランチ>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git branch
  develop
  feature
* main
C:\Users\hirok\!work\study\Git\git-practice-80 [develop]> git branch -m develop develop2
C:\Users\hirok\!work\study\Git\git-practice-80 [develop2]> git branch
* develop2
  feature
  main
C:\Users\hirok\!work\study\Git\git-practice-80 [develop2]>
```

#### 現在のブランチ名を変更する

```powershell
git branch -m <変更後ブランチ>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [develop2]> git branch
* develop2
  feature
  main
C:\Users\hirok\!work\study\Git\git-practice-80 [develop2]> git branch -m develop3
C:\Users\hirok\!work\study\Git\git-practice-80 [develop3]> git branch
* develop3
  feature
  main
```

#### 現在のブランチ名を指定したブランチ名の強制変更する

```powershell
git branch -M <変更後ブランチ>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [develop3]> git branch
* develop3
  feature
  main
C:\Users\hirok\!work\study\Git\git-practice-80 [develop3]> git branch -M feature
C:\Users\hirok\!work\study\Git\git-practice-80 [feature]> git branch
* feature
  main
```

## リモート同期(fetch)

#### リモートリポジトリの情報をブランチを指定して取得する

```powershell
git fetch origin main
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git fetch origin main
From github.com:dhirose/git-practice-80
 * branch            main       -> FETCH_HEAD
```

#### リモートリポジトリの全てのブランチの情報を取得する

```powershell
git fetch
git fetch origin <ブランチ名> ## 特定のブランチを取得
```

```powershell
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑5]> git fetch
```

## チェリーピック（cherry-pick）

#### 特定のコミットを取り入れる（コミットID指定）

※cherry-pickについては以下のサイトが分かりやすくまとまっている

https://aruruchang.hatenablog.com/entry/2023/01/02/232158

```powershell
git cherry-pick <コミットID>
```

```powershell
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]> git log --pretty='format:%C(yellow)%h %C(green)%cd %C(reset)%s %C(red)%d %C(cyan)[%an]' --date=iso
51f928e 2024-08-29 00:22:42 +0900 practice20_add3  (HEAD -> develop) [dhirose]
70a1086 2024-08-29 00:21:37 +0900 practice20_add2  [dhirose]
f828d39 2024-08-29 00:20:06 +0900 practice20_add  [dhirose]
a2ab143 2024-08-29 00:01:31 +0900 practice20  (main) [dhirose]
95093cc 2024-08-27 13:11:50 +0900 practice15  (feature) [dhirose]
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑6]> git log --pretty='format:%C(yellow)%h %C(green)%cd %C(reset)%s %C(red)%d %C(cyan)[%an]' --date=iso
a2ab143 2024-08-29 00:01:31 +0900 practice20  (HEAD -> main) [dhirose]
95093cc 2024-08-27 13:11:50 +0900 practice15  (feature) [dhirose]
a1f2358 2024-08-27 12:59:06 +0900 main practice  [dhirose]
55213b4 2024-08-27 12:49:53 +0900 develop practice14  [dhirose]
64b4b9a 2024-08-27 12:42:25 +0900 develop practice14  [dhirose]
089f081 2024-08-27 12:35:15 +0900 main practice14  [dhirose]
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑6]> git cherry-pick 51f928e
[main c038f02] practice20_add3
 Date: Thu Aug 29 00:22:42 2024 +0900
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 practice20_add3.txt
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑7]>
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑7]> git log --pretty='format:%C(yellow)%h %C(green)%cd %C(reset)%s %C(red)%d %C(cyan)[%an]' --date=iso
c038f02 2024-08-29 00:25:26 +0900 practice20_add3  (HEAD -> main) [dhirose]
a2ab143 2024-08-29 00:01:31 +0900 practice20  [dhirose]
95093cc 2024-08-27 13:11:50 +0900 practice15  (feature) [dhirose]
a1f2358 2024-08-27 12:59:06 +0900 main practice  [dhirose]
55213b4 2024-08-27 12:49:53 +0900 develop practice14  [dhirose]
64b4b9a 2024-08-27 12:42:25 +0900 develop practice14  [dhirose]
089f081 2024-08-27 12:35:15 +0900 main practice14  [dhirose]
```

#### 特定の複数のコミットを取り入れる

```powershell
git cherry-pick <始点のコミットID>..<終点のコミットID>
```

※注意点は、<始点のコミットID>は、含めたいコミットの1つ前を指定しなければいけない

例）[開点]①→②→③→④→⑤→⑥(HEAD)[終点]

③～⑤のコミットを取り込む場合は、<始点のコミットID>が②、<終点のコミットID>が⑤のコミットハッシュを設定する。

<始点のコミットID>が⑥、<終点のコミットID>が③でも同様の結果が取れるはず。

#### チェリーピックをしてコンフリクトが起きた時に、チェリーピックを取り消す

```powershell
git cherry-pick --abort
```

#### チェリーピック時のコンフリクトを解消して、チェリーピックを確定させる

※コンフリクトを解消させた後、ステージングした後に実施する

```powershell
git add <コンフリクト解消ファイル>
git cherry-pick --continue
```

#### 特定のブランチの特定のコミットから、ブランチを作成する（branch）

```powershell
git branch <作成するブランチ名> <コミットID>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]> git log --oneline
51f928e (HEAD -> develop) practice20_add3
70a1086 practice20_add2
f828d39 practice20_add
a2ab143 practice20
95093cc (feature) practice15
a1f2358 main practice
55213b4 develop practice14
64b4b9a develop practice14
089f081 main practice14
0ab0fdf (origin/main, origin/HEAD) practice13.txtの更新
069831b practice13.txtのコミット
4eb6ade Merge pull request #1 from dhirose/develop
098ecc9 (origin/develop) practice9_2
db6a146 practice9
55bf67a practce3
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]>
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]> git branch develop2 f828d39

```

## ブランチ切替（switch）

#### 特定のブランチの特定のコミットから、ブランチを作成する

```powershell
git switch -c <作成するブランチ名> <コミットID>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]> git log --oneline
51f928e (HEAD -> develop) practice20_add3
70a1086 practice20_add2
f828d39 (develop2) practice20_add
a2ab143 practice20
95093cc (feature) practice15
a1f2358 main practice
55213b4 develop practice14
64b4b9a develop practice14
089f081 main practice14
0ab0fdf (origin/main, origin/HEAD) practice13.txtの更新
069831b practice13.txtのコミット
4eb6ade Merge pull request #1 from dhirose/develop
098ecc9 (origin/develop) practice9_2
db6a146 practice9
55bf67a practce3
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑12]> git switch -c develop3 f828d39
Switched to a new branch 'develop3'
C:\Users\hirok\!work\study\Git\git-practice-80 [develop3]>

```

## 変更を取り消す（ワーキングツリー）

ワーキングツリーで変更したステージング前のファイルの変更を取り消す

```powershell
git restore <取り消すファイル名 or フォルダ名>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8 +0 ~1 -0 !]> git status
On branch main
Your branch is ahead of 'origin/main' by 8 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   practice22.txt

no changes added to commit (use "git add" and/or "git commit -a")
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8 +0 ~1 -0 !]> git restore practice22.txt
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]> git status
On branch main
Your branch is ahead of 'origin/main' by 8 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]>

```

## 変更を取り消す（ステージング　→　ワーキングツリー）

ステージングしたコミット前のファイルの変更を取り消す

```powershell
#「git reset」と「git reset HEAD」は同様の動作
git reset / git reset HEAD /
## ファイル名指定で変更を取り消す
## 「git reset」と「git reset --mixed HEAD」は同様の動作
git reset -- <ファイル名>
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑9 +0 ~1 -0 ~]> git status
On branch main
Your branch is ahead of 'origin/main' by 9 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   practice24.txt

C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑9 +0 ~1 -0 ~]> git reset HEAD
Unstaged changes after reset:
M       practice24.txt
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑9 +0 ~1 -0 !]> git status
On branch main
Your branch is ahead of 'origin/main' by 9 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   practice24.txt

no changes added to commit (use "git add" and/or "git commit -a")
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑9 +0 ~1 -0 !]>

```

## 変更を取り消す（ローカルリポジトリ　→　ステージング）

コミットしたファイルの変更を取り消して、インデックスエリア（ステージング）まで戻す

```powershell
git reset --soft HEAD^
## カレット(^)は、いくつ前に戻すかを指定する。（^^^の指定もできる。3つ戻る）
## --softオプションは、HEADのみを変更しHEADのみを前のコミットに戻している
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑11]> git status
On branch main
Your branch is ahead of 'origin/main' by 11 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑11]> git reset --soft HEAD^
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑10 +0 ~1 -0 ~]> git status
On branch main
Your branch is ahead of 'origin/main' by 10 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   practice25.txt

C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑10 +0 ~1 -0 ~]>
```

## 変更を取り消す（ローカルリポジトリ　→　ステージング　→　ワーキングツリー　→　変更取り消し）

```powershell
git reset --hard HEAD^
## --hardは、ローカルリポジトリからワーキングツリーまでの変更を戻す（つまり、完全にコミット直前の状態に戻す）
```

## コミットを修正する

このコマンドで出来るのは、

- コミットメッセージの修正
- コミット内容を後から追加

使っていいのは、**pushする前のコミットを修正したいときだけ**

[コミットの修正には git commit --amend が便利](https://tech-blog.rakus.co.jp/entry/20191113/git)

```powershell
git commit --amend -m "コメント" 
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]> git log --oneline
3d70611 (HEAD -> main) practice22
c038f02 practice20_add3
a2ab143 practice20
95093cc (feature) practice15
a1f2358 main practice
55213b4 develop practice14
64b4b9a develop practice14
089f081 main practice14
0ab0fdf (origin/main, origin/HEAD) practice13.txtの更新
069831b practice13.txtのコミット
4eb6ade Merge pull request #1 from dhirose/develop
098ecc9 (origin/develop) practice9_2
db6a146 practice9
55bf67a practce3
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]> git commit --amend -m "practice23" ## コメント変更
[main 20288ad] practice23
 Date: Thu Aug 29 22:13:39 2024 +0900
 1 file changed, 1 insertion(+)
 create mode 100644 practice22.txt
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]> git log --oneline
20288ad (HEAD -> main) practice23
c038f02 practice20_add3
a2ab143 practice20
95093cc (feature) practice15
a1f2358 main practice
55213b4 develop practice14
64b4b9a develop practice14
089f081 main practice14
0ab0fdf (origin/main, origin/HEAD) practice13.txtの更新
069831b practice13.txtのコミット
4eb6ade Merge pull request #1 from dhirose/develop
098ecc9 (origin/develop) practice9_2
db6a146 practice9
55bf67a practce3
C:\Users\hirok\!work\study\Git\git-practice-80 [main ↑8]>

```

## ログを見る

```powershell
## 実行例
git log ## 現在のブランチのログを表示する
git log --all ## 全てのブランチのログを表示する
git log --oneline
git log --graph
git log --pretty=format:"%h %s" ## ログを整形する。参考サイト：https://git-scm.com/docs/pretty-formats
```

## ブランチ間のファイルの差分を確認する

```powershell
git diff <ブランチ名> <ブランチ名> <ファイル名>
git diff <ブランチ名> <ブランチ名>..<ファイル名>
## どちらも同様の動作
```

```powershell
## 実行例
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑13]>
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑13]> git diff main develop .\practice28.txt
diff --git a/practice28.txt b/practice28.txt
index d66c3db..033536e 100644
--- a/practice28.txt
+++ b/practice28.txt
@@ -1 +1 @@
-mainのブランチ
+developのブランチ
```

## ブランチ間の差分を確認する

```powershell
git diff <ブランチ名> <ブランチ名>
```

## コミットの内容を確認する

```powershell
git show ## HEAD（現在のブランチの直前）のコミット内容を確認する。
git show --oneline ## onelineで確認する。
git show <コミットID> ## コミットを指定して確認する。
git show <コミットID>:<ファイル名> ## 指定したコミットのファイルの中身を確認する。
```

## 作業履歴を確認する

出力内容は以下のように表示される

**コミットハッシュ HEAD@{n個前}: <作業内容>: <コミットメッセージ>**

HEADに関する作業内容を出力するコマンド。例えば以下

- ブランチの切り替え
- マージ

```powershell
git reflog
git reflog -<作業履歴数>
```

## ファイルを削除してステージングする

ファイル削除(rm)とステージング(add)を同時に行う

```powershell
git rm <ファイル名>
git rm -r <フォルダ名> #フォルダごと削除する
```

## ファイル名orフォルダ名を変更してステージングする

```powershell
git mv <変更前ファイル名> <変更前ファイル名>
git mv <変更前フォルダ名> <変更前フォルダ名>
```

## プル(pull)する

動き的にはgit fetchとgit mergeを同時に行う。

```powershell
git pull origin <ブランチ名>
git pull --all ## 全てのブランチをpullする
```

## リベースする

リベースはマージコマンドの一種。

リベースコマンドを実行した後は、マージコマンドでマージする。

```powershell
git rebase <リベース先ブランチ名>
#この後、マージまで忘れずに
```

※リベースにはインタラクティブモードがある（git rebase **-i**）。詳しくは検索

## ファイルの編集履歴を表示する

```powershell
git blame <ファイル名>
git blame -s <ファイル名>  ## 編集者名とタイムスタンプを非表示
git blame -L <開始行番号>,<終了行番号> <ファイル名> ## 指定した開始行番号～終了行番号までの編集履歴を表示する
```

## ローカルでのみ変更を無視する

追跡済みのファイルを無視する

```powershell
git update-index --skip-worktree <ファイル名> ## 無視する

## 再度追跡する場合は、以下コマンドを実行する
git update-index --no-skip-worktree <ファイル名> ## 再度追跡する
```

## エイリアスを作成する

```powershell
git config --global alias.<短縮Gitコマンド> <Gitコマンド> ## エイリアスを作成

## 削除は以下コマンドを実行する
git config --global --unset alias.<短縮Gitコマンド> ## エイリアスを削除
```

## リモートリポジトリを登録する

```powershell
git remote add origin <GitUrl>
```

## リモートリポジトリを確認する

```powershell
git remote -v
```

## リモートリポジトリを変更する

```powershell
git remote set-url <エイリアス> <GitUrl>
```

## スカッシュする

```powershell
git merge --squash <ブランチ名>
```

## 軽量版タグを作成する

```powershell
git tag <タグ名> <コミットID>
```

## 注釈タグを作成する

```powershell
git tag <タグ名> <コミットID> -a -m <メッセージ>
```

## タグを確認する

```powershell
git tag
```

## タグの詳細を確認する

```powershell
git show <タグ名>
```

## タグを削除する

```powershell
git tag -d <タグ名>
```

## タグをリモートリポジトリへ反映する

```powershell
git push origin --tags

## ※1つのタグ名を反映させる場合
git push origin <タグ名>
```

リモートブランチを削除したが、ローカルのブランチが残り続ける場合

[消したはずのリモートブランチがローカルで表示されるので削除する](https://qiita.com/nantekkotai/items/0ca7c9e850eff65aaf66)

[Git Pruneとは](https://simo-blog.com/articles/git-prune/#:~:text=Git%20Prune%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9,-git%20fetch%20%2D%2D&text=%E3%81%93%E3%81%AE%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%81%AF%E3%80%81%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA,%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%8B%E3%82%89%E3%82%82%E5%89%8A%E9%99%A4%E3%81%99%E3%82%8B%E3%80%82)

```powershell
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑13]>
C:\Users\hirok\!work\study\Git\git-practice-80 [develop ↑13]> git diff main develop .\practice28.txt
diff --git a/practice28.txt b/practice28.txt
index d66c3db..033536e 100644
--- a/practice28.txt
+++ b/practice28.txt
@@ -1 +1 @@
-mainのブランチ
+developのブランチ
```