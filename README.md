Single-cell Analysis of Host-Microbiome Interactions
================

<!-- README.md is generated from README.Rmd. Please edit that file -->

<!-- badges: start -->

<!-- badges: end -->

## Installation

Here, we use pak to install package, you can also use `remotes`

``` r
if (!requireNamespace("pak")) {
  install.packages("pak",
    repos = sprintf(
      "https://r-lib.github.io/p/pak/devel/%s/%s/%s",
      .Platform$pkgType, R.Version()$os, R.Version()$arch
    )
  )
}
```

`rsami` depends on [polars](https://rpolars.github.io/index.html), you
can install it by:

``` r
pak::repo_add("https://rpolars.r-universe.dev")
pak::pkg_install("polars")
```

You can also install the development version of `polars` from
[Github](https://github.com/pola-rs/r-polars)

In this way, you must install `cargo` to compile `polars`

If you have `root` rights, you can do:

    sudo apt purge cargo

Otherwise, you can install `cargo` in your home directory:

    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Then install the `polars` package:

``` r
pak::pkg_install("pola-rs/r-polars")
```

You can install the development version of `rsahmi` from
[GitHub](https://github.com/Yunuuuu/rsahmi):

``` r
pak::pkg_install("Yunuuuu/rsahmi")
```

You must also install [seqkit](https://bioinf.shenwei.me/seqkit/) and
[kraken2](https://github.com/DerrickWood/kraken2/wiki/Manual).

## Workflow

Prepare output directory and input data

``` r
odir <- "rsahmi"
fq1 <- 
fq2 <- # can be `NULL`
kraken_db <- # specify the kraken database
```

### Taxonomic sequence classifier

We must run with `--use-names`, and `--report-minimizer-data`,
`classified_out` is not necessary in `rsahmi` package, but should be
specified if you want to use official `SAHMI` script.

``` r
biosys::kraken2(
  fq1 = fq1, 
  fq2 = fq2,
  classified_out = "classified.fq",
  biosys::arg("--threads", 30L, format = "%d"),
  biosys::arg("--db", kraken_db),
  "--use-names", "--report-minimizer-data",
  odir = odir
)$run()
```

### Extract microbiome kraken output

``` r
rsahmi::extract_kraken_output(
  file.path(odir, "kraken_output.txt"),
  taxids = rsahmi::extract_taxids(file.path(odir, "kraken_report.txt")),
  dir = odir
)
```

### Extract microbiome kraken reads

``` r
rsahmi::extract_kraken_reads(
  kraken_out = file.path(odir, "kraken_microbiome_output.txt"),
  reads = c(fq1, fq2),
  odir = odir,
  threads = 10L
)
```
