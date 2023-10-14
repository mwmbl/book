---
weight: 3
bookFlatSection: true
title: "Developers"
date: "2023-02-05"
lastmod: "2023-02-05"
---

# Architecture

## Domain architecture and handling multiple locales

We are currently using the following sub-domains:
 - mwmbl.org: hosting the search engine front end
 - api.mwmbl.org: hosting the API
 - book.mwmbl.org: this book

Eventually we will have multiple Mwmbl instances, one for each country
where there are enough volunteers to host an instance. In order to
support this we will have subdomains in the format
`xx.mwmbl.org` where xx is a [two letter country
code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). We prefer to
split the instances by country rather than language, since search
results will vary by location. Initially, however, we will not have
enough volunteers for many instances, so will start with
`en.mwmbl.org` which will be a global instance for the English
language.

The top level domain name `mwmbl.org` will switch to being a generic
home page with information about the Mwmbl community.


## Index Layout
The following diagram describes how the index is laid out:

![](/TinyIndexStorageLayout.png)

Key points to note:
1. The index consists of a single metadata page, and subsequent data pages. Each page has a size of `4096` bytes.
2. The metadata page stores information about the index such as 
    - The version it was created at
    - How many pages are in the index
    - The size of each page in the index
    - The data type of the items stored in the data page
3. Each data page consists of a list of items
    - The data type of item matches the data type in the metadata page (currently `Document`)
    - Each `document` data type consists of a `Title`, `URL`, `Extract`, and `Score`
    - Documents are stored sorted by `Score` (in descending order)
