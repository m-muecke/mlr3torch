# Materialize Lazy Tensor Columns

This will materialize a
[`lazy_tensor()`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)
or a [`data.frame()`](https://rdrr.io/r/base/data.frame.html) /
[`list()`](https://rdrr.io/r/base/list.html) containing – among other
things –
[`lazy_tensor()`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)
columns. I.e. the data described in the underlying
[`DataDescriptor`](https://mlr3torch.mlr-org.com/reference/DataDescriptor.md)s
is loaded for the indices in the
[`lazy_tensor()`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md),
is preprocessed and then put unto the specified device. Because not all
elements in a lazy tensor must have the same shape, a list of tensors is
returned by default. If all elements have the same shape, these tensors
can also be rbinded into a single tensor (parameter `rbind`).

## Usage

``` r
materialize(x, device = "cpu", rbind = FALSE, ...)

# S3 method for class 'list'
materialize(x, device = "cpu", rbind = FALSE, cache = "auto", ...)
```

## Arguments

- x:

  (any)  
  The object to materialize. Either a
  [`lazy_tensor`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)
  or a [`list()`](https://rdrr.io/r/base/list.html) /
  [`data.frame()`](https://rdrr.io/r/base/data.frame.html) containing
  [`lazy_tensor`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)
  columns.

- device:

  (`character(1)`)  
  The torch device.

- rbind:

  (`logical(1)`)  
  Whether to rbind the lazy tensor columns (`TRUE`) or return them as a
  list of tensors (`FALSE`). In the second case, there is no batch
  dimension.

- ...:

  (any)  
  Additional arguments.

- cache:

  (`character(1)` or
  [`environment()`](https://rdrr.io/r/base/environment.html) or
  `NULL`)  
  Optional cache for (intermediate) materialization results. Per
  default, caching will be enabled when the same dataset or data
  descriptor (with different output pointer) is used for more than one
  lazy tensor column.

## Value

([`list()`](https://rdrr.io/r/base/list.html) of
[`lazy_tensor`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)s
or a
[`lazy_tensor`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md))

## Details

Materializing a lazy tensor consists of:

1.  Loading the data from the internal dataset of the
    [`DataDescriptor`](https://mlr3torch.mlr-org.com/reference/DataDescriptor.md).

2.  Processing these batches in the preprocessing
    [`Graph`](https://mlr3pipelines.mlr-org.com/reference/Graph.html)s.

3.  Returning the result of the
    [`PipeOp`](https://mlr3pipelines.mlr-org.com/reference/PipeOp.html)
    pointed to by the
    [`DataDescriptor`](https://mlr3torch.mlr-org.com/reference/DataDescriptor.md)
    (`pointer`).

With multiple
[`lazy_tensor`](https://mlr3torch.mlr-org.com/reference/lazy_tensor.md)
columns we can benefit from caching because: a) Output(s) from the
dataset might be input to multiple graphs. b) Different lazy tensors
might be outputs from the same graph.

For this reason it is possible to provide a cache environment. The hash
key for a) is the hash of the indices and the dataset. The hash key for
b) is the hash of the indices, dataset and preprocessing graph.

## Examples

``` r
lt1 = as_lazy_tensor(torch_randn(10, 3))
materialize(lt1, rbind = TRUE)
#> torch_tensor
#>  0.4458  0.8765 -1.0486
#> -0.7363 -2.1589 -1.1278
#> -0.5703 -0.3349 -0.3037
#>  0.6554 -0.3373  0.2153
#>  1.1992 -1.9602  0.1317
#>  0.1289 -1.7932 -1.5333
#> -0.4634  0.2005 -2.2686
#>  0.8984  0.7810  0.1907
#>  0.5772 -2.0493 -1.0871
#>  0.1051 -0.4498 -0.3032
#> [ CPUFloatType{10,3} ]
materialize(lt1, rbind = FALSE)
#> [[1]]
#> torch_tensor
#>  0.4458
#>  0.8765
#> -1.0486
#> [ CPUFloatType{3} ]
#> 
#> [[2]]
#> torch_tensor
#> -0.7363
#> -2.1589
#> -1.1278
#> [ CPUFloatType{3} ]
#> 
#> [[3]]
#> torch_tensor
#> -0.5703
#> -0.3349
#> -0.3037
#> [ CPUFloatType{3} ]
#> 
#> [[4]]
#> torch_tensor
#>  0.6554
#> -0.3373
#>  0.2153
#> [ CPUFloatType{3} ]
#> 
#> [[5]]
#> torch_tensor
#>  1.1992
#> -1.9602
#>  0.1317
#> [ CPUFloatType{3} ]
#> 
#> [[6]]
#> torch_tensor
#>  0.1289
#> -1.7932
#> -1.5333
#> [ CPUFloatType{3} ]
#> 
#> [[7]]
#> torch_tensor
#> -0.4634
#>  0.2005
#> -2.2686
#> [ CPUFloatType{3} ]
#> 
#> [[8]]
#> torch_tensor
#>  0.8984
#>  0.7810
#>  0.1907
#> [ CPUFloatType{3} ]
#> 
#> [[9]]
#> torch_tensor
#>  0.5772
#> -2.0493
#> -1.0871
#> [ CPUFloatType{3} ]
#> 
#> [[10]]
#> torch_tensor
#>  0.1051
#> -0.4498
#> -0.3032
#> [ CPUFloatType{3} ]
#> 
lt2 = as_lazy_tensor(torch_randn(10, 4))
d = data.table::data.table(lt1 = lt1, lt2 = lt2)
materialize(d, rbind = TRUE)
#> $lt1
#> torch_tensor
#>  0.4458  0.8765 -1.0486
#> -0.7363 -2.1589 -1.1278
#> -0.5703 -0.3349 -0.3037
#>  0.6554 -0.3373  0.2153
#>  1.1992 -1.9602  0.1317
#>  0.1289 -1.7932 -1.5333
#> -0.4634  0.2005 -2.2686
#>  0.8984  0.7810  0.1907
#>  0.5772 -2.0493 -1.0871
#>  0.1051 -0.4498 -0.3032
#> [ CPUFloatType{10,3} ]
#> 
#> $lt2
#> torch_tensor
#>  1.8409 -1.6603 -0.1027  0.1131
#> -0.0947  0.3357  1.1116 -0.0871
#>  0.1818  0.0358  0.6230 -0.4388
#>  1.0388 -0.3414  1.2644 -0.0672
#> -0.5510 -1.1758 -0.5988  0.2328
#> -1.1481 -1.2162  0.0221 -0.5304
#> -0.8076 -1.0243 -1.3932  0.6068
#> -0.9630  1.2717 -0.6593  0.2943
#> -0.5638 -0.5540  0.1075  0.1769
#> -0.3778 -0.5159 -0.8521 -2.0174
#> [ CPUFloatType{10,4} ]
#> 
materialize(d, rbind = FALSE)
#> $lt1
#> $lt1[[1]]
#> torch_tensor
#>  0.4458
#>  0.8765
#> -1.0486
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[2]]
#> torch_tensor
#> -0.7363
#> -2.1589
#> -1.1278
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[3]]
#> torch_tensor
#> -0.5703
#> -0.3349
#> -0.3037
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[4]]
#> torch_tensor
#>  0.6554
#> -0.3373
#>  0.2153
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[5]]
#> torch_tensor
#>  1.1992
#> -1.9602
#>  0.1317
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[6]]
#> torch_tensor
#>  0.1289
#> -1.7932
#> -1.5333
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[7]]
#> torch_tensor
#> -0.4634
#>  0.2005
#> -2.2686
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[8]]
#> torch_tensor
#>  0.8984
#>  0.7810
#>  0.1907
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[9]]
#> torch_tensor
#>  0.5772
#> -2.0493
#> -1.0871
#> [ CPUFloatType{3} ]
#> 
#> $lt1[[10]]
#> torch_tensor
#>  0.1051
#> -0.4498
#> -0.3032
#> [ CPUFloatType{3} ]
#> 
#> 
#> $lt2
#> $lt2[[1]]
#> torch_tensor
#>  1.8409
#> -1.6603
#> -0.1027
#>  0.1131
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[2]]
#> torch_tensor
#> -0.0947
#>  0.3357
#>  1.1116
#> -0.0871
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[3]]
#> torch_tensor
#>  0.1818
#>  0.0358
#>  0.6230
#> -0.4388
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[4]]
#> torch_tensor
#>  1.0388
#> -0.3414
#>  1.2644
#> -0.0672
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[5]]
#> torch_tensor
#> -0.5510
#> -1.1758
#> -0.5988
#>  0.2328
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[6]]
#> torch_tensor
#> -1.1481
#> -1.2162
#>  0.0221
#> -0.5304
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[7]]
#> torch_tensor
#> -0.8076
#> -1.0243
#> -1.3932
#>  0.6068
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[8]]
#> torch_tensor
#> -0.9630
#>  1.2717
#> -0.6593
#>  0.2943
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[9]]
#> torch_tensor
#> -0.5638
#> -0.5540
#>  0.1075
#>  0.1769
#> [ CPUFloatType{4} ]
#> 
#> $lt2[[10]]
#> torch_tensor
#> -0.3778
#> -0.5159
#> -0.8521
#> -2.0174
#> [ CPUFloatType{4} ]
#> 
#> 
```
