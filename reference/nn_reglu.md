# ReGLU Module

Rectified Gated Linear Unit (ReGLU) module. Computes the output as
\\\text{ReGLU}(x, g) = x \cdot \text{ReLU}(g)\\ where \\x\\ and \\g\\
are created by splitting the input tensor in half along the last
dimension.

## Usage

``` r
nn_reglu()
```

## References

Shazeer N (2020). “GLU Variants Improve Transformer.” 2002.05202,
<https://arxiv.org/abs/2002.05202>.

## Examples

``` r
x = torch::torch_randn(10, 10)
reglu = nn_reglu()
reglu(x)
#> torch_tensor
#> -0.0000 -0.0000  0.0000  0.0000 -0.0000
#> -0.0000 -0.2296 -0.0000  0.1478  0.0863
#> -0.2450  0.6687 -0.0615  0.5279 -0.2217
#>  0.0051  0.5357  0.0000  0.0000 -0.0000
#>  0.4085 -0.9790 -0.0000  1.0190  0.1112
#> -0.0000  0.9608 -0.0000 -0.0000 -0.0000
#> -0.0000  0.0000 -0.1465  0.0000  1.7120
#>  0.0000  0.6495 -0.6907  0.0002 -0.0000
#> -0.2944 -0.3650  0.0000 -0.2884  0.3732
#>  0.0000 -0.0583 -0.0000  0.0000 -0.0222
#> [ CPUFloatType{10,5} ]
```
