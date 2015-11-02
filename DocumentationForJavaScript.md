# JavaScript RPC Client #
This RPC client implemented in JavaScript enables synchronously and asynchronously calls to XML-RPC and JSON-RPC servers. Asynchronous calls may also be made to supporting JSON-RPC servers (such as the associated PHP implementation) that are hosted cross-site domains without violating the same-origin policy; requests made in such a way should avoid passing parameters containing large data values since they must be passed as the query string in an HTTP GET request. This client implements the 7 August 2006 working draft of JSON-RPC 1.1, but also works with JSON-RPC 1.0 servers as long as they do not fail because of the passed "version" property; furthermore, this client supports JSON-RPC 1.0 class hinting for Date objects and is also able to decode and encode date strings as ISO8601, delimited timestamps (`@1234567@`), and ASP.NET JSON dates (`\/Date(1234567)\/`).
Client Instantiation and Configuration

The JavaScript RPC client is implemented as a service proxy class, inspired by the implementation in jsolait. The class may be instantiated as follows:
```
var service = new rpc.ServiceProxy("/app/service", {/*options*/});
```
Once instantiated, all of the indicated service's methods are accessible as methods of the ServiceProxy object itself. For example, using the "service" object above, if the service's methods are "greetTheWorld" and "math.add" you may execute them by calling the functions "service.greetTheWorld" and "service.math.add" respectively, passing parameters as described further below. The possible configuration options for instantiating a ServiceProxy are listed below, each with its type and default value:

| _Name and Type_ | _Default_ | _Comment_ |
|:----------------|:----------|:----------|
| `Boolean` asynchronous | `true`    | Synchronous calls are not possible to cross-site servers. This option may be changed later by calling: `rpc.setAsynchronous(service, bIsAsync)` |
| `Boolean` sanitize | `true`    | Forces the response data to be validated as legal JSON. This option is relevant to JSON-RPC only, and must be set to 'false' when making calls to a cross-site server. |
| `String` user   | `null`    | Username for HTTP authentication. |
| `String` password | `null`    | Password for HTTP authentication. |
| `String` callbackParamName | `"JSON-response-callback"` | This option is only relevant to cross-site JSON-RPC calls. The value is the name of the HTTP GET parameter which the server will use to generate the json-in-script response.|
|`String` protocol | `"JSON-RPC"` | May also be "XML-RPC".|
|`String` dateEncoding | `"ISO8601"` | Dates are by default encoded as [ISO 8601](http://www.w3.org/TR/NOTE-datetime) strings. If the server expects dates to be encoded differently, you may also supply the values "[classHinting](http://json-rpc.org/wiki/specification#a3.JSONClasshinting)", "[@timestamp@](http://www.nikhilk.net/DateSyntaxForJSON2.aspx)", and "[ASP.NET](http://msdn2.microsoft.com/en-us/library/bb299886.aspx)". |
| `Boolean` decodeISO8601 | `true`    | Determines whether ISO 8601 date strings are automatically constructed into Date objects. UTC is assumed. |
| `Array` methods |           | When this option is not supplied, the service proxy performs introspection via system.listMethods. If the service resides on a cross-site server, this option containing the method list must be provided since synchronous calls cannot be made in this case.|

The following is an example of instantiating the ServiceProxy class for a cross-site service since doing so requires the most configuration. Cross-site services may only be accessed asynchronously, their response data may not be sanitized, and introspection is not possible so you must provide the methods yourself. In order to obtain the method response, the JSON-RPC server must be provided the name of a callback function which will be generated in the JavaScript (json-in-script) response. The HTTP GET parameter for passing the callback function is currently non-standardized and so varies from server to server. A service proxy must be created with the option 'callbackParamName' in order to specify the callback function name parameter; the default is 'JSON-response-callback', as used by associatedserver implementation. Note the data here returned by Google is not a JSON-RPC Procedure Return object so it is automatically wrapped within one; in this case, of course, no errors may be returned by the service:
```
var gcalService = new rpc.ServiceProxy("http://www.google.com/calendar/feeds/myemail%40gmail.com/public", {
                        asynchronous: true,  //default value, but if otherwise error raised
                        protocol: "JSON-RPC",//default value, but if otherwise error raised
                        sanitize: false,     //explicit false required, otherwise error raised
                        methods: ['full']    //explicit list required, otherwise error raised
                        callbackParamName: 'callback'
}); //(note how this specific service is used below)
```
## Making Synchronous Calls ##

Service introspection is always done with a synchronous call so that the code after the ServiceProxy instantiation may call the service's methods right away. Synchronous calls are not possible for cross-site services. A synchronous service may be created as follows:
```
var service = new rpc.ServiceProxy("/app/service", {asynchronous:false});
```
Or synchronous mode may be enabled by executing:
```
rpc.setAsynchronous(service, bIsAsync)
```
Synchronous calls are the simplest and easiest to use. The parameters supplied to the service proxy's method are simply the parameters which the service's method accepts. Furthermore, the result of the service's method is returned from the service proxy's method:
```
try {
   var sum = service.math.add(1,1);
   alert("The sum is: " + sum);
}
catch(e){
   alert("Unable to add numbers because: " + e);
}
```
However, while synchronous calls are simpler and easier than asynchronous calls, they have the disadvantage of freezing the page until the server responds. This may frustrate the user if the server takes longer than a couple hundred milliseconds to respond since the page is non-responsive during that time. In order to avoid freezing up the browser and to create true Ajax interfaces, asynchronous calls must be used.

## Making Asynchronous Calls ##
When creating a new service proxy, the default configuration is for methods to be called asynchronously. Making asynchronous calls is more involved since callback handlers must be provided if the state of the method's result is desired. When in asynchronous mode, there are two options for how a service proxy's method may be called. The first (and most readible) option is to pass a single object with the following properties (all of which are optional), and the second option is to pass four separate parameters in the following order:

| _Argument_ | _Type_ | _Comment_ |
|:-----------|:-------|:----------|
| **params** | `Array` or `Object` | This may either be an array whose members are positionally correspond to the parameters defined in the parameter list of the service's corresponding method, or if using JSON-RPC 1.1 you may pass an object whose properties' names correspond to the parameter names in the parameter list of the service's corresponding method. See examples below. |
| **onSuccess** | `function(result)` | When the server returns without an error or fault, this callback function is executed. If an uncaught error is raised within this callback function, then the following "onException" callback is executed being passed the raised Error object. The parameter passed to this function is the exact return value of the service's corresponding method. |
| **onException** | `function(errorObj)` | When the server returns with an error or fault, or when the onSuccess or onComplete callbacks raise exceptions and fail to catch them, this callback function is executed. The parameter passed to this function is an Error object either from the server or raised on the client. |
| **onComplete** | `function(responseObj)` | This callback function is called after the "onSuccess" and "onException" callbacks, and is analagous to the final step in the try/catch/final exception handing chain.As with the "onSuccess" callback, if an error is raised within this callback function, then the preceeding "onException" callback is executed being passed the raised Error object. |

If the service returns with an error or the "onSuccess" and "onComplete" callbacks raise an error, and if the "onException" callback is not provided, then the error is thrown in the browser. Of course theparameter names and execution logic for these callback functions is modeled after Prototype.

Here is an example of making an asynchronous call:
```
service.math.add({params:[1,1],
                  onSuccess:function(sum){
                     alert("The sum is: " + sum);
                  },
                  onException:function(errorObj){
                     alert("Unable to add numbers because: " + errorObj);
                  },
                  onComplete:function(responseObj){
                     //any 'final' logic
                  }
});
```
And as referenced above, here is how to access data from the Google Calendar service:
```
gcalService.full({
     params:{
         alt:'json-in-script' //required for this to work
         'start-min':new Date() //automatically converted to ISO8601
         /*other Google Calendar parameters*/
     },
     onSuccess:function(json){
         json.feed.entry.each(function(entry){
             //do something
         });
     }
});
```