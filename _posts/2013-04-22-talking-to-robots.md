---
id: 267
title: Talking To Robots
date: 2013-04-22T16:44:37+00:00
author: Paul Kiddle
layout: post
guid: /?p=267
permalink: /2013/talking-to-robots/
categories:
  - Uncategorized
tags:
  - freelancing
  - web development
---
Recently one of my clients wanted to generate a site map using an online generator that has a limit of 500 pages per each site map. Their site isn&#8217;t particularly large and easily has less than 500 pages on it. But when we attempted to generate the map it hit the 500 page limit &#8211; very mysterious! We decided to open up the document and see what was going wrong.

**Fluff and Nonsense**

There were a few culprits, one was some mis-spelt links which were easily fixable using the many [broken link finders](https://www.google.co.uk/search?q=broken+link+finder&aq=f&oq=broken+link+finder&aqs=chrome.0.57j60j61l2j0.2530j0&sourceid=chrome&ie=UTF-8) on the web.

The second one was the WordPress blog that was hosted on the same domain: there were categories and tags and multiple pages alongside all of the individual posts and whatnot. We decided that only about half of this was useful and that the rest, specifically the categories and tag pages, should be ignored if possible.

And last but not least there search results. The main website contains a search page that brings up the profiles of various business affiliates. Because the site contains links to different search results, and those search results _themselves_ link to related searches, the site map ended up with hundreds of different complicated search configurations, which were mostly unnecessary duplicates of very simple searches.

**Solution #1 &#8211; robots.txt**

The blog issue was resolved by using the [robots.txt](http://www.robotstxt.org/) file to instruct robots on what they should be looking at or, more specifically, what they shouldn&#8217;t.

<pre>User-Agent: *
Disallow: /blog/category/
Disallow: /blog/tag/</pre>

This basically says &#8220;Hey, all robots, please stay away from the /blog/category/ and /blog/tag/ pages okay?&#8221;.

Okay, that&#8217;s one bunch of pages dealt with!

**Solution #2 &#8211; Robots meta tag**

Next we want to stop search results pages from being indexed &#8211; but only for certain results. We decided to allow very simple one-parameter searches to be indexed. But we can&#8217;t use a robots.txt file for this because we need to decide whether the page should be indexed _as it loads_. So what we can do is use the robots meta tag.

First of all we use PHP to look at the parameters and decide if this page should be indexed, and if the answer is no we can put this in the header:

<pre>&lt;meta name="robots" content="noindex,follow"&gt;</pre>

This tag tells robots that it should not index the page. Notice also that we have the word &#8220;follow&#8221; in there &#8211; that&#8217;s because we still want robots to follow and index the links on the page, just not this page itself.

&nbsp;

And that is how you talk to robots.