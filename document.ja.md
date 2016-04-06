---
layout: default
title: The documentation for version 1.1.0
css: page.css
place: second
prev: About
prev_url: about.ja.html
upper: yes
---
> ### Definitions
* ログを書き込むファイル名を`ログファイル`と表記することがあります。
* ログファイルに書き込む内容(`#w(message)`の`message`の部分)を`ログ`と表記することがあります。

> ### Jump quickly
* [Public Class Methods](#public-class-methods)
    * [instance](#instance)
* [Public Instance Methods](#public-instance-methods)
    * [init](#init)
    * [with_stdout](#with_stdout)
    * [compress](#compress)
    * [compress_level](#compress_level)
    * [w](#w)
    * [&lt;&lt;](#<<)
    * [enable_wflush](#enable_wflush)
    * [disable_wflush](#disable_wflush)
    * [e](#e)
    * [reopen](#reopen)
    * [flush](#flush)
    * [threshold](#threshold)
    * [logging_format](#logging_format)
    * [date_format](#date_format)
    * [rename_format](#rename_format)
    * [no_wcheck](#no_wcheck)
    * [file_closed?](#file_closed?)
    * [debug](#debug)
    * [info](#info)
    * [warn](#warn)
    * [error](#error)
    * [fatal](#fatal)
    * [sleep_time](#sleep_time)
    * [psync](#psync)
    * [sync_inode](#sync_inode)
    * [no_sync_inode](#no_sync_inode)



## Public Class Methods
{% include header_method.html text="instance" id="instance" %}
> `SimpleRotate`クラスは`Singleton`パターンで実装されています。
> 従って返却するオブジェクトは唯一の`SimpleRotate`オブジェクトです。
> `initilize`メソッドが`private`になりオブジェクトからのアクセスはできないため`new`メソッドを使うとエラーなります。  

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。



## Public Instance Methods
{% include header_method.html text="init([file_name, [limit, [generation]]])" id="init" %}
> ログの取り方に関する設定をします。

> ここで設定した内容は、後に`SimpleRotate::instance`で返されるオブジェクトも保持し続けます。返り値は`self`です。  

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='file_name = File.absolute_path($0+".log")' %}
>> ログを書き込むファイル名を`String`か`Symbol`で指定します。デフォルトは`./実行ファイル名.log`です。フルパス、もしくは相対パスで指定します。
>> 相対パスの場合は、実行ファイルが起点になります。指定したファイルが存在しない場合はこのタイミングでファイルが生成されます。

>> ファイルの1行目はローテーションに関する情報です。この行は消さないでください。

>> ここで指定したファイルと同じファイルが存在する場合、そのファイルに追加書き込みします。ファイルではなく標準出力のみに出力したい場合はシンボルで`:STDOUT`と指定します。   

> * {% include header_param.html text='limit = "100M"' %}
>> ログファイルの最大サイズを`Integer`もしくは`"1G"`等の文字列で指定します。`"K", "M", "G"`が認識されます。デフォルトは`"100M"`です。
>> 例えば`SimpleRotate.init("/var/log/ruby/app/foo.log", "500M")`と指定すると 500MB までファイルにログを書き込みます。

>> `500MB`と書いてはいけません。認識するのは末尾の`"K", "M", "G"`だけです。それ以外の文字列は`to_i`によって排除されます。従って`500MB`と書いたときは`500`バイトと認識されます。

>> `init`メソッド、`w`メソッドでログファイルのサイズを評価し ここで指定した設定値を超えていた場合、次のファイルに書き込みます。  
>> その際、古いファイルは`file_name.1`, `file_name.2`, `file_name.3`, `file_name.4`のようにリネームされ、古いログファイルほど古い数字が記されることになります。

>> また、`"DAILY"`, `"WEEKLY"`, `"MONTHLY"`を指定することでファイルの容量でなく一定の期日ごとにローテーションする事も可能です。
>> その場合は、それぞれログを書き込んだファイルの作成日を起点に`1日毎`, `7日毎`, `30日毎`に次のファイルに書き込みされます。
>> 次のファイルにログを書き込む際に古いファイル名は`file_name.YYYYmmdd`というフォーマットでリネームされます。

> * {% include header_param.html text='generation=0' %}
>> 古いログファイルの最大数を指定します。古いログファイルはここで指定した数で世代交代します。

>> 例えば`generation`に`4`を設定すると、古いログファイルは`file_name.1`, `file_name.2`, `file_name.3`, `file_name.4`の 4世代まで作られます。
>> この場合、新しいログファイルを入れると最大 5つのファイルでローテーションします。  

>> 値を`0`に設定すると世代交代は行わません。デフォルトは`0`ですので世代交代は行われません。   

> ブロック付きでコールする事もできます。ブロックを抜けると自動でログファイルの I/O ポートを閉じます。   
> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance

logger.init("foo.log") do |sr|
  sr << "log message"
end

logger.init("bar.log") do |sr|
  sr << "log message"
end
{% endhighlight %}


{% include header_method.html text="with_stdout" id="with_stdout" %}
> ログを標準出力(STDOUT)にも出すようにします。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text="compress" id="compress" %}
> ローテーションする際に古いログファイルを gzip 圧縮します。`zlib`を読み込みます。デフォルトは圧縮は行いません。
> `init`メソッドでログファイルのローテーションが行われる可能性がある為`init`メソッドの前に行うべきです。  

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text='compress_level(level)' id="compress_level" %}
> 古いログファイルを圧縮する際の圧縮レベルを指定します。数字が高い方が圧縮度が高くなります。デフォルトの圧縮レベルは`Zlib::DEFAULT_COMPRESSION`です。
> このメソッドを呼び出すとログファイルの圧縮が有効に切り替わります。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='lelvel' %}
>> 圧縮レベルを`0-9`までの`Integer`で指定します。


{% include header_method.html text="w(log_message)" id="w" %}
> パラメータ`log_message`をログファイルに書き込みます。

> ここでログファイルに書き込むログは必ず深刻度（ログレベル）の情報を持ちます。
> 直近に実行したログレベルを決めるメソッド(`#debug`,`#info`,`#warn`,`#error`,`#fatal`)でログレベルは決まります。
> 一度ログレベルを決めたあとはログレベルの情報は保持され続けます。
> ログレベルを決めるメソッドが一度も実行されてないオブジェクトは`INFO`をログレベルに持ちます。

> {% include small_header.html text='Returns' %}
> 引数で指定した`log_message`を返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='log_message' %}
>> ログファイルに出力するログを指定します。`string`でなくても`integer`や`float`でも構いません。`array`を指定する事も可能です。   

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
ary = [111, 333, 555]
logger.w ary
logger.error.w("エラーです")
{% endhighlight %}

> 下記のようにログファイルに出力されます。
{% highlight plain %}
[2014/01/15 19:44:22] - INFO : [111, 333, 555]
[2014/01/15 19:44:22] - ERROR : エラーです
{% endhighlight %}


{% include header_method.html text='&lt;&lt; log_message' id="<<" %}
> `#w`のエイリアスです。  

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
logger << "エラーです"
{% endhighlight %}


{% include header_method.html text='enable_wflush' id="enable_wflush" %}
> `#w`を呼び出した後 I/Oポートの内部バッファをフラッシュします。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text='disable_wflush' id="disable_wflush" %}
> `#w`を呼び出した後 I/Oポートの内部バッファをフラッシュしません。デフォルトはこの挙動です。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text='e' id="e" %}
> ログファイルの I/Oポートを閉じます。

> {% include small_header.html text='Returns' %}
> `#init`のパラメータ`file_name`に`:STDOUT`を指定した場合は`nil`を返します。それ以外は`true`を返します。


{% include header_method.html text='reopen' id="reopen" %}
> 閉じたログファイルの I/Oポートを開きます。

> {% include small_header.html text='Returns' %}
> ログを記録するファイルの`File class`のオブジェクトを返します。

> `#init`のパラメータ`file_name`に`:STDOUT`を指定した場合は`nil`を返します。また、I/Oポートを閉じていない時に呼び出すと WARNINGメッセージを出し`nil`を返します。


{% include header_method.html text='flush' id="flush" %}
> 新しくファイルを生成し、今後はそのファイルにログを書き込むようにします。
> ファイルサイズが`limit`に満たないファイルをローテーションしたい時に使います。  

> {% include small_header.html text='Returns' %}
> `#init`のパラメータ`limit`に`"DAILY"`や`"WEEKLY"`などのファイルサイズ以外のものを指定した場合はローテーションせず`nil`を返します。
> `#init`のパラメータ`file_name`に`:STDOUT`を指定した場合も同様に`nil`を返します。


{% include header_method.html text='threshold [= log_level]' id="threshold" %}
> 全てのログは`"DEBUG" > "INFO" > "WARN" > "ERROR" > "FATAL"`までのログレベルを持ちます。左から右にかけてログの深刻度は増していきます。
> ここで指定するのはどのログレベル以降のログをログファイルに出力するかです。

> 例えば、`ERROR`を閾値に指定すると`ERROR`もしくはそれ以上のログレベルを持つ`FATAL`をログだけがログファイルに出力されるようになります。

> {% include small_header.html text='Returns' %}
> 現在値を`string`で返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='log_lelvel' %}
>> `DEBUG`,`INFO`,`WARN`,`ERROR`,`FATAL`のいずれかを`string`で指定します。デフォルトは`INFO`です。

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "DAILY")

# ERROR 以上のログレベルを持つログだけがファイルに出力されます。
logger.threshold = "ERROR"

logger.debug << "message" #=> DEBUG は書き込まれません。
logger.info << "message" #=> INFO は書き込まれません。
logger.warn << "message" #=> WARN は書き込まれません。
logger.error << "message" #=> ERROR は書き込まれます。
logger.fatal << "message" #=> FATAL は書き込まれます。
{% endhighlight %}


{% include header_method.html text='logging_format [= format]' id="logging_format" %}
> ファイルにログを出力する際のフォーマットを指定します。デフォルトの`format`の値は`"[$DATE] - $LEVEL : $LOG"`です。

> {% include small_header.html text='Returns' %}
> 現在値を`string`で返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='format' %}
>> `string`で指定します。フォーマットで使用できる定数は以下です。

> * **$DATE**  - 日付です。日付のフォーマットは #date_format で定義できます。
> * **$PID**   - プログラムのプロセスIDです。
> * **$LEVEL** - ログのレベルです。
> * **$LOG**   - ログすなわち #w(message) の message です。
> * **$FILE**  - 現在実行中の Ruby スクリプトのファイル名(ファイル名のみ)です。
> * **$FILE-FUL**  - 現在実行中の Ruby スクリプトのファイル名(絶対パス)です。

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "1G")
logger << "message"
{% endhighlight %}

> 下記のようにログファイルに出力されます。

{% highlight plain %}
[2013/10/23 20:15:13] - INFO : message
{% endhighlight %}

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "1G")

logger.logging_format = "[$LEVEL] : $DATE => $LEVEL: [$LOG] @ $FILE-FUL"
logger.fatal << "message"
{% endhighlight %}

> 下記のようにログファイルに出力されます。

{% highlight plain %}
[FATAL] : 2013/10/23 20:15:13 => FATAL: [message] @ /var/log/ruby/app/foo.log
{% endhighlight %}


{% include header_method.html text='date_format [= format]' id="date_format" %}
> ログをファイルに出力する時の $DATE のフォーマットを指定します。
> デフォルトは`"%Y/%m/%d %H:%M:%S"`ですので日付のフォーマットは`2013/10/04 20:04:59`のようになります。

> {% include small_header.html text='Returns' %}
> 現在値を`string`で返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='format' %}
>> `string`で指定します。`format`の書式は`Date#strftime(format)`の引数と同様です。

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "DAILY")

logger.date_format = "%y-%m-%d %H:%M:%S"
logger << "message"
{% endhighlight %}

> 下記のようにログファイルに出力されます。

{% highlight plain %}
[13-10-04 20:04:59] - INFO : message
{% endhighlight %}


{% include header_method.html text='rename_format [= format]' id="rename_format" %}

> {% include small_header.html text='Returns' %}
> 現在値を`string`で返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='format' %}
>> 新しくログファイルを作る際に古いログファイルはリネームされます。ファイル名は`file_name.1`や`file_name.20131024`のような命名規則でリネームされます。
>> この古いファイル名の`.`の部分を`format`で指定する任意の`string`に変更できます。デフォルトは`.`です。

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance

# init でローテーションが行われることがあるので init の前で定義します。
logger.rename_format = ".example."

logger.init("/var/log/ruby/app/foo.log", "1G")
{% endhighlight %}
> この場合ローテーションする際に`app.log.example.1`というファイル名でリネームされます。


{% include header_method.html text='no_wcheck' id="no_wcheck" %}
> 次のログファイルを作成するべきかどうかの判断は`#w`か`#init`で行われます。

> このメソッドをコールすると`#w`実行時にローテーションを行うべきかチェックを行いません。従って`#w `の実行によってファイルのローテーションは行われません。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text='file_closed?' id="file_closed?" %}
> ログファイルがI/Oポートが閉じているかどうかを調べます。

> {% include small_header.html text='Returns' %}
> ログファイルのI/Oポートが開いているときは`true`、それ以外で`false`を返します。
> なお、`#init`のパラメータ`file_name`に`:STDOUT`を指定した場合は`nil`を返します。


{% include header_method.html text='silence' id="silence" %}
> WARNINGメッセージを出力しないようにします。
> WARNINGメッセージとは`SimpleRotate`クラス内部で予期せぬ状況が発生した時に標準エラー出力に吐かれる`[WARNING] File is already open! - (SimpleRotate::Error)`
のようなメッセージです。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。


{% include header_method.html text='debug' id="debug" %}
> ログレベルを`DEBUG`にします。`DEBUG`はデバッグ用のログです。

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。従ってそのままメソッドチェインで`#w`につなぐことができます。


{% include header_method.html text='info' id="info" %}
> ログレベルを`INFO`にします。`INFO`はプログラム上のある特定の情報を知らせるログです。

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。従ってそのままメソッドチェインで`#w`につなぐことができます。


{% include header_method.html text='warn' id="warn" %}
> ログレベルを`WARN`にします。`WARN`は深刻なエラーではありませんが警告を促すログです。

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。従ってそのままメソッドチェインで`#w`につなぐことができます。


{% include header_method.html text='error' id="error" %}
> ログレベルを`ERROR`にします。`ERROR`はエラーを知らせるログです。

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。従ってそのままメソッドチェインで`#w`につなぐことができます。


{% include header_method.html text='fatal' id="fatal" %}
> ログレベルを`FATAL`にします。`FATAL`はプログラムが停止するような致命的なログです。

> {% include small_header.html text='Returns' %}
> `SimpleRotate`オブジェクトを返します。従ってそのままメソッドチェインで`#w`につなぐことができます。


> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
logger.warn << "log message"
logger << "log message" # 省略してもログレベルは"WARN"を引き継ぎます。
logger.fatal << "log message"
logger << "log message" # 省略してもログレベルは"FATAL"を引き継ぎます。
{% endhighlight %}

> 以下のようにログファイルに書き込まれます。

{% highlight plain %}
[2013/12/16 14:15:03] - WARN : log message
[2013/12/16 14:15:03] - WARN : log message
[2013/12/16 14:15:03] - FATAL : log message
[2013/12/16 14:15:03] - FATAL : log message
{% endhighlight %}


{% include header_method.html text='sleep_time [= sec]' id="sleep_time" %}
> ログファイルのローテーションが完了したあと停止する時間を秒で指定します。これはマルチスレッド、マルチプロセスの際に重要です。
> シングルで動かす場合は特に呼び出す意味はありません。この値は`#psync`のパラメータでも指定可能です。

> もし複数のスレッドやプロセスが同時にローテーションを行ってしまう場合はこの値を大きくしてみてください。
> 実際にリネームが行われるまでのオーバーヘッドを考慮しての事です。

> {% include small_header.html text='Returns' %}
> 現在値を`float`もしくは`fixnum`で返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='sec' %}
>> `float`もしくは`fixnum`で指定します。デフォルトは`0.1`です。`#psync`をコールしてない場合は`0`です。


{% include header_method.html text='psync(sec=0.1)' id="psync" %}
> プロセス間でクリティカルセクションを排他制御し、複数のプロセスによる実行でも安全にロギングできるようにします。`#init`にクリティカルセクションがある為`#init`の前に行うべきです。
> 書き込みの際に inode番号を調べ常に新しいファイルに書き込みできるように同期を試みます。また、書き込みのあと I/Oポートの内部バッファをフラッシュします。

> このメソッドをコールすると排他制御用の一時ファイルが生成されます。
> 一時ファイルは`.SimpleRotate_tempfile_【プログラムのファイル名】`という命名規則でログファイルと同じディレクトリに生成され、Ruby の終了処理で削除されるようにスケジューリングされます。
> 一時ファイル生成の際に既に同じ名前のファイルが存在する場合や終了処理で一時ファイルが空でない場合や削除するパーミッションがない場合は一時ファイルの削除は実行されません。
> 通常起こり得ませんがロック取得時に予期しないエラーが起きると3回まで再びロック取得を試みます。3度目が失敗するとロックを取得しないで処理を行います。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。

> {% include small_header.html text='Parameters' %}
> * {% include header_param.html text='sec' %}
>> ログファイルのローテーションが完了したあと停止する時間を秒で指定します。`float`もしくは`fixnum`で指定します。デフォルトは`0.1`です。

> {% include small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.psync(0.5)
logger.init("/var/log/ruby/app/foo.log")
{% endhighlight %}


{% include header_method.html text='sync_inode' id="sync_inode" %}
> 現在開いているファイルの inode番号と`#init`の`file_name`で指定したファイルの inode番号を比較し差異を確認した場合`file_name`で指定したファイルを開き直します。これは`#w`呼び出し時に自動で行われるのであまり意識して呼び出すメソッドではありません。

> {% include small_header.html text='Returns' %}
> inode番号に差異があった場合やなんらかの原因で inode番号を取得できない場合は最大で 3回開き直し、それでも inode番号が一致しなければ WARNINGメッセージを出力し`false`を返します。
> ログファイルが正常に開けていない場合や`#init`の`file_name`に`:STDOUT`を指定している場合は`nil`を返します。
> それ以外では`true`を返します。


{% include header_method.html text='no_sync_inode' id="no_sync_inode" %}
> 現在開いているファイルの inode番号と`#init`の`file_name`で指定したファイルの inode番号を比較しません。
> シングルスレッド、シングルプロセス時に使用すべきです。

> {% include small_header.html text='Returns' %}
> 返り値はありません。`nil`を返します。



<h2 class="header">Required</h2>
> `SimpleRotate`クラスが使用している標準添付ライブラリです。

> ### singleton

> ### monitor

> ### zlib



## Classes

> ### SimpleRotate::Error
>> `SimpleRotate`クラス内での例外を取り扱う内部クラスです。
>> 基本的に`SimpleRotate`クラス内部で発生し得るエラーはこの例外です。  

> ### SimpleRotate::ProcessSync
>> プロセス間で安全にロギングするための機能を提供する内部クラスです。 
>> `SimpleRotate::ProcessSyncMixin`を`Mix-in`しています。



## Modules
> `SimpleRotate`クラス内で使用している内部モジュールです。  

> ### SimpleRotate::LogLevel

> ### SimpleRotate::RotateTerm

> ### SimpleRotate::ProcessSyncMixin

> ### SimpleRotate::Validator


