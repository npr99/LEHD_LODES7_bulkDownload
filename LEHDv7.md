LEHD OnTheMap LODES Data Downloading
========================================================

Since wget doesn't work with the new URLs for bulk downloads, I had to write this script. Thank goodness, at least they supllied a list of files (.md5sum).

Total size for all states: 53.2GB - change the code to select the files you need.

```r
# cpath <- "C:/Temp/wget/lehd.ces.census.gov/onthemap/LODES7/"
cpath<-'/mnt/ide0/home/cmaene/data/LED_OTM/version7/'
setwd(cpath)
# need a list of states with abbreviated state names (US postal abbrev.
# state name = STUSAB)
download.file("http://www.census.gov/geo/reference/docs/state.txt", destfile = "state.txt")
states <- read.table("state.txt", sep = "|", header = T, colClasses = "character")
states <- states[order(as.numeric(states$STATE)), ]
states.keep <- states[-c(52:57),]
nstates <- dim(states.keep)[1]
for (i in 1:nstates) {
    stusab <- tolower(states.keep[i, 2])
    fname <- paste0("lodes_", stusab)
    fname <- paste0(fname, ".md5sum")
    npath <- paste0(cpath, stusab)
    opath <- paste0(npath, "/od")
    rpath <- paste0(npath, "/rac")
    wpath <- paste0(npath, "/wac")
    dir.create(npath)
    dir.create(opath)
    dir.create(rpath)
    dir.create(wpath)
    url <- paste0("http://lehd.ces.census.gov/onthemap/LODES7/", stusab)
    url <- paste0(url, "/")
    ourl <- paste0(url, "od/")
    rurl <- paste0(url, "rac/")
    wurl <- paste0(url, "wac/")
    setwd(npath)
    dfile <- paste0(url, fname)
    download.file(dfile, destfile = fname)
    md5sum <- read.table(fname, sep = " ", header = F, colClasses = "character")
    md5sum <- md5sum[, -c(2)]
    nobs <- dim(md5sum)[1]
    for (n in 1:nobs) {
        target <- md5sum[n, 2]
        if (length(grep("_od_", target)) > 0) {
            setwd(opath)
            oname <- paste0(target, ".gz")
            ofile <- paste0(ourl, oname)
            download.file(ofile, destfile = oname)
        } else if (length(grep("_rac_", target)) > 0) {
            setwd(rpath)
            rname <- paste0(target, ".gz")
            rfile <- paste0(rurl, rname)
            download.file(rfile, destfile = rname)
        } else if (length(grep("_wac_", target)) > 0) {
            setwd(wpath)
            wname <- paste0(target, ".gz")
            wfile <- paste0(wurl, wname)
            download.file(wfile, destfile = wname)
        } else {
            setwd(npath)
            xname <- paste0(target, ".gz")
            xfile <- paste0(url, xname)
            download.file(xfile, destfile = xname)
        }
    }
}
```


This is optional for making a list of files to unzip but I have a routine of data processing in Stata:


```r
setwd(cpath)
filelist <- list.files(pattern = ".gz$", recursive = T, include.dirs = T)
for (i in 1:length(filelist)) {
    unziplist[i] <- paste("shell gzip -d", filelist[i])
}
```

```
## 
```

```r
write.table(unziplist, file = "unzipfilelist.txt", quote = F, row.names = F)
```

```
## 
```


