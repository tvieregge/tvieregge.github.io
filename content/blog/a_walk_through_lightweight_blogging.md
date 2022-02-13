+++
title = "A walk through lightweight blogging"
date = "2022-02-12T23:57:11-05:00"

tags = []
+++

Let's say you want to set up a blog, there are plenty of [good reasons](https://sites.google.com/site/steveyegge2/you-should-write-blogs) after all. But, you hate websites that are slow, full of spyware, and [unusable on spotty connections](https://danluu.com/web-bloat/). This leaves you with a bit of a problem, the big names aren't going to cut it. 

This is what I ran into while setting up this blog. I didn't find any other resources going over what was currently available, so I thought I'd run through my experience in the hopes that it will be easier for the next person.

### What was I aiming for?
First I needed to figure out what I actually wanted so I knew what I was building. This is what I came up with:
- "fast"
- Simple layout and styling, but more than basic HTML (I'm looking for [bettermotherfuckingwebsite](https://axelletrt.github.io/bettermotherfuckingwebsite/) not [motherfuckingwebsite](http://motherfuckingwebsite.com/))
- No tracking. No JS at all ideally
- Easy to get to (simple, flexible domain)
- Easy for me to use (This means publishing in markdown, since that's what I take my notes in)

How fast is fast though? I had speed as a goal, so I needed a reliable way to measure it and a target to aim for.

http://danluu.com/web-bloat/ shows that his site is one of the fastest out there, and mentions [webpage test](https://www.webpagetest.org/), so lets run the first through the second and see what we get. All tests are done with "desktop" performance located in Virginia, and repeated results were stable enough I could just grab one.

![img](/static/img/20220212234525.png)

So we got 290ms until "Visually Complete", which seemed like the best metric to go by, even though I still can't find exactly what it means... We also got 187 ms for the data transfer to complete. This is a very clear metric, there's no ambiguity about what it means, but in non-trivial cases it can be difficult to interpret. Luckily I'm aiming for the trivial case.

### Can I use a platform?
The easiest option I could think of was using some other, less well know, blogging platform. After digging around for a while I found [bearblog](https://bearblog.dev/) and [mataroa.blog](https://bearblog.dev/), both of which are on the wonderful [Plain Text Project](https://plaintextproject.online/tools.html). They both support markdown, seem pretty easy to use, and look pretty good. Running them through webpagetest highlighted some issues though.

mataroa was particularly slow to load, getting uncomfortably close to 1s to be visually complete. Most of this was from a strangely slow connection. I tested from various locations around the world and this stayed consistent. Not sure what's up, but it didn't look like a good option.
![img](/static/img/20220128001042.png)

bearblog looked like just what I wanted. It loaded super quickly, used markdown, was simple to set up and you can link up custom domains! Looking closely though it was loading some JS. Specifically it was loading a [fathom](https://usefathom.com/) tracking script. It's a privacy focused tracker and all that, but I would prefer to avoid it.

![img](/static/img/20220128002449.png)


This would be the pragmatic place to stop, but this isn't work, it's play! I wanted to see if I could get something better. It was time to look into hosting and site generation. 

### Hosting
So I needed to test a hosting provider too see get some performance benchmarks. Github pages seems popular, and they use a CDN (fastly), so I hosted a near empty site and ran it through webpagetest.

There's some JS junk going on that they generate by default, but everything before the html loading (i.e. the stuff I we need to worry about at this point) is fast, really fast. It's nice when things work out.
![img](/static/img/20220128002937.png)


### Site generation
There were so many options. I saw [HUGO](https://gohugo.io/) pop up repeatedly so I took a look. After spending way too long looking through themes that weren't *quite* right, I eventually found  [hugo-bearblog](https://themes.gohugo.io/themes/hugo-bearblog/). Exactly what I wanted!

This was all pretty smooth. The only hiccup was that I needed to use a `/docs` directory as the output, and set github to source the page from the `/docs` since you can't give github an arbitrary directory to source from.

Otherwise I just check out the [HUGO quick start guide](https://gohugo.io/getting-started/quick-start/), and hugo-bearblog [installation](https://github.com/janraasch/hugo-bearblog#installation) / [starting-fresh](https://github.com/janraasch/hugo-bearblog#starting-fresh) sections. The result: a pretty quick site!


![img](/static/img/20220210225520.png)

One thing bothered me though, that favicon attempting to load. What do you mean loading a favicon? I don't have one! This [stackoverflow post](https://stackoverflow.com/a/13416784) had the solution: add  `<link rel="icon" href="data:;base64,=">` to `<head>`

I did this using hugo [partial templates](https://gohugo.io/templates/partials/) and overriding `/layouts/partials/favicon.html`

![img](/static/img/20220210225558.png)

Ahh! Much better.

#### DNS
My goal is to have the `www` subdomain and the apex domain point to the blog.
This is a little tricky because of how apex domains work.

Namecheap has their [own docs](https://www.namecheap.com/support/knowledgebase/article.aspx/9645/2208/how-do-i-link-my-domain-to-github-pages/) on how to do it, but they're bad. It suggests a bunch of A records pointing to github IPs that could change.

What I ended up doing was using an `ALIAS` record that points at the github domain. This tells the DNS server to look up the address of your github domain and return that address for your domain. You can read more about them at [NS1](https://help.ns1.com/hc/en-us/articles/360017511293-What-is-the-difference-between-CNAME-and-ALIAS-records-), and why you need them at the [isc](https://www.isc.org/blogs/cname-at-the-apex-of-a-zone/)

If you're new to DNS (like me) I'd recommend using a private/incognito browser for all queries to your new site. Browser caching on top of DNS caching is a recipe for frustration. [Mess with DNS](https://jvns.ca/blog/2021/12/15/mess-with-dns/) from the wonderful Julia Evans is a great tool for learning as well.

Here's what my setup looked like in the end.

![img](/static/img/20220131225723.png)

The [github documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) was a little scattered, but they'll automatically redirect between the `www` and apex domain.

After the DNS was set up I put my domain into the UI box, and after chugging away for a few minutes everything looked good. This is what's shown if it works (why isn't that in their docs!).
![img](/static/img/20220116163530.png)

Then it was done! The final webpagetest result showed that nothing strange was going on with DNS.
![img](/static/img/20220210232118.png)

I spent a lot of time poking around for different solutions. There's no lack of them, but they can be hard to find. Hopefully this helps.
