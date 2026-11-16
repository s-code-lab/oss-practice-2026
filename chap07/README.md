# 第7回：GitHubでサイトを公開する（git push と GitHub Pages）

## この回で学ぶこと
これまでのGitの操作は，すべて自分のパソコンの中（**ローカル**）で完結していました．
この回では，インターネット上の **GitHub**（**リモート**）にコミットを送る `git push` を学びます．

題材は，第6回で生成AIと一緒に作ったポートフォリオサイトです．
GitHubには，リポジトリに置いたHTMLファイルをWebサイトとして公開できる **GitHub Pages** という機能があります．
これを使って，第6回のサイトを `https://あなたのユーザー名.github.io/` として，世界に公開します．

### 今日のゴール
- [ ] GitHubアカウントを用意し，メールアドレスを非公開にする
- [ ] SSH鍵を作ってGitHubに登録し，`ssh -T git@github.com` で接続を確かめる
- [ ] テンプレートから `ユーザー名.github.io` リポジトリを作り，「準備中」のページが公開されることを確かめる
- [ ] そのリポジトリを `git clone` し，第6回で作った `index.html` に置きかえてコミットする
- [ ] `bash scripts/check.sh` で，公開してよい状態か確認する
- [ ] `git push` して，`https://ユーザー名.github.io/` に自分のサイトが表示されることを確かめる
- [ ] 演習課題（サイトのURLとコマンドの出力結果）を提出する

### 事前に用意するもの
- 第6回で作った `design-a` と `design-b` ブランチ（どちらのデザインを公開するか決めておく）
- GitHubアカウント（まだの人は実習1で作ります）
- GitHubに登録するメールアドレスの受信箱（確認コードが届きます）

> この資料では，GitHubのユーザー名が `taku-shoku` の人を例にします．
> `ユーザー名` や `taku-shoku` と書いてあるところは，**自分のユーザー名に置きかえて**ください．

## GitとGitHub

### Gitは「道具」，GitHubは「場所」
これまで学んだGitは，みなさんのパソコンで動く「道具（ソフトウェア）」でした．
`git commit` も `git branch` も，すべて自分のパソコンの中で完結していましたね．

その履歴をインターネット上で預かってくれる「場所（Webサービス）」が **GitHub** です．
GitHubに履歴を送っておけば，ほかの人と共有したり，別のパソコンから取り出したりできます．

### ローカルリポジトリとリモートリポジトリ
| 名前 | 場所 | 例 |
| --- | --- | --- |
| **ローカルリポジトリ** | 自分のパソコンの中 | 第5回でクローンした `oss-practice-2026` フォルダ |
| **リモートリポジトリ** | GitHubなど，インターネット上 | `https://github.com/tomohitoy/oss-practice-2026` |

この2つの間で，履歴をやり取りするコマンドがあります．

| コマンド | 向き | 意味 | いつ使った？ |
| --- | --- | --- | --- |
| `git clone` | リモート → ローカル | リポジトリを丸ごとコピーしてくる | 第5回 |
| `git fetch` | リモート → ローカル | 新しいブランチやコミットを取ってくる | 第6回 |
| **`git push`** | **ローカル → リモート** | **自分のコミットを送る** | **今日** |

### なぜ，自分のリポジトリを作るの？
第5回でクローンした `oss-practice-2026` は，教員（`tomohitoy`）のリポジトリです．
みなさんには書きこむ権限がないので，ここへ `git push` することはできません．

そこで今日は，**自分のGitHubアカウントに，自分のリポジトリを作って**，そこへ `git push` します．

### GitHub Pages とは
- リポジトリに置いたHTMLファイルを，Webサイトとして公開できるGitHubの機能です
- **`ユーザー名.github.io` という名前のリポジトリ**を作ると，その中の `index.html` が `https://ユーザー名.github.io/` で公開されます（1つのアカウントにつき1つだけ作れる，特別なサイトです）
- `git push` するたびに，サイトが自動で更新されます
- 無料プランでは，リポジトリを **Public（公開）** にする必要があります．つまり，**サイトだけでなく，リポジトリの中身やコミットの履歴も，全世界から見える**ということです

### 今日の全体像
```
 GitHub   tomohitoy/oss-template-2026           <- 教員が用意したテンプレート
            |
            | (1) Use this template（GitHubの画面で，自分のアカウントにコピー）
            v
 GitHub   taku-shoku/taku-shoku.github.io       <- あなたのリポジトリ（最初は「準備中」のページ）
            |       ^       |
            |       |       +-- (5) GitHub Pages が公開 --> https://taku-shoku.github.io/
            |       |
(2) clone   |       | (4) git push
            v       |
 PC       taku-shoku.github.io/                 <- あなたのパソコンのフォルダ
            ^
            | (3) 第6回で作った index.html をコピーして，commit
            |
 PC       oss-practice-2026/chap06/index.html   <- design-a か design-b ブランチ
```

## 実習の流れ
1. 実習1：GitHubアカウントを用意する
2. 実習2：SSH鍵を作って，GitHubに登録する
3. 実習3：テンプレートから，サイト用のリポジトリを作る
4. 実習4：clone して，自分のサイトに置きかえて，push する
5. 実習5：公開されたサイトを確認する
6. 演習課題：サイトのURLとコマンドの出力結果を提出する

それでは，順番に進めていきましょう．

---

## 実習1：GitHubアカウントを用意する

### 1-A. アカウントを作る（まだ作っていない人）
すでにアカウントを持っている人は，1-Bに進んでください．

1. ブラウザで https://github.com/signup を開きます
2. 画面の指示にしたがって，**メールアドレス**・**パスワード**・**ユーザー名（Username）** を入力します
3. 登録したメールアドレスに届いた確認コードを入力すると，アカウントが作られます

**ユーザー名の決め方（重要）**：
- **ユーザー名が，そのままサイトのURL（`https://ユーザー名.github.io/`）になります**
- 公開されても困らない名前にしましょう．本名のフルネームや学籍番号は避けるのがおすすめです
- 使える文字は，英数字とハイフン（`-`）だけです．URLは小文字で表示されるので，小文字だけにしておくとわかりやすいです
- あとから変えることもできますが，サイトのURLも変わってしまいます

### 1-B. ログインを確認する
https://github.com にログインし，右上の自分のアイコンをクリックします．
メニューのいちばん上に表示されている名前が，あなたのユーザー名です．

### 1-C. メールアドレスを非公開にする（重要）
GitHubでは，コミットに記録されたメールアドレスを，誰でも見ることができます．
大学や個人のメールアドレスを公開しないように，GitHubが用意している「公開用のダミーのメールアドレス（noreplyアドレス）」を使えるようにしておきます．

1. 右上の自分のアイコン → **Settings** を開きます
2. 左のメニューの **Emails** を開きます
3. **Keep my email addresses private** にチェックを入れます
4. チェックボックスの下の説明文に，次のような形のアドレスが表示されます．**このアドレスをメモしておきます**（実習4で使います）

```
12345678+taku-shoku@users.noreply.github.com
```

> 数字の部分は，人によって違います．

---

## 実習2：SSH鍵を作って，GitHubに登録する

### なぜ認証が必要？
`git push` すると，GitHubは「このコミットを送ってきたのは，本当にこのリポジトリの持ち主か？」を確かめる必要があります．
GitHubでは，`git push` のときにGitHubのパスワードを使うことはできません．
この授業では，**SSH鍵** という方法で本人確認をします．

SSH鍵は，「公開鍵」と「秘密鍵」の2つのファイルがペアになっています．

| ファイル | たとえると | どうするか |
| --- | --- | --- |
| `id_ed25519.pub`（**公開鍵**） | 南京錠 | GitHubに預ける．人に見られても大丈夫 |
| `id_ed25519`（**秘密鍵**） | 家の鍵 | 自分のパソコンにだけ置いておく．**絶対に人に見せたり，送ったりしない** |

GitHubに預けた南京錠（公開鍵）を開けられるのは，ペアの家の鍵（秘密鍵）を持っているあなたのパソコンだけ，という仕組みです．

### 2-A. ターミナルを開く
- **Windows**：「Git Bash」を開きます（コマンドプロンプトや PowerShell ではありません）
- **Mac**：「ターミナル」を開きます

### 2-B. すでに鍵があるか確認する
```bash
ls ~/.ssh
```

`~` は，ホームフォルダ（`/Users/あなたの名前` など）を表す記号です．

- `id_ed25519` と `id_ed25519.pub` の**両方**が表示された人：すでに鍵があります．**2-Cは飛ばして，2-Dに進んでください**
- `No such file or directory` と表示された人，または2つのファイルがない人：2-Cに進んでください

### 2-C. 鍵のペアを作る
次のコマンドを実行します．`taku-shoku` の部分は，**自分のGitHubユーザー名**に置きかえてください．

```bash
ssh-keygen -t ed25519 -C "taku-shoku"
```

**コマンドの意味**：
- `ssh-keygen`：SSH鍵を作るコマンド
- `-t ed25519`：鍵の種類（ed25519 という，短くて安全な方式）
- `-C "taku-shoku"`：鍵につけるメモ（コメント）．どの鍵かわかるように，ユーザー名をつけておきます

途中で3回質問されます．今日は，**3回とも何も入力せずに Enter キー**を押してください．

```
Enter file in which to save the key (/Users/yourname/.ssh/id_ed25519):    <- Enter
Enter passphrase (empty for no passphrase):                               <- Enter
Enter same passphrase again:                                              <- Enter
```

> パスフレーズを入力しても，画面には何も表示されません．今日はパスフレーズなしで進めます（「発展」を参照）．

次のように表示されれば成功です．

```
Your identification has been saved in /Users/yourname/.ssh/id_ed25519
Your public key has been saved in /Users/yourname/.ssh/id_ed25519.pub
```

もう一度確認してみましょう．

```bash
ls ~/.ssh
```

`id_ed25519` と `id_ed25519.pub` の2つが表示されればOKです．

### 2-D. 公開鍵（.pub）の中身をコピーする
公開鍵の中身を表示します．**最後に `.pub` がついていることを必ず確認**してください．

```bash
cat ~/.ssh/id_ed25519.pub
```

表示例：
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJqJzCl0x8f...（長い英数字）... taku-shoku
```

`ssh-ed25519` から最後のユーザー名までの**1行すべて**をコピーします．
マウスで選択してコピーしてもよいですし，次のコマンドでコピーすることもできます．

Macの場合：
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

Windows（Git Bash）の場合：
```bash
clip < ~/.ssh/id_ed25519.pub
```

> `-----BEGIN OPENSSH PRIVATE KEY-----` で始まる文字が表示されたら，それは**秘密鍵**です．コピーしたり，どこかに貼り付けたりしてはいけません．

### 2-E. 公開鍵をGitHubに登録する
1. GitHubの右上の自分のアイコン → **Settings** を開きます
2. 左のメニューの **SSH and GPG keys** を開きます
3. **New SSH key** をクリックします
4. 次のように入力します
   - **Title**：どのパソコンの鍵かわかる名前（例：`授業用ノートPC`）
   - **Key type**：`Authentication Key` のまま
   - **Key**：2-Dでコピーした公開鍵を貼り付けます
5. **Add SSH key** をクリックします

> パスワードや確認コードの入力を求められたら，画面の指示にしたがってください．

### 2-F. 接続を確かめる
```bash
ssh -T git@github.com
```

はじめて接続するときだけ，次のように聞かれます．

```
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

これは「接続先は本当に github.com ですか？」という確認です．
`SHA256:` のあとの文字列（フィンガープリント）が，[GitHubが公開しているもの](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)と同じであることを確認したら，`yes` と入力して Enter キーを押します．

次のように，**自分のユーザー名**が表示されれば成功です！

```
Hi taku-shoku! You've successfully authenticated, but GitHub does not provide shell access.
```

> 後半の「does not provide shell access」は，エラーではありません．「認証はできたけれど，GitHubのサーバーを直接操作することはできませんよ」という意味です．

---

## 実習3：テンプレートから，サイト用のリポジトリを作る

### 3-A. テンプレートを開く
ブラウザで，教員が用意したテンプレートのリポジトリを開きます．

```
https://github.com/tomohitoy/oss-template-2026
```

このリポジトリには，次のファイルが入っています．

| ファイル | 役割 |
| --- | --- |
| `index.html` | サイトのトップページ．いまは「準備中」のページが入っています．**今日，第6回のサイトに置きかえます** |
| `404.html` | 存在しないページを開いたときに表示されるページ |
| `robots.txt` | 検索エンジンへのお願い．はじめは「検索結果に載せないで」という設定です（「発展」を参照） |
| `scripts/check.sh` | 公開前のセルフチェックをするスクリプト（実習4で使います） |
| `README.md` | リポジトリの説明．GitHubのリポジトリのページに表示されます |
| `LICENSE` | ライセンス（MITライセンス）．ほかの人がファイルをどんな条件で使ってよいかを示します（「発展」を参照） |
| `.nojekyll` | GitHub Pages に「ファイルを変換せずに，そのまま公開して」と伝えるための空のファイル（実習4で説明します） |
| `.gitignore` | Gitで管理しないファイル（Macが自動で作る `.DS_Store` など）の一覧 |
| `.gitattributes` | Windowsでも，`scripts/check.sh` の改行コードが変わらないようにする設定 |

**テンプレートリポジトリ**とは，「このファイル一式をコピーして，自分の新しいリポジトリを作る」ためのひな形です．
コピーして作ったリポジトリは，テンプレートとは別の，**あなただけのリポジトリ**になります．

### 3-B. テンプレートから，自分のリポジトリを作る
1. ファイルの一覧の上にある，緑色の **Use this template** ボタンをクリックし，**Create a new repository** を選びます

> **Use this template ボタンが見つからないとき**
> GitHubにログインしていないと，このボタンは表示されません．ページ右上に **Sign in** と表示されていたら，ログインしてから開き直しましょう．
> または，次のURLを開いても，同じ作成画面に進めます（ログインしていない場合は，ログインのあとで作成画面に移ります）．
>
> ```
> https://github.com/tomohitoy/oss-template-2026/generate
> ```

2. 次のように入力します

| 項目 | 入力する内容 |
| --- | --- |
| **Owner** | 自分のユーザー名を選ぶ |
| **Repository name** | **`ユーザー名.github.io`**（例：`taku-shoku.github.io`） |
| **Description** | 空のままでOK |
| **Public / Private** | **Public** を選ぶ |
| **Include all branches** | チェックしない |

3. **Create repository**（または **Create repository from template**）をクリックします

**Repository name は，一字一句まちがえないでください．**
ユーザー名とちがう名前にすると，`https://ユーザー名.github.io/` では公開されません．

`taku-shoku/taku-shoku.github.io` のようなページが表示され，リポジトリ名の下に `generated from tomohitoy/oss-template-2026` と表示されれば成功です．

### 3-C. GitHub Pages の設定を確認する
作ったリポジトリのページで，次の順に開きます．

1. 上のタブの **Settings**（右上のアイコンのメニューではなく，**リポジトリの** Settings です）
2. 左のメニューの **Pages**

**Build and deployment** の欄が，次のようになっているか確認します．

| 項目 | 設定 |
| --- | --- |
| **Source** | `Deploy from a branch` |
| **Branch** | `main` と `/ (root)` |

なっていない場合は，上の表のとおりに選んで **Save** をクリックします．

**意味**：「`main` ブランチの，いちばん上のフォルダ（root）にあるファイルを，そのまま公開してください」という設定です．

### 3-D. 「準備中」のページが公開されるのを確かめる
公開されるまで，1〜数分かかります（長いときは10分ほど）．
リポジトリの **Actions** タブを開くと，`pages build and deployment` という処理が動いているのが見えます．
黄色い丸（実行中）が，緑のチェック（完了）になるまで待ちましょう．

完了したら，ブラウザで自分のサイトを開きます．

```
https://ユーザー名.github.io/
```

「このサイトは準備中です」というページが表示されれば，**GitHub Pages の設定は成功**です！

ついでに，存在しないページも開いてみましょう．

```
https://ユーザー名.github.io/abc
```

「404 ページが見つかりません」と表示されます．これは，テンプレートに入っている `404.html` です．
GitHub Pages は，見つからないページを開かれると，自動で `404.html` を表示してくれます．

> **ポイント**：先に「準備中」のページで公開を確かめておくのは，うまくいかないときに原因を切り分けるためです．
> ここで表示できていれば，このあと自分のサイトが表示されないときは「GitHub Pages の設定」ではなく「push」のほうに原因がある，とわかります．

---

## 実習4：clone して，自分のサイトに置きかえて，push する

### 4-A. 公開するデザインのブランチに切り替える
ターミナルで，第5回でクローンした `oss-practice-2026` フォルダに移動します．
（ホームフォルダにクローンした人は，次のコマンドで移動できます）

```bash
cd ~/oss-practice-2026
```
```bash
pwd
```

表示の最後が `oss-practice-2026` になっていればOKです．
コミットし忘れがないか確認します．

```bash
git status
```

`nothing to commit, working tree clean` と表示されれば，次に進んでください．
ブランチを確認します．

```bash
git branch
```

表示例：
```
* design-a
  design-b
  main-05
  main-06
```

`design-a` と `design-b` があればOKです（`*` がどこについていてもかまいません）．
**公開したいほうのデザイン**のブランチに切り替えます（ここでは `design-a` を公開する例です）．

```bash
git switch design-a
```

`chap06` フォルダに `index.html` があることを確認します．

```bash
ls chap06
```

表示例：
```
README.md  index.html  template
```

> 第6回で学んだとおり，`chap06/index.html` は `design-a` と `design-b` ブランチにしかありません．
> `main-06` などにいるままだと，`index.html` は見つかりません．

### 4-B. サイト用のリポジトリを clone する

#### 1. SSHのURLをコピーする
実習3で作った自分のリポジトリ（`ユーザー名/ユーザー名.github.io`）のページを開きます．

1. 緑色の **Code** ボタンをクリックします
2. **Local** タブの中の **SSH** を選びます（**HTTPS ではありません**）
3. `git@github.com:` で始まるURLをコピーします

```
git@github.com:taku-shoku/taku-shoku.github.io.git
```

> `https://github.com/` で始まるURLは，今日は使いません．

#### 2. oss-practice-2026 の「1つ上」のフォルダに移動する
サイト用のリポジトリは，`oss-practice-2026` の**中ではなく，となり**にクローンします．

```bash
cd ..
```
```bash
ls
```

`oss-practice-2026` が表示されればOKです．

#### 3. clone する
`git clone` のあとに，コピーしたURLを貼り付けて実行します．

```bash
git clone git@github.com:ユーザー名/ユーザー名.github.io.git
```

表示例：
```
Cloning into 'taku-shoku.github.io'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (6/6), done.
```

これで，フォルダがこのように並びました．

```
ホームフォルダ など
 |
 +-- oss-practice-2026/       <- 授業の資料（教員のリポジトリをclone．pushはできない）
 |
 +-- taku-shoku.github.io/    <- 今日作った，自分のサイトのリポジトリ（pushできる）
```

#### 4. clone したフォルダに移動して，中身を見る
```bash
cd ユーザー名.github.io
```
```bash
ls -a
```

表示例：
```
.               .gitattributes  .nojekyll       LICENSE         index.html      scripts
..              .gitignore      404.html        README.md       robots.txt
.git
```

`-a` をつけると，`.` で始まる「隠しファイル」も表示されます．
`.git` フォルダがあるので，ここもGitのリポジトリだとわかります．

> **`.nojekyll` とは？**
> GitHub Pages は，ふだんはファイルを公開する前に **Jekyll（ジキル）** というツールでWebサイト用に変換します．
> 私たちは完成した `index.html` をそのまま公開したいので，「変換しないで」という目印として，この空のファイルを置いています．**消さないでください．**

#### 5. リモートを確認する
```bash
git remote -v
```

表示例：
```
origin	git@github.com:taku-shoku/taku-shoku.github.io.git (fetch)
origin	git@github.com:taku-shoku/taku-shoku.github.io.git (push)
```

`origin` は，clone したときに自動でつけられる「clone元のリモートリポジトリ」の名前です．
`oss-practice-2026` の `origin` は教員のリポジトリでしたが，**ここの `origin` は，あなた自身のリポジトリ**です．だから push できます．

### 4-C. コミットに記録される名前とメールアドレスを確認する（重要）
このリポジトリは Public なので，**コミットに記録された名前とメールアドレスも，全世界から見えます**．
第5回で設定した名前とメールアドレスを確認しましょう．

```bash
git config user.name
```
```bash
git config user.email
```

本名のフルネームや，大学・個人のメールアドレスが表示された人は，**このリポジトリだけ**，公開用の名前とアドレスに変えておきます．

```bash
git config user.name "taku-shoku"
```
```bash
git config user.email "12345678+taku-shoku@users.noreply.github.com"
```

`taku-shoku` は自分のユーザー名に，メールアドレスは**実習1-Cでメモしたnoreplyアドレス**に置きかえてください．

**ポイント**：第5回とちがって，`--global` をつけていません．
`--global` をつけないと，**いまいるリポジトリだけ**の設定になります（`oss-practice-2026` の設定は変わりません）．
設定がどこに保存されたか，のぞいてみましょう．

```bash
cat .git/config
```

いちばん下に，次のような `[user]` の欄が追加されていればOKです．

```
[user]
	name = taku-shoku
	email = 12345678+taku-shoku@users.noreply.github.com
```

### 4-D. 「準備中」のページを，第6回のサイトに置きかえる
`oss-practice-2026` にある第6回のサイトを，このリポジトリの `index.html` に上書きコピーします．

```bash
cp ../oss-practice-2026/chap06/index.html index.html
```

**コマンドの意味**：
- `cp コピー元 コピー先`：ファイルをコピーするコマンド
- `../oss-practice-2026/chap06/index.html`：1つ上（`..`）のフォルダにある `oss-practice-2026` の中の `chap06/index.html`
- `index.html`：いまいるフォルダの `index.html`（「準備中」のページ）に上書きする

Gitで確認してみましょう．

```bash
git status
```

表示例：
```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   index.html
```

```bash
git diff --stat
```

表示例：
```
 index.html | 214 ++++++++++++++++++++++++++++++++++++++++++++++++++-------------
 1 file changed, 180 insertions(+), 34 deletions(-)
```

ブラウザでも確認します．

Macの場合：
```bash
open index.html
```

Windows（Git Bash）の場合：
```bash
start index.html
```

自分のサイトが表示されたら，**第6回と同じように，公開してよい内容か最終確認**をしましょう．

- [ ] `PROFILE.md` に書いていないこと（経歴・資格・受賞歴など）を，AIが勝手に付け足していない
- [ ] 本名のフルネーム・学籍番号・住所・電話番号・メールアドレスなど，公開したくない情報が入っていない
- [ ] 文字化けしていない

> 直したいところがあれば，ここでエディタで `index.html` を書きかえて保存しましょう．

### 4-E. コミットする
```bash
git add index.html
```
```bash
git commit -m "feat: 第6回で作ったサイトを公開する"
```

履歴を見てみましょう．

```bash
git log --oneline
```

表示例：
```
44bfb2f (HEAD -> main) feat: 第6回で作ったサイトを公開する
f8c69f3 (origin/main, origin/HEAD) Initial commit
```

**見方**：
- `HEAD -> main`：あなたのパソコンの `main` ブランチは，いま作ったコミットを指しています
- `origin/main`：「GitHub（origin）の `main` ブランチは，ここを指していた」という記録です．まだ `Initial commit`（テンプレートからコピーしたときのコミット）のままです

つまり，**いま作ったコミットは，まだあなたのパソコンの中にしかありません**．
`git status` でも確かめられます．

```bash
git status
```

表示例：
```
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

「`origin/main` より1コミット進んでいます．`git push` で公開しましょう」と教えてくれています．

### 4-F. push する前に，セルフチェックをする
push すると，全世界に公開されます．その前に，テンプレートに入っているチェック用のスクリプトで確認しましょう．

```bash
bash scripts/check.sh
```

このスクリプトは，ファイルやGitの設定を何も書きかえず，次のことを確認して表示するだけです．
- `index.html` が「準備中」のページから置きかわっているか，文字コードの指定があるか
- `index.html` に，メールアドレスや電話番号のような文字列が入っていないか
- コミットに記録された名前とメールアドレス（4-Cの設定）
- まだコミットや push をしていないものがないか

表示例（後半）：
```
== 3. コミットに記録される名前とメールアドレス（全世界から見えます） ==
         名前：taku-shoku
         メール：12345678+taku-shoku@users.noreply.github.com
  [OK]   名前が設定されています（公開してよい名前か，上の表示を確認しましょう）
  [OK]   メールアドレスは GitHub の noreply アドレスです

== 4. Git の状態 ==
  [OK]   main ブランチにいます
  [注意] まだ push していないコミットが 1 個あります
         -> git push origin main で GitHub に送りましょう

== 結果 ==
  [NG] はありません．[注意] の 1 個を確認してから進みましょう
  ※ このチェックは目安です．最後は，ブラウザで自分のサイトを見て確認しましょう
```

- **[NG]**：push する前に，必ず直してください．`→` のあとにヒントが表示されます
- **[注意]**：問題ないか，自分の目で確認してください．この時点では「まだ push していないコミットが 1 個あります」と出るのが正常です

`index.html` を直した場合は，もう一度 `git add` → `git commit` してから，`bash scripts/check.sh` で確認しましょう．

> 4-Cの設定をする前にコミットしてしまった人は，「まだ push していないコミットに，次のアドレスが記録されています」と表示されます．
> 4-Cの設定をしてから，次のコマンドで直前のコミットを作り直しましょう．
> ```bash
> git commit --amend --reset-author --no-edit
> ```

### 4-G. push する
いよいよ，あなたのコミットをGitHubに送ります！

```bash
git push origin main
```

**コマンドの意味**：
- `git push`：ローカルのコミットを，リモートリポジトリに送るコマンド
- `origin`：送り先のリモートリポジトリの名前（4-Bの5で確認したもの）
- `main`：送るブランチの名前

表示例：
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 2.10 KiB | 2.10 MiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
To github.com:taku-shoku/taku-shoku.github.io.git
   f8c69f3..44bfb2f  main -> main
```

最後の行は，「GitHubの `main` ブランチを，`f8c69f3` から `44bfb2f` に進めました」という意味です．
もう一度，履歴を見てみましょう．

```bash
git log --oneline
```

表示例：
```
44bfb2f (HEAD -> main, origin/main, origin/HEAD) feat: 第6回で作ったサイトを公開する
f8c69f3 Initial commit
```

`origin/main` が，あなたのコミットのところまで進みました！
第6回で学んだとおり，**ブランチはコミットを指すポインタ**でした．
`git push` は，**GitHubにコミットを送って，GitHub側のブランチのポインタを進める**コマンドなのです．

---

## 実習5：公開されたサイトを確認する

### 5-A. GitHubでコミットを確認する
ブラウザで，自分のリポジトリ（`https://github.com/ユーザー名/ユーザー名.github.io`）を再読み込みします．

- ファイル一覧の上に，`feat: 第6回で作ったサイトを公開する` というコミットメッセージが表示されている
- `index.html` をクリックすると，第6回のサイトのHTMLになっている

この2つを確認できれば，push は成功しています．

### 5-B. 公開を待つ
**Actions** タブを開き，新しく始まった `pages build and deployment` が緑のチェックになるまで待ちます（1〜数分）．

### 5-C. サイトを開く
```
https://ユーザー名.github.io/
```

ブラウザで開いて（すでに開いている人は再読み込みして），第6回で作ったサイトが表示されれば完成です！
**おめでとうございます．あなたのサイトが，世界に公開されました．**

まだ「準備中」のページが表示される場合は，ブラウザが古いページを覚えている可能性があります．次のキーで，強制的に再読み込みしてみましょう．
- Windows：`Ctrl + F5`
- Mac：`Command + Shift + R`

スマートフォンでも開いてみましょう．第6回で「スマートフォンでも見やすいレイアウト」と頼んだ効果がわかるはずです．

> **一度 push した内容は，コミットの履歴に残ります．**
> あとで `index.html` を直して push し直しても，前のコミットを見れば，前の内容を読むことができます．
> 公開してはいけない情報を push してしまった場合は，すぐに教員に相談してください．

---

## 演習課題：公開したサイトのURLと，push の記録を提出する
次の2つを，課題に貼り付けて提出してください．

**1. 公開したサイトのURL**

```
https://ユーザー名.github.io/
```

**2. サイト用のリポジトリ（`ユーザー名.github.io` フォルダ）で実行した，次のコマンドの出力結果**

```bash
git log --oneline
```
```bash
git remote -v
```

> いまいるフォルダがわからなくなったら，`pwd` で確認しましょう．表示の最後が `ユーザー名.github.io` になっていればOKです．

### 提出前のチェック
- [ ] URLをブラウザで開くと，第6回で作ったサイトが表示される（「準備中」のページではない）
- [ ] `git log --oneline` の最新のコミットに，`HEAD -> main, origin/main` がそろって表示されている（push できた証拠）
- [ ] `git remote -v` のURLが，`git@github.com:自分のユーザー名/自分のユーザー名.github.io.git` になっている

---

## 困ったときは

| 症状 | 原因と対処 |
| --- | --- |
| `ssh-keygen: command not found` や `ssh: command not found` と表示される | Windowsでは，コマンドプロンプトや PowerShell ではなく **Git Bash** を使いましょう |
| `ssh -T git@github.com` で `Permission denied (publickey).` と表示される | 公開鍵がGitHubに登録されていません．2-Dで表示した公開鍵と，GitHubの **Settings → SSH and GPG keys** に登録した鍵が同じか確認しましょう．`.pub` のないほう（秘密鍵）を貼り付けていないかも確認しましょう |
| SSH鍵の登録で `Key is already in use` と表示される | その鍵は，別のGitHubアカウントに登録済みです．教員に相談してください |
| `ssh -T` の最初の質問で，`yes` 以外を入力してしまった | もう一度 `ssh -T git@github.com` を実行して，`yes` と入力しましょう |
| テンプレートのページに **Use this template** ボタンがない | GitHubにログインしていません．ページ右上に **Sign in** と表示されていたら，ログインしてから開き直しましょう．`https://github.com/tomohitoy/oss-template-2026/generate` を開いても，同じ作成画面に進めます |
| リポジトリを作ったのに，Settings に **Pages** の設定が出てこない／「Upgrade or make this repository public」と表示される | リポジトリが Private になっています．**Settings → General** のいちばん下の **Change visibility** で Public にしましょう |
| サイトを開くと `404` と表示される（`There isn't a GitHub Pages site here.`） | ①公開に時間がかかっています．Actions タブで緑のチェックになるまで待ちましょう ②リポジトリ名が `ユーザー名.github.io` になっていません．**Settings → General** の **Repository name** で直せます ③3-Cの Pages の設定がされていません |
| `git clone` で `Username for 'https://github.com':` と聞かれる | HTTPS のURLをコピーしています．`Ctrl + C` で中断し，4-Bの1で **SSH** のURLをコピーし直しましょう |
| `git clone` や `git push` で `Permission denied (publickey).` と表示される | SSH鍵の設定ができていません．2-Fの `ssh -T git@github.com` が成功するか，確認しましょう |
| `git clone` で `Repository not found.` と表示される | URLのユーザー名やリポジトリ名がまちがっています．4-Bの1で，URLをコピーし直しましょう |
| `git clone` で `destination path '...' already exists` と表示される | すでに clone 済みです．`cd ユーザー名.github.io` で移動するだけでOKです |
| `oss-practice-2026` の中に clone してしまった | `oss-practice-2026` で `cd ..` してから，`mv oss-practice-2026/ユーザー名.github.io .` で，となりに移動させましょう |
| `cp: ../oss-practice-2026/chap06/index.html: No such file or directory` | ①`oss-practice-2026` が `design-a`（または `design-b`）ブランチになっていません．4-Aをやり直しましょう ②サイト用のリポジトリを，`oss-practice-2026` のとなりに clone していません．`ls ..` で確認しましょう |
| `git push` で `GH007: Your push would publish a private email address.` と表示される | コミットに，非公開にしたメールアドレスが記録されています．4-Cの設定をしてから，`git commit --amend --reset-author --no-edit`（直前のコミットを，いまの名前とアドレスで作り直す）を実行して，もう一度 push しましょう |
| `git push` で `! [rejected]  main -> main (fetch first)` と表示される | GitHubの画面で，直接ファイルを編集した可能性があります．`git pull --no-rebase origin main` で取りこんでから，もう一度 push しましょう（エディタが開いたら，Vimなら `:wq` で保存して閉じます） |
| push したのに，サイトが「準備中」のまま | ①Actions タブで，処理が終わるまで待ちましょう ②強制再読み込み（Windows：`Ctrl + F5`，Mac：`Command + Shift + R`）をしましょう ③`git log --oneline` で，`origin/main` が自分のコミットまで進んでいるか確認しましょう |
| サイトの日本語が文字化けする | `index.html` に `<meta charset="UTF-8">` が入っているか確認しましょう |
| `bash scripts/check.sh` で `No such file or directory` と表示される | サイト用のリポジトリ（`ユーザー名.github.io`）のいちばん上のフォルダで実行していません．`pwd` で確認しましょう |
| `bash scripts/check.sh` で `$'\r': command not found` と表示される | エディタで開いたときに，改行コードが変わってしまっています．`rm scripts/check.sh` → `git restore scripts/check.sh` で元に戻しましょう |
| `git log` の画面から戻れない | `q` キーを押すと戻れます |

## まとめ

### この回で学んだコマンド
- **`ssh-keygen -t ed25519 -C "メモ"`**：SSH鍵のペア（公開鍵と秘密鍵）を作る
- **`ssh -T git@github.com`**：GitHubにSSHで接続できるか確かめる
- **`git clone git@github.com:ユーザー名/リポジトリ名.git`**：SSHでリポジトリをクローンする
- **`git remote -v`**：リモートリポジトリの名前とURLを表示する
- **`git config user.email "アドレス"`**：`--global` なしで，いまいるリポジトリだけの設定をする
- **`cp コピー元 コピー先`**：ファイルをコピーする
- **`bash scripts/check.sh`**：テンプレートに入っている，公開前のセルフチェック
- **`git push origin main`**：ローカルの `main` ブランチのコミットを，リモート（`origin`）に送る

### ローカルとリモート
1. ローカルリポジトリは自分のパソコンの中，リモートリポジトリはGitHubなどインターネット上にある
2. `git clone` でリモートをローカルにコピーすると，clone元が `origin` という名前で登録される
3. `origin/main` は，「GitHubの `main` がどのコミットを指していたか」という記録
4. `git commit` しただけでは，コミットは自分のパソコンの中にしかない．`git push` してはじめてGitHubに届く
5. GitHubへの push には認証が必要．SSH鍵では，公開鍵をGitHubに預け，秘密鍵は自分のパソコンだけに置く

### GitHub Pages
1. `ユーザー名.github.io` という名前の Public なリポジトリの `index.html` が，`https://ユーザー名.github.io/` で公開される
2. `git push` するたびに，サイトが自動で更新される（反映には数分かかる）
3. Public リポジトリなので，サイトだけでなく，ファイルの中身・コミットの履歴・コミットの名前とメールアドレスも公開される

---

## 発展：もっと学びたい人へ

### 公開するデザインを入れ替える
`design-b` のサイトに入れ替えてみましょう．`oss-practice-2026` のブランチを切り替えて，同じ手順でコピー → コミット → push するだけです．

```bash
cd ../oss-practice-2026
```
```bash
git switch design-b
```
```bash
cd ../ユーザー名.github.io
```
```bash
cp ../oss-practice-2026/chap06/index.html index.html
```
```bash
git add index.html
```
```bash
git commit -m "feat: デザインBに入れ替える"
```
```bash
git push origin main
```

数分後にサイトを再読み込みすると，デザインBに変わっているはずです．
`git log --oneline` で，履歴が1つ増えていることも確かめましょう．

### README.md を自分用に書きかえる
`README.md` は，GitHubのリポジトリのページに表示される説明文です（サイトには表示されません）．
テンプレートの説明のままになっているので，自分のサイトの説明に書きかえて，コミット → push してみましょう．

### LICENSE を自分の名前にする
`LICENSE` は，このリポジトリのファイルを，ほかの人がどんな条件で使ってよいかを示すファイルです．
テンプレートの `LICENSE` は MIT ライセンスで，`Copyright` の行にはテンプレートの作者（教員）の名前が書かれています．

```
Copyright (c) 2026 Tomohito Yamazaki
```

`index.html` は，もうあなたが作ったサイトに置きかわっています．
エディタで `LICENSE` を開き，この行の名前を自分のユーザー名に書きかえて，コミット → push してみましょう．

```
Copyright (c) 2026 taku-shoku
```

> 自分の作ったものを，どんな条件で使ってもらうか（MITのままにするか，ほかのライセンスにするか）は，作った人が決めることができます．
> オープンソースのライセンスについては，今後の講義でくわしく学びます．

### 検索エンジンに載せる（robots.txt）
テンプレートの `robots.txt` は，「このサイトのページを，検索エンジンに集めないでください」という設定になっています．
URLを知っている人は誰でも見られますが，Google などの検索結果には載りにくくなります．

検索結果に載せたくなったら，`robots.txt` を削除して，コミット → push します．

```bash
git rm robots.txt
```
```bash
git commit -m "検索エンジンに載せるため robots.txt を削除"
```
```bash
git push origin main
```

> `robots.txt` は「お願い」なので，すべての検索エンジンが守るとはかぎりません．公開したくない情報は，そもそもサイトに載せないようにしましょう．

### SSH鍵にパスフレーズをつける
今日はパスフレーズなしで鍵を作りましたが，パスフレーズをつけると，万が一秘密鍵のファイルを盗まれても，すぐには使われないようにできます．
すでに作った鍵にパスフレーズをつけるには，次のコマンドを使います．

```bash
ssh-keygen -p -f ~/.ssh/id_ed25519
```

---

## 参考資料
- [GitHub Docs - GitHub Pages サイトを作成する](https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site)
- [GitHub Docs - テンプレートからリポジトリを作成する](https://docs.github.com/ja/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)
- [GitHub Docs - SSH を使用した GitHub への接続](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh)
- [GitHub Docs - GitHub の SSH キーのフィンガープリント](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)
- [GitHub Docs - コミットメールアドレスを設定する](https://docs.github.com/ja/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address)
- [Pro Git - リモートでの作業](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%88%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD)

お疲れさまでした！自分のサイトを公開できましたか？

### 次回までの準備
- 今日作ったGitHubアカウントとSSH鍵は，次回以降も使います．GitHubにログインできるようにしておきましょう
- `ユーザー名.github.io` リポジトリは，削除しないでください
