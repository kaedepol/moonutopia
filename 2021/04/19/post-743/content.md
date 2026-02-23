Windows PEの情報が思ったほど見当たらないので備忘録もかねて。

## そもそもWindows PEって何ぞや？

Windows Preinstallation Environmentの略で、Windowsのインストーラーや復旧環境で使われる簡易版Windowsです。

ちょっと高機能なMS DOSみたいなものです。

これの何がすごいかというと、RAMディスクで起動するのでライブCDのような使い方ができることですね。にもかかわらずWindowsシステムの一部を利用できるのです。

Windows PE最大のメリットはこれ、Windowsシステムであることです。

## 具体的に何ができるのか

ファイルのコピー・移動ができるのでWindowsが起動しないが機械故障がないPCからデータを取り出すことができます。ノートパソコンでHDDが取り出せないとかSSDがはんだ付けされているとかいったパターンの時に役立ちます。

Linuxライブディスクでもできますが、NTFSやexFATを”厳密に”正しく扱えるのはWindowsだけなので、Linuxで大量のファイルをコピーすると破損する場合があります。

Windows PEだとこの点の問題がなくなるのがメリットです。

### 捕捉

NTFSやexFATの読み書きは大抵のLinuxでできるのですが、Microsoftはつい最近(2019年)までexFATの技術仕様を公開しておらず、それっぽい動作をすることで読み書きできるだけの状態でした。つまりハックして無理矢理読み書きできるようにしているだけです。

NTFSは未だに公開されていないのでexFATよりもさらに危険を伴います。

データの破損が起こるのはこの辺りに起因しているので、可能な限りWindowsのファイルシステムはWindowsで読むべきなんですよね。

[米Microsoftが「exFAT」の技術仕様を公開、Linuxカーネルへのマージを認める | OSDN Magazine](https://mag.osdn.jp/19/09/02/180000)

あとは例えば、システムの修復コマンドを叩くことで起動しないシステムを復旧させることができます。有名どころだとsfc /scannowやcheckdisk c: /fなんかがあります。

レジストリの修復や置換もできます。これはLinuxだと絶対にできないメリットです。

また、コマンドラインを叩けばポータブル版のWindows用のアプリケーションが動作するのでOSなしでも作業を行うことができます。

CPU-ZやCrystal Disk infoでパーツの状態を見る、OSのボトルネックを無くしたベンチマークを立ち上げる、システムドライブにゼロ書きして情報流出対策するなどなど。

## Windows PEの作り方

### 材料を用意する

Microsoftの公式ページからダウンロードします。

[Windows ADK のダウンロードとインストール](https://docs.microsoft.com/ja-jp/windows-hardware/get-started/adk-install)

ダウンロードするのはふたつ。

- `adksetup.exe`
- `adkwinpesetup.exe`

*※2021/04/18の情報です。常に更新されているので細かい表記が異なる場合があります。*

ダウンロードした`adksetup.exe`を起動します。

インストール場所やら情報収集やら利用規約やらいつものやつが流れてくるので適当に選んで進みます。

インストールを行う機能を選択するのでここで必要なものを選んでください。

Windows PEが必要なだけならDeployment Toolsだけあればいいです。

インストールが終わったらダウンロードした`adkwinpesetup.exe`を起動。こっちは流れで進めばOK。Deployment Toolsが先に入ってないと失敗するよ。

### イメージを準備する

正常にインストールされていればWindows Kitsフォルダがスタートメニューに追加されるのでその中の「展開およびイメージングツール環境」を管理者権限で起動。

管理者でないと失敗するので注意！

コマンドプロンプトが立ち上がるので必要なコマンドを打ち込んでいく。

以下64bit版で進めます。32bit版はamd64をx86に置き換えて下さい。あとここでつまずく人はいないと思いますが、IntelのCPUを使っていてもamd64です。

ディレクトリは仮にD:\WinPE64としていますが、必要に応じて適宜変更します。

これによりPEのイメージが作業ディレクトリにコピーされます。

```cmd
copype amd64 D:\WinPE64
```

続いて、イメージをマウントします。この操作をすることで，`D:\WinPE64\mount\`内にイメージファイルがマウントされます。

マウントした後はアンマウントするまでコマンドプロンプトを絶対に消さないでください。
違うコマンドプロンプトからアンマウントすることはできません。

```cmd
Dism /Mount–Image /ImageFile:“D:\WinPE64\media\sources\boot.wim” /index:1 /MountDir:“D:\WinPE64\mount”
```

RAMディスクで確保する領域を変更します。32mb, 64mb, 128mb, 256mb, 512mbから選べます。多くすると様々なプログラムを導入できますが、そのぶんメモリを消費するためPCによっては動作が困難になります。今の主流である4GB以上のメモリが乗ったPCなら512でいいと思います。

```cmd
Dism /Set–ScratchSpace:512 /Image:“D:\WinPE64\mount”
```

日本語化します。

純粋にシステム修復のみに使うなど英語でいいならこの手順は飛ばします。

```cmd
dism /Image:“D:\WinPE64\mount” /add–package /packagepath:“C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs\WinPE-WMI.cab”
dism /Image:“D:\WinPE64\mount” /add–package /packagepath:“C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs\ja-jp\WinPE-WMI_ja-jp.cab”
dism /Image:“D:\WinPE64\mount” /add–package /packagepath:“C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs\WinPE-FontSupport-JA-JP.cab”
dism /Image:“D:\WinPE64\mount” /add–package /packagepath:“C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs\ja-jp\lp.cab”
dism /Image:“D:\WinPE64\mount” /Set–AllIntl:ja–JP
dism /Image:“D:\WinPE64\mount” /set–InputLocale:0411:00000411
dism /Image:“D:\WinPE64\mount” /Set–TimeZone:“Tokyo Standard Time”
dism /Image:“D:\WinPE64\mount” /Set–LayeredDriver:6
dism /Image:“D:\WinPE64\mount” /Gen–LangINI /distribution:“D:\WinPE64\media”
dism /Image:“D:\WinPE64\mount” /Set–SetupUILang:ja–JP /distribution:“D:\WinPE64\media”
```

必要なプログラムを入れます。

ライブラリが必要なモノ、インストールが必要なモノは基本的に使えないです。ポータブル版アプリを選びましょう。

またWinPEでは64bitPE上で32bitアプリを起動することはできません。厳密にはできないことはないですが、手順が死ぬほど複雑になるのでここでは紹介しません。

`D:\WinPE64\mount\`内がRAMディスク(通常X:)で展開される中身になります。アプリはProgram Filesに入れるのが主流です。X:直下にToolsフォルダを作って自作の.batファイルを入れるのもアリだと思います。

エクスプローラーでコピーペーストすればOK。管理者権限を要求されますが問題ありません。

ドライバは`Dism /Image:D:\WinPE64\mount” /Add-Driver /Driver:ドライバファイル`で追加できます。
RAIDやよほど特殊な機器でない限りはなくても大抵は大丈夫だと思います。

何かしらの事情でネットワークドライブを使うなどの場合、ネットワーク系のドライバを入れてインターネットにつなぐこともありますが通常は必要ないでしょう。

次に環境変数の追加。

`D:\WinPE64\mount\Windows\system32\startnet.cmd`を一度適当なフォルダにコピーし、内部を書き換えます。

startnet.cmd ファイルはWindowsPE起動時に読み込まれるファイルです。ここに何かコマンドを記述すると自動的に実行されます。
set PATH=%PATH%;X:\Tools

これで環境変数PATHにToolsフォルダが追加され、batファイルを直打できるようになります。出来上がったstart.netを元の場所にコピーして上書き。

作業が完了したら、エクスプローラーなどを閉じてアンマウントします。閉じ忘れると失敗します。再度になりますが、アンマウントするまでマウントを行ったコマンドプロンプトは消さないでください。アンマウントし終えたらイメージの作成は完了です。お疲れさまでした。

```cmd
Dism /unmount–image /mountdir:D:\WinPE64\mount\ /commit
```

### イメージファイルの書き出し・焼き込み

#### isoファイルに書き出す(CDブート用)

ファイル名は適宜変えて下さい。USBに書き込むこともできるので、複数作る場合はこちらが便利かもしれません。

```cmd
MakeWinPEMedia /iso D:\WinPE64 D:\WinPE64\WinPE.iso
```

#### USBに直接焼き込む

F:がUSBだと仮定します。各自で確認してください。フォーマットされる（中のデータはすべて消える）ので注意。一応最終確認されるのでyを入力。

```cmd
MakeWinPEMedia /UFD D:\WinPE64 F:
```

## Tips

- この手順を多少楽にするサードパーティー製ツールがある
  - Microsoft公式ではないのでこのページでは紹介しません
- 大抵のソフトはPEでの動作なんて想定していないので動かなくても作者に苦情など言わないように
  - 経験上Cで書かれたソフトはほぼ動く
- ランチャーを導入し、startnet.cmdにランチャーの起動コマンドを入れるとアプリの起動メニューを疑似的に作れる
- 環境変数にショートカットを作って導入したアプリをコマンドラインから起動できるようにすると便利
- インターネットにつなぐ必要はないと思うが、ドライバを入れてやればできる
  - NASから必要なファイルを落とす場合などに
  - 有線LANなら標準ドライバで動くことがある
  - ポータブル版ブラウザも開ける(Firefoxとか)
  - 修復で有名なDISM /onlineはWindows PEだと動かないよ
- エクスプローラーはないけどポータブル版のファイラーは動く
  - 動かそうと思えばできるが相当メンドイ……
  - notepadで近いことはできるので必要性はやや薄い
- 一部のシステムファイルはmount\Windows\System32にぶち込めば動く
  - cipherやSDeleteがよく使われる
- 作りこむと便利だが、PEである必要がどんどんなくなっていくのでよく検討すること

楓がよく追加するもの

- 復旧ソフト
  - bluescreenview
  - recuva
- 診断ソフト
  - Crystaldiskinfo
  - MemTest64 (TechPower)
  - HWmonitor
  - CPU-Z

その他、自作のrobocopyのbatファイルやcipherなど。

あと地味に便利なのは`Wpeutil shutdown`をbat化して`shutdown.bat`で保存しとく。いつもの`shutdown -h now`でPEを落とせるので楽。
