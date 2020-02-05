---
title:  "How I built my blog"
excerpt: Short overview of technologies which have been used for building this web site.
date:   2019-07-22 00:00:00 +0100
categories: blog
toc: true
toc_label: On this pages
toc_sticky: true

header:
  teaser: assets/images/2019/blog/how-i-built-my-blog/how-i-built-my-blog-teaser.jpg  
  overlay_image: assets/images/2019/blog/how-i-built-my-blog/how-i-built-my-blog.jpg  
---

## Intro
I’ve been planning to create a website and start blogging about the things I’m passionate about for a long time.
Finally I investigated all possible options about how can I build my blog and keep it simple and which technologies fit the best for this.

In the end I choose [Jekyll](http://jekyllrb.com/) and [GitHub pages](https://pages.github.com) as a hosting platform.
And I am going to give you an overview what you can build using this technologies and how I did it.

## Requirements and investigation

Keeping it simple, I only had few requirements for my personal blog.

**Includes:**
- Responsive (phones/tablets/etc support)
- Lightweight. Should be easy to setup and maintain without DB or server.
- Ability to migrate to other platforms in future
- Pagination and comments support
- Simple hosting and deployment process of new posts
- Free

Everyone know that the easiest and fastest type of web site is just a **static** web site. The main advantages are simplicity, speed, easy testing, etc.
But usually modifying of such type of websites become a hell, especially if you want to have more than 1 page and also need some nice feature like pagination, categories, tags and so on.
Thats why the best option here would be to use static site generators. They give you super nice possibility to use Markdown and keep separately your texts and the rest with all power of static site.

>I choose Jekyll, because it simple, powerful, secure, fast and there is a huge community in the internet.

## Setup and templating

To start your static website with Jekyll all you need to do is write this commands one by one:
```
 1. gem install bundler jekyll
 2. jekyll new project_name
 3. bundle exec jekyll serve
```

Out of the box your Jekyll website will be just simple. So if it's okay for you or you want to make it completely custom - go forward with this. For everyone else I can suggest to use jekyll themes. The most popular sites for this: [jekyllthemes.org](jekyllthemes.org) and [jekyllthemes.io](jekyllthemes.io)
As you can see there are a lot of free/paid options which you can choose from based on your purpose.
For my web site I choose this template: [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes)

I strongly suggest you to read carefully the documentation which is going with template in order to understand how it works and features it provides to you out of the box. Of course you can always modify it based on your needs, but it would be better to reuse as much as possible in order to keep it simple and to have possibility to upgrade easily to new versions of template.

Installation can be different and here are possible options:
- [gem-based theme installation](https://jekyllrb.com/docs/themes/#understanding-gem-based-themes)
- [remote theme installation](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/)
- forking or directly copying all of the theme files into your own project.

The disadvantage of last option that you will loose ability to get theme upgrade easily in the future.
In my case I choose **remote** theme installation which are similar to **gem-based** themes, but do not require Gemfile changes or whitelisting making them ideal for sites hosted with GitHub Pages. And we still have possibility to override default templates/styles if we want to.

All installation steps are extremely simple. Just in order to show you, everything what you should do is:

```
1. Add gem "github-pages", group: :jekyll_plugins into Gemfile
2. bundle install
3. Add remote_theme: "mmistakes/minimal-mistakes@4.16.3" to your config.yml file.
```

That's all. Initial setup is done. And we are ready to start our web site locally with this command:

```
1. bundle exec jekyll serve
2. open http://localhost:4000/ in browser
```

## Hosting on GitHub Pages
GitHub gives us not only a great possibility to host static website for free, but also provides more features like instances for building website (CI/CD feature).

>The best part is that you can simply place your unbuilt Jekyll blog on the master branch of your user repository, and GitHub Pages will build the static website and serve it for you. You don’t have to worry about the build process at all — it’s all taken care of.

By default yours web site will be available on URL like this: **'username'.github.io**, where username is the name of yours GitHub account. The limitation here - that you can host only 1 site like this with 1 account. (actually you can host several, but it's looks more like workaround and require additional setup (check [GitHub pages doc](https://help.github.com/en/articles/user-organization-and-project-pages))

You can also easily buy any domain register your GitHub site there. See below the detailed explanation how to do it.

Note: You can also do the same if you are using GitLab.
{: .notice}

## Custom domain
If you want to have a custom name for yours blog instead of **'username'.github.io** - you can easily do this.
Here are steps which you should follow:
1. Buy domain. You can use this web sites: [Godaddy](https://www.godaddy.com), [namecheap](https://www.namecheap.com)
2. Create CNAME file in github root folder of your repo and put your domain into it
3. Configure hosting with github pages and namecheap. [Tutorial](https://www.namecheap.com/support/knowledgebase/article.aspx/9645/2208/how-do-i-link-my-domain-to-github-pages) for Namecheap as an example
3. Wait for few hours and enjoy your new domain.

## Comments
Apparently we are building blog - it's highly desirable to have possibility to add comments. And because we are building static web site - we are highly limited in technologies which we can use for this.
Luckily we have several options how to do it and Jekyll helps us with this as well.

Jekyll Minimal mistakes template provide support for next popular commenting platforms:
[Disqus](https://disqus.com), [Discourse](https://www.discourse.org), [Facebook Comments](https://developers.facebook.com), [Staticman](https://staticman.net), [Utterances](https://utteranc.es).

We just need to configure it properly.

As for me - Staticman can be one of the best option here. Because the idea of it to keeps all user data(markup, styles and texts) where it belongs: on your GitHub repository and you don't need any external service for this.
The main disadvantage here - that it's a bit more difficult to configure it and you will not get any statistics or anything. Even rendering and styling of the stored comments you have to do manually. And it can be really tricky if you want to have at least threaded comments (reply feature).

**Disqus** and similar services keep the comments on their servers. So you just have to register an account and use of power of this system.
>I choose Disqus for my blog because it's one of the most popular(even [spring.io blog](https://spring.io/blog) using it), there are a lot of statistics and information you can see in your account and it's very easy to setup.

Because I have Disqus support in my Jekyll template, all configuration which I did is this (in main _config.yml_ file):

```
comments:
  provider: "disqus"
  disqus:
    shortname: "yours-disqus-account-name"
```    


## SEO/Analytics
Another good feature of Jekyll, that everything what you need for SEO/Analytics you can get OOTB.
All you need to do - it's to provide configuration.

For me I choose [Google analitics](https://marketingplatform.google.com/about/analytics/) and [Google gtag](https://developers.google.com/gtagjs/).

```
analytics:
  provider      : "google-universal"
  google:
    tracking_id : "UA-*******-1"
```

Also this template silently generate a _sitemaps.org_ compliant sitemap for your automatically.

## Mailing engines
Of course it's optional, but if you want to bring some mailing features to yours static web site there are several options how to achieve this.
There are a lot of different free and paid solutions starting from simple email senders and ending with huge email marketing platforms with subscription/mailing , statistics and a more.

I started with the simple one and choose [FormSpree](https://formspree.io) .

To make it works you just need to add this block of code to your html page and you are done:

```
<form action="https://formspree.io/email@domain.tld" method="POST">
  <input type="text" name="name">
  <input type="email" name="-replyto">
  <input type="submit" value="Send">
</form>
```

No registration or anything else is needed, so I think it's pretty good for the beginning.

In future if I would like to add subscription and mailing features to my blog - I'll migrate to something else.

## Conclusion
I’m glad that I finally got my blog up and running. In total I spent few evenings in order to do all this steps which I explained in this article and in the end everything is automated, maintainable and I can easily add new blog posts now.

If you want to build your own blog/portfolio/business card site etc I would definitely recommend you to take a look on Jekyll.

If you still have any questions left - please do not hesitate to ask them in comments.
