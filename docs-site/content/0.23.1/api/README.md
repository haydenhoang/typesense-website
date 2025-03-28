---
sitemap:
  priority: 0.3
---

# Typesense API Reference for v{{ $page.typesenseVersion }}

This section of the documentation details all the API Endpoints available in Typesense and all the parameters you can use with them.

Use the links on the side navigation bar to get to the appropriate section you're looking for.

To learn how to install and run Typesense, see the [Guide section](/guide/README.md) instead.

<br/>

## What's new

This version `v0.23.1` fixes some important bugs in the recently released [`v0.23.0`](../../0.23.0/).

The changelog below contains aggregates all the changes between `v0.22.2` and `v0.23.x`.

### New Features

- Phrase search: wrap keywords in a query with double quotes to search them as a phrase, e.g. `"new york"`.
- Schema changes: we now support fields to be added or dropped from a collection schema in-place.
- Improved multi-field matching: much better performance and accuracy when query keywords have to be matched across
  multiple fields in a document.
- Infix searching: find string within strings, which is useful for entities like model number or email address.
- Allow string fields to be sorted: sorting on string fields can be enabled by setting `sort: true` parameter of the field.
- **[New in v0.23.1]** Support for `sort_by` parameter in override rule.

### Enhancements

- Improved update and delete performance of numerical fields by 10x.
- Emplace mode for imports: using the `emplace` action creates a document if it does not exist in a collection 
  or updates it (partially or fully) if it already exists.
- Treat space as typo: search for `basket ball` if `basketball` is not found or vice-versa. You can disable this behavior
  by setting `split_join_tokens: false`.
- Improved Cyrillic support: better highlighting and fuzzy search for fields configured with: 
  `el`, `ru`, `sr`, `uk` and `be` locales.
- ARM compatibility: an ARM build is now published for every release.
- Each multi-search query can have an independent `x-typesense-api-key` key. This is useful to specify different scoped search API keys for each search.
- Control the number of words that Typesense considers for typo and prefix searching via the `max_candidates` parameter.
- CORS can now be enabled for a specific set of domains using the `--cors-domains` flag.
- Search results are now highlighted by prefix, rather than the full world. 
  Previously, searching for "app" will highlight the full word "apple" in the results, but now it will only highlight the "app"le prefix within the word.
- "Remove Matched Tokens" can be used by itself in Overrides. So you can now setup rules like, if query contains "word", remove "word" from the search query.
- Ability to toggle if filters should by applied to overrides or not using the `filter_curated_hits` flag.
- Ability to hide `out_of` and `search_time_ms` from the search API response, using the `exclude_fields` parameter.
- Ability to control typo tolerance for facet queries using `facet_query_num_typos`.
- Ability to specify which subnet to use for peering using `--peering-subnet` server parameter.
- **[New in v0.23.1]** Improved filtering performance, upto 5x-10x faster when querying a small subset on large datasets.
- **[New in v0.23.1]** Allow word position in a field value to be used a ranking signal via the `prioritize_token_position` search parameter.
- **[New in v0.23.1]** Improved stability in rotation of geographically distributed clusters having a large dataset.
- **[New in v0.23.1]** ARM builds now work on more ARM processors (previously worked only on Graviton instances)
- **[New in v0.23.1]** Null / empty string values can be configured to be pushed to the end during sorting.

### Bug Fixes

- Fixed exact match of synonym query candidates not being ranked correctly.
- Fix glibc incompatibility on recent Linux distros (Ubuntu 21.04+). [#531](https://github.com/typesense/typesense/issues/531)
- Fixed the `snippet` containing the full field value when `highlight_full_fields` is enabled.
- Fixed `--enable-cors=true` flag format not working. Earlier, only the `--enable-cors` format worked.
- Fixed exact match for repeated words (when searching for repeated words such as "Boom Boom"). [#427](https://github.com/typesense/typesense/issues/427)
- Fixed `highlight_fields` parameter not respecting `include_fields`. [#556](https://github.com/typesense/typesense/issues/556)
- Fixed document ids that are accepted with space char (%20) but cannot be deleted later. [#574](https://github.com/typesense/typesense/issues/574)
- Improved highlighting of text containing punctuations. [#528](https://github.com/typesense/typesense/issues/528)
- Fixed case sensitivity of facet fields. [#504](https://github.com/typesense/typesense/issues/504)
- **[New in v0.23.1]** Fixed a potential crash caused by attempting to alter the `id` field.
- **[New in v0.23.1]** Fixed synonym queries not respecting the `prefix` setting.
- **[New in v0.23.1]** Fixed a rare bug present in exact filtering of string arrays.
- **[New in v0.23.1]** Fixed a race condition in slow request logging that led to a crash under high concurrency.
- **[New in v0.23.1]** Better validation of regular expression used in API key collection allow list.

### Deprecations / behavior changes

- In prefix queries, only the prefix part of a word in the result is highlighted now, instead of the whole word. 
  For e.g. given a query like "new y", the result will be highlighted as `<mark>New Y</mark>ork City`.

## Upgrading

Before upgrading your existing Typesense cluster to v{{ $page.typesenseVersion }}, please review the behavior
changes above to prepare your application for the upgrade.

We'd recommend testing on your development / staging environments before upgrading. 

### Typesense Cloud

If you're on Typesense Cloud:

1. Go to [https://cloud.typesense.org/clusters](https://cloud.typesense.org/clusters).
2. Click on your cluster
3. Click on "Modify Configuration" on the right side pane
4. Schedule a time for the upgrade.

### Self Hosted

If you're self-hosting Typesense, here's how to upgrade:

#### Single node deployment

1. Trigger a snapshot to [create a backup](cluster-operations.md#create-snapshot-for-backups) of your data.
2. Stop Typesense server.
3. Replace the binary via the tar package or via the DEB/RPM installer. 
4. Start Typesense server back again.

#### Multi-node deployment

To upgrade a multi-node cluster, we will be proceeding node by node to ensure the cluster remains healthy during the rolling upgrade.

**NOTE:** During the upgrade, we have to ensure that the leader of the cluster is using the **older** Typesense version. 
So we will upgrade the leader last. You can determine whether a node is a leader or follower by the value of the `state` 
field in the `/debug` end-point response.

| State | Role     |
|-------|----------|
| 1     | LEADER   |
| 4     | FOLLOWER |

1. Trigger a snapshot to [create a backup](cluster-operations.md#create-snapshot-for-backups) of your data 
   on the leader node.
2. On any follower, stop Typesense and replace the binary via the tar package or via the DEB/RPM installer.
3. Start Typesense server back again and wait for node to rejoin the cluster as a follower and catch-up (`/health` should return healthy). 
4. Repeat steps 2 and 3 for the other _followers_, leaving the leader node alone for now.
5. Once all the followers have been upgraded to v{{ $page.typesenseVersion }}, stop Typesense on the leader.
6. The other nodes will elect a new leader and keep working. 
7. Replace the binary on the old leader and start the Typesense server back again. 
8. This node will re-join the cluster as a follower, and we are done.

:::tip
This documentation itself is open source. If you find any issues, click on the Edit page button at the bottom of the page and send us a Pull Request.
:::

<RedirectOldLinks />
