## 테스트 실행하기

CIDER 안에서 `clojure.test` 테스트를 빠르게 실행할 수 있습니다. 소스 버퍼나 REPL 버퍼에서
<kbd>C-c C-t n</kbd> 이나 <kbd>C-c C-t C-n</kbd> 키를 누르면 현재 네임스페이스에 관련된
테스트를 실행합니다. CIDER 테스트가 포함되어 있는 네임스페이스를 스스로 찾을 수 있습니다.
<kbd>C-c C-t l</kbd> 이나 <kbd>C-c C-t C-l</kbd> 키를 누르면 프로젝트에 로드된 모든 테스트를
실행 할 수 있고  <kbd>C-c C-t p</kbd> 이나 <kbd>C-c C-t C-p</kbd> 키로 프로젝트에 포함된
모든 테스트를 실행할 수 있습니다. (프로젝트에 **all** 네임 스페이스) 또 <kbd>C-c C-t t</kbd> 이나
 <kbd>C-c C-t C-t</kbd> 키로 특정 테스트를 따로 실행 할 수도 있습니다.

All test commands are available in REPL buffers as well. There you can also use
<kbd>,</kbd> to invoke some of the testing commands.

In the buffer displaying the test execution results (`*cider-test-results*`)
you'll have a bit of additional functionality at your disposal.

Keyboard shortcut               | Description
--------------------------------|-------------------------------
<kbd>g</kbd>                    | Run test at point.
<kbd>n</kbd>                    | Run tests for current namespace.
<kbd>l</kbd>                    | Run tests for all loaded namespaces.
<kbd>p</kbd>                    | Run tests for all project namespaces. This loads the additional namespaces.
<kbd>f</kbd>                    | Re-run test failures/errors.
<kbd>M-p</kbd>                  | Move point to previous test.
<kbd>M-n</kbd>                  | Move point to next test.
<kbd>t</kbd> or <kbd>M-.</kbd>  | Jump to test definition.
<kbd>d</kbd>                    | Display diff of actual vs expected.
<kbd>e</kbd>                    | Display test error cause and stacktrace info.

Certain aspects of the test execution behavior are configurable:

* If your tests are not following the `some.ns-test` naming convention you can
customize the variable `cider-test-infer-test-ns`. It should be bound to a
function that takes the current namespace and returns the matching test
namespace (which may be the same as the current namespace).

* If you want to view the test report regardless of whether the tests have
passed or failed:

```el
(setq cider-test-show-report-on-success t)
```

### Running tests automatically (test-driven development)

CIDER provides a minor-mode that automatically runs all tests for a namespace
whenever you load a file (with <kbd>C-c C-k</kbd>). You can toggle it
manually with <kbd>M-x</kbd> `cider-auto-test-mode`, or you can use:

```el
(cider-auto-test-mode 1)
```

This is completely equivalent to manually typing <kbd>C-c C-t C-n</kbd> every
time you load a Clojure buffer. Also, as described above before, CIDER is smart
enough to figure out the namespace containing the tests.

### Using cider-test with alternative test libraries

The `clojure.test` machinery is designed to be pluggable. Any test library
can implement it if so desired, and therefore leverage `cider-test`. For
instance, [test.check](https://github.com/clojure/test.check/) does this, and
`cider-test` handles `defspec` just like `deftest`.

As a test framework author, supporting the built-in `clojure.test` machinery
(and hence `cider-test`) is pretty straightforward:

1. Assoc each test fn as `:test` metadata on some var. These are what get run.
2. Implement the `clojure.test/report` multimethod to capture the test results.

The `test.check` library is a good example here. It was also designed completely
independently of `clojure.test`. It just adds compatibility in this
[namespace](https://github.com/clojure/test.check/blob/24f74b83f1c7a032f98efdcc1db9d74b3a6a794d/src/main/clojure/clojure/test/check/clojure_test.cljc).
