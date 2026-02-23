## Chroboook用Linux環境の比較

すべて机上論の感想なので実用データではありません。

crostini

- Google公式のLinux環境だが、あまり古い機種だと使用不可
- 仮想マシンで動作する
- ディストリビューションはDebianらしい
- chromebookの一部として使用できるよう作られているが不完全
- この中で唯一developerモードが不要

croton

- chrootで隔離したLinux環境
- ディストリビューションごとの導入なので互換性は高いはず
- chromebookとの親和性もそこそこあるが、guiは別動作
- メジャーらしい

Chrx

- デュアルブート
- ディストリビューションは様々、なんならwindowsも行けそう
- chromebookアプリと併用ができない
- chromebookのメリットをほぼ潰している気がしなくもない

chromebrew

- 直接動かすためのパッケージマネージャー
- ChromiumOSはLinuxなので、Linuxアプリはネイティブ動作する
- chromebookアプリもAndoroidアプリも併用可能なはず
- システムファイルを少なからず弄ることになる

多分動作の軽量さで言うなら

Chromebrew = Chrx >>超えられない壁>> crouton > crostini

になると思われる。

1. シャットダウン後、Esc + F3 + Powerを押してリカバリモードに入る
2. Ctrl + Dを投下し警告画面に切り替わったらEnter
3. リカバリが始まり、すべてのデータが初期化される
4. 初期設定画面で開発者モードを有効にするを選択
5. Ctrl + Dを投下
6. root用のパスワードを設定する
7. 開発者モードが有効になりましたが出れば完了
8. 雑に初期設定を済ませる
9. Ctrl + Alt + F2でデベロッパーコンソールを開く
10. rootと先ほど設定したパスワードでログイン
11. chromeos-setdevpasswdでsu用のパスワードを決める
12. Ctrl + Alt + F1でchromeOSに戻る
13. Ctrl + Alt + Tでコンソールを開く
14. crosh> shell
15. chronos@localhost / $ curl -Ls git.io/vddgY | bash
16. しばらく待ったら完了

とりあえずいろいろと触ってみよう……。
