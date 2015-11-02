## PHP Server 0.8.1.1 (2008-01-06) ##
  * Fixed [issue 5](https://code.google.com/p/json-xml-rpc/issues/detail?id=5) regarding `system.describe`.

## PHP Server 0.8.1 (2007-12-10) ##
  * Addressed [issue 4](https://code.google.com/p/json-xml-rpc/issues/detail?id=4): now using [Reflection API](http://www.php.net/manual/en/language.oop5.reflection.php) for introspection if it is available (PHP >= 5.1.0).
  * The `useIncludedFunctions()` method is now disregarded if the Reflection API is available: functions from any included file may be turned into an RPC method.
  * Non-user-defined functions may now be made into methods (e.g. `strtolower()`), if the Reflection API is available.

## JavaScript Client 0.8.0.2 (2007-12-06) ##
Fixed [issue 3](https://code.google.com/p/json-xml-rpc/issues/detail?id=3).

## PHP Server 0.8.0.2 (2007-11-07) ##
Fixed an issue when running under PHP 5.1.2 where a strict error is raised when statically calling a static method (apparently a PHP 5.1.x bug).

## PHP Server 0.8.0.1 (2007-11-02) ##
  * Fixed an error where if the last item in the parameter list had an array provided as the default, the parser would fail.
  * Fixed an issue where if a class was specified for a parameter, the default value would not be parsed.
With these two fixes, the following function may now be added as a method and work as expected:
```
function tryDefaultParameters(array $list = array(1,2,3)){
    return func_get_args();
}
$service->addMethod("tryDefaultParameters");
```
If the preceding method is called without supplying any parameters, then an array (1,2,3) is returned; if a number or a boolean or any other non-array value is provided, the server will raise an error.

## JavaScript Client 0.8.0.1 (2007-09-28) ##
  * Now when making asynchronous call with XMLHttpRequest, the procedure return ID is automatically supplied if not provided. In PHP4, when an error is raised, the request ID is for some reason lost. This helps compensate for that issue.
  * Fixed [issue 1](https://code.google.com/p/json-xml-rpc/issues/detail?id=1) where boolean values passed over XML-RPC always evaluated to true.

## 2007-08-15 ##
_Initial releases of both the JavaScript client (0.8) and the PHP server (0.8)._