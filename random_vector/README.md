# Random Vector Track

This track is designed for benchmarking filtered search on random vectors.

By default, the `flat` vector_index_type is utilized to evaluate the performance
of brute force search over vectors filtered by a partition ID.

## Indexing

Indexing runs in one of two modes, depending on whether `index_target_throughput` is specified.
The track launches `index_clients` parallel clients. Each client sends `index_iterations` bulk requests, with each request containing `index_bulk_size` documents.

The total number of documents indexed is:
`index_clients` × `index_iterations` × `index_bulk_size`

* If `index_target_throughput` is set, each client will send bulk operations at a rate of:
  `index_target_throughput` ÷ `index_clients` bulk requests per second.
* If `index_target_throughput` is not set, each client will send bulk operations as fast as possible.

### Document content and index layout

Each document indexed includes:

* A random vector with `dims` dimensions.
* A randomly assigned partition ID.

The index is sorted by partition ID. 
This ensures that vectors from the same partition are stored close together, improving the efficiency of filtered searches.

## Search Operations

Search operations involve filtering by a random partition ID and scoring against a random query vector. 
These operations are executed against the index using various DSL flavors, including script score and knn section.

### Parameters

This track accepts the following parameters with Rally 0.8.0+ using `--track-params`:

 - use_synthetic_source (default: true)
 - number_of_shards (default: 1)
 - number_of_replicas (default: 0)
 - vector_index_type (default: bbq_flat)
 - index_target_throughput (optional)
 - index_clients (default: 1)
 - index_iterations (default: 1000)
 - index_bulk_size (default: 1000)
 - search_iterations (default: 1000)
 - search_clients (default: 8)
 - dims (default: 128)
 - partitions (default: 1000)
 - rescore_oversample (default: 0)


### Parameters for ingest-autoscale challenge

- vector_index_type (default: bbq_flat)
- use_synthetic_source (default: true)
- data_retention (optional)
- dsl_rollover (optional)
- index_bulk_size (default: 100)
- partitions (default: 1000)
- Initial indexing:
  - initial_index_clients (default: 4)
  - initial_index_iterations (default: 100)
  - index_bulk_size` (default: 100)
- Parallel search and index:
  - warmup_time_period (default: 30): Warmup time period for the parallel index search operation in second.
  - time_period (default: 120): Time period for the parallel index search operation in second.
  - parallel_index_target_throughput (optional)
  - parallel_index_clients (default: 8)
  - parallel_search_target_throughput (optional)
  - parallel_search_clients (default: 8)
