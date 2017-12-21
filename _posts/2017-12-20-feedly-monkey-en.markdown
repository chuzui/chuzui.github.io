---
layout: post
title: "Blocking the ads of feedly using Tampermonkey"
data: 2017-12-21 22:03:02
description: 
category: css Tampermonkey
tags: [feedly css Tampermonkey ads]
---

I have been using feedly for over one year, following more than one hundred rss feed, including hacker news, tech blogs and news websites.
Recently, feedly adds ads link in every rss reading list in the pretense of normal articles for profitting.
Although I do not mind paying for pro features, they are not useful for me. As a programmer who knows little about web, I try to block
the ads. for better reading experience.

I decide to use Tampermonkey. We can write some javascript script and they will run automatically in the websites whose urls match the specific patterns.
I once wrote some scripts for baidupedia(a Chinese wiki-like website), which saved me a large amount of time. 

Now, let's begin. First, by inspecting the ads' block to see their html code, I find out that every ad has the form of ```<div class="entry unread u4 density-29"```.
It seems easy. I create a new Tampermonkey script with 

```
	$(".entry.unread.u4.density-29").remove();
```

Save and refresh feedly. Nothing changes. Why? Observing feedly's loading process carefully, I find that reading list is loaded asynchronously after the whole page being loaded completely.
I've mentioned before that I know little about web. This situation confuses me. I do not know the right time to run the code I write.

Suddenly, a good idea comes to me. Although I do not know the right time to block the ads elements, I can change the CSS style of ads elements to hide them.
So, whenever the ads are loaded, they are under the affect of CSS style I change.
After searching and pasting, I write

```
function addGlobalStyle(css) {
    var head, style;
    head = document.getElementsByTagName('head')[0];
    if (!head) { return; }
    style = document.createElement('style');
    style.type = 'text/css';
    style.innerHTML = css;
    head.appendChild(style);
}

addGlobalStyle('.entry.unread.u4.density-29 { display: none ; }');
```
Refresh feedly again, it works. However, not only ads but also normal articles are gone. I check the html code of normal articles and find they also have the form ```<div class="entry unread u4 density-29"```.
Comparing the ```div``` of normal articles and ads, I find that the ```div``` of normal articles have many additional attributes which are not found in ads.
Then I search the rule of attributes match of CSS and change the code to

```
addGlobalStyle('.entry.unread.u4.density-29:not([data-dot]) { display: none ; }');
```

Finally, back again, former clean and clear ads free feedly.