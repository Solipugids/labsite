---
title: 代谢组分析工具metaX和MetaboAnalystR的安装
date: 2021-12-09
image:
  focal_point: "top"
---

metaX是BGI开发的一个代谢组分析包，MetaboAnalystR是一个完整的代谢组分析流程。

<!--more-->

[metaX](https://bioconductor.statistik.tu-dortmund.de/packages/3.2/bioc/html/metaX.html)是`BGI`开发的一个代谢组分析包，[MetaboAnalystR](https://github.com/xia-lab/MetaboAnalystR)是一个完整的代谢组分析流程。

`metaX`年久未更新，已经从`bioconductor`上移除了。

`MetaboAnalystR`的安装流程比较复杂，按照git上指南的安装方法未必能够走得通。

这两个流程里都有[SSPA](https://mirrors.nju.edu.cn/bioconductor/packages/3.5/bioc/html/SSPA.html)的依赖，很遗憾`SSPA`也已经从`bioconductor`上移除了。虽然不知道这个包厉害在哪里，但是如果这个包的话，这两个分析软件都无法正确运行。

所以要先解决`SSPA`这个问题。

### `SSPA`的安装

`SSPA`的bioconductor网页下面有一个源代码的repo：
```bash
git clone https://git.bioconductor.org/packages/SSPA
```
直接用git克隆源代码到本地，然后在`rstudio`里本地运行安装（这个包有三个依赖`qvalue`, `lattice`, `limma`）:
```r
install.packages("/path/to/SSPA/", repos = NULL, type="source")
```
可以正常安装。

### `metaX`的安装

`metaX`的bioconductor网页下面的源链接也已经失效了，所以没办法下载。看来bioconductor已经完全放弃。所以要想其他办法。

从`git`上考古找了个`MetabLAB`的原始更新备份，这个版本已经是六年前的了，但是版本比bioconductor上的要新一点。可以直接下载源文件的zip压缩包或者用git命令克隆：
```bash
git clone https://github.com/MetabLAB/metaX
```
下载以后用跟`SSPA`同样的命令安装（（有四个依赖`VennDiagram`, `pROC`, `grid`，`futile.logger`））：
```r
install.packages("/path/to/metaX/", repos = NULL, type="source")
```
可以正常安装。

### `MetaboAnalystR`的安装
[MetaboAnalystR](https://github.com/xia-lab/MetaboAnalystR)的安装比较复杂了，但是官方页面的介绍很详细，只要把他的依赖先安装好。先安装系统依赖(`github`上介绍了`ubuntu`依赖，`centos`的名称略不同)：
```bash
sudo yum install netcdf-devel
sudo yum install openssl-devel
sudo yum install libXt-devel
sudo yum install cairo cairo-devel
```
还有一个是其他的r包依赖，通过官方提供脚本安装：
```r
metanr_packages <- function(){

  metr_pkgs <- c("impute", "pcaMethods", "globaltest", "GlobalAncova", "Rgraphviz", "preprocessCore", "genefilter", "SSPA", "sva", "limma", "KEGGgraph", "siggenes","BiocParallel", "MSnbase", "multtest","RBGL","edgeR","fgsea","devtools","crmn","httr","qs")
  
  list_installed <- installed.packages()
  
  new_pkgs <- subset(metr_pkgs, !(metr_pkgs %in% list_installed[, "Package"]))
  
  if(length(new_pkgs)!=0){
    
    if (!requireNamespace("BiocManager", quietly = TRUE))
        install.packages("BiocManager")
    BiocManager::install(new_pkgs)
    print(c(new_pkgs, " packages added..."))
  }
  
  if((length(new_pkgs)<1)){
    print("No new packages added...")
  }
}
```
直接运行：
```r
metanr_packages()
```
还有一些需要手动安装的包，比如`OptiLCMS`：
```r
install.packages("devtools")
library(devtools)
devtools::install_github("xia-lab/OptiLCMS", build = TRUE, build_vignettes = FALSE, build_manual =TRUE)
```
最后安装`MetaboAnalystR`:
```r
install_github("xia-lab/MetaboAnalystR", build = TRUE, build_vignettes = TRUE, build_manual =TRUE)
```

### 备注

1. `SSPA`，`metaX`两个失效包的源代码已经保存在本地，以防丢失。
1. `git clone`命令有时候会报错，因为`github`的DNS在国内被污染了。可以修改系统的host文件，直接把github的地址对应到直接的IP地址上。
1. 直接克隆下来的`SSPA`安装会显示一个报错：
    Error in .install_package_code_files(".", instdir) : 
    duplicated files in 'Collate' field:
      zzz.R
    ERROR: unable to collate and parse R files for package ‘SSPA’
  问题在于`SSPA`目录下`DESCRIPTION`文件中的`Collate`行有两个`zzz.R`文件，删除一个即可。