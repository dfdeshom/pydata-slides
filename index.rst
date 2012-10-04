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

Extracting pages from the XML dump
----------------------------------

The file is (predictably) huge, so use incremental parsing:
http://effbot.org/zone/element-iterparse.htm

Data peculiarities
----------------------------------

Not Wikipedia pages are created equal. We are not interested in
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
