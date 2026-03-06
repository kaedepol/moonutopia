Cloudflareのドメイン関係の設定はとにかくいろいろできて便利です。

国内のサービスでもできるのかもしれないですが、検索でヒットしやすいのは軒並みアフィリエイト記事ばかりで、「こういったことをしたい」に対する分かり易い文章があまりないように思います。

今回ブログで設定したリダイレクトたちを備忘録もかねてご紹介です。

## メールのリダイレクト

![Email Routing](https://www.moonutopia.com/img/cloudflare-mx.jpg)

Email Routingから設定。

ルーティングルールに転送先のアドレスを入力するだけ。MXレコードは自動的に追加され、とても簡単。

ただし、Cloudflareでできるのは受信の転送のみ。送信もしたい場合はもうひと捻り必要。

## HTTPをHTTPSにリダイレクト（常時SSL）

これだけ少しわかりにくいところにある。SSL/TLS→エッジ証明書の下の方。常に HTTPS を使用をオン。

## wwwなしをwwwありにリダイレクト

DNS設定とルールの設定両方が必要。

### DNSレコードの追加

wwwなしのAレコードに192.0.2.1を設定。

このブログの場合、wwwありの方はtunnel利用なのでAレコードなし。もしレンタルサーバーのIPを直接割り当てているなら、そちらをそのまま。

### ルールの設定

![Redirect rule](https://www.moonutopia.com/img/cloudflare.jpg)

リクエストURLに`https://example.com*`、ターゲットURLに`https://www.example.com${1}`を設定。

これで上記のHTTPSリダイレクトと合わせて`http://example.com/`→`https://example.com/`→`https://www.example.com/`と全部HTTPSのWWWありにリダイレクトできるようになった。

## まとめ

初回だけクレジットカードの設定が必要ですが、これだけやって無料です。cloudflare様々ですね。

もしドメイン・DNSをCloudflareで管理していないなら、ぜひ変更してみてはいかがでしょうか。
