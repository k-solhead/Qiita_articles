# Gitの流れ
参照  
[【GitHub入門】Webアプリを作りながらGitHub開発フローを習得してみよう](https://www.youtube.com/watch?v=cOTBf8bHsXo)  
[【GitHub入門】初心者向け！GitHubでチーム開発するための基本操作を解説！](https://www.youtube.com/watch?v=Dz95iUNt-fg)

## 1.リモートリポジトリの作成
  
①　Githubの右上「＋」―「New Repositry」―「Create a new repository」

②　「Repository name」にリポジトリの名前をつける。

③　PublicかPrivateを選択

④　ボタン「Create repository」を押し、空のリモートリポジトリを作成

⑤　VSCのターミナル（Git Bash）を起動し、ローカルリポジトリを置くディレクトリ（の親ディレクトリ）に移動

⑥　先ほど作成した空のリモートリポジトリをローカルにクローンする。  
　　Github上の「Quick setup」のURLをコピーし、ターミナルに`git clone URL`と貼り付けて実行

⑦　リポジトリ名の新しいディレクトリができているので、そこに移動し、`code .`で編集用のウィンドウを立ち上げる

⑧　ファーストコミットをpushしてみる。  
　　ターミナルで`git commit --allow-empty -m "first commit"`を実行し、`git push origin main`

## 2,ブランチを切る

①　現在いるブランチを確認する  
　　ターミナルで`git branch`を実行し、mainブランチにいることを確認する

②　新たに作業用のブランチを切る  
　　ターミナルで`git branch base`を実行し、baseブランチを作成する

③　作業用ブランチに移動する  
　　ターミナルで`git checkout base`を実行し、baseブランチに切り替える

④　コードを書く  
　　編集用のウィンドウでコードを編集する

## 3.プルリクをする

①　ステージに上げる  
　　ターミナルで`git add *`か`git add .`を実行する

②　コミットする  
　　ターミナルで`git commit -m "completed base"`を実行する

③　作業用ブランチをプッシュする  
　　ターミナルで`git push origin base`

④　Github上でプルリクエストをする  
　　リモートリポジトリでpushを確認し、「Compare & pull request」ボタンを押す  
　　タイトルと内容を書き込み「Create pull request」ボタンを押す

## 4.レビューし、マージする

①　上部の「Files changed」タブを押してコードの編集状況を確認する。

②　OKなら上部の「Conversation」タブを押して表紙に戻り、「Write」欄に「実装お疲れさまでした」などのコメントを入れて、「Comment」ボタンを押して確定した後、「Merge pull request」ボタンを押す。さらに「Confirm merge」ボタンを押す。mainブランチに作業用ブランチを合体させることができた。

③　マージできたら「Pull request successfully merged and closed」という欄に「Delete branch」のボタンが出るので、押して作業用ブランチを削除しておく。

## 5.ローカルの作業用ブランチを削除する

①　先ほど削除したのはリモートの作業用ブランチなので、今度はVSCに戻り、ローカルの作業用ブランチを削除する。  
　　VSCのターミナルに`git checkout main`でメインブランチに移動し`git branch`で移動を確認する。

②　`git branch -D 削除するブランチ名`で作業用ブランチを削除する。`git branch`で削除を確認する。

## 6.mainブランチをローカルにプルする

①　作業用ブランチを削除したローカルの作業用ディレクトリには何もない状態になるので、リモートからマージしたメインブランチをローカルに引っ張ってくるため、VSCのターミナルに`git pull origin main`を