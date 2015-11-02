# PHP Server #

_This is a draft._

This server implementation is implemented as the class **RPCServer**; it is available in both PHP 4 and PHP 5. The class is implemented as a singleton. Since PHP 4 does not support singletons, authors using the PHP 4 version should restrict themselves to using the RPCServer class's `getInstance` method instead of constructing a new instance.

The key feature of the PHP implementation is that it parses the server PHP source file(s) for the parameter names and default values in the function prototypes for the public methods; this enables parameters to be passed as an associative array instead of a positional array, and furthermore the default function parameter values defined in the PHP functions may be employed instead of always supplying the 'null' default value as specified by JSON-RPC 1.1 WD (which I believe is deficient in this point).

## Usage ##
```
if(!class_exists('DateTime'))
    require_once('DateTime.class.php');
require_once('RPCServer.class.php');
$server = RPCServer::getInstance(); //note that the RPCServer class is a singleton
function getTemp($zipCode){
    //...
    return $row['temperature'];
}
$server->addMethod("getTemp");
$server->processRequest(); //required in PHP4, otherwise called automatically by the destructor in PHP 5
```
## Class Methods ##
  * preserveDefaultParameters(defaultParametersPreserved)
  * convertISO8601Strings(iso8601StringsConverted)
  * useIncludedFunctions(isUsingIncludedFunctions)
  * setDefaultResponseType(type)
  * setJSONDateFormat(formatName) _(sets cookie)_
  * setDBResultIndexType(indexType) _(sets cookie)_
  * setDebugMode(state)
  * addMethod(privateProcName, publicMethodName)
  * processRequest()

## RPC Methods ##
  * system.listMethods
  * system.describe _(as defined by JSON-RPC 1.1 WD)_
  * system.setJSONDateFormat _(envokes RPCServer::setJSONDateFormat)_
  * system.setDBResultIndexType _(envokes RPCServer::setDBResultIndexType)_