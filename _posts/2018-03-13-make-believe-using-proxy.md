---
layout: post
title: Playing make-believe using proxy server
date: 2018-03-13
categories: tools
subtitle: Guide to using proxy server (mitmproxy) to get the response you want
share-img: /assets/images/make-believe-using-proxy/share-img.png
cover-img: /assets/images/make-believe-using-proxy/heroImage.jpg
thumbnail-img: ""
css: /assets/css/style.css
---

Say, you are developing a feature in an E-commerce web/mobile app.  
You have to show "Item is out of stock" banner on "Cart" page when an item is out of stock.

You hit an endpoint `/cart` and this endpoint returns you something like this.

~~~json
[
  {
    "name": "SomeShoes",
    "soldOut": false,
    "price": "$50",
    "quantity": 1
  }
]
~~~

You get `soldOut` as `true` when the item is sold out.  
To ease out development you’d need to simulate this behaviour.

Some ways to do so could be:

1. You add an item to cart and then login as another user to buy all the available stock.
This’d simulate out of stock in the first user.

2. You add an item to cart and then modify data or change code in backend API to make sure that item is sold out.

Both of the above approaches would work but both might require a lot of work.
If those APIs are external, then you’d have next to zero control.
First approach might be possible but the second approach is impossible.

---

What if you could hack your way and change the value of `soldOut` to `true` without any of the above methods.

>Enter proxy server

A proxy server that lets you see the response from the server and modify it even before it comes to the front-end app.
You can intercept the `/cart` API call and modify the value of `soldOut` to `true` in response.

Imagine how much time you’d save.
All you wanted was to check how your sold out banner looks like when it appears, if it even does.

No one wants to change the world to make sure they get a true instead of a false.

---

There are many proxy servers out there that’ll let you do this, one such is **mitmproxy**.

mitmproxy is a free and open source tool for Windows, Linux and Mac.

Here’s how to use mitmproxy (commands below are for Mac, they should work on Linux as well).

#### Installation
Mac: `brew install mitmproxy`  
Others: You can find installation instruction [here](https://docs.mitmproxy.org/stable/overview-installation/){:target="_blank"}.

mitmproxy has command line and web interfaces.

#### Web Interface
To start the proxy server use the command

Mac: `mitmweb --port 9000 --web-port 9001`  
Windows: `mitmweb.exe --listen-port 9000 --web-port 9001`  

Here, `--port` and `--listen-port` is used to specify the port on which proxy server would run and `--web-port` is used to specify the port for web interface of server.

Now open the url [localhost:9001](http://localhost:9001){:target="_blank"}  
Below is the web interface of the proxy server where you can see every request that goes through it.

![mitmproxy’s web interface](/assets/images/make-believe-using-proxy/mitmProxyWebInterface.png){: .center-image }

Now we need to route all network request through it.

I use Firefox for this as it has support for adding proxy settings and certificates for just Firefox.

---

### Configuring Firefox

**Configuring Proxy**

Install and open Firefox.
Search "Connection Settings" in settings.

Here, you will manually add a proxy configuration.
If you are following this guide then add `localhost` in HTTP proxy and port as `9000`.  
Also, clear "No Proxy for" text.

![Firefox's connection setting](/assets/images/make-believe-using-proxy/firefoxSetting.png){: .center-image }

---

**Installing mitmproxy certificate**

You need to install a certificate for mitmproxy to work.
Otherwise, your browser won’t let you access anything.

**DO NOT INSTALL THESE CERTIFICATES ON YOUR SYSTEM. INSTALL THEM ONLY ON CLIENT** (client is Firefox in this guide).  
**Installing these certificate on your system is a security vulnerability.**  
This is one other reason why I choose Firefox. Firefox allows you to install certificates only for itself.

![Certificate installation prompt](/assets/images/make-believe-using-proxy/firefoxCertInstall.png){: .center-image }

Open url "[mitm.it](http://mitm.it){:target="_blank"}" in your Firefox and then click on "Other" to install the certificate.
Check the checkbox "Trust this CA to identify websites."

Above step will install mitmproxy certificate only in Firefox.

Do not install certificate by clicking on Apple, Windows or Android unless your client is a Windows VM or an iOS simulator/android emulator or a real phone which you want to use as a client.

---

>We are locked and loaded

Hit any url you want and check the web interface of mitmproxy.
You’ll find your request/response in there.

### Modifying request and response

To modify the request and response you must first intercept a request.
When you intercept a request it is stopped twice.
Once when it is going to the server and second when the response is coming from the server.

To intercept add a URL pattern in "Intercept" text box.

![Intercepting Request](/assets/images/make-believe-using-proxy/mitmproxyIntercept.png){: .center-image }

In the image above I’ve intercepted the URL which have "localhost" in them.
The requests are shown in the bottom left box. The current request is paused as it is intercepted.
You can modify the request here.

Now switch to "Flow" tab and hit resume button.

![mitmproxy Flow screen](/assets/images/make-believe-using-proxy/mitmProxyFlows.png){: .center-image }

Your request has now gone to server and response will come.
This request will be paused again but this time it is going back to Firefox and you can change the response as well.

>the moment to shine

Now change that `soldOut` to `true` and hit that Resume button to see the magic (hack *cough) happen.
Your frontend will receive the modified response and it should show you the "Item is out of stock" banner you waited all your life for.

You can change everything there is to a response and request. Literally, from head to body, cookies and whatnot.

---

### Command Line Interface (Unavailable in Windows)

mitmproxy has an excellent command line interface as well.

To start the proxy server in CLI use the command `mitmproxy --port 9000`  
Here,`--port` is used to specify the port on which this server must run.

![mitmproxy Command Line Interface](/assets/images/make-believe-using-proxy/mitmProxyCLI.png){: .center-image }

You’d be greeted by this. Hit `?` for shortcuts of CLI.

As mentioned in Web Interface configuration. Configure your Firefox.

At this point, you should be able to see all the request/response going through your proxy server.

To intercept a request hit `i` and add a URL pattern (this is a RegEx).  
Same as the web interface, mitmproxy intercepts request twice.
Once while going to server and second when coming back from the server.

![Command line intercept](/assets/images/make-believe-using-proxy/mitmproxyCLIIntercept.png){: .center-image }

I’ve added a pattern `localhost`. Which intercepted a request. Hit Enter on it to view this paused request.

![Request tab in mitmproxy CLI](/assets/images/make-believe-using-proxy/mitmproxyCLIRequest.png){: .center-image }

To modify hit `e` and you’d be given options to modify whatever you want to.

Hit `a` to let request go to the server and now you can view response and modify it as well.

![Response tab in mitmproxy CLI](/assets/images/make-believe-using-proxy/mitmproxyCLIResponse.png){: .center-image }

Above is the response tab. Hit `e` and you can modify the response.
Press `a` again to let the response go to Firefox.

These are some basic commands you can use. For more see help, hit `?`.

---

>Now hack your way through software development

![Hacker Cat](/assets/images/make-believe-using-proxy/hackerCat.jpg){: .center-image }

For further information on mitmproxy refer to documentation [here](https://mitmproxy.org).

Feel free to leave a comment below. If you enjoyed this article, please share to help others find it.