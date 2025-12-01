# GeGLU Module

This module implements the Gaussian Error Linear Unit Gated Linear Unit
(GeGLU) activation function. It computes \\\text{GeGLU}(x, g) = x \cdot
\text{GELU}(g)\\ where \\x\\ and \\g\\ are created by splitting the
input tensor in half along the last dimension.

## Usage

``` r
nn_geglu()
```

## References

Shazeer N (2020). “GLU Variants Improve Transformer.” 2002.05202,
<https://arxiv.org/abs/2002.05202>.

## Examples

``` r
x = torch::torch_randn(10, 10)
glu = nn_geglu()
glu(x)
#> torch_tensor
#>  0.3324  1.1756 -0.2864  0.0104  0.6558
#>  0.0801  2.9708  0.0059 -0.0053  0.1819
#> -2.0131 -0.0863 -0.1162 -0.0260 -0.0697
#> -0.0032  0.0483  0.0764 -0.0015  0.1294
#> -1.1054 -0.0788  0.1161  0.6535 -0.0930
#>  0.2725 -0.0323  0.5761  0.1205 -0.0803
#> -0.0176  0.3415 -0.3882 -0.0956  0.0913
#>  0.0124 -0.1042 -0.0092  0.0542  0.1069
#> -2.4439  0.0775  0.0114  0.1326  0.0213
#>  0.1965 -0.0261  0.0814  0.8542  0.0001
#> [ CPUFloatType{10,5} ]
```
