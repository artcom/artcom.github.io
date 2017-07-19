---
layout: post
title: 'Why we choose the Hippo CMS?'
author: <a href="https://github.com/mask/">Martin Skinner</a>
excerpt_separator: <!--end-of-excerpt-->
---
Nearly all so-called CMS are actually Website-Authoring Tools - i.e. they assist the user in building a website. This is the case with WordPress, Drupal, etc... Basically, they all allow the author to add pages, change the layout, add images at arbitrary positions in the text, etc. We need something which is literally a pure **Content** Management System and does not empower the author into thinking he is buildng a website - our use case is much closer to _Digital-Signage_. 
<!--end-of-excerpt-->

* Focus on Content, including complex, custom content types
* Look and feel is programmed into our exhibits, and is not authorable via CMS.
  The CMS should therefore not allow structure, layout or styles to be edited.

A new family of CMS have popped up recently, dubbed "headless CMS" or "decoupled CMS" which fit our use case much better.

* Most have a SaaS business model (such as
  "Contentful":https://www.contentful.com/). No self-hosting is offered, which
  is a deal-breaker with our corporate customers.
* Others have an architectures or are written in languages which makes
  customization difficult (such as "Directus":https://getdirectus.com, PHP) 

Hippo-CMS has the following pros:

* Open Source Community Edition as well as a commercial version with many
  additional features for corporate use:
 * Support for Active Directory, Oracle and MS databases, clustering, etc.
* Written in Java, runs with Tomcat (high acceptance with corporate customers)
* Nice Architecture: Hippo is a framework, not a system.
