「メモ帳でプログラミング」、「開発ツールのないWindowsでプログラミング」はその手の記事の常套句です。

でも、これらって大抵は入力補助できるエディタが事実上必須要件です。バッチファイルならまだしも、本格的なプログラム言語ならまともに書いていられないです。入力補助なんて夢のまた夢。メモ帳はインデントや色分けさえ使えないんですよ。だからといって、外部のエディタを利用する場合、もはや真の意味でのWindows標準とは言えないですよね。

## 極限の開発環境

コンパイラさえ存在しない極限の開発環境。そんなものに出くわす日が来るかもしれません。

何かしらの作業を効率化したいけど、たまたま手元にあるのがインストールが禁止されているパソコンかあるいはインターネットにつなぐ事ができないパソコンしかない場合。そうです、会社のパソコンのことです。

それの対策を今回は考えてみます。

## 前提条件

極力あり得る程度の条件を指定します。

ハードウェア

- CPU　：Intel Coreシリーズ
- メモリ：4GB～8GB程度
- ストレージ：128GB~512GB程度のHDD or SSD

ソフトウェア

- OSは現在最もスタンダードなWindows 10 Home 64bitを想定
- 管理者権限はあるものとして考える
- インターネット上のソースコードはコピペは可能
- セキュリティ上の観点からソフトウェアのインストールは禁止
- 外部のライブラリをダウンロードするのも禁止
- コンパイラももちろん持ち込み禁止
- OS標準のアプリ、dllは利用可能

ヒューマンウェア

- ある程度のプログラミング能力
- 統合開発環境ならそれなりのアプリが開発できる
- リファレンスを読んで使える（理解できるとは言っていない）
- メモ帳にゴリゴリ書き込むことはできない
- 納期や時間はそこまで考慮しないが、無限ではない
- 開発者は単独でありチームではない

顧客の要求

- コンソールアプリケーションは不可
- GUIで直観的に操作可能なツール
- 起動に環境変数や拡張子の事前設定が要らず、ダブルクリックで実行できる
- ローカルに何らかの設定ファイルを残す
- 電卓や簡単なレジ程度の複雑さ

## 開発環境を整えよう

まずは言語の候補を絞り込みましょう。Windows標準で使えそうなツールは以下の通りです。

- バッチファイル
- JavaScript(Edge経由)
- VBScript
- PowerShell
- C#

### バッチファイル

古典的なWindows標準のシェルスクリプトもどき。

残念ながらGUI非対応の為早速お役御免。

これ自体をアプリにするのは不可能なので、コンパイルやファイル操作に使う。

### JavaScript

ブラウザから実行できる言語。

HTMLと併用前提なのでGUIを作る敷居は低く、ブラウザの開発者モードでデバッグできる。

変数をリアルタイムで確認したり、ブレークポイントの設定ができるのはこの中だとこれとPowershellだけ。

HTMLをダブルクリックで実行可能。Edge/IEは必ず入っているし、関連付けも不要。

しかしローカルにファイルを保存できない。

### VBScript

シェルスクリプト。とてもマイナー。情報もかなり少なく衰退傾向。どう考えてもメモ帳で書くとか正気じゃない。

できることはjavascriptより少しだけ多いが、果たして役立つのか。

### PowerShell

一番まともなシェルスクリプト。希望の星。.NET Frameworkのライブラリを使用可能な他、C#のコードを組み込める。しかもこれだけ高機能なのにWindows標準でPowerShell ISEという統合開発環境が付いてくる。色分けや正規表現検索はもちろん、補完や変数管理、コマンドレットのヘルプもある。

ここまでだと文句なしに最強なのだが、ダブルクリックで実行できない。仮に拡張子関連付けをしても実行ポリシーが初期値ですべて拒否なのでスクリプトの実行ができない。

これにより使用するためのバッチファイルを書かされるという意味の分からない仕様。

### C-sharp

言語のできることで云えばなんでもできる。最高の言語。

機能が豊富で、ライブラリを利用できなくてもあまり困らない。

軽量で、コンパイルすればexeファイルなのでダブルクリック対応可能。

コンパイラのバージョンが環境によりまちまち。Windows 10なら少なくとも.NET Framework 4.6以降が入っており、C# 6.0か7.0が使える。

最大の難点はメモ帳で書くのがつらい。IDE前提であらゆる仕組みが構築されているうえ、コンパイルする手間も合わさりメモ帳で凝ったことをするのは無謀。デバッグもできないもんね。

## で、何で開発するの？

単体でダメなら合わせればいいじゃない。

C#の変数にPowershellスクリプトを埋め込んで、C＃から呼び出すプログラムを作ればいい。

C#から呼び出したPowershellスクリプトは実行ポリシーに関係なく実行できる（Powershell.exeが実行されているわけではないため）

Powershellスクリプトの冒頭であらかじめ実行ポリシーを変更しておけば他のスクリプトを呼び出すこともできるのでコンフィグファイルのような使い方もできる。

よって、基本的なプログラムは全てPowershell ISEで書く。

ps1をC#と合体させたcsファイルを作成するバッチファイルを用意し、そのままコンパイル。

これなら、.net Frameworkの豊富なライブラリを利用したGUIアプリが作れる上に、空の環境でもそれなりの開発力で臨むことができる。

## では作ってみよう

C#からPowershellを実行するには「`system.management.automation.dll`」が必要です。

これCドライブのいずこかにあるのでコンパイルするバッチファイルと同じフォルダにコピーしておきます。

ネットで検索するとVisual Studioをインストールした後のパスばかり表示されますが、.Net FrameworkのライブラリなのでVisual Studioなしでもどこかにある。

コマンドプロンプトから次のコマンドで検索可能。

```cmd
where /r c:\ system.management.automation.dll
```

実行環境がWindows10だとこのDLLは同梱しなくてもちゃんと動く模様。ただしコンパイル時は自動で探してくれない。

Powershellを実行するにはRunspaceInvokeクラスを使います。引数を渡せばPowershell側で使用することもできますので、幅は効きますね。

CUIの場合はMain関数の引数をそのまま渡せばいいですし、GUIの場合はSystem.Environment.GetCommandLineArgs()を利用します。

いずれにしても$inputにパイプライン引数として格納されます。

guisample.cs

``` cs
namespace ps1exe {
  using System;
  using System.Windows.Forms;
  using System.Management.Automation;
 
  public class Base : Form
  {
    private void Fload(object sender, EventArgs e)
    {
      string[] cStr = System.Environment.GetCommandLineArgs();
      using (RunspaceInvoke invoker = new RunspaceInvoke())
      {
        invoker.Invoke(psscript,cStr);
      }
      this.Close();
    }
    public Base()
    {
      this.Load += this.Fload;
    }
    [STAThread]
    public static void Main()
    {
      Application.Run(new Base());
    }
    private string psscript = @"
# region PowerShell Scrpit
# Escape double-quotation in this variable.
 
$a = @($input)
 
Add-Type -AssemblyName System.Windows.Forms
$form = New-Object System.Windows.Forms.Form
$form.Size = New-Object System.Drawing.Size(400,100)
$form.Text = ""Test of hogehoge""
 
$label = New-Object System.Windows.Forms.Label
$label.text = $a
$label.Size = New-Object System.Drawing.Size(400,10)
$label.Location = New-Object System.Drawing.Point(0,10)
$form.Controls.Add($label)
$form.ShowDialog();
 
# endregion
";
  }
}
```

GUICompile.bat

```cmd
c:\Windows\Microsoft.NET\Framework\v4.0.xxxxx\csc.exe /t:winexe %~1 /r:system.management.automation.dll
```

## 余談

プログラムの中で全く別の言語のスクリプトを実行している関係で、ウイルス扱いされる可能性があります。

何かあっても私は何の責任も取れませんので、この記事を見て何かするなら自己責任でどうぞ。
