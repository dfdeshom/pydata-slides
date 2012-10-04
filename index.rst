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

Why Wikipedia?
----------------
Wikipedia as a data source is very attractive:


SS
---------------------

.. sourcecode:: python

    >>> nums = [45, 23, 51, 32, 5]
    >>> for idx, num in enumerate(nums):
    ...    print idx, num
    0 45
    1 23
    2 51
    3 32
    4 5
