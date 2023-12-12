---
weight: 3
bookFlatSection: true
title: "Guidelines for Curating Search Results"
date: "2023-12-12"
lastmod: "2022-12-12"
---

# Guidelines for Curating Search Results

Thank you for helping improve Mwmbl search results! Please read these
guidelines before you make changes.

This is a living document and will be updated based on the concensus
of the community.

## Summary

TLDR; edit the results to make them better for users, not for
self-promotion.

## General guidelines

The goal of editing search results is twofold:

 - To improve the search results for the user.
 - To train a [learning to rank](https://en.wikipedia.org/wiki/Learning_to_rank) model. 
   No matter how many queries are manually curated, most user queries
   will be organic because of the natural diversity of user
   queries. Curation is still important for these results since it
   impacts the machine learning model that will be trained on the
   curated rankings.
  
Results should be:

 - Relevant to the user's query
 - High quality

It is also important _which_ queries are curated.

## Choosing queries to curate

Good queries to curate are those that are likely to be popular
queries. The best way to identify these are through using Mwmbl as
your regular search engine. If you know that your query is
particularly obscure, then it is probably not a good candidate for
curation.

Please do not curate queries if:

 - They are obscene, defamatory or discriminatory
 - They contain mis-spellings - we have not implemented spelling
   correction yet, but curation is not the way to fix it

## How to curate results

Search for something on [Mwmbl](https://mwmbl.org). Click the "tick"
(✓) button for each relevant result. The results you approve will go
to the top of the search results.

The interface for this is in active development and will likely
change.

### Co-opting results

The [Mwmbl Firefox extension](https://addons.mozilla.org/en-GB/firefox/addon/mwmbl-web-crawler/)
now has an option to search Google and add these into the search
results. When typing in the search bar on the Mwmbl website, you need
to press "Enter" to see the Google results. To prevent sending too
many queries to Google, we do not query Google as you type.

When you approve (✓) results from Google, they are saved to our
index. This allows other users to benefit from Google search results
without needing to install the extension. Only results that you
approve are stored in our index.

### Adding results

If you know of a relevant result that is not returned by Mwmbl or
Google, you can add results manually by clicking the "Add new" button
and entering a URL.

## Relevance

Search results should contain the user's query or something
semantically equivalent. Sometimes this requires interpreting a user's
query.

Where a query is ambiguous, results for the most common
interpretations should be included. For example, a query for "apple"
can include results for Apple Computer as well as e.g. a Wikipedia
article on the fruit apple.

## Quality

The criteria for quality are:

 - Informative: How comprehensive is it? How well written?
 - Accurate: does it contain factual, typographical or spelling errors?
 - Authoritative: what is the source of the page? Is it created by a
   well respected organization or individual?
 - Privacy respecting: does it allow third-party tracking cookies?
 - Light-weight: how much bandwidth does it require to load the page?
   Does it load and play unnecessary videos and adverts?
 - Public domain: is the content released under a creative commons or
   open source license?

A page does not need to satisfy all of these criteria in order to be
considered high-quality, 

## Conflicts of interest

Editing a search results page where there is a conflict of interest is
not allowed. Specifically, self-promotion or promoting the site of an
employer, relative or associate is not allowed.



