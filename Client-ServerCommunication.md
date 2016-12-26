# Client-Server Communication
## HTTP's Request/Response Cycle
1. Tim Berners Lee - father of HTML
1. HTML - Hyper Text Markup Language
1. HTTP - Hyper Text Transfer Protocol - to transfer these HTML docs
1. Hyper Text basically means the text in the docs has reference to other docs via links.
1. Example HTTP request:
```HTML
	GET /pictures/kitty.jpg HTTP/1.1
	Host: www.google.com <!-- header begins, minimum info to provide --> 
	User-Agent: Mozilla/5.0 <!-- browser that makes the request -->
	Connection: keep-alive 
	Accept: text/html <!-- format the browser supports -->
	If-None-Match: b2arb0a1r6a <!-- version of document already available in the browser local cache -->
```
	1. GET is a HTTP method or verb. Others: POST, DELETE, ADD, UPDATE, PUT
	1. GET means we want the server to send data to us. POST instructs server to save the data you're sending.
	1. HTTP method is follow by a path in this case /pictures........
	1. HTTP/1.1 is the version to use, most common and widely supported although HTTP/2 is slowly catching up.
	1. The bottom part is the **headers section**.
1. HTTP Response
```HTML
	HTTP/1.1 200 OK
	Content-Length: 1357394 <!-- tells the client how many bytes of data to expect -->
	Server: Apache
	Content-Type: text/html
	Date: Wed, 06 Apr 2016
	Etag: fd87e6789
	
	<!-- where the file data is -->
	<binary data>
```
1. POST request - its usually recommended for the backend developer to not respond to a POST request with a website but with a redirect to avoid posting the data again which could be disruptive. This avoids the prompt on reload.
1. XHR or XMLHttpRequest is too complicated.
1. Use `fetch` API which returns promises and hence much more usable.

## HTTP/1
1. Netcat is a utility that's used for sending and receiving messages over a network connection. Netcat is known as the Swiss Army knife of networking tools used to communicate directly with a server.
1. HTTP Methods: GET, POST, PUT, DELETE, HEAD, OPTIONS
1. HEAD - gets header of the file without having to receive the entire file itself. Hence you can check if you have enough space to store the response or if the cache version of the page is still up to date. This way the browser can avoid re-downloading the file. But you are sending request twice, i.e. once with HEAD and then probably, GET.
1. OPTIONS - get the list of methods accept by the current URL. This is not supported by every server.
1. Headers contain additional data about requests or responses. These are some of the important ones:

	1. `Content-Length` is a header that must be contained in every response and tells the browser the size of the body in the response. This way the browser knows how many bytes it can expect to receive after the header section and can show you a meaningful progress bar when downloading a file.

	1. `Content-Type` is also a non-optional header and tells you what type the document has. This way the browser knows which parsing engine to spin up. If it's an image/jpeg, show the image. It’s text/html? Let’s parse it and fire off the necessary, additional HTTP requests. And so on.

	1. `Last-Modified` is a header that contains the date when the document was last changed. It turned out that the Last-Modified date is not very reliable when trying to figure out if a document has been changed. Sometimes developers will uploaded all files to the server after fixing something, resetting the Last-Modified date on all files even though the contents only changed on a subset. To accommodate this, most servers also send out an ETag. ETag stands for entity tag, and is a unique identifier that changes solely depending on the content of the file. Most servers actually use a hash function like SHA256 to calculate the ETag.

	1. `Cache-Control` is exactly what it sounds like. It allows the server to control how and for how long the client will cache the response it received. Cache-Control is a complex beast and has a lot of built-in features. 99% of the time, you only need the “cacheability“ and the “max-age”.

	1. `If-Modified-Since` permits the server to skip sending the actual content of the document if it hasn’t been changed since the date provided in that header. Is there something similar for ETags? Yes there is! The header is called If-None-Match and does exactly that. If the ETag for the document is still matching the ETag sent in the If-None-Match header, the server won’t send the actual document. Both If-None-Match and If-Modified-Since can be present in the same request, but the ETag takes precedence over the If-Modified-Since, as it is considered more accurate.

	1. There are a lot more headers and a lot to explore. If you want to know more, check out the following information on [HTTP headers](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

1. When writing web apps, you'll encounter a lot of APIs you need to talk to.
1. RESTul(REpresentational State Transfer) API - The basic entities are collections and objects inside those collections. The general pattern to retrieve items from collections is using a GET request with both collection name and unique item name is that collection:
`GET <collection name>/<item name>`
1. Or PUT to update existing records:
`PUT /persons/Richard/`
1. POST creates new records:
`POST /persons/`
	1. Notice you do not make the name but the server make the choice for you.
	1. The response is usually a redirect to the newly created record.
1. DELETE, well, deletes:
`DELETE /persons/Surma`
1. Network Architechture - We are using HTTP on top of Transmission Control Protocol (TCP) on top of Internet Protocol(IP) on top of Ethernet (maybe).
1. IP allows us to talk to other machines on the internet while TCP allows us to have multiple independent streams of data between these two machines. These streams are distinguised by port numbers.
1. The TCP protocol also ensures that no packages gets lost and they arrived in the right order. All of this requires precautions that cost time and resources. Opening a new connections especially costly as the TCP handshake, which makes sure both machines are aware of the newly created communication channel has to be executed that requires two round trips.
1. If you're using HTTPS - the additional TLS handshake has to be executed as well. Once all these are done the HTTP protocol can finally take over.
1. Head of line (HOL) blocking is a huge bottleneck to website performance. Browsers being able to open up to six parallel connection helps but it's not great.
1. Time to First Byte (TTFB) - the waiting time for you to recieve first response. If there is multiple requests, we need to wait for the first response of first request before we can send out a second request. Another period of time waiting not spent effectively. This problem is called HOL blocking.
1. HTTP /1.1 introduces a concept of keep alive, if the client sets the connection keep-alive in the header, the server will not close the connection after successfully delivering the response but allows the client to reuse the already established connection for additional requests. Keep in ind though that you can't still send requests before the response for the last request has been fully transferred. All in all this forces the web developers to keep the number of additional resources as low as possible making the best possible use of their six connections. This is why JS and CSS files are commonly concatenated into bundles(`bundle.js`) and why images are put together into sprites. The bundles can be obtained in just one request.

## HTTPS
1. All modern browsers API are only available if your website is secured with a HTTPS. Use it for every single page.
1. [Firesheep](https://en.wikipedia.org/wiki/Firesheep) is an extension for the Firefox web browser that uses a packet sniffer to intercept unencrypted cookies from websites sych as FB and Twitter.
1. One feature of HTTPS is encryption: it will make your browser encrypt request in a way that only the server you're connected to can decrypt them. 
1. In the man-in-the-middle-attack (mitm), the attackers gets between you and the server, when this happens your browser will make an encrypted connection to their server out of the server you thought you were trying to connect to like Facebook. The attacker will decrypt your data and read all the private info and re-encrypted and forwarded to the FB server and vice versa. Neither FB nor you would know that they are sitting in the middle.
1. To remedy this, HTTPS has off indication, the server will have to identify itself in a way only the real server could as to be sure you're talking to the right server.
1. A proxy is a legitimate man-in-the-middle and has many benefits like saving bandwidth by adding additional compression, downsampling images, and doing aggressive caching.
1. HTTPS = HTTP + TLS (formerly known as SSL). TLS stands for Transport Layer Security.
1. TLS is not specific to HTTP. FTPS = FTP + TLS
1. TLS utilises Chain of Trust and server identifies itself with a certificate that contains both little metadata about itself and the fingerprint on the encryption.
1. These certificates are issued by certificate authorities. If that certificate is signed by such an authority, you know that if the key you are using matches that fingerprint then you're talking to the correct server.
1. You need to buy certificates from these authorities.
1. [Let's Encrypt](https://letsencrypt.org/) offers certificate for free.
1. TLS has two important cryptographic building blocks: encryption and hashing.
	1. Symmetric encryption: encrypt some data and give it to someone else, the recipient needs the same key to decrypt the data.
	1. Asymmetrial encryption: Browser can create encryption and decryption keys using mathematical algorithms. Mostly the key for encryption is made public to anyone who wants to send the message, then you need the decryption key to decrypt it. Both keys can be used for encryption and decryption. Public key is available to everyone and private key is avaialble only to the owner. Hence this is also called Public Key Encryption
	1. Hashing - the process of transforming data into a short representation of the original data. The smallest change in the original data will have enormous change in the hash. If two documents yield the same value they are very likely to be the same docs. Some things to note about hashing functions:
		1. It should be impossible to reverse the conversion process meaning once the data is converted into a hash it can't be unconverted back into the original data.
		1. It should be just as impossible to find a different document yeilding an identical hash value.
		1. Most common hash function is SHA - e.g. SHA256 always yeild 256 bits as output no matter the size of the document
1. Certificate: when a server signs a document and encrypts it with a private key they give it back as a signed document. Only the holder of the key is able to decrypt the document. For large docs, encrypting and decrypting will take a long time. That's why rather than encrypting the doc, just the hash is encrypted. If you want to check if the signature is valid, you will decrypt the signature and hash the doc yourself and see if those two values match. This way you know the doc receive is exactly what the server has sent. If the values don't match, it means something changed, this is called invalid signature.
1. The [TLS Handshake](https://www.youtube.com/watch?v=AJ9BEKTcg3A):
	1. The server sends you a certificate which contains the public key of the server, domain the cert is for and the signature by cert authority etc.
	1. The client checks if the domain is correct and the authority signature is valid. All browsers have a collection of certificate authorities including the public key saved locally so it is trivial to check if the signature is valid.
	1. The client generates a random key for asymmetric encryption. The browser encrypts the random key with a service public key and send it over.
	1. This has two benefits: symmetric encryption is much faster and more efficient and scales better to big data compared to asymmetric encryption but more importantly the server will only be able to continue communicating if it is truly in possession of the private key and can decrypt the new random key. This effectively validates the server identity.
	1. If all of these succeed, a secure connection is succeesfully established and the HTTP protocol can take over and you'll get the green padlock. :)
	1. 
1. Things that can go wrong: certificates have an expiration date, hash and encryption functions can be weak, certficate is valid but rest of the server setup is not. 
1. See how a browser behave when the TLS connection has some issues at [badssl.com](badssl.com) 
1. Chrome will deny access if expired cert and cert from a different host. It will allow access if it's e.g. mixed content, incomplete chain and using SHA256.
1. Servers might serve over HTTPS, but sites asset might not. E.g. pulling JQuery from non TLS-enabled CDN. Depending on the content, you might still be functional but cost you the green padlock, resource might be blocked or you might even get the red padlock. Hence, it's recommended to server all content over HTTPS.
1. When working locally developers usually resolve to using self-signed certificates. These certs refer to themselves as their own cert authority. Since they are self-signed, they don't provide any kind of authentication and browser will give a red padlock but it will let you test if your website has mixed content. Easiest way is to check console. Or at Security tab and click the mixed content results to filter it at the Network tab.

## HTTP/2
1. HTTP/1 is old, no. of requests made have increased enormously in the past decade, design choice made back then has now become a burden in terms of development and performance. Current best practice to concatenate your JS and CSS into a single file exist because of this shortcoming.
1. Head of line blocking - is when one request is blocking others from completing. A browser will open up to 6 connections to the same server, a round trip time takes between 20-50 ms on a good connection. If there's 100 requests, each connection will have to handle 17 requests. And if the round trip time is 35 ms, it will take about 17 x 35 = 525 ms to send all requests and get response. This is a lot of waiting time (without considering use of `async` of course). Furthermore, larger files will take longer round trips
1. [Rising number of requests](http://httparchive.org/trends.php?s=All&minlabel=Nov+15+2010&maxlabel=May+15+2016)
1. To reduce time to send data a lot of websites compress their assets through GZIP or other compression algorithms. HTML5 boiler plate project uses GZIP. However, headers of request and response are not compressed but actually they should be highly compressible as they are just plain text. Also they repeat a lot across requests: headers is always the same, cookies are the same, and so are some other headers.
1. According to Google, on average there's 800 bytes per header. 100 requests means 80 kbs of header files. Save a lot of space if we can compress the headers but can't do it with HTTP /1
1. Browsers only support HTTP /2 with TLS - this is becoming more important as ecommerce has become more and more common.
1. HTTP /2 Improvement:
	1. Headers no longer written in plain text - with this a single bit is sufficient. Dev tools still allow you to read the headers.
	1. Solves HOL blocking by multiplexing - combining multiple signals into a new single signal. With HTTP 2 we just need one connection instead of six. What used to be dedicated connection in HTTP /1 is now called a stream and all streams shared the single connection. These streams are split up into frames and are being multiplex onto single connection. When one stream is blocked, the other stream can take over and make good use of what would have been idle time. HOL blocking would have been gone.
	1. Header compression - compressed with GZIP, engineers can come up with HTTP /2 compression tailored to specific structure of headers and the multiplexing feature of HTTP /2. Since all streams share the connection and compressor, headers never has to be sent twice as the compressor recognizes that it's been sent before and hence send a reference instead.
	1. e.g. cookies have long headers.
	1. More info on [Header Compression for HTTP/2](https://http2.github.io/http2-spec/compression.html)
1. No need for concatenation - since it's actually worse. For example if you cache all the bundled js file, if you change one single semi colon, you need to download the whole file again. But if you have them separated, you just download the ones necessary.
1. More requests sent = more headers can be reuse, better!
1. Should still minify files and deferring JS and inlining CSS.
1. HTTP /2 is backwards compatible. 
1. As of early 2016, 71 % of web traffic has support for HTTP/2 so its fair to say you can optimise your web for HTTP/2 without paying much attention to HTTP/1 anymore as this number will only grow.
1. You should:
	1. Minify CSS, JS and Markups
	1. Use a CDN, just a single one, outweighs the drawbacks of sharding as it brings your assets geographically closer to your users and reduces the round trip time.
1. You shouldn't:
	1. Concatenate JS, CSS
	1. Sharding assets - makes HTTP/2 header compression less efficient and causes new connections to be opened by the browser which is costly.
	1. Sprite images
1. Takeaway: HTTP/2 works exactly as HTTP/1 just that the performance and precautions you need to take is a little different. Just start building web apps with HTTP/2 in mind.

## Security
1. As a rule of thumb, JS is not allowed to access the data of any other origin than it's own.
1. An origin is made up of 3 parts: Data Scheme (https://), Hostname(www.google.com), and Port(80). If you change any of these parts you are on a different origin and different rules will apply.
1. Apart from the mixed content problem, this is another reason not to mix HTTP and HTTPS urls.
1. Rules:
	1. You can't make `fetch()` request to other origins (under certain criteria you can, but then you can't read the answer). Just like you can't fetch other people's messages from Facebook.
	1. You can't inspect `iframe` or windows with JS if they are from another origin.
	1. This is called the same origin policy.
1. Exceptions:
	1. Stylesheets, images, videos, iframes even scripts from other origins as well as send form data to other origins can be included by can't be access the same way you can for those from the same origin.
	1. The contents will just silently appear empty or just explicitly throw an error (with modern API). The user browser enforces the same origin policy, not the server.
1. Cross Origin Resource Sharing (CORS) - solution to single origin problem.
1. JSONP was one of the older techniques - returns scripts containing the data. This exploits the fact that scripts from other origin will execute and share the execution environment with your own scripts.
1. An API that supports JSONP:
```JavaScript
	<script src="http://stuff.com/stuffs=teddybear&callback=f"></script>
	function f (["teddybear1", "teddybear2" ]) {
		// do something when callback..
	}
```
1. Message passing -  Another technique that was explicitly designed to allow cross-origin communication is called message passing. postMessage() is a function that can be called to pass a message to other windows and iframes, even if they come from a different origin. This creates a message event you subscribe to like any other event. For security, the receiver can inspect the message’s origin and content. 
1. While postMessage is much cleaner and allows more granular control than the other cross-origin options, it sadly hasn’t been as widely adopted by API providers.
1. CORS has been adopted by API providers as primary way to share resources. CORS allows servers to specify a set of origin that are allowed to access resources, if the request referring header is on that it will be able to inspect the answer and use the data. 
1. By the time server sends back the headers, the request would already have executed. This can be problematic with destructive operations because it is already too late to ignore the request. Preflight Requests uses the OPTIONS method that allows the browser to signal that it only wants to check what is allowed and what is not. Server should not execute any kind of business logic but only return the headers similar to a head request.
1. Not all requests will be preflighted. Requests that are made because of image tags or forms will not be preflighted. More details on [Preflighted Requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS#Preflighted_requests)
1. Request with GET and POST method are not preflighted, but with a optional header, the request can be preflighted.
1. Request with PUT is preflighted.
1. Sometimes you don't need to see response (e.g. request coming from a form) to wreak havoc, e.g. a bank has a form to transfer money, a bad person doesn't need to know what the server answers and set up a website that forges a request for the same url the form uses into the parameters so that the money is wired to him. This kind of attack is called Cross-site request forgery (CSRF). Banks have sophisticated protection, but in most cases a CSRF token is enough.
1. A CSRF toekn is an additional field appended to a form that has been put there by the server and stored at server side as well. Server will only execute if the tokens match.
1. Example of scam: using credentials stored in browser cookie to send POST request to bank and have the money transferred. 
```JavaScript
	var button = document.getElementById('button');
	var body = 'recipient=Umbrella+Corp&amount=666';

	button.addEventListener('click', function() {
		fetch('htttp://bank.127.0.0.1.xip.io:8080/transfer', {
			method: 'POST',
			headers: {
				'Content-Length': body.length,
				'Content-Type': 'application/x-www.form-urlencoded' // content type for POST request
			},
			credentials: 'include', // this includes the credentials in the stored in cookie
			body: body,
		});
	});
```
1. [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting) - e.g. in a comment section, type in JS code and hence have access to site data inlcuding DOM and cookies. Always validate users input server side.
1. 
1. 