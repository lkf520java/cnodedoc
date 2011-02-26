## HTTP

To use the HTTP server and client one must `require('http')`.

���Ҫʹ��HTTP��server�Լ�clientģ�������ʹ��require('http')����httpģ�� 

The HTTP interfaces in Node are designed to support many features
of the protocol which have been traditionally difficult to use.
In particular, large, possibly chunk-encoded, messages. The interface is
careful to never buffer entire requests or responses--the
user is able to stream data.

NODE�е�HTTP�ӿڱ���Ƴ�Ϊ֧��HTTPЭ��ĺܶ����ԣ���Щ����ͨ���������ƿأ��ر��� large,possible chunk-encoded(�����),messages�� 
����ӿ����ⲻ������������(request)������Ӧ(responses)ʹ�û�����ʹ��������ʽ�������ݡ� 

HTTP message headers are represented by an object like this:

�������ö������ʽ��ʾ��HTTP��Ϣͷ 

    { 'content-length': '123',
      'content-type': 'text/plain',
      'connection': 'keep-alive',
      'accept': '*/*' }

Keys are lowercased. Values are not modified.

����key����Сд����ֵ���ܱ��޸� 

In order to support the full spectrum of possible HTTP applications, Node's
HTTP API is very low-level. It deals with stream handling and message
parsing only. It parses a message into headers and body but it does not
parse the actual headers or the body.

Ϊ��֧�־����ܶ��HTTPӦ�ã�NODE��HTTP API�ǳ��ײ㡣 ��ֻ������(stream)��صĲ����Լ���Ϣ������
API����Ϣ������Ϊ��Ϣͷ����Ϣ�壬����������ʵ�ʵ���Ϣͷ����Ϣ��ľ������ݡ�

## http.Server

This is an `EventEmitter` with the following events:

��ģ��ᴥ�������¼� 

### Event: 'request'

`function (request, response) { }`

 `request` is an instance of `http.ServerRequest` and `response` is
 an instance of `http.ServerResponse`
 
`request`��`http.ServerRequest`��һ��ʵ������`response`��`http.ServerResponse`��һ��ʵ��
 
### Event: 'connection'

`function (stream) { }`

 When a new TCP stream is established. `stream` is an object of type
 `net.Stream`. Usually users will not want to access this event. The
 `stream` can also be accessed at `request.connection`.
 
��һ���µ�TCP stream�����󷢳�����Ϣ��stream��һ��net.Stream�Ķ���
ͨ���û��������/ʹ������¼�������streamҲ������request.connection�з��ʵ�. 

### Event: 'close'

`function (errno) { }`

 Emitted when the server closes.
 
���������رյ�ʱ�򴥷����¼��� 

### Event: 'request'

`function (request, response) {}`

Emitted each time there is request. Note that there may be multiple requests
per connection (in the case of keep-alive connections).

ÿ����������ʱ����ᱻ���������ס��ÿ�����ӿ��ܻ��ж������(��keep-alive���������) 

### Event: 'checkContinue'

`function (request, response) {}`

Emitted each time a request with an http Expect: 100-continue is received.
If this event isn't listened for, the server will automatically respond
with a 100 Continue as appropriate.

ÿ�������������յ�100-continueʱ�ɷ���������¼�δ�����������������Զ�
����100-continue���ͻ��ˡ�

Handling this event involves calling `response.writeContinue` if the client
should continue to send the request body, or generating an appropriate HTTP
response (e.g., 400 Bad Request) if the client should not continue to send the
request body.

���¼��Ĵ����漰�������������ͻ���Ӧ���������������壬��ô��Ҫ����response.writeContinue��
������ͻ��˲�Ӧ�ü��������������ģ���ôӦ�ò���һ���ʵ���HTTP��Ӧ����400�������󣩡�

Note that when this event is emitted and handled, the `request` event will
not be emitted.

ע��������¼����ɷ�������Ļ�����ô�������ɷ�`request`�¼���

### Event: 'upgrade'

`function (request, socket, head)`

Emitted each time a client requests a http upgrade. If this event isn't
listened for, then clients requesting an upgrade will have their connections
closed.

ÿ��һ���ͻ�������һ��http upgradeʱ�򷢳�����Ϣ���������¼�û�м�����
��ô����upgrade�Ŀͻ��˶�Ӧ�����ӽ����رա� 

* `request` is the arguments for the http request, as it is in the request event.
������request������һ��http���󣬺�'request'�¼��Ĳ���������ͬ��

* `socket` is the network socket between the server and client.
socket���ڷ�������ͻ���֮�������õ�����socket 

* `head` is an instance of Buffer, the first packet of the upgraded stream, this may be empty.
head��Buffer��һ��ʵ��,��upgraded stream(������stream....Ӧ������http upgrade)�������ĵ�һ�����������������Ϊ�ա� 

After this event is emitted, the request's socket will not have a `data`
event listener, meaning you will need to bind to it in order to handle data
sent to the server on that socket.

�����¼��������󣬸�������ʹ�õ�socket��������һ�������¼��ļ�����,����ζ���������Ҫ
����ͨ�����SOCKET���͵��������˵����ݵĻ�����Ҫ�Լ��������¼������� 

### Event: 'clientError'

`function (exception) {}`

If a client connection emits an 'error' event - it will forwarded here.

���ͻ������ӳ��ִ���ʱ�ᴥ��'error'�¼�

### http.createServer(requestListener)

Returns a new web server object.

����һ���µ�web server���� 

The `requestListener` is a function which is automatically
added to the `'request'` event.

requestListener���������Զ���ӵ�`'request'`�¼���

### server.listen(port, [hostname], [callback])

Begin accepting connections on the specified port and hostname.  If the
hostname is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

��ָ���˿ں��������Ͻ������ӡ����hostnameû��д,�����������ֱ���ڴ˻�����
����IPV4��ַ�Ͻ�������(INADDR_ANY). 

To listen to a unix socket, supply a filename instead of port and hostname.

���Ҫ��UNIX SOCKET�ϼ����Ļ�������Ҫ�ṩһ���ļ������滻�˿ں�������. 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound to the port.

���������һ���첽�ķ�������Ϊ���һ�������Ļص��������ڷ������Ѿ��ڴ˶˿��ϰ󶨺ú󱻵���. 

### server.listen(path, [callback])

Start a UNIX socket server listening for connections on the given `path`.

����һ��UNIX SOCKET����������ָ��·�������� 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

���������һ���첽�ķ�������Ϊ���һ�������Ļص��������ڷ������Ѿ��ڴ˶˿��ϰ󶨺ú󱻵��á� 

### server.close()

ʹ�˷�����ֹͣ�����κ������ӡ� 

## http.ServerRequest

This object is created internally by a HTTP server -- not by
the user -- and passed as the first argument to a `'request'` listener.

�������ͨ����HTTP SERVER���������û��ֶ����������һ���Ϊ���ݸ� 'request'�¼���������һ������

This is an `EventEmitter` with the following events:

�˶���Ŀ��Դ��������¼��� 

### Event: 'data'

`function (chunk) { }`

Emitted when a piece of the message body is received.

�����յ���Ϣ���е�һ����ʱ��ᷢ��data�¼��� 

Example: A chunk of the body is given as the single
argument. The transfer-encoding has been decoded.  The
body chunk is a string.  The body encoding is set with
`request.setBodyEncoding()`.

����:������Ϣ������ݿ齫��ΪΨһ�Ĳ������ݸ��ص����������ʱ�������Ѿ����մ������
�����˽��루�����ַ������룩����Ϣ�屾����һ���ַ���������ʹ��request.setBodyEncoding()�����趨��Ϣ��ı��롣 

### Event: 'end'

`function () { }`

Emitted exactly once for each message. No arguments.  After
emitted no other events will be emitted on the request.

ÿ����ȫ��������Ϣ�󶼻ᴥ��һ�Ρ�û�в�����������¼������󣬽������ٴ��������¼��� 

### request.method

The request method as a string. Read only. Example:
`'GET'`, `'DELETE'`.

request.method��һ��ֻ���ַ���������'GET','DELETE' 

### request.url

Request URL string. This contains only the URL that is
present in the actual HTTP request. If the request is:

�����������URL�ַ���.��������ʵ�ʵ�HTTP�����е�URL��ַ�������������� 

    GET /status?name=ryan HTTP/1.1\r\n
    Accept: text/plain\r\n
    \r\n

Then `request.url` will be:

��request.url Ӧ���� 

    '/status?name=ryan'

If you would like to parse the URL into its parts, you can use
`require('url').parse(request.url)`.  Example:

�������Ҫ�������URL�еĸ������֣���Ӧ��ʹ�� require('url').parse(request.url). 

    node> require('url').parse('/status?name=ryan')
    { href: '/status?name=ryan',
      search: '?name=ryan',
      query: 'name=ryan',
      pathname: '/status' }

If you would like to extract the params from the query string,
you can use the `require('querystring').parse` function, or pass
`true` as the second argument to `require('url').parse`.  Example:

�������Ӳ�ѯ�ַ����������Щ�����������ʹ��require('querystring').parse����,
���ߴ�һ��true��Ϊ�ڶ���������require('url').parse������ 

    node> require('url').parse('/status?name=ryan', true)
    { href: '/status?name=ryan',
      search: '?name=ryan',
      query: { name: 'ryan' },
      pathname: '/status' }



### request.headers

Read only.

ֻ����

### request.trailers

Read only; HTTP trailers (if present). Only populated after the 'end' event.

ֻ����HTTPβ����������ڵĻ�����ֻ����end�¼��ɷ����ֵ�Żᱻ��䡣

### request.httpVersion

The HTTP protocol version as a string. Read only. Examples:
`'1.1'`, `'1.0'`.
Also `request.httpVersionMajor` is the first integer and
`request.httpVersionMinor` is the second.

���ַ�����ʽ��ʾHTTPЭ��汾������'1.1','1.0'��request.httpVersionMajor��Ӧ�汾�ŵĵ�һ�����֣�
request.httpVersionMinor���Ӧ�ڶ������֡� 

### request.setEncoding(encoding=null)

Set the encoding for the request body. Either `'utf8'` or `'binary'`. Defaults
to `null`, which means that the `'data'` event will emit a `Buffer` object..

���ô�����İ�����ּ�����,'utf8'����'binary'��ȱʡֵ��null�����ʾ'data'�¼��Ĳ���������һ��Buffer���� 

### request.pause()

Pauses request from emitting events.  Useful to throttle back an upload.

��ͣ��request�����¼�.���ڿ����ϴ��ǳ����á� 

### request.resume()

Resumes a paused request.

�ָ�һ����ͣ��request�� 

### request.connection

The `net.Stream` object associated with the connection.

request.connection��һ������ǰ���ӵ�net.Stream���� 

With HTTPS support, use request.connection.verifyPeer() and
request.connection.getPeerCertificate() to obtain the client's
authentication details.

����HTTPS��ʹ��request.connection.verifyPeer() �� request.connection.getPeerCertificate()����ÿͻ��˵���֤���顣 

## http.ServerResponse

This object is created internally by a HTTP server--not by the user. It is
passed as the second parameter to the `'request'` event. It is a `Writable Stream`.

�������һ����HTTP���������������û��Լ��ֶ�����������Ϊ'request'�¼��ĵڶ�������������һ����д����

### response.writeContinue()

Sends a HTTP/1.1 100 Continue message to the client, indicating that
the request body should be sent. See the the `checkContinue` event on
`Server`.

����HTTP/1.1 100 Continue��Ϣ���ͻ��ˣ�˵���������Ϣ�彫Ҫ�����ͣ��μ�������`Server`�е�`checkContinue`�¼���

### response.writeHead(statusCode, [reasonPhrase], [headers])

Sends a response header to the request. The status code is a 3-digit HTTP
status code, like `404`. The last argument, `headers`, are the response headers.
Optionally one can give a human-readable `reasonPhrase` as the second
argument.

�������������������һ����Ӧ����ͷ�����ε����󷽣���һ������״̬������һ��3λ���������ɵ�HTTP״̬��
����404֮��ġ����һ������headers����Ӧͷ��������.Ҳ����ʹ��һ����������ֱ���˽��reasonPhrase��Ϊ�ڶ��������� 

Example:

    var body = 'hello world';
    response.writeHead(200, {
      'Content-Length': body.length,
      'Content-Type': 'text/plain' });

This method must only be called once on a message and it must
be called before `response.end()` is called.

��һ��������Ϣ�����д˷���ֻ�ܵ���һ�Σ����ұ����ڵ���response.end()֮ǰ���á� 

If you call `response.write()` or `response.end()` before calling this, the
implicit/mutable headers will be calculated and call this function for you.

�������`response.write()` ���� `response.end()`֮�����writeHead����ô��������ʽ��ͷ����Ϣ���ǲ���Ԥ�ڵġ�

### response.statusCode

When using implicit headers (not calling `response.writeHead()` explicitly), this property
controlls the status code that will be send to the client when the headers get
flushed.

���û����ʾָ����Ϣͷ����Ϣ��û����ȷ���ã���������Խ����Ƶ�ͷ��ˢ��ʱ���ظ��ͻ��˵�״̬�롣

Example:

���磺

    response.statusCode = 404;

### response.setHeader(name, value)

Sets a single header value for implicit headers.  If this header already exists
in the to-be-sent headers, it's value will be replaced.  Use an array of strings
here if you need to send multiple headers with the same name.

��ʾ����ͷ����Ϣ����������Ѿ������˶�Ӧ��ͷ����Ϣ����ô��ֵ�����滻��������뷢����ͬ���ֵĶ��ͷ����Ϣ��
����ʹ���ַ����������ʽ����

Example:

���磺

    response.setHeader("Content-Type", "text/html");

or

    response.setHeader("Set-Cookie", ["type=ninja", "language=javascript"]);


### response.getHeader(name)

Reads out a header that's already been queued but not sent to the client.  Note
that the name is case insensitive.  This can only be called before headers get
implicitly flushed.

��ȡ��δ���͸��ͻ������Ѿ����кõĵ�ͷ����Ϣ��ע������������ִ�Сд���÷���ֻ����
ͷ����Ϣû����ʽˢ��ǰ���á�

Example:

    var contentType = response.getHeader('content-type');

### response.removeHeader(name)

Removes a header that's queued for implicit sending.

�Ƴ���ʽ���͵�ͷ����Ϣ��

Example:

    response.removeHeader("Content-Encoding");


### response.write(chunk, encoding='utf8')

If this method is called and `response.writeHead()` has not been called, it will
switch to implicit header mode and flush the implicit headers.

�����`response.writeHead()`û�е��õ�ʱ����øú����������л�ͷ����Ϣ��ģʽ��ˢ��ͷ����Ϣ�� 

This sends a chunk of the response body. This method may
be called multiple times to provide successive parts of the body.

����������Ӧ�����еĲ������ݣ����Ҫ����һ��������Ķ�����֣�����Զ�ε��ô˷����� 

`chunk` can be a string or a buffer. If `chunk` is a string,
the second parameter specifies how to encode it into a byte stream.
By default the `encoding` is `'utf8'`.

����chunk������һ���ַ�������һ��buffer�����chunk��һ���ַ�������ڶ�������ָ����ν�
����ַ���������ֽ�����ȱʡ����£�����Ϊ'utf8'�� 

**Note**: This is the raw HTTP body and has nothing to do with
higher-level multi-part body encodings that may be used.

ע��:����һ��ԭʼ��ʽhttp�����壬�͸߲�Э���еĶ����Ϣ������ʽ({'Transfer-Encoding':'chunked'})�޹ء� 

The first time `response.write()` is called, it will send the buffered
header information and the first body to the client. The second time
`response.write()` is called, Node assumes you're going to be streaming
data, and sends that separately. That is, the response is buffered up to the
first chunk of body.

��һ�ε���response.write()ʱ���˷����Ὣ�Ѿ��������Ϣͷ�͵�һ����Ϣ�巢�͸��ͻ��� 
���ڶ��ε���response.write()��ʱ��node���ٶ�����Ҫ��������ʽ�������ݣ��ֱ���ÿһ�����ݿ鲢�������棩�� 
��������ʵresponse����ֻ�ǻ�����Ϣ��ĵ�һ�����ݿ顣 

### response.addTrailers(headers)

This method adds HTTP trailing headers (a header but at the end of the
message) to the response.

�÷������HTTP Trailers ͷ������Ϣ��β����httpͷ��Ϣ������Ӧ������

Trailers will **only** be emitted if chunked encoding is used for the
response; if it is not (e.g., if the request was HTTP/1.0), they will
be silently discarded.

����Ӧ����ʹ��chunked����ʱ�� Trailers�Żᴥ�����������ǽ��ᱻ��ʽ������

Note that HTTP requires the `Trailer` header to be sent if you intend to
emit trailers, with a list of the header fields in its value. E.g.,

ע����������ɷ�β����Ϣ����Ҫ���`Trailer`��HTTPͷ�Ĳ����б���

    response.writeHead(200, { 'Content-Type': 'text/plain',
                              'Trailer': 'TraceInfo' });
    response.write(fileData);
    response.addTrailers({'Content-MD5': "7895bf4b8828b55ceaf47747b4bca667"});
    response.end();


### response.end([data], [encoding])

This method signals to the server that all of the response headers and body
has been sent; that server should consider this message complete.
The method, `response.end()`, MUST be called on each
response.

�����������߷���������Ӧ�����б���ͷ���������Ѿ��������������ڴ˵��ú���Ϊ������Ϣ�Ѿ�������ϣ�
������������ÿ����Ӧ����һ�Ρ� 

If `data` is specified, it is equivalent to calling `response.write(data, encoding)`
followed by `response.end()`.

���ָ��data�����������൱�ڵ�����response.write(data, encoding)Ȼ����ŵ�����response.end()�� 

## http.request(options, callback)

Node maintains several connections per server to make HTTP requests.
This function allows one to transparently issue requests.

Node����Ϊ������ά��������ӵ�HTTP����ͨ����������������������������

Options:

ѡ�

- `host`: A domain name or IP address of the server to issue the request to.
          ������������߷�������IP��ַ
          
- `port`: Port of remote server.
          Զ�˷������Ķ˿�
          
- `method`: A string specifing the HTTP request method. Possible values:
  `'GET'` (default), `'POST'`, `'PUT'`, and `'DELETE'`.
          ָ��HTTP����ķ����������ͣ���ѡ��ֵ�У�`'GET'` (default), 
          `'POST'`, `'PUT'`, and `'DELETE'`��
  
- `path`: Request path. Should include query string and fragments if any.
   E.G. `'/index.html?page=12'`
          �����ַ�������Ҫ���԰�����ѯ�ַ���Ƭ��
   
- `headers`: An object containing request headers.
          һ����������ͷ�Ķ���

`http.request()` returns an instance of the `http.ClientRequest`
class. The `ClientRequest` instance is a writable stream. If one needs to
upload a file with a POST request, then write to the `ClientRequest` object.

`http.request()`��������`http.ClientRequest`���һ��ʵ����`ClientRequest`����
��һ����д��������������Ҫ��POST���������ϴ�һ���ļ�����Ҫ����д�뵽`ClientRequest`�����С�

Example:

    var options = {
      host: 'www.google.com',
      port: 80,
      path: '/upload',
      method: 'POST'
    };

    var req = http.request(options, function(res) {
      console.log('STATUS: ' + res.statusCode);
      console.log('HEADERS: ' + JSON.stringify(res.headers));
      res.setEncoding('utf8');
      res.on('data', function (chunk) {
        console.log('BODY: ' + chunk);
      });
    });

    // write data to request body
    req.write('data\n');
    req.write('data\n');
    req.end();

Note that in the example `req.end()` was called. With `http.request()` one
must always call `req.end()` to signify that you're done with the request -
even if there is no data being written to the request body.

ע�����������`req.end()`�������ˣ������������Ƿ�������ݣ�ÿһ�ε���`http.request()`
�����Ҫ����һ��`req.end()`��ʾ�Ѿ����������

If any error is encountered during the request (be that with DNS resolution,
TCP level errors, or actual HTTP parse errors) an `'error'` event is emitted
on the returned request object.

�������������г����˴��󣨿�����DNS������TCP�Ĵ��󡢻���HTTP�������󣩣�
��Ϊ`'error'`���¼����ɷ������ص����������

There are a few special headers that should be noted.

������ϢͷӦ��ע�⣺ 

* Sending a 'Connection: keep-alive' will notify Node that the connection to
  the server should be persisted until the next request.
  
  ����'Connection: keep-alive'ͷ����֪ͨNode�������˵����ӽ����ֵ���һ�����ӵ���

* Sending a 'Content-length' header will disable the default chunked encoding.

  ����'Content-length'ͷ������֯Ĭ�ϵ�chunked����

* Sending an 'Expect' header will immediately send the request headers.
  Usually, when sending 'Expect: 100-continue', you should both set a timeout
  and listen for the `continue` event. See RFC2616 Section 8.2.3 for more
  information.
  
  ����'Expect'ͷ����������������ͷ����ͨ���أ�������'Expect: 100-continue'ʱ��
  ����Ҫ����`continue`�¼���ͬʱ���ó�ʱ���μ�RFC2616 8.2.3�½ڻ�ø������Ϣ��

## http.get(options, callback)

Since most requests are GET requests without bodies, Node provides this
convience method. The only difference between this method and `http.request()` is
that it sets the method to GET and calls `req.end()` automatically.

���ںܶ�GET���������󲻻���������壬Node�ṩ������������ڵ��ã�Ψһ�Ĳ�ͬ��
�Ǹ÷�����`http.request()`�ķ�������ΪGET�����Զ���������`req.end()`��

Example:

    var options = {
      host: 'www.google.com',
      port: 80,
      path: '/index.html'
    };

    http.get(options, function(res) {
      console.log("Got response: " + res.statusCode);
    }).on('error', function(e) {
      console.log("Got error: " + e.message);
    });


## http.Agent
## http.getAgent(host, port)

`http.request()` uses a special `Agent` for managing multiple connections to
an HTTP server. Normally `Agent` instances should not be exposed to user
code, however in certain situations it's useful to check the status of the
agent. The `http.getAgent()` function allows you to access the agents.

`http.request()`ʹ��һ���ر��`Agent`����������������ϵĶ�����ӣ�ͨ��`Agent`����
��Ӧ�ñ�¶���û�������ĳЩ�ض�������£��ö�����û��������״̬�ǳ����ã�`http.getAgent()`
���������������Щ�������

### Event: 'upgrade'

`function (request, socket, head)`

Emitted each time a server responds to a request with an upgrade. If this event
isn't being listened for, clients receiving an upgrade header will have their
connections closed.

����������Ӧupgrade����ʱ�������¼�����������Ϣû�б��������ͻ��˽��յ�һ��upgradeͷ�Ļ��ᵼ��������ӱ��رա�

See the description of the `upgrade` event for `http.Server` for further details.

���Բ鿴http.Server����upgrade�¼��Ľ������˽�������ݡ� 

### Event: 'continue'

`function ()`

Emitted when the server sends a '100 Continue' HTTP response, usually because
the request contained 'Expect: 100-continue'. This is an instruction that
the client should send the request body.

������������'100 Continue'��ʱ������ͨ������Ϊ�������'Expect: 100-continue',��˵���˿ͻ��˽�Ҫ����������

### agent.maxSockets

By default set to 5. Determines how many concurrent sockets the agent can have open.

Ĭ��ֵΪ5��ȷ���ܲ���֧�ֶ��ٸ��򿪵��׽���

### agent.sockets

An array of sockets currently inuse by the Agent. Do not modify.

��ǰ���������׽������飬�޷����ģ�ֻ��

### agent.queue

A queue of requests waiting to be sent to sockets.

�����͵��׽��ֵ��������

## http.ClientRequest

This object is created internally and returned from `http.request()`.  It
represents an _in-progress_ request whose header has already been queued.  The 
header is still mutable using the `setHeader(name, value)`, `getHeader(name)`,
`removeHeader(name)` API.  The actual header will be sent along with the first
data chunk or when closing the connection.

����������ڲ�������ͨ��`http.request()`���ظö�������ʾһ�����ڽ�������
ͷ����Ϣ�Ѿ����ź��˵�������ʱ��ͨ��`setHeader(name, value)`, `getHeader(name)`,
`removeHeader(name)`��ЩAPI���޷��ı�ͷ����Ϣ�ģ�ʵ�ʵ�ͷ����Ϣ������chunk�ĵ�һ
���ֻ��߹ر�����ʱ���ͳ�ȥ

To get the response, add a listener for `'response'` to the request object.
`'response'` will be emitted from the request object when the response
headers have been received.  The `'response'` event is executed with one
argument which is an instance of `http.ClientResponse`.

Ϊ�˻����Ӧ��Ϊ�����������һ������Ӧ�ļ�������

During the `'response'` event, one can add listeners to the
response object; particularly to listen for the `'data'` event. Note that
the `'response'` event is called before any part of the response body is received,
so there is no need to worry about racing to catch the first part of the
body. As long as a listener for `'data'` is added during the `'response'`
event, the entire body will be caught.

��`'response'`�¼��У����Ը���Ӧ������Ӽ��������ر��Ǽ���`'data'`�¼���
ע��`'response'`�¼������������֮ǰ���Ѿ������ã����Բ���Ҫ���Ĳ��񲻵�������ĵ�һ���֣�
һ����`'response'`�¼�������˶�`'data'`�ļ���������ô������Ϣ�彫������

    // Good
    request.on('response', function (response) {
      response.on('data', function (chunk) {
        console.log('BODY: ' + chunk);
      });
    });

    // Bad - misses all or part of the body
    request.on('response', function (response) {
      setTimeout(function () {
        response.on('data', function (chunk) {
          console.log('BODY: ' + chunk);
        });
      }, 10);
    });

This is a `Writable Stream`.

����һ��`Writable Stream`

This is an `EventEmitter` with the following events:

�˷����ᴥ�������¼�

### Event 'response'

`function (response) { }`

Emitted when a response is received to this request. This event is emitted only once. The
`response` argument will be an instance of `http.ClientResponse`.

���������Ӧ����ʱ���������¼�������һ�Σ�`response`������`http.ClientResponse`��һ��ʵ��

### request.write(chunk, encoding='utf8')

Sends a chunk of the body.  By calling this method
many times, the user can stream a request body to a
server--in that case it is suggested to use the
`['Transfer-Encoding', 'chunked']` header line when
creating the request.

����body�е�һ�顣�û�����ͨ����ε�������������������ݰ�ͨ�����ķ�ʽ���͵���������
�����ʱ�����ǽ���ʹ���ڽ��������ʱ���['Transfer-Encoding', 'chunked']��������ͷ� 

The `chunk` argument should be an array of integers
or a string.

����'chunk'Ӧ����һ������������������ַ����� 

The `encoding` argument is optional and only
applies when `chunk` is a string.

����'encoding'�ǿ�ѡ�ģ�����chunkΪ�ַ�����ʱʹ�á� 


### request.end([data], [encoding])

Finishes sending the request. If any parts of the body are
unsent, it will flush them to the stream. If the request is
chunked, this will send the terminating `'0\r\n\r\n'`.

��ɱ�������ķ��͡������Ϣ���е��κ�һ������û�����ü����ͣ�request.end��������ȫ��ˢ�µ����С�
������������Ƿֿ�ģ�������������������ַ�'0\r\n\r\n'�� 

If `data` is specified, it is equivalent to calling `request.write(data, encoding)`
followed by `request.end()`.

���ʹ�ò���data���͵����ڵ���request.write(data, encoding)֮������ŵ���request.end()�� 

### request.abort()

Aborts a request.  (New since v0.3.8.)

��ֹһ������

## http.ClientResponse

This object is created when making a request with `http.request()`. It is
passed to the `'response'` event of the request object.

���������ʹ��http.Client��������ʱ�������������Բ�������ʽ���ݸ�request����'response'�¼�����Ӧ������ 

The response implements the `Readable Stream` interface.

'response'ʵ���˿ɶ����Ľӿڡ�

### Event: 'data'

`function (chunk) {}`

Emitted when a piece of the message body is received.

�����յ���Ϣ��һ���ֵ�ʱ�򴥷��� 

### Event: 'end'

`function () {}`

Emitted exactly once for each message. No arguments. After
emitted no other events will be emitted on the response.

��ÿ����Ϣ����ֻ����һ�Σ����¼������󽫲������κζ�������Ӧ�д���

### response.statusCode

The 3-digit HTTP response status code. E.G. `404`.

��Ӧͷ����״̬��

### response.httpVersion

The HTTP version of the connected-to server. Probably either
`'1.1'` or `'1.0'`.
Also `response.httpVersionMajor` is the first integer and
`response.httpVersionMinor` is the second.

�������������˵�HTTP�汾�����ܵ�ֵΪ`'1.1'` or `'1.0'`����Ҳ
����ʹ��`response.httpVersionMajor`��ð汾�ŵ�һλ��ʹ��`response.httpVersionMinor`
��ð汾�ŵڶ�λ

### response.headers

The response headers object.

��Ӧͷ������

### response.trailers

The response trailers object. Only populated after the 'end' event.

��Ӧβ��������'end'�¼����������ɸö���

### response.setEncoding(encoding=null)

Set the encoding for the response body. Either `'utf8'`, `'ascii'`, or `'base64'`.
Defaults to `null`, which means that the `'data'` event will emit a `Buffer` object..

������Ӧ��ı��룬������`'utf8'`, `'ascii'`, ���� `'base64'`��Ĭ��ֵΪ`null`��
Ҳ����˵`'data'`�¼������ͻ���������

### response.pause()

Pauses response from emitting events.  Useful to throttle back a download.

ֹͣӦ���ɷ��¼������ж����طǳ����á�

### response.resume()

Resumes a paused response.

�ָ�һ����ͣ����Ӧ
