---
layout: post
title: Transcription and Programmatic Markup for the DigitalSEL
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
date: 2016-04-30T13:55:24-04:00
---
Medieval writers were keenly aware that their basic mode of textual reproduction, scribal transmission, introduced all sorts of variation and errors into their texts. Ælfric of Eynsham, the most prolific Old English author, included a well-known colophon in many of his works in which he prays that:

>hwá þas bóc awritan wylle þæt hé hí geornlice gerihte be ðære bysne  
whoever wishes to copy this book, will eagerly correct it according to the exemplar.[^1]  
{: .medieval }

More than three hundred years later, Chaucer complains that he is forced to “correcte” transcriptions of his own work after his scribe negligently copied them.[^2]

![Scribal Error]({{site.url}}/images/scribal_error.gif)
*Scribal transmission is error prone.*
{:style='height: 20%; width: 20%; margin-right: 10px;'}
{: .image-left }

As modern editors and scholars, we would do well to consider Ælfric and Chaucer’s complaints when we transcribe and reproduce texts we find in manuscript form. To say nothing of the errors we might introduce in a transcription, the editorial decisions we make when we represent abbreviations, flaws, or other textual features have potential to change our texts in ways that might have struck our medieval subjects as negligent.

In this post, I describe my strategy for transcribing and editing the DigitalSEL. Ultimately, my goal is to create the most conservative machine-readable transcription of each manuscript text that I can, and then use programming to re-write and store these transcriptions in other formats. My goal is to make an edition that has different strata of intervention, rather than an edition that tries to represent editorial decisions in one pass.

### How Print Editions Deal with Manuscript Readings

Print editions represent manuscript readings in ways that are bound to the physical limitations of the medium. It is just really difficult to represent complicated manuscripts on a printed page. Abbreviations are a good example of this constraint. Though it could be possible to use a font face with medieval characters to make a printed edition, they are hard for modern readers to understand without training. Print editions, therefore, tend to either silently expand abbreviations or expand them and set the abbreviation in italic font in order to indicate to interested readers where the abbreviations were. Take, for example, this little line from the London, British Library, Stowe 949 version of Saint Michael:[^3]

![Saint Michael Snippet]({{site.url}}/images/michael_snippet.png)
{: .image-center}

I might transcribe this for print like this:

>Aft*er* þ*a*t vr lord vor vs  in is moder was alyȝt
{: .medieval }

This is fine, of course, but we don't really get any information about exactly what the abbreviation was in my transcription, and it comes into the world in an altered form.

### Using Metadata to Deal with Manuscript Readings

>XML is like violence — if it doesn’t solve your problems, you are not using enough of it.[^4]

![Excited]({{site.url}}/images/really_excited.gif)
*Library science folks get really excited about metadata.*
{:style='height: 35%; width: 35%; margin-left: 10px;'}
{: .image-right }

A popular way to capture some of this extra information in digital editions is to use XML and, specifically, an XML schema established by the [Text Encoding Intuitive](http://www.tei-c.org/index.xml){:target='_blank'}, or TEI.[^5] In this editorial strategy, an editor uses XML tags to wrap text with metadata. After a complete TEI encoded transcription is made, editors use XLST to parse or transform the transcription into something reader-friendly.[^6]

A really simple TEI version of our line from Saint Michael might look something like this:

{% highlight xml %}
<l id="some_id_number">
    <abbr>aft&er;</abbr>
    <expan>after</expan>
    <abbr>þ&tsup;</abbr>
    <expan>þat</expan>
    vr lord vor vs <pc>&punctelev;</pc> in is moder was alyȝt
</l>
{% endhighlight %}

Though using TEI encoding is probably the most common strategy for making a modern digital edition, it has some problems. Besides the trouble of working with complex XML and the difficulty of parsing and transforming it into something that is easy for humans to read, it produces documents with editorial decisions hard-coded into them. If I change my mind about how to handle an abbreviation, for example, it would mean that I would have to go back and emend my original transcription.

### A Layered Approach

Rather than start with XML, I plan to create conservative transcriptions and then layer on any encoding or editorial decisions in subsequent versions of the text. For the DigitalSEL, this will happen in two steps:

1. First, I will use [Junicode](http://junicode.sourceforge.net/){:target='_blank'} or [Andron Scriptor](http://folk.uib.no/hnooh/mufi/fonts/#Andron){:target='_blank'} to make very close Unicode transcriptions of each text. These transcriptions will represent the base version of my edition and I am going to try to make them with as little editorial intervention possible.
2. Second, I will write text parsers that will convert these transcriptions into other formats and use these as the basis of any editorial versions.

![String Manipulation]({{site.url}}/images/string_manipulation.gif)
*It is easy to use Ruby for string manipulation.*
{:style='height: 35%; width: 35%; margin-left: 10px;'}
{: .image-right }

On the first front, I am lucky to be working on this project at a moment when there has been some great work done on Unicode fonts for medieval studies.[^7] This makes it possible to use recently made font characters to create a fairly good digital representation of most of the things one might find in a medieval or ancient text. Granted, converting a manuscript into any type of font will obscure paleographic and orthographic information, but fonts make it possible to do something pretty amazing — search and parse a text programatically. Good Unicode encoded transcriptions will make it possible to program parsers to rewrite these texts in any way we want. Let's take our line from Saint Michael for an example.

First, we make a close Unicode transcription of our line, something like this:

>Aft͛ þᵗ vr lord vor vs  in is moder was alyȝt
{: .medieval }

Then we can use this text in a little Ruby string parser.[^8]

Let's store the line in a variable:[^9]
{% highlight ruby %}
text = "Aft͛ þᵗ vr lord vor vs  in is moder was alyȝt"
# The text editor has trouble rendering "punctus elevatus", but trust me, it's there!
{% endhighlight %}

Next, let's store the special "er" abbreviation in a separate variable.

{% highlight ruby %}
text = "Aft͛ þᵗ vr lord vor vs  in is moder was alyȝt"
er = /͛/ # I am using a "regular expression" here rather than a string.
# It looks a little wacky because the character is set above the one in front of it.
{% endhighlight %}

Now, we can use `gsub` to replace the abbreviated character with "er" and, just for the fun of it, I'm going to wrap it in some HTML:[^10]

{% highlight ruby %}
text = "Aft͛ þᵗ vr lord vor vs  in is moder was alyȝt"
er = /͛/
puts text.gsub(er, "<em>er</em>")
# This renders "Aft<em>er</em> þᵗ vr lord vor vs  in is moder was alyȝt"
{% endhighlight %}

If we flex our muscles a little bit, we can extend the standard `gsub` method into something that will accept `key => value` pairs so that we can replace as many characters as we want in a single pass:[^11]

{% highlight ruby %}
class String
  def mgsub(key_value_pairs=[].freeze)
    regexp_fragments = key_value_pairs.collect { |k,v| k }
    gsub(Regexp.union(*regexp_fragments)) do |match|
      key_value_pairs.detect{|k,v| k =~ match}[1]
    end
  end
end
text = "Aft͛ þᵗ vr lord vor vs  in is moder was alyȝt"
replacements = [[/͛/, "<em>er</em>"], [/ᵗ/, "<em>a</em>t"]]
puts text.mgsub(replacements)
# This gives us "Aft<em>er</em> þ<em>a</em>t vr lord vor vs  in is moder was alyȝt"
{% endhighlight %}

Rendered in the browser with a medieval font, our line looks like an edited text:

> Aft<em>er</em> þ<em>a</em>t vr lord vor vs  in is moder was alyȝt
{: .medieval }

If we make our initial transcriptions in CSV format, then we will also be able to manipulate entire lines of poetry because we can use `regexp` to detect and iterate over new line characters.[^12]

This is just a little example, but I hope it demonstrates the ease with which we can start with a conservative Unicode transcription and rewrite the text any way we might feel like. This strategy has the advantage, I think, of creating a base document that won't be altered too much by editorial intervention and then enabling us to programmatically rewrite the text, layering in either XML metadata for storing information or HTML for display.

[^1]: My translation. The colophon is found in a number of Ælfric’s works. This quotation, with abbreviations silently expanded, is taken from Peter Clemoes, ed., *Ælfric’s Catholic Homilies: The First Series*, EETS s.s. 17 (Oxford: Oxford University Press, 1997), Old English Preface, 177; see also Malcolm Godden, ed., *Ælfric’s Catholic Homilies: Second Series*, EETS s.s. 5 (Oxford: Oxford University Press, 1979), Latin Preface, 42–9; *Ælfric’s Lives of Saints*, ed. and trans. W. W. Skeat, 2 vols. in 4 pts. EETS o.s. 76, 82, 94, 114 (London: Oxford University Press, 1881–1900), I, 1.74–76; Julius Zupitza, ed., *Aelfrics Grammatik und Glossar* (Berlin: Weidmann, 1880), 3.20–25; and Richard Marsden, ed., *The Old English Heptateuch and Ælfric’s Libellus de Veteri Testamento et Novo* I, EETS o.s. 330 (Oxford: Oxford University Press, 2008), 80.117–21. The prefaces were edited as a group by Jonathan Wilcox, ed., *Ælfric’s Prefaces*, Durham Medieval Texts 9 (Durham: Durham Medieval Texts, 1994).
[^2]: This short poem goes by many names, including ["His Owne Scriveyn"](http://www.bartleby.com/258/61.html){:target='_blank'}.
[^3]: An image of the entire page is available from the [British Library](http://www.bl.uk/catalogues/illuminatedmanuscripts/ILLUMIN.ASP?Size=mid&IllID=6467){:target='_blank'}.
[^4]: From the [Nokogiri](https://rubygems.org/gems/nokogiri/versions/1.6.7.2){:target='_blank'} page of [rubygems.org](https://rubygems.org/){:target='_blank'}.
[^5]: XML stands for [Extensible Markup Language](https://en.wikipedia.org/wiki/XML){:target='_blank'}. It is basically a very flexible markup language that makes it easy to append metadata to text.
[^6]: XSLT stands for [Extensible Stylesheet Language Transformations](https://en.wikipedia.org/wiki/XSLT){:target='_blank'}.
[^7]: All hail the [Medieval Unicode Font Initiative](http://folk.uib.no/hnooh/mufi/){:target='_blank'}!
[^8]: I am using Ruby, but I bet that this would be easy and probably even process faster in Python.
[^9]: We don't have to store our text in a variable, but it makes it easier to look at. Programming languages treat different types of data differently. Our medieval transcriptions are "strings," or strings of characters.
[^10]: `gsub` is a [standard Ruby String class method](http://ruby-doc.org/core-2.1.4/String.html){:target='_blank'} for iterating over a string and replacing things in it.
[^11]: Thanks to Lucas Carlson's [*Ruby Cookbook*](http://www.amazon.com/gp/product/1449373712/ref=pd_lpo_sbs_dp_ss_2?pf_rd_p=1944687742&pf_rd_s=lpo-top-stripe-1&pf_rd_t=201&pf_rd_i=0596523696&pf_rd_m=ATVPDKIKX0DER&pf_rd_r=0WB5E4SEHRTRXAFZZADB){:target='_blank'} for this.
[^12]: `regexp` or ["regular expression"](https://en.wikipedia.org/wiki/Regular_expression){:target='_blank'} is a powerful pattern-matching method found in most computer languages. CSV stands for [Comma Separated Values](https://en.wikipedia.org/wiki/Comma-separated_values){:target='_blank'}, AKA spreadsheets.
