
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Single-cell Analysis of Host-Microbiome Interactions

<!-- badges: start -->

<!-- badges: end -->

## Installation

You can install the development version of rsahmi from
[GitHub](https://github.com/) with:

``` r
if (!requireNamespace("pak")) {
  install.packages("pak",
    repos = sprintf(
      "https://r-lib.github.io/p/pak/devel/%s/%s/%s",
      .Platform$pkgType, R.Version()$os, R.Version()$arch
    )
  )
}
pak::pkg_install("Yunuuuu/rsahmi")
```

## Workflow

Prepare output directory

``` r
odir <- "rsahmi"
fq1 <- 
fq2 <- # can be `NULL`
```

### taxonomic sequence classifier

We must run with `--use-names`, and `--report-minimizer-data`,
`classified_out` is not necessary in `rsahmi` package, but should be
specified if you want to use official `SAHMI` script.

``` r
biosys::kraken2(
  fq1 = fq1, 
  fq2 = fq2,
  classified_out = "classified.fq",
  biosys::arg("--threads", 30L, format = "%d"),
  biosys::arg("--db", kraken_db), # specify the kraken database
  "--use-names", "--report-minimizer-data",
  odir = odir
)
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
