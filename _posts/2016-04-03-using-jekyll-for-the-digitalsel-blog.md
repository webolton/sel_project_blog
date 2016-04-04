---
layout: post
title: Using Jekyll for the DigitalSEL Blog
modified:
categories:
description:
tags: []
image:
  feature:
  credit:
  creditlink:
comments:
share:
date: 2016-04-03T13:59:39-04:00
---
![Jekyll Logo]({{site.url}}/images/jekyll.png)
{:style='text-align: center;'}

### Showing my work
Early on, I decided that I should blog about everything I did on the reboot of the DigitalSEL project. A blog will be a good place to document the project’s development and record its successes and challenges. I also hope that the blog will be useful to other independent scholars and academics who might be building similar applications. Goodness knows, I find other people's blogs incredibly helpful when I am trying to solve programming problems and I would love to return the favor if I can.

### So, which platform?
There are many good options for academics looking for a blogging platform and they all have advantages and disadvantages. [Wordpress.com](https://wordpress.com/){:target='_blank'} is easy to use and inexpensive. [Wordpress.org](https://wordpress.org/){:target='_blank'} (yeah, they are different) offers more customization and is also easy to use, but is sometimes not as cheap to set up. [Squarespace](http://www.squarespace.com/){:target='_blank'} is also a bit more expensive, but their sites are famously good looking are are also easy to use.

If you have the technical skills or are brave enough to learn a bit, I think that [Jekyll](https://jekyllrb.com/){:target='_blank'} is a particularly good option for blogging about medieval topics. Since a Jekyll blog is basically a lightweight [static website](https://en.wikipedia.org/wiki/Static_web_page){:target='_blank'}, you can customize a Jekyll blog however you like, which is really handy for writing about a topic with specialized style requirements like medieval studies.

### Cool stuff that comes out of the box
There are tons of nice, free [Jekyll themes](http://jekyllthemes.org/){:target='_blank'} and tutorials that make it easy to get up and running. I based my blog on [Aron Bordin's](https://github.com/aron-bordin){:target='_blank'} [Neo-HPSTR](http://aronbordin.com/neo-hpstr-jekyll-theme/){:target='_blank'} theme, which has a clean, responsive design, [Octopress](http://octopress.org/){:target='_blank'} page generation, [kramdown](http://kramdown.gettalong.org/){:target='_blank'} for flexible and programmatic [Markdown](https://daringfireball.net/projects/markdown/){:target='_blank'}, and includes built-in [Rouge](http://rouge.jneen.net/){:target='_blank'} syntax-highlighting which will be particularly nice when I get on to writing about technical aspects of the project:

{% highlight ruby %}
# A little Ruby example
def hwaet
  if !current_user.old_english.nil?
    puts "Hwæt we gardena in geardagum"
  else
    puts "You should learn some Old English!"
  end
end
{% endhighlight %}

### Customization is diacritically important
It is also easy to customize the look and feel of the site and add assets and styles to handle font requirements for medieval texts. Because I would like to use medieval diacritics, I added Junicode to my fonts folder, extended the theme’s HTML `<blockquote>` tag style with a new `.medieval` CSS class, *et voila*, I have a nice and easy way to include all of the special diacritics I could ever want:

>[S]eint birin þe confessor þt godma̅ was y nou  
þe tou̅ of rome was ibore & to eche godnesse drou  
to clannesse he drou wel ȝong & to pena̅uce also  
þ᷑ inne he wex so al wei as he miȝte hit do  
þt eche dai hadde somdel nwe þt hi̅ ou᷑ spᷓng [206r]  
& þe latt᷑ dai more & more nere he no so long  
me þingþ such wexing was god he so miȝte also  
wide spᷓng his gode los as hit moste nedes do[^1]  
{: .medieval }

Lastly, you can host your Jekyll blog at [GitHub Pages](https://pages.github.com/){:target='_blank'} for free, which makes it very inexpensive and publishing is as easy as [`git push origin master`](https://help.github.com/articles/pushing-to-a-remote/){:target='_blank'}.

### Wait. What about Mr. Hyde?
A Jekyll blog is probably not for everyone. If you are not used to writing in a text editor and and are just looking to get up and blogging, a blog like Wordpress with a graphical [CMS](https://en.wikipedia.org/wiki/Content_management_system){:target='_blank'} is probably what you should go for. (By all means, do blog about your projects! It is useful to everyone to check out what you are working on.) However, if you willing to use the [command line](https://en.wikipedia.org/wiki/Unix_shell){:target='_blank'}, know a little bit of HTML/CSS, and have heard of [Git](https://en.wikipedia.org/wiki/Git_(software)){:target='_blank'}, Jekyll is a highly customizable, free, and open-source blogging tool that seems particularly useful for people writing about specialized subjects like medieval studies.

[^1]: Check out this sweet footnote too! This is the first eight lines of the Life of Birinus from London, British Library, Cotton Julius D.IX, fols. 205v–207r.