PowerShellはもともとWindows用のCUIアプリケーション。

CUIってのは昔ながらの映画でハッカーが使ってる黒い画面のようなもので、コマンドでパソコンを操作できる。

きちんと覚えればマウスでポチポチ作業するよりはるかに効率的に操作ができる。効果を発揮するのは主に人の眼の判断が必要ない定型作業。

たとえば事務仕事で毎週売上の集計をとって上司にメールで送信する、特定の共有フォルダに新しいファイルがないかチェックする、とか。

若い人だとPythonと似たようなことができる、ちょっと歳をとった人ならマクロの代わりにできるというとピンと来るかも。

## Windows PowerShell と PowerShell 7.xどちらにする？

PowerShellには二系統あって、Windowsに同梱されている昔ながらのWindows PowerShell 5.1と、オープンソースプロジェクトで.NET上で動作するPowerShell 7.x。

PowerShell 7.xの方が新しくて多機能だけれど、別途インストールが必要。

インストールといっても難しい作業ではないので、基本的にはPowerShell 7.xで、事情がある場合にWindows PowerShellを使う感じ。

||Windows PowerShell|PowerShell 7.x|
|--|--|--|
|最新バージョン|5.1|7.5|
|ベース|.NET Framework 4.5|.NET 9.0|
|デフォルトエンコード|ASCII (Shift-JIS)|UTF-8|
|実行ファイル|powershell.exe|Pwsh.exe|
|インストール|不要（標準搭載）|ストア / インストーラ―|
|Windows固有機能|標準搭載|別途インポート|
|IDE|PowerShell ISE|VSCode (推奨)|

Windows PowerShellは保守モードに入ったものの、決してサポートされていないわけではない。コマンドプロンプトよりもはるかに高機能だし、PowerShell 7.xは一部のWindows固有機能にアクセスするためにWindows PowerShellを呼び出していることもある。

例えば業務でレンタルパソコンやリースパソコンを使用していて、そこそこ頻繁に入れ替えがあるならWindows PowerShellが楽かもしれない。

また意外な盲点として、デフォルトのエンコードが変わっている。Windows 11移行後もMicrosoft Excelをガンガン使い、テキストファイルもエンコードをShift-JIS指定しているような環境であれば、Shift-JISを使えるWindows PowerShellが無難。

## Windows PowerShellの準備

標準搭載なので何も必要ない。スタートメニューから起動しよう。

エクスプローラーの何もないところをShift+右クリック→「PowerShellウィンドウをここで開く」から起動することもできる。好きな方で。

### PowerShell 7.4のインストール

記事執筆時点ではPowerShell 7.5 (.NET 9)はプレビュー版。なので実質的な最新版はPowerShell 7.4 (.NET 8)。とりあえず初心者は安定板。

Microsoft StoreからPowerShellをインストールするだけ。

<https://www.microsoft.com/store/productId/9MZ1SNWT0N5D?ocid=pdpshare>

### PowerShellスクリプトの有効化

PowerShellはかなり強力で、いろんなことができる。できてしまう。

なので標準搭載こそされているけれど、初期状態ではスクリプトの実行ができない。

有効化するにはWindows PowerShellを立ち上げて下記コマンドを実行する。
`Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser`

実行すると特に成功したとかの表示は出ないまま先に進む。エラーが出た場合は構文が間違えている。
