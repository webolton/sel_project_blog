---
layout: post
title: Data Modeling for the DigitalSEL
modified:
categories:
description:
tags: []
image:
  feature: whiteboard.jpg
  credit:
  creditlink:
comments: true
share: true
date: 2016-04-09T15:17:12-04:00
---

### The Virtues of Planning

"Prudence," to paraphrase [Augustine](https://en.wikipedia.org/wiki/Augustine_of_Hippo){:target='_blank'}, "is the desire to choose wisely between the things that help and those that hinder."[^1]
Though it holds less moral imperative for the design of a digital edition, Augustine’s description of foresight is a useful maxim for these first steps on the DigitalSEL. Good data modeling and database design make a big difference in the viability of a project and how easy it will be to maintain. Fundamentally for the DigitalSEL, the database will store most of the text in the project, so it is important to design it with care.

![Magic]({{site.url}}/images/magic.gif)
*Tools of the Trade*
{:style='height: 35%; width: 35%;'}
{: .image-right }

I knew nothing about [relational databases](https://en.wikipedia.org/wiki/Relational_database){:target='_blank'} and [data modeling](https://en.wikipedia.org/wiki/Data_modeling){:target='_blank'} until I became a developer, but they have changed my life (for the better). A relational database is a very powerful and lightweight way of storing structured data that is easy to create, edit, destroy, and most importantly, connect with other data. Basically, a database table is conceptually similar to a spreadsheet with column headings and values assigned to each column.

For example, we could make a database table for a saint:[^2]

![Aquinas Table]({{site.url}}/images/aquinas_table.png)
{: .image-center }

It's pretty clear from a table like this that we can store and retrieve information about something specific like a saint — if we locate the `saint` table with the `:id`[^3] number 1, we would be able to discover that it happens to store information about [Aquinas](https://en.wikipedia.org/wiki/Thomas_Aquinas){:target='_blank'}. Our data becomes much more exciting and structured, however, if we create another table and create a relationship. For example, we could make a table for a `fraternal_order` and give it a `has_many` relationship to `saints`:

![Dominican Table]({{site.url}}/images/dominican_table.png)
{: .image-center }

Here, I specified that the `fraternal_order` table is for the Dominicans. I also edited Aquinas's table, by changing his `:fraternal_order` attribute to `:fraternal_order_id` and assigning it the Dominican's "primary key": their `:id` number. Aquinas is now linked to the Dominicans through the `:fraternal_order_id`, which can be called a "foreign key." If we specify in our data model that a `:fraternal_order has_many :saints` and a `:saint belongs_to :fraternal_order`, then we can query the database and ask it to return all of the `saints` who belong to the `fraternal_order` "Dominican." In other words, we can link database tables together in logical ways that will allow us to store information about inheritance, groupings, and other meaningful relationships.

### Naming Convention in the DigitalSEL

At its core, the data model for the DigitalSEL has to contend with the relationships between three main data types: the individual instantiations of a text, the manuscript where an instantiation of a text appears, and the conceptual "text" of a saint’s legend to which textual instantiation belongs. It should be immediately apparent that "text" is a confusing word in this context, and that giving these data useful and semantic names is a an important first step in creating a data model. After some back and forth, I have settled on the following nomenclature:

* **witness:** Though I was tempted to borrow the term "instantiation" from [object oriented programming](https://en.wikipedia.org/wiki/Object-oriented_programming){:target='_blank'}, I decided to use the word "witness" to describe individual manuscript versions of a text. It is the most important piece of data in the model because it stores the edition / transcription of the text.

* **manuscript:** It is probably self-explanatory, but this is the physical document where an *archetype* is recorded.

* **archetype:** This is the most abstract object in my data model. I decided to use the term *archetype* to describe the conceptual or "ideal" form of a text.[^4] It is used to describe an abstracted idea of the "original" or "intended text." When the DigitalSEL refers to the "South English Legendary Life of St. Oswald the King," it means the *archetype*, but when it refers to a specific manuscript version of the Life of Oswald, it means the *witness*.

### The Data Model for the DigitalSEL's Texts

Here is a diagram of the basic data structure I have designed for the DigitalSEL's textual edition:

![DSEL text table]({{site.url}}/images/dsel_table.png)
{: .image-center }

Because I am fundamentally interested in making a critical edition, the textual *witness* is at the center of my data model and the actual edition of each text will be stored in its `witness_text` attribute (OMG, more on that later). A *witness* belongs to one particular manuscript and a manuscript has only one of any single witness. An *archetype* will have many *witnesses* and will have a `one_to_many` relationship with *manuscripts* through *witnesses*. Lastly, I have decided that I am going to store textual notes in a separate table because I want to create a feature that will allow users to annotate sections of the a *witness* text in real time.

To work through an example, let’s take the Life of Oswald the King in MS BL, Egerton 1993:

  * The Life of Oswald recorded in the Egerton 1993 would be the *witness.* This *witness* `belongs_to` one *manuscript*, London, British Library, Egerton 1993.

  * The Egerton *witness* also belongs to the *archetype* "South English Legendary Life of Oswald," which `has_many :witnesses` because there are other versions of this text.

  * Since I have included a link between both the `manuscript` and `archetype` through the `witness` table, I can also say that the `archetype` will `have_many` different manuscript versions.

  * If I choose to create notes about this *witness* Life of Oswald, they will be saved in such a way that they would also be available to the *manuscript* object through the *witness* table. Since this is true for all *witnesses* associated with Egerton 1993, it will be possible to query the database starting with Egerton 1993, through each *witness*, and then return all of the *textual_notes*. This will tie all of the notes I might add to a *witness* to the *manuscript* it belongs to.

### Iteration, Iteration, Iteration

It is likely that the model will change as I actually start writing the database and generating the models, but prudence and a little database modeling can really help to get started down the right path. **Next up**, I plan to type [`rails new digital_sel`](http://guides.rubyonrails.org/getting_started.html){:target='_blank'} so that I can start testing some ideas about how to store texts.

[^1]: Augustine's actual phrasing treats the virtue allegorically: "prudentia, amor ea quibus adujvatur ab eis quibus impeditur, sagaciter seligens" in [*De Moribus Ecclesiae Catholicae et de Moribus Manichaeorum*](http://www.documentacatholicaomnia.eu/04z/z_0354-0430__Augustinus__De_Moribus_Ecclesiae_Catholicae_et_de_Moribus_Manichaeorum__MLT.pdf.html){:target='_blank'}, cap. 15.
[^2]: I used a web-based tool, [Creatly](http://creately.com/){:target='_blank'}, to draw these tables.
[^3]: In case you are wondering what is going on with these words with a colon in front of them, they are a special data type called a `symbol`. In order to cut down on memory use, most databases have different rules about the data types that you store in them. When you specify that a piece of data will be an `integer`, the database will only except integers in that space. A `symbol` looks a lot like a `string`, which is basically a word or string of characters. A `symbol` is special, however, because its is stored in memory in a special way that saves memory.
[^4]: "Archetype" has a lot going for it in this context. It is not specific like "legend" or "saint's life," less vague than "text" or "item," and accurately describes the fact that the *ursprüngliche* form of a text in the *SEL* doesn’t actually exist.