---
layout: post
title:  "JavaFX WebView"
date:   2015-01-27 23:04:20
categories: javafx java
---

For my first post, I just find out the tak to include the gist directly in the jekyll post. So I decided to look into my few gists, and I find this one. 

{% gist ajiniesta/4709535 %}

Thanks to one of the most versatile controls in JavaFX, the [WebView][webview].

On this sample, we have a TextField, where we can input an address, a ProgressBar to show the progress of the load of the pages, and the WebView, where it actually renders the pages.

Notice how works the javafx binding mechanism:

{% highlight java linenos %}

progressBar.progressProperty().bind(webEngine.getLoadWorker().progressProperty());

{% endhighlight %}

Binding the web engine loader to the control displayed in the screen.

Pretty easy and straight forward to do easy things with this control. Explore this [Oracle Tutorial][webview] to know it better, and keep playing with it.

See you in the next time

[webview]:      http://docs.oracle.com/javafx/2/webview/jfxpub-webview.htm
