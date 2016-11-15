이 글은 문제 해결에 도움이 될 만 한 팁을 몇 가지 소개합니다. 문제가 발생한 경우 참조 하세요.]

`*Messages*` 버퍼에 있는 로그를 보는 것 대신 에러 내용을 볼 수 있도록 이맥스를 설정하는 것이 좋습니다.
<kbd>M-x</kbd>키를 누르고 `toggle-debug-on-error`하면 활성화 할 수 있습니다.

## CIDER 명령어 디버깅하기

이맥스는 [Emacs Lisp debugger](http://www.gnu.org/software/emacs/manual/html_node/elisp/Edebug.html)라는
강력한 기능이 있습니다. 이 기능으로 문제를 파악하는 것이 가장 좋은 방법입니다.

여기에 ([great crash course](https://www.youtube.com/watch?v=odkYXXYOxpo)) 디버거를
서용하는 방법이 잘 설명되어 있습니다.

어떤 명령어를 디버깅하려면 다음과 같이 합니다:

* 디버깅할 명령어를 알아냅니다.(예 <kbd>C-h k</kbd>로 키바인딩과 연결된 명령어를 알 수 있습니다.)
* 명령어의 소스 코드를 찾습니다.(예  <kbd>M-x</kbd> `find-function`를 입력하고 <kbd>RET</kbd>
  를 누르고 `function-name`을 입력합니다.)
* 함수 안에 커서를 놓고 <kbd>C-u C-M-x</kbd>키를 누릅니다.
* 커맨드를 다시 실행합니다.

이렇게 하면 디버거 모드로 들어가게 되고 문제를 찾을 때까지 진행하면 됩니다.

## REPL이 실행되지 않음

Make sure that your CIDER version matches your `cider-nrepl` version. Check
the contents of the `*Messages*` buffer for CIDER-related errors. You should
also check the nREPL messages passed between CIDER and nREPL in
`*nrepl-messages*`. If you don't see anything useful there it's time to bring
out the big guns.

### Debugging the REPL init

To debug CIDER's REPL initialization it's a good idea to hook into one of its
entry points. Add a breakpoint to `cider-make-repl` (<kbd>C-u C-M-x</kbd>, while
in its body). Next time you start CIDER you'll be dropped in the debugger and
you can step forward until you find the problem.

## Missing `*nrepl-messages*` buffer

nREPL message logging is not enabled by default.  Set `nrepl-log-messages` to
`t` to activate it. Alternatively you can use <kbd>M-x</kbd> `nrepl-toggle-message-logging`
to enable/disable logging temporary within your current Emacs session.

## `cider-debug` complains that it “failed to instrument ...”

In the REPL buffer, issue the following.

    your.namespace> (ns cider.nrepl.middleware.util.instrument)
    cider.nrepl.middleware.util.instrument> (def verbose-debug true)

This will cause CIDER to print extensive information to the REPL buffer when you
try to debug an expression (e.g., with <kbd>C-u
C-M-x</kbd>). [File an issue](https://github.com/clojure-emacs/cider-repl/issues/new)
and copy this information.

## Debugging freezes & lock-ups

Sometimes a CIDER command might hang for a while (e.g. due to a bug or a
configuration issue). Such problems are super annoying, but are relatively easy
to debug. Here are a few steps you can take in such situations:

* Do <kbd>M-x</kbd> `toggle-debug-on-quit`
* Reproduce the problem
* Hit <kbd>C-g</kbd> around 10 seconds into the hang

This will bring up a backtrace with the entire function stack, including
function arguments. So you should be able to figure out what's going on (or at
least what's being required).

## Warning saying you have to use nREPL 0.2.12+

CIDER currently requires at least nREPL 0.2.12 to work properly (there were some
nasty bugs in older version and no support for tracking where some var was
defined in the source code). Leiningen users can add this to their
`profiles.clj` to force the proper dependency:

```clojure
{:repl {:dependencies [[org.clojure/tools.nrepl "0.2.12"]]}}
```

Make sure you add the newer nREPL dependency to the `:dependencies` key instead
of `:plugins` (where `cider-nrepl` Lein plugin resides). That's a pretty common
mistake.

Generally you're advised to use the newest nREPL with CIDER, as bugs get fixed
in pretty much every release.

Note, that running `cider-jack-in` from outside the scope of a project will
result in the **older (0.2.6) nREPL dependency being used** (at least on Leiningen
2.5.1). This is likely a Leiningen bug.

## Missing clojure-... function after CIDER update

Most likely you've updated CIDER, without updating `clojure-mode` as well.

CIDER depends on `clojure-mode` and you should always update them together, as
the latest CIDER version might depend on functionality present only in the latest
`clojure-mode` version.

## I upgraded CIDER using `package.el` and it broke

The built-in package manager isn't perfect and sometimes it messes up.  If you
just updated and encountered an error you should try the following before
opening an issue: Go into the `.emacs.d/elpa` directory, delete any folders
related to CIDER, restart Emacs and then re-install the missing packages.  Note
that the order here matters.

## I upgraded CIDER using `package.el` and nothing changed

Emacs doesn't load the new files, it only installs them on disk.  To see the
effect of changes you have to restart Emacs.

## `cider-nrepl` 버전 관련된 문제

이 경우 보통 REPL이 실행 될 때 REPL 버퍼에 다음과 같은 경고가 표시 됩니다:

> **WARNING:** CIDER's version (0.12.0) does not match cider-nrepl's version (...). Things will break!

`...` 부분에 `0.10.0`나 `not installed` 또는 `nil` 같은 것이 적혀있고 이것이 실제 버전입니다.
여기에 뭐가 적혀있는지에 따라 해결 방법이 나뉩니다.

### `cider-connect`으로 REPL을 실행하고 `X.X.X` 처럼 생긴 버전이 적혀 있는 경우

프로젝트에 cider-nrepl 미들웨어 버전이 잘 못되어 있습니다.
[instructions](http://cider.readthedocs.org/en/latest/installation/#ciders-nrepl-middleware)에
설치 부분을 참고 하세요.

### `cider-connect`으로 REPL을 실행하고 `not installed` 또는 `nil`이 적혀 있는 경우

`cider-connect`을 사용하려면 프로젝트에 cider-nrepl 미들웨어를 추가해줘야 합니다.
[instructions](http://cider.readthedocs.org/en/latest/installation/#ciders-nrepl-middleware)에
설치 부분을 참고 하세요.

### `cider-jack-in`으로 REPL을 실행하고 `not installed` 또는 `nil`이 적혀 있는 경우

- `C-h v cider-inject-dependencies-at-jack-in`를 실행해서 값이 nil이 아닌지 확인합니다.
- 프로젝트가 Clojure `1.7.0` 이상 사용하도록 합니다.
- leiningen을 사용한다면 `lein --version`으로 `2.6.1` 버전 이상인지 확인합니다.
- boot를 사용하고 `cider-boot-parameters` 값을 바꿨다면 그것 때문에 문제가 발생할 수 있습니다.

위에 것을 모두 확인했는데도 문제가 해결되지 않는다면 cider-nrepl 미들웨어 버전을 수동으로 설정해줘야 합니다.
[instructions](http://cider.readthedocs.org/en/latest/installation/#ciders-nrepl-middleware)에
설치 부분을 참고 하세요.

###  `cider-jack-in`으로 REPL을 실행하고 `X.X.X` 처럼 생긴 버전이 적혀 있는 경우

이 경우는 프로젝트에 cider-nrepl 미들웨어를 추가를 했지만 `cider-jack-in`이 이미 버전을 설정하고
있기 때문에 그렇습니다. 그래서 다음과 같은 파일에 미들웨어 추가된 것이 있다면 제거하세요. `cider-nrepl`
`tools.nrepl`: `project.clj`, `build.boot`, `~/.lein/profiles.clj`, `~/.boot/profile.boot`.
