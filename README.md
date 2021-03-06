Basic logging with batteries
----------------------------

[![IRC #go-nuts](https://www.irccloud.com/invite-svg?channel=%23go-nuts&amp;hostname=chat.freenode.net&amp;port=6697&amp;ssl=1)](https://www.irccloud.com/invite?channel=%23go-nuts&amp;hostname=chat.freenode.net&amp;port=6697&amp;ssl=1)
[![Build Status](https://travis-ci.org/bnclabs/golog.png)](https://travis-ci.org/bnclabs/golog)
[![Coverage Status](https://coveralls.io/repos/github/bnclabs/golog/badge.svg?branch=master)](https://coveralls.io/github/bnclabs/golog?branch=master)
[![GoDoc](https://godoc.org/github.com/bnclabs/golog?status.png)](https://godoc.org/github.com/bnclabs/golog)
[![GitPitch](https://gitpitch.com/assets/badge.svg)](https://gitpitch.com/bnclabs/golog/master?grs=github&t=white)

* APIs to prefix log-level in log messages.
* Global option to redirect logs to a file.
* Include/Exclude/Format log time.
* Colorize log messages for different levels.
* Console logging.
* Stable APIs, existing APIs are not going to change.

How to use golog
----------------

Packages can import golog and use its methods

```go
import github.com/bnclabs/golog
func myfunc() {
    ..
    log.Fatalf(...)
    ..
    log.Warnf(...)
    ..
    log.Debugf(...)
}
```

Note here that *log* is not an object name, importing *golog* resolves to
*log* package that has exported methods *Fatalf()* *Warnf()* etc ... For more
information please read the go-documentation for *log* package.

By default, importing the package will initialize the logger to
default-logger that shall log to standard output. To use custom logger
use the following initializer function in your package or application:

```go
import github.com/bnclabs/golog

var mylogger = newmylogger()
func init() {
    setts := map[string]interface{}{
        "log.level": "info",
        "log.file":  "",
    }
    SetLogger(mylogger, setts)
}
```

*mylogger* should implement the *log.Logger* interface{}.

**Order of log levels**

```golang
const (
	logLevelIgnore LogLevel = iota + 1
	logLevelFatal
	logLevelError
	logLevelWarn
	logLevelInfo
	logLevelVerbose
	logLevelDebug
	logLevelTrace
)
```

Console Logging
---------------

By default log APIs will worry about log-level, prefix format, time-format
sometimes it become too much of clutter on the screen to communicate simple
messages with user via console. In such cases use the `Consolef` API.

```go
    log.Consolef("goledger version - goledger%v\n", api.LedgerVersion)
```

`Consolef` does not print the log time, log level and always outputs to
stdout.

Settings
--------

* **log.level**, filter all messages logged at level greater than the
  configured value. Can be one of the following names -
  ignore, fatal, error, warn, info, verbose, debug, trace
* **log.flag**, comma separated value of log.Flags,
  eg: `Ldate,Ltime,Llongfile`, described further down.
* **log.file**, if not empty string, all log messages are appended to
  configured file.
* **log.timeformat**, format of time string prefixed to log message,
  should confirm to `time.Now().Format()`.
* **log.prefix**, `fmt.Sprintf` format string for log level, by
  default `[<leve>]` format is used.
* **log.colorfatal**, comma separated value of attribute names -
  bold, underline, blinkslow, blinkrapid, crossedout, red, green,
  yellow, blue, magenta, cyan, white, hired, higreen, hiyellow, hiblue,
  himagenta, hicyan, hiwhite. Attribute-settings available for all log levels.

**Ignore** ignore level can be used to ignore all log messages. Note that
only log-level can be specified as `ignore`, no corresponding API
is supported.

**log.flags**

```go
const (
    // Bits or'ed together to control what's printed.
    // There is no control over the order they appear (the order listed
    // here) or the format they present (as described in the comments).
    // The prefix is followed by a colon only when Llongfile or Lshortfile
    // is specified.
    // For example, flags Ldate | Ltime (or LstdFlags) produce,
    //  2009/01/23 01:23:23 message
    // while flags Ldate | Ltime | Lmicroseconds | Llongfile produce,
    //  2009/01/23 01:23:23.123123 /a/b/c/d.go:23: message
    Ldate         = 1 << iota     // the date in the local time zone: 2009/01/23
    Ltime                         // the time in the local time zone: 01:23:23
    Lmicroseconds                 // microsecond resolution: 01:23:23.123123.  assumes Ltime.
    Llongfile                     // full file name and line number: /a/b/c/d.go:23
    Lshortfile                    // final file name element and line number: d.go:23. overrides Llongfile
    LUTC                          // if Ldate or Ltime is set, use UTC rather than the local time zone
    LstdFlags     = Ldate | Ltime // initial values for the standard logger
)
```

Default **log.level** is `Info`.

Panic and recovery
------------------

* API `SetLogger()`
  * If `log.file` is not string.
  * If creating or opening `log.file` fails.
  * If `log.level` is not an allowed log string.
  * If `log.prefix` is neither string, nor bool.
* API `SetLogLevel()`
  * If `log.level` is not an allowed log string.
* API `SetLogprefix()`
  * If `log.prefix` is neither string, nor bool.

Typically all the above panic cases needs to be fixed during development, and
should never occur during production. If panics become unavoidable please use
[panic/recover](https://blog.golang.org/defer-panic-and-recover).

How to contribute
-----------------

* Pick an issue, or create an new issue. Provide adequate documentation for
  the issue.
* Assign the issue or get it assigned.
* Work on the code, once finished, raise a pull request.
* Golog is written in [golang](https://golang.org/), hence expected to
  follow the global guidelines for writing go programs.
* As of now, branch `master` is the development branch.
