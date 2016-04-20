---
layout: post
title: Tools for the DigitalSEL
modified:
categories:
description:
tags: []
image:
  feature: ploughing.png
  credit:
  creditlink:
comments:
share:
date: 2016-04-17T12:31:12-04:00
---

### *Conflabunt gladios suos in vomeres* [^1]

In *passus* VI of *Piers Plowman* appears Langland's famous allegorical description of utopian labor&mdash;the plowing of the half-acre. Here, Piers the Plowman describes the purpose of his work and, notably, the tools with which he does it:

>I wil worschip þer-with · treuthe bi my lyue  
And ben his pilgryme atte plow · for pore mennes sake  
My plow-[p]ote shal be my pyk-staf · and picche atwo þe rotes  
And helpe my culter to kerue · and clense þe forwes[^2]  
{: .medieval }

After I finished writing my last post, as I was [making a new project directory](https://en.wikipedia.org/wiki/Mkdir){:target='_blank'} and started looking around in the [Ruby Toolbox](https://www.ruby-toolbox.com/){:target='_blank'} to compare libraries, it occurred to me that I should probably offer some sort of description of the tools I plan to use to develop the project. Though I don’t really plan to make this a "how to program" blog, it would probably be useful to outline the technologies I will be talking about and why I think they will be effective for building the project.

Readers of this blog are doubtless aware of the degree of excitement right now in Medieval Studies for using digital technology in academic work. Though there are some really [impressive digital manuscript projects](http://www.bl.uk/manuscripts/Default.aspx){:target='_blank'} and [reference materials](http://quod.lib.umich.edu/m/med/){:target='_blank'}, there is opportunity for innovation in an old fashioned corner of [philology](https://en.wikipedia.org/wiki/Philology){:target='_blank'}: [textual studies](https://en.wikipedia.org/wiki/Textual_criticism){:target='_blank'}. I plan to write much more about the theoretical underpinnings of what I am up to later, but suffice it to say now that I am building a digital critical edition, and so I am fundamentally interested in storing, retrieving, and parsing texts. Although digital tools for making editions of Middle English texts don’t really exist, it should be possible to recast existing technologies for my purposes.

### Our Field to Plow: Some Basics

It sort of goes without saying that the internet is a great platform for a project like the DigitalSEL. It is widespread, does a great job of handling text, and is stable enough that you can watch live video on the subway, for goodness sake. What is less clear, however, are which tools and software would be appropriate for building this kind of digital edition. This is made more complicated because the computation for the internet happens at different stages and in different places: the browser and the server.


### How the Internetz Works:

![Cat surfing the web]({{site.url}}/images/internetz.png)
{: .image-center}

### Browsers
The work that is done in a web browser like [Chrome](https://www.google.com/chrome/browser/desktop/){:target='_blank'}, [Safari](http://www.apple.com/safari/){:target='_blank'}, [Firefox](https://www.mozilla.org/en-US/firefox/new/){:target='_blank'}, or (shudders) [Internet Explorer](https://en.wikipedia.org/wiki/Hamster_wheel){:target='_blank'} deals with a how a webpage looks and "acts." The browser renders content and structure on the page ([HTML](https://en.wikipedia.org/wiki/HTML){:target='_blank'}), implements the page’s style ([CSS](https://en.wikipedia.org/wiki/Cascading_Style_Sheets){:target='_blank'}), and determines the sort of behavior you might notice when a button changes color when you drag your mouse over it ([JavaScript](https://en.wikipedia.org/wiki/JavaScript){:target='_blank'}). The fact that this work happens on your personal computer or phone accounts for the fact that webpages look different in different browsers, like Chrome or Safari.

### Servers

![Daamn]({{site.url}}/images/daamn.gif)
*Checking your bank account after the book fair*
{:style='height: 35%; width: 35%; margin-left: 10px;'}
{: .image-right }

Most of the data-heavy computing determining what information is sent to your browser occurs on a server. When you log into your bank account, you are communicating with a server in order to fetch your specific information. Since your bank’s server is basically a special, big computer that you communicate with remotely, the programmer who set it up could install and use whatever software she thought would be good for the project. You can run whatever computer language or software you might like on one. Common server-side programming languages are [Java](https://en.wikipedia.org/wiki/Java_(programming_language){:target='_blank'}), [C++](https://en.wikipedia.org/wiki/C%2B%2B){:target='_blank'}, [PHP](https://en.wikipedia.org/wiki/PHP){:target='_blank'}, [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language){:target='_blank'}), and [Python](https://en.wikipedia.org/wiki/Python_(programming_language){:target='_blank'}).

### Model View Controllers
Over the years, a number of software frameworks written in these languages have emerged that make it easy to build a new web application complete with server-side components. For the most part, they include elements that constitute a popular pattern called a [Model View Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller){:target='_blank'} (MVC):

* **Model:** The model, as you could guess from [last week's post]({{site.url}}/data-modeling-for-the-digitalsel/), is the part of the application that maps onto and deals with objects in the database. When I wrote about how Aquinas `belongs_to` the Dominicans, I was talking about logic that is written in the model.

* **Controller:** The controller makes the decisions about the requests that it gets from users. When the cat above requested information about [Julian of Norwich](https://en.wikipedia.org/wiki/Julian_of_Norwich){:target='_blank'}, it was the controller that decided what to do with the request. After that, it sends the appropriate response back to the user.

* **View:** The view section of a MVC includes all of the code that gets sent to your browser so that you can “view” the information you requested. This includes all of the code that makes up the structure, style, and behavior discussed above.

So, for example with the DigitalSEL, if you were to choose to look at the "Life of Mary of Egypt," the controller would decided whether that was a reasonable request (it is!), and it would send that information along to the model. The model would then figure out if you had made any other special requests about the text you wanted to look at, and then it would retrieve Mary’s life from the database and send it back to the controller. After the controller heard from the model, it would then determine which views it needed, queue them up, and send them back to your browser which would display them for you to take a look at.

![bread_cat]({{site.url}}/images/bread_meme.jpg)
{:style='text-align: center; height: 70%; width: 70%; margin-left: 15%;'}
*In a medieval bread-meme, Mary of Egypt is famous for bringing three loaves with her into desert-exile.*

### The DigitalSEL on Rails
![RoR]({{site.url}}/images/rails-logo.svg)
{:style='height: 100%; width: 150px; margin-right: 15px;'}
{: .image-left }
There are a number of good MVC frameworks that could work for a project like the DigitalSEL, but I'm using [Ruby on Rails](http://rubyonrails.org/){:target='_blank'} (Rails). It is perfectly suited for what I want to do, is actively supported, has a ton of libraries, is easy to get running, and it has the strong advantage of being the tool I have and know. Additionally, Rails is completely free and [open source](https://en.wikipedia.org/wiki/Open-source_software){:target='_blank'}, meaning it is a distributed and community-supported framework that anyone can use.


### Caveat Lector

As I suggest above, I probably won't spend time explaining basic programming concepts (though I will link to them). Nevertheless, I *do* plan to document every single step I take on the project so it will be possible to follow along and learn from and build upon my work. If you are just interested in tracking my progress, some terminology will be helpful for understanding basic concepts in Rails development:

* **Command line:** It is ironic, but a great deal of modern web and software development takes place in an operating system developed in the 1970s called [Unix](https://en.wikipedia.org/wiki/Unix){:target='_blank'}. Programs like Unix or Linux are commonly called the "command line," sometimes abbreviated "CLi." On a Mac, the program that runs a born-again version of Unix is called [Terminal](https://en.wikipedia.org/wiki/Terminal_(OS_X)){:target='_blank'}.

* **Ruby:** [Ruby](https://www.ruby-lang.org/en/){:target='_blank'} is a programming language that was developed in the 1990s by a developer called "Matz," who, by all accounts, is a really nice fellow. Ruby is the language upon which Rails, the framework, runs.

* **Gem:** A [gem](https://rubygems.org/){:target='_blank'} is the Ruby term for a package of code that you can download and include in your own project. Gems are generally all free and community supported. They are called "modules" or "libraries" in other languages. For example, the [Prawn](https://github.com/prawnpdf/prawn){:target'_blank'} gem is a PDF generator that I plan to use in my project.

* **Git:** [Git](https://git-scm.com/){:target='_blank'} is a command line application for version control. Basically, it allows you to take a snapshot of your project so that if you mess everything up, you can go back and recover previous versions of your work. It is an extremely powerful tool, but is also very confusing. For example, Git is the program and GitHub is a website where people store Git repositories. I plan to blog about it in the future.

* **Text editor:** A text editor is just that: a program on your computer that reads and writes text documents. This is different from a document editor, like MS Word, in that a text editor will only save files in the format you tell it to. I use [Sublime Text](https://www.subimetext.com/){:target='_blank'}, but there are many others, including [Vi](https://en.wikipedia.org/wiki/Vi){:target='_blank'} or its younger cousin "VIM," which, believe it or not, you probably already have installed on your computer if you are currently reading this on a Mac. We will talk more about VIM when we have to do programming on the server.

**Next up,** I plan to walk through the first steps of building the project, explain what comes in a Rails app, and explain how to fire up the server so we can start building the database. I am hoping that the posts get shorter as I plan to get down to brass tacks and just talk about building the app and what to do about mark-up.

[^1]: The header image is from [London, British Library, Additional 42130 (The Luttrell Psalter)](http://www.bl.uk/manuscripts/FullDisplay.aspx?ref=Add_MS_42130){:target='_blank'}, fol. 170r.
[^2]: BX.6.105-107. Text from the [Piers Plowman Electronic Archive](http://piers.iath.virginia.edu/exist/piers/crit/docs/B/Bx/6/critical/0). A Modern English translation is [available from Harvard](http://sites.fas.harvard.edu/~chaucer/special/authors/langland/pp-pass6.html){:target='_blank'}
