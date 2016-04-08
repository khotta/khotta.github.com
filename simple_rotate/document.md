---
layout: sr/default
title: The documentation for version 1.1.0
css: page.css
place: second
prev: About
prev_url: about.html
upper: yes
---
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
{% include sr/header_method.html text="instance" id="instance" %}
> SimpleRotate class is implemented as singleton design pattern,   
> therefore you can take SimpleRotate object that's having your preference in any context.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate.



## Public Instance Methods
{% include sr/header_method.html text="init([file_name, [limit, [generation]]])" id="init" %}
> Specifies some informations about logging.


> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='file_name = File.absolute_path($0+".log")' %}
>> Specifies filename that is written logs. You must specify absolute path or relative path by `symbol` or `string`.

>> Default is `./<current filename>.log`.

>> When the file you set dosen't exist, the file will be created at this method was called.

>> The first of row in log file is an information of when the file was created.
>> Because some file system don't support to give file creation datetime.
>> You shold not delete it.

>> When the file you set already exists, append logs to the file instead of overwriting it.

>> When you prefer to write logs to STDOUT, You should specify this value to `:STDOUT`.


> * {% include sr/header_param.html text='limit = "100M"' %}
>> When the log file reaches a specific size or specific term that are set as `limit`, SimpleRotate renames the log file and creates a new one.

>> There is necessary to set the value with `integer` or `string` for max limit size of log file, You can use the following words `"K", "M", "G"` like `"1G"`.
>> Default is `"100M"`.

>> For example, When specified like `SimpleRotate.init("/var/log/ruby/app/foo.log", "500M")`, log files are written untill it reaches its size to be "500M" and over.

>> You must not set like `"500MB"`, the keywords are only accepted `"K", "M", "G"`. Others will be removed by `to_i`. Therefore `500MB` will be converted to `500` and it means 500 byte.

>> Log file's size is checked when `#init`, `#w` methods are called. After that if file size reaches specific size, the log file is renamed and new log file is created, after logs are written to the new one.
>> When renaming is done, old log files are renamed as followng `file_name.1`, `file_name.2`, `file_name.3`, `file_name.4`. The older log file, The bigger number.

>> When you set `"DAILY"`, `"WEEKLY"`, `"MONTHLY"` as `string`, you can rotate log files by specific term.

>> * `"DAILY"`: Writes logs to the log file just 24 hours from it has been created.
>> * `"WEEKLY"`: Writes logs to the log file just 7 days from it has been created.
>> * `"MONTHLY"`: Writes logs to the log file just 30 days from it has been created.

>> The renaming format of log files is `file_name.YYYYmmdd`.

> * {% include sr/header_param.html text='generation=0' %}
>> Maximam number of rotations. Renamed log files are rotated by this count.

>> For example, If you set `4` to value of `generation`, old log files are rotated between `file_name.1`, `file_name.2`, `file_name.3`, `file_name.4`.

>> Default value is `0`. If it is set `0`, old log files aren't deleted.

> This example is calling the method with a block. When exits from a block the I/O port of it is closed automatically.
> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance

logger.init("foo.log") do |sr|
  sr << "log message"
end

logger.init("bar.log") do |sr|
  sr << "log message"
end
{% endhighlight %}


{% include sr/header_method.html text="with_stdout" id="with_stdout" %}
> When `#w` is called, writes logs to not only a log file but also STDOUT.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.

{% include sr/header_method.html text="compress" id="compress" %}
> Turns on gzip compression. Log files are compressed by gzip when these are rotated and renamed.

> When you enable gzip compression, you must call this method before `init` was called because there is a possibility a log file is rotated.

> Defalt is not available to compress log files.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.


{% include sr/header_method.html text='compress_level(level)' id="compress_level" %}
> Sets gzip compression level.

> Default gzip compression level is `Zlib::DEFAULT_COMPRESSION`.  
> The grator number, The higher copression.

> * Note: Default compression level which is a good trade-off between space and time.

> Turns on gzip compression when this method is called.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='lelvel' %}
>> Compression levels from 0 to 9 as `integer`.


{% include sr/header_method.html text="w(message)" id="w" %}
> Writes `message` to a log file.

> All of `message` have log levels. Log levels are information of seriousness.   
> Log levels are defined by `#debug`,`#info`,`#warn`,`#error`,`#fatal`.

> For example, when `#warn` is called, log's seriousness is switched to `WARN`, therefore the defined constant [`$LOG`](#logging_format) will be converted to `WARN`.

> Default log's seriousness is `INFO`.

> {% include sr/small_header.html text='Returns' %}
> Returns `message`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='message' %}
>> Logs to write to a log file. You can set some types not only `string` but also e.g. `integer`, `float`, `array`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
ary = [111, 333, 555]
logger.w ary
logger.error.w("This is ERROR message")
{% endhighlight %}

> Expects to be wrriten in a log file as,
{% highlight plain %}
[2014/01/15 19:44:22] - INFO : [111, 333, 555]
[2014/01/15 19:44:22] - ERROR : This is ERROR message
{% endhighlight %}


{% include sr/header_method.html text='&lt;&lt; message' id="<<" %}
> The alias of `#w`. You can use `<<` istead of `#w`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
logger.debug << "This is DEBUG message"
{% endhighlight %}


{% include sr/header_method.html text='enable_wflush' id="enable_wflush" %}
> Flushes any buffered data after `#w` was called.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.


{% include sr/header_method.html text='disable_wflush' id="disable_wflush" %}
> Dosen't flush any buffered data after `#w` was called. This is Default.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.


{% include sr/header_method.html text='e' id="e" %}
> Closes I/O stream of log files.

> {% include sr/small_header.html text='Returns' %}
> Usually retuns `true`, But if set `:STDOUT` in `file_name` when call `#init`, it returns `nil`.


{% include sr/header_method.html text='reopen' id="reopen" %}
> Opens I/O stream of the log file that is closed by `#e`.

> {% include sr/small_header.html text='Returns' %}
> The `file` object that is wrotten logs.

> If set `:STDOUT` in `file_name` when call `#init`, it returns `nil`.   
> Also returns `nil` and output the warning messag to STDERR if a log file isn't closed by `#e`.


{% include sr/header_method.html text='flush' id="flush" %}
> Rotates log files even if a log file dosen't reaches its threshold(file size).

> {% include sr/small_header.html text='Returns' %}
> If set `"DAILY"` or `"WEEKLY"` or `"MONTHLY"` in `limit` when call `#init`, dosen't rotate log files and it returns `nil`.

> If set `:STDOUT` in `file_name` when call `#init`, it returns `nil`.


{% include sr/header_method.html text='threshold [= log_level]' id="threshold" %}
> All of logs have its seriousness as follows `"DEBUG" > "INFO" > "WARN" > "ERROR" > "FATAL"`.  
> Log's seriousness is increased form left to right.

> Specifies in this method, which logs that is having seriousness should write to a log file.

> For exmaple, If set `"ERROR"` in `threshold`, logs that are having seriousness of `ERROR` and up (means `"FAITAL"`) are written to a log file.

> {% include sr/small_header.html text='Returns' %}
> Returns current value as `string`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='log_lelvel' %}
>> Log's seriousness. You can select it form the following `"DEBUG"`,`"INFO"`,`"WARN"`,`"ERROR"`,`"FATAL"` as `string`. Default of seriousness is `"INFO"`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "DAILY")

# "ERROR" and up are written to a log file.
logger.threshold = "ERROR"

logger.debug << "message" #=> "DEBUG" isn't written
logger.info << "message" #=> "INFO" isn't written to a log file
logger.warn << "message" #=> "WARN" isn't written to a log file
logger.error << "message" #=> "ERROR" is written to a log file
logger.fatal << "message" #=> "FATAL" is written to a log file
{% endhighlight %}


{% include sr/header_method.html text='logging_format [= format]' id="logging_format" %}
> Defines log's format. Default is `"[$DATE] - $LEVEL : $LOG"`.

> {% include sr/small_header.html text='Returns' %}
> Returns current value as `string`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='format' %}
>> Required to set as `string`. You can use following predefined constants in `format`.

> * **$DATE**  - Date or/and time. You can define the format to call `#date_format`.
> * **$PID**   - Current Ruby process ID.
> * **$LEVEL** - Log's seriousness.
> * **$LOG**   - Logs. it means `message` in `#w(message)`.
> * **$FILE**  - Current Ruby filename.
> * **$FILE-FUL**  - Current Ruby absolute filename.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "1G")
logger << "message"
{% endhighlight %}

> Expects to be wrriten in a log file as,
{% highlight plain %}
[2013/10/23 20:15:13] - INFO : message
{% endhighlight %}

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "1G")

logger.logging_format = "[$LEVEL] : $DATE => $LEVEL: [$LOG] @ $FILE-FUL"
logger.fatal << "message"
{% endhighlight %}

> Expects to be wrriten in a log file as,
{% highlight plain %}
[FATAL] : 2013/10/23 20:15:13 => FATAL: [message] @ /var/log/ruby/app/foo.log
{% endhighlight %}


{% include sr/header_method.html text='date_format [= format]' id="date_format" %}
> Defines the format of [`$DATE`](#logging_format).
> Default is `"%Y/%m/%d %H:%M:%S"` therefore [`$DATE`](#logging_format) will be coverted to like `2013/10/04 20:04:59`.

> {% include sr/small_header.html text='Returns' %}
> Returns current value as `string`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='format' %}
>> Required to set as `string`. The format is same as `Date#strftime(string)`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log", "DAILY")

logger.date_format = "%y-%m-%d %H:%M:%S"
logger << "message"
{% endhighlight %}

> Expects to be wrriten in a log file as,
{% highlight plain %}
[13-10-04 20:04:59] - INFO : message
{% endhighlight %}


{% include sr/header_method.html text='rename_format [= format]' id="rename_format" %}

> {% include sr/small_header.html text='Returns' %}
> Returns current value as `string.`

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='format' %}
>> When rotation is done and then a log file is renamed to following like `file_name.1`, `file_name.20131024`.

>> Defines renaming format that is a part of `.`.  For example if you set as `#rename_format(".log.")`, It will be renamed as `file_name.log.1`, `file_name.log.20131024`.

>> Default is a period `.`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance

# Must to define before `#init` was called because there is a possibility a log file is rotated.
logger.rename_format = ".example."

logger.init("/var/log/ruby/app/foo.log", "1G")
{% endhighlight %}
> Renames the log filename to `app.log.example.1` when rotation is done.


{% include sr/header_method.html text='no_wcheck' id="no_wcheck" %}
> The judgement that there is a necessarity to rotate log files is checked when `#w` or `#init` are called.  

> If this method has been called, dosen't check whether to rotate log files or not even when `#w` is called.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.


{% include sr/header_method.html text='file_closed?' id="file_closed?" %}
> Check whether a log file's I/O stream is closed or not.

> {% include sr/small_header.html text='Returns' %}
> When I/O stream is opened, it returns `true`, isn't opened returns `false`.

> If set `:STDOUT` in file_name when call `#init`, it returns `nil`.


{% include sr/header_method.html text='silence' id="silence" %}
> No Warning messages if there are any problems.

> Warning messages are message that are written to STDERR when unexpected problems are occurred in SimpleRotate class.

> For example `[WARNING] File is already open! - (SimpleRotate::Error)`.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.


{% include sr/header_method.html text='debug' id="debug" %}
> Switches log's seriousness to `"DEBUG"`. `"DEBUG"` is log for debugging.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate.

> Therefore you can use method chaining like `logger.debug.w "debug message"`


{% include sr/header_method.html text='info' id="info" %}
> Switches log's seriousness to `"INFO"`. `"INFO"` is log for informations in programs.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate, therefore you can use method chaining.


{% include sr/header_method.html text='warn' id="warn" %}
> Switches log's seriousness to `"WARN"`. `"WARN"` is log to inform warning of programs.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate, therefore you can use method chaining.


{% include sr/header_method.html text='error' id="error" %}
> Switches log's seriousness to `"ERROR"`. `"ERROR"` is log to inform errors of programs.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate, therefore you can use method chaining.


{% include sr/header_method.html text='fatal' id="fatal" %}
> Switches log's seriousness to `"FATAL"`. `"FATAL"` is critical log.

> {% include sr/small_header.html text='Returns' %}
> Returns an object of SimpleRotate, therefore you can use method chaining.


> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.init("/var/log/ruby/app/foo.log")
logger.warn << "log message"
logger << "log message" # the seriousness is keeping "WARN"
logger.fatal << "log message"
logger << "log message" # the seriousness is keeping "FATAL"
{% endhighlight %}

> Expects to be wrriten in a log file as,
{% highlight plain %}
[2013/12/16 14:15:03] - WARN : log message
[2013/12/16 14:15:03] - WARN : log message
[2013/12/16 14:15:03] - FATAL : log message
[2013/12/16 14:15:03] - FATAL : log message
{% endhighlight %}


{% include sr/header_method.html text='sleep_time [= sec]' id="sleep_time" %}
> Specifies the number of secounds to stop after rotation is finished.

> It is important to run multiple threads or multiple processes.
> If you run the program just single, there isn't any meaning to call this method.

> You can also specify this value to call `#psync(sleep_time)`.

> If threads or processes rotate log files at the same time, It may be good to increase the number.
> It is for overhead that renaming is done actually.

> {% include sr/small_header.html text='Returns' %}
> Retruns current value as `float` or `fixnum`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='sec' %}
>> Required to set as `float` or `fixnum`.

>> Default is `0.1` but when you didn't call `#psync` its value is `0`.


{% include sr/header_method.html text='psync(sec=0.1)' id="psync" %}
> Resolves critical section problem and it provides safety logging even if multi-process program.

> Must to cal before `#init` was called because it has critical section problem.

> When write logs to a log file, SimpleRotate check its inode number and try to write logs to a newest log file, also flushes any buffered data after `#w` was called.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.

> {% include sr/small_header.html text='Parameters' %}
> * {% include sr/header_param.html text='sec' %}
>> Specifies the number of secounds to stop after rotation is finished. Default is `0.1`.

> {% include sr/small_header.html text='For Example' %}
{% highlight ruby %}
logger = SimpleRotate.instance
logger.psync(0.5)
logger.init("/var/log/ruby/app/foo.log")
{% endhighlight %}


{% include sr/header_method.html text='sync_inode' id="sync_inode" %}
> Opens a newest log file if inode numbers have a diffrence between a log file be opened and a newest log file. 

> This method is necessarity called in `#w` therefore there is an necessarity to call this method youselves.

> {% include sr/small_header.html text='Returns' %}
> When there is a difference of both inode numbers, try to open a newest log file, but failed to open it or other problem was occurred it returns `failse` and write the warning message to STDERR.

> If failed to open a log file or didn't open a log file or set `:STDOUNT` in file_name when call `#init`, in the above cases mentioned return `nil`.

> In the other case, returns `true`.


{% include sr/header_method.html text='no_sync_inode' id="no_sync_inode" %}
> When this method is called dosen't check log file's inode number. You must to call this method when run single thread or process programs.

> {% include sr/small_header.html text='Returns' %}
> Returns `nil`.



<h2 class="header">Required</h2>
> The standard libraris are required for SimpleRotate.

> ### singleton

> ### monitor

> ### zlib



## Internal Classes
> ### SimpleRotate::Error

> ### SimpleRotate::ProcessSync



## Internal Modules
> ### SimpleRotate::LogLevel

> ### SimpleRotate::RotateTerm

> ### SimpleRotate::ProcessSyncMixin

> ### SimpleRotate::Validator
