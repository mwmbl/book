---
weight: 3
bookFlatSection: true
title: "Developers"
date: "2023-02-05"
lastmod: "2023-02-05"
---

# Architecture
Note: This page is a work in progress - read everything with a grain of salt.

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