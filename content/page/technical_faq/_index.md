---
weight: 3
bookFlatSection: true
title: "Technical FAQ"
date: "2025-06-06"
lastmod: "2025-06-06"
---

# Technical FAQ

This document addresses common technical questions about Mwmbl's architecture, scalability, and design decisions.

If you have more technical questions, feel free to ask them in the issue section and we'll curate the answers here.

## Implementation Details

### How do you handle popular query terms that cause linear search bottlenecks in TinyIndex pages?

While we do have the limitation that we don't store all good results for popular query terms, most users only care about the top 10 results. If they need something more specific, they can add another query term to refine their search.

The hash-based page distribution means popular terms might fill up their assigned pages, but the system is designed around serving the most relevant results rather than exhaustive results.

### How do you ensure search results stay current without link expiration?

The monthly recycling of bloom filters provides an element of recency - we know the approximate age of links based on which bloom filter cycle they were crawled in. This helps prioritize newer content over older links.

### Why use a hash map design instead of traditional inverted indexes?

The design is founded on the observation that most items rank for a small set of terms. In the extreme case where each item ranks for a single term, traditional inverted indexes are grossly inefficient since each term must be stored twice: once in the index and once in the item data.

Our hash map design:
- Uses a single store of fixed-size pages (currently 4096 bytes each)
- Each page contains a compressed list of items
- Term queries hash to specific pages for O(1) lookup
- Compression allows ranking for multiple terms while maintaining efficiency

### How does the tiny search engine indexer work?

The `TinyIndex` class provides:
- **Fixed page size**: Each page is exactly 4096 bytes (matching memory pages)
- **Compression**: Uses zstd compression to fit more items per page
- **Binary search optimization**: Uses binary search to find the maximum number of items that fit on a page
- **Memory mapping**: Uses mmap for efficient file access
- **Configurable serialization**: Supports different item types via generic factory functions

### How do you handle the trust for distributed crawlers or curation without dynamic reputation scoring?

TODO

### What is the process to get an API key for crawling for mwmbl.org?

TODO

### How does the distributed crawler coordinate to avoid duplicates?

TODO: Explain the deduplication mechanisms across multiple crawler instances.

### Where do the URLs to crawl originate and how are they prioritized?

TODO: Document the sources of URLs for crawling and the prioritization mechanisms. Note: The new crawler chooses its own URLs, but the complete process needs explanation.

## Architecture & Scalability

### Can Mwmbl scale to web-scale with a single node architecture?

We believe the single node can get to web scale if we're careful about resource usage. Currently (mid 2025) the main constraint is disk space rather than computational power.

Additionally:
- The reranking algorithm was rewritten in Rust to run in WASM on the browser, so the server now just needs to retrieve static files as the reordering happens on the client side.
- The crawler-v2 should fix the CPU usage issues from the old crawler-v1.

### How do you handle the CPU-intensive zstd compression and concurrency issues?

If that starts to become an issue, we can switch from zstd to gzip compression and return the gzipped content directly, letting the client handle decompression. This would reduce server CPU load and improves concurrency.

### Don't bloom filters have scalability issues with false positive rates?

The bloom filters are recycled every month, which provides both recency and prevents the false positive rate from growing unbounded. We can recycle them more frequently if necessary as the system grows.

### What are the plans for handling exponential data growth?

TODO: Discuss strategies for when the index grows beyond single-machine storage capacity.

### What is the current total index size in MB?

As of mid-2025: TODO

### Does Mwmbl index images? Files like PDF?

No, images and files are completely ignored at the crawling step. Mwmbl currently focuses exclusively on HTML webpage content for indexing. This design choice helps maintain simplicity and keeps the index focused on textual content that can be effectively searched.

This may change in the future once the feasibility of webpage crawling at scale is established and the core search functionality is robust.

### What are the performance characteristics of the current implementation?

TODO: Provide benchmarks for query latency, indexing throughput, and memory usage patterns.

## Future Technical Directions

### As an open source search engine, how does Mwmbl plan to avoid SEO enshitification?

TODO

### Can LLM and other "AI" tech be useful for Mwmbl?

We're definitely thinking about using LLM-based tech for some aspects of the stack, notably to replace the modulo hashing (LSH) of the tokenized search query. However, any LLM approach would need to run in the browser to maintain our client-side processing architecture. ModernBERT or [model2vec](https://github.com/MinishLab/model2vec/) look promising in that regard.

### Can Mwmbl evolve into something fully distributed?

TODO: Explore the feasibility and roadmap for transitioning from single-node to fully distributed architecture, especially as more of the architecture gets done on the client side anyway.



