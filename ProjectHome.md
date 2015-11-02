**_THIS PROJECT IS NO LONGER BEING MAINTAINED._**

To make changes, please fork the repo at GitHub and share them with the community: https://github.com/westonruter/json-xml-rpc


---


XML-RPC and JSON-RPC have enabled Ajax developers to cleanly send and receive data from the server. By providing an API in either RPC protocol, the server and client application logic are kept separate and multiple clients may be written using the same server API. The similarity between XML-RPC and JSON-RPC motivates the development of client and server libraries which abstract-away the differences between the two protocols. The client libraries available here allow requests to be made in either XML-RPC or JSON-RPC to the same service, and the server implementations available provide responses according to the type of RPC request provided. The desired request and response formats are determined by setting the `Content-Type` and `Accept` headers respectively to `application/json` and `text/xml` accordingly.

The implementations support every JSON date format currently used, including:
  1. ISO 8601
  1. `@ticks@` string format ([about](http://www.nikhilk.net/DateSyntaxForJSON2.aspx))
  1. ASP.NET AJAX `\/Date(ticks)\/` string format ([about](http://msdn2.microsoft.com/en-us/library/bb299886.aspx))
  1. JSON-RPC 1.0 class hinting ([about](http://json-rpc.org/wiki/specification#a3.JSONClasshinting))
These date formats are automatically converted into native date objects (such as `Date` for JavaScript and `DateTime` for PHP), and you may specify the preferred format for date serialization.

The client allows for asynchronous as well as synchronous calls. The server library allows developers to turn any function into a RPC method since it does not require special parameters to be passed or special values to be returned; all native data types are handled automatically.

The client and server implementations allow for cross-site requests in JSON-RPC by means of the "[JSON-response-callback](http://tech.groups.yahoo.com/group/json-rpc/message/584)" extension. The "[JSON-omit-response-wrapper](http://tech.groups.yahoo.com/group/json-rpc/message/585)" server extension is also implemented to allow for the Procedure Return object wrapper to be omitted so that the JSON result data alone is returned (this allows the server to be used in conjunction with [Ext](http://extjs.com/)'s [AsyncTreeNode](http://extjs.com/deploy/ext/docs/output/Ext.tree.AsyncTreeNode.html), for example).

These libraries implement the 7 August 2006 working draft of JSON-RPC 1.1, which is mostly backwards compatible with JSON-RPC 1.0, including class hinting. They also implement XML-RPC with the discriminated [null value](http://ontosys.com/xml-rpc/extensions.php) extension.

## Donations ##
If you value this software, [please donate](http://weston.ruter.net/donate/) to ensure that it may continue to be maintained and improved. Thank you!