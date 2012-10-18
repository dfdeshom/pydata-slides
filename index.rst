.. include:: <s5defs.txt>

================================
Wikipedia Indexing and Analysis
================================

:Author:  Didier Deshommes
:Date:    $Date: 2012-06-24 11:00:00 -0500 (Sun, 24 Jun 2012) $

.. This document is copyright Didier Deshommes

Meta Information
----------------

**Me**: I've been using Python for a long time. I work at Parse.ly, a
  tech startup in digital media space. We build web analytics systems  and APIs for publishers on the web.

**E-mail me**: didier@parsely.com

Why Wikipedia?
----------------
Wikipedia as a data source is very attractive:

.. class:: incremental

   * the number of articles rivals traditional corpora
   * the number of topics covered is broad
   * the Wikimedia Foundation ensures this data is free and publicly
     accessible to everyone

What This Talk Covers
---------------------
.. class:: incremental

   * indexing Wikipedia data on MongoDB and Solr
   * indexing Wikipedia traffic data on MongoDB and Solr
   * using Wikipedia data and Solr for traditional NLP tasks 

Indexing Wikipedia data 
-----------------------------

The Wikimedia Foundation project hosts dumps from all its projects at
http://dumps.wikimedia.org/

Useful page for knowing where to download what:
http://en.wikipedia.org/wiki/Wikipedia:Database_download

Link to latest XML dump of Wikipedia:
http://download.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2

The file is (predictably) huge, so use incremental parsing:
http://effbot.org/zone/element-iterparse.htm


Data peculiarities
----------------------------------

Not all Wikipedia pages are created equal. We are not interested in

.. class:: incremental

   * redirect pages: page with no content that send the
     reader to another article
   * special pages: pages whose title stats with ``Wikipedia:``
   * stub pages: pages that do not exist yet


Data to be extracted from a page
--------------------------------

    * page title
    * links in the page
    * categories the page belongs to
    * page text

Indexing to Solr
--------------------------------
.. class:: incremental

    * we will index and store titles to the ``title`` field 
    * we will index links to the multi-valued ``links`` field 
    * we will index categories to the multi-valued ``categories`` field 
    * we will index title and page text to the ``text`` field


Fiding topics in documents using the corpus
-------------------------------------------
Brute-force algorithm::

      for each sentence in text: 
          query wikipedia corpus, retain say, top 10 results
      out of these 10n results, select the most frequent ones

Fiding topics in documents using the corpus (2)
------------------------------------------------
There are 2 ways to select the most frequent articles:

    * taking the top ``y`` articles (top 5, 10)
    * taking the top ``y%`` of articles (top 1%, 10% )

(Show run Example and results)
------------------------------------------------


Analysis
------------------------------------------------
Drawbacks and work-arounds:
    * The running time complexity depends on the number of
      sentences in the candidate text and the number of results to be retained
          
    * We could submit 2 or more sentences at a time, which would cut
      on the number of queries but provide slightly less accurate results

(Show run Example?)
------------------------------------------------
Example

Using Redirects for topic Aliasing
-------------------------------------
These pages are the same:
    * ``Barack_Obama``
    * ``Barack_Hussein_Obama`` (redirect)

``Barack_Obama`` has over 30 redirects. This could be used to whittle
down the number of topics extracted from a text.


Indexing Wikipedia traffic data 
----------------------------------
Contains page view statistics from Wikipedia pages accessed: http://dumps.wikimedia.org/other/pagecounts-raw/

Useful for determining near-real-time trends on the web. 

Updated every hour.

Let's build a ``Trending Now`` page.

Parsing traffic data 
----------------------------------------

Pages are stored in a predictable way, broken down by hour.

Line format:
   * project name 
   * page title
   * number of requests to page
   * page size    

Example entry: ``fr.b Special:Recherche/Achille_Baraguey_d%5C%27Hilliers 1 624``

Saving traffic data 
----------------------------------------
We are not interested in every page, just pages that display Wikipedia
articles (including redirects). we will exclude the following pages
that start with these strings:

.. sourcecode:: python

    set(["Special:", "Wiktionary:", "Category:",
         "Wikinews:", "Subject:","Mediawiki:",
         "File:","Wikiversity:", "Topic:", "User_talk:",
         "Appendix:","Wikibooks:","Wikijunior:",
         "Portal:", "Data:", "Wikipedia:", "Wikiquote:",
         ">","<","Wikt:","Wikisource:","Wikipedia_Talk:",
         "Help:","]","[","Mediawiki_Talk:","Talk:","Image:",
         "jquery","Main_Page","404_error","index.html",".css(",
         "this.elem","User:","Template:"
         ])

We also exclude pages that have more than 1 slash (``/``) in them
and pages that have very low page views (say, less than 5 views)

What about redirects?
----------------------------------------
``Barack_Hussein_Obama`` and ``Barack_Obama`` will not have the same
number of page hits, even though they will refer to the same page.

The best solution to this is to sum up all the hits from redirect
pages for a 'canonical' page. 

Knowing when a traffic spike occurs 
----------------------------------------
We need to know the *total* number of page views occur for a period of
time

Wikipedia has this data stored in their project-level counts

Can be found with traffic data, also broken down by hour.

Line format: 
    * Project name; generally indicates languages (en, fr, etc)
    * total page views for period
    * total bytes transfered for period 

Example line ``en - 8758112 248450166157``

Traffic spikes uses
----------------------------------------

With this, we can determine whether the traffic to a page has really
spiked beyond some baseline (ie, weekend, vs Monday traffic)

We can also determine which page has seen the most increase in page
views *in percentage*. For example:

    * page ``A`` normally has 100 view, but gets 200 views
    * page ``B`` normally has 1000 views, but gets 1200 views

The spike to page ``A`` is more interesting because the change in
percentage is greater for it

A traffic spike example (and validation)
------------------------------------------
Consider the presidential debate that took place on 2012/10/03. Most
people think Romney "won" that debate. Could that be reflected in the
number of pages accessed in Wikipedia?

.. class:: incremental

    * On 2012/10/03, ``Barack_Obama`` got around 100K views
    * On 2012/10/04, ``Barack_Obama``  tripled to around 300K views
    * On 2012/10/03, ``Mitt_Romney`` got around 150K views
    * On 2012/10/04, ``Mitt_Romney``  quintupled to around 800K views

Reflects both the public's renewed interest in both candidates and
the consensus that Mitt Romney "won" that debate 

A trending topics validation
------------------------------------------


What about redirects?
----------------------------------------
 Examples : 
 ``http://174.143.145.74/wikistats/demand/Barack_Obama``

 ``http://174.143.145.74/wikistats/demand/Barack``



Finding how close 2 search terms are
-------------------------------------
.. ngd


Finding related articles 
-----------------------------------
Solr's ``MoreLikeThisSupport`` makes this easy.


.. SS
.. ---------------------

.. .. sourcecode:: python

..     >>> nums = [45, 23, 51, 32, 5]
..     >>> for idx, num in enumerate(nums):
..     ...    print idx, num
..     0 45
..     1 23
..     2 51
..     3 32
..     4 5
