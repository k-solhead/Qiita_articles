# 初めてのウェブサービスに縦書きエディタを選んだ理由
# 1.はじめに
　初めまして。55歳を過ぎてWebの勉強を始め、このほど初めてウェブサービスを公開しました。開発したのは、ブラウザ上の原稿用紙とでもいいましょうか、[縦書きエディタ](https://www.sakubun.tokyo)です。
![sakubun動画.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2779438/622d6577-0bfa-3b58-4010-95c71368b059.gif)

https://www.sakubun.tokyo/
  
  produced by K.Hyodo
# 2.なぜ縦書きエディタ？
　なぜ縦書きエディタかというと、GIGAスクールが関係しています。

　学校現場に配布される児童・生徒１人１台端末は、手軽さなどからChromebookが採用されるケースが多いのですが、文書を作成するツールは、GoogleドキュメントなどのGoogle系ツールが主体です。Google系ツールには縦書き機能がなく、日本語を教える国語の先生は大変困っています。

　教育現場で使われているにもかかわらず、日本語を縦書きできる環境がないのは教育の根幹にかかわる問題だと思い、今回、ネット環境さえあれば、無料で使える原稿用紙をウェブサービスで実現しました。

# 3.狙った機能
　原稿用紙に書き込むように打ち込めること。打ち込んだ文章はローカルにテキスト保存できるほか、ネット環境があればいつでもログインして原稿作成・保存、上書きできるようWebサーバーでユーザー管理と原稿管理ができることです。

　HTML、CSS、JavaScript、Ruby on Railsなどの技術が必要で、習得には苦労しましたが、先人の方々がネット上に公開していただいたライブラリやコード、ノウハウ、情報の数々が何よりも大きな助けとなりました。中にはライブラリやコードをまるまるいただいているところもあり、とても公開できるようなものではありませんが、教育現場の現状とサービスを一人でも多くの人に知ってもらいたいと思い、投稿しました。

# 4.便利なcontenteditable属性
　ウェブエディタを実現するうえで欠かせなかったのが、contenteditable属性です。divタブやpタグに`contenteditable="true"`と記すとその要素の内容が簡単に編集可能になる、まるで魔法の呪文のような属性です。これがなかったら開発しようとも思わなかったでしょう。

```html:HTML
<div class="sakubun">
	<div id="article" contenteditable="true">
	</div>
</div>
```

# 5.原稿用紙をどう実現するか
　次の課題は原稿用紙をどう表現して、マス目の間に文字を打ち込めるようにするかです。その答えはQiita記事[「懐かしの作文をCSSグラデーションで表現する」](https://qiita.com/cawpea/items/5eb01e9f48a334455acb)にありました。

　僭越ながら説明させていただきます。CSSで背景を指定できる`background`ですが、連続的な色のグラデーションを表現できる`linear-gradient`を使うと、直線を引くことができ、ルビのための縦線を含む縦横3本の線でマス目一コマ分を描くと、`background`の繰り返し処理で縦横に広がり、原稿用紙のマス目ができあがります。それを疑似要素の`before`を使い、`position:absolute`で絶対配置を使うと文字の下に重ねることができます。

　筆者のMasaki Osumiさんありがとうございます。contenteditable属性とともに開発初期の段階でこの記事に出会わかったらとてもデプロイまでいかなかったと思います。大きな励みにもなりました。

　ご自身でご指摘されている横スクロールで罫線の表示が途切れてしまうという問題ですが、最初は、bodyタグに30000pxという十分に長いwidthを与え、あらかじめ長い行数を確保しておくという方法でごまかしていました。その後の開発で、原稿用紙にページネーションを取り入れることになり、自動ページめくりを導入したことで、原稿用紙のwidth属性をページ数に応じて操作することができるようになり、解決できました。

# 6.原稿をどう保存するか
　続いて、書いた文章をローカルに保存できるよう`localStorage`（ローカルストレージ）を使いました。cookieと同様、クライアント側にデータを保存でき、保存容量はcookieより大きいのが特徴です。HTML5で導入されて本当によかったです。

## 6.1.Storageへの書き込み
　保存時、文章のデータは「キー」とペアで保存され、キーは読みだす時にも使うため、原稿のタイトルをそのまま使うことにしました。原稿のタイトルを取得するための入力窓をcontenteditable属性を付けて設けました。

```html:HTML
<textarea id="txt_title" placeholder="タイトルを入力" contenteditable="true"></textarea>
<input type="button" value="保存" onclick="saveStorage()" />
```

```js:JavaScript
function saveStorage(){
    const text = document.getElementById("article").innerHTML;
    const key = document.getElementById("txt_title").value;
        if(!key) {
            alert("タイトルを入力してください");
            return;
        }
    localStorage.setItem(key, text);
    alert("保存しました。");
}
```
## 6.2.Storageからの読み込み
　`localStorage`に書き込んだ原稿はタイトル（キー）が一覧でみられるようdivタグで窓をつくりました。「一覧表示」ボタンを押すと、`localStorage`に保存されている文章のキー（タイトル）を順番に取り出し、表示します。

　各タイトルにはイベントリスナーを設け、クリックすると一つだけ選択状態になるようにactive属性を付与。「開く」ボタンを押すと選択状態のタイトルから原稿を読み込み、原稿用紙に文章を流し込みます。


```html:HTML
<input type="button" value="一覧表示" onclick="loadStorage()" />
<div id="msg"></div>
<input type="button" value="開く" onclick="load()" />
```

```js:JavaScript
function loadStorage(){
  let result = '<table>';
  for(let i = 0;i < localStorage.length;i++){
    let key = localStorage.key(i);
    result += '<tr><td class="list">' + key + '</td></tr>';
  }
  result += '</table>';
  const target = document.getElementById("msg");
  target.innerHTML = result;

// 一覧からワンクリックで原稿選択
  let list = document.getElementsByClassName('list');

  for (let i = list.length - 1; i >= 0; i--) {
    listAction(list[i],i);
  }

  function listAction(listDOM,listId){
    // 各タイトルをイベントリスナーに登録
    listDOM.addEventListener("click", function(){
        // activeクラスの追加と削除
        this.classList.toggle('active');

        // クリックされていないボタンにactiveがついていたら外す
        for (let i = list.length - 1; i >= 0; i--){
            if(listId !== i){
                if(list[i].classList.contains('active')){
                    list[i].classList.remove('active');
                }
            }
        }
    })
  }
}

function load() {
  const active = document.getElementsByClassName('active');
  const key = active[0].innerHTML;
  const text = localStorage.getItem(key);
  document.getElementById("article").innerHTML = text;
}
```

# 7.終わりに
　まだまだ途中ですが、知ったかぶりして恥ずかし気もなく長々と書いてしまいました。初めて書いた技術系の記事です。失礼なことやルールに反していることがあるかもしれません。すぐに修正いたしますので、ご指摘ください。また、原稿の内容やウェブサービスについてご指導・ご助言いただけたら幸いです。
