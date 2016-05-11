# xhr-xdr-adapter
xhr-xdr-adapter enables, to the extent possible, support for Cross Origin Resource Sharing (CORS) on Internet Explorer versions 8 and 9 using standard JavaScript libraries.

## Why is this necessary?
Most modern web browsers allow AJAX requests to load assets from other servers, as long as those servers are configured to support CORS.  In most cases, JavaScript's standard XMLHttpRequest API can be used on the client.
However, on IE 8 and 9, the non-standard XDomainRequest object must be used instead.  XDomainRequest has a slightly different API, and some additional [restrictions](http://blogs.msdn.com/b/ieinternals/archive/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds.aspx). 
As developers, we'd love to focus only on the more modern browsers and forget about the old flavors of IE.  But even in 2014, the percentage of our users on IE 8 and IE 9 makes them impossible to ignore.

Common JavaScript libraries and frameworks such as jQuery and AngularJS do not support IE's XDomainRequest out of the box.  By including xhr-xdr-adapter.js, those libraries will be able to load assets from different CORS-enabled servers, subject to IE 8 and 9's limitations. 
For example, if view templates are hosted on a properly configured CDN, including this file will allow AngularJS's router to load them at run time on the mentioned IE browsers.

## How does it work?
xhr-xdr-adapter replaces the native XMLHttpRequest with a custom function having the same API, only if it's running in IE 8 or IE 9. When the open() method is called, the library figures out whether to create a native XMLHttpRequest object, or an XDomainRequest.

The goal is to provide as complete support as possible for cross domain XMLHttpRequest on a browser such as IE 8. For this reason, the extra step is taken to support async=false requests, as well as doing a complete check for whether XDomainRequest should be used.  This includes checking for base href="..." before concluding whether a request is cross-origin.

## Limitations and alternatives 
It's important to emphasize that this solution won't magically give IE 8/9 capabilities it doesn't have. It will still only support a subset of CORS functionality, which suffices for many applications, and will be able to do so using a standard API and standard JavaScript libraries.
The full list of limitations and restrictions is on the [MSDN](http://blogs.msdn.com/b/ieinternals/archive/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds.aspx) site.  The main ones are that for CORS requests on IE 8/9:

* No authentication or cookies can be sent
* POST or GET only
* No custom headers can be sent
* text/plain contentType only
* only http<-->http or https<-->https
 
If you're required to support IE 8 or 9, and make CORS requests that are outside of those limitations, there are a few alternatives.  
[XDomain](https://github.com/jpillora/xdomain) is a popular alternative that involves loading an iframe from the remote server, and using postMessage to communicate with that iframe, which does the asset loading (from its own domain.) 
Another alternative is to proxy the requests via your own server, assuming the bandwidth and processing costs are not an issue.

## Usage
Simply include xhr-xdr-adapter.js before making cross domain requests with the popular libraries. The function is small enough to incorporate into your build process and minify together with the rest of your code.
Alternatively, you could conditionally include it in the relevant versions of IE only:

    <!--[if lt IE 10]>
    <script src="scripts/xhr-xdr-adapter.js" type="text/javascript"></script>
    <![endif]-->

If your are using a npm module packager:

```js
require('xhr-xdr-adapter');
import 'xhr-xdr-adapter';
```

## Contributing
Contributions are welcome. If you find issues or features that we could support on IE 8 and are not currently supported, please submit a pull request.

## License
xhr-xdr-adapter is provided under the MIT license.
