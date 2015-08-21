LEHD OnTheMap LODES Data Downloading One Year
========================================================

Since wget doesn't work with the new URLs for bulk downloads, I had to write this script. Thank goodness, at least they supllied a list of files (.md5sum).

LODESv7.1 adds 2012 to data set. This program will download 2012 and 2013.

Total size for all states: 53.2GB - change the code to select the files you need.

```r
# https://github.com/npr99/LEHD_LODES7_bulkDownload/blob/master/LEHDv7.md
# R
# cpath <- "C:/Temp/wget/lehd.ces.census.gov/onthemap/LODES7/"
cpath<- "F:/onthemap/LODES7/"
setwd(cpath)

# What year do you want to download?
year <- "_2012"
# need a list of states with abbreviated state names (US postal abbrev.
# state name = STUSAB)
download.file("http://web.archive.org/web/20141125122851/http://www.census.gov/geo/reference/docs/state.txt", destfile = "state.txt")
states <- read.table("state.txt", sep = "|", header = T, colClasses = "character")
states <- states[order(as.numeric(states$STATE)), ]
states.keep <- states[-c(52:57),]
nstates <- dim(states.keep)[1]
for (i in 1:nstates) {
    stusab <- tolower(states.keep[i, 2])
    fname <- paste0("lodes_", stusab)
    # md5sum new name for LODES7.1 - possible to compare to old file
    mname <- paste0(fname, "_7_1.md5sum")
    fname <- paste0(fname, ".md5sum")
    npath <- paste0(cpath, stusab)
    opath <- paste0(npath, "/od")
    rpath <- paste0(npath, "/rac")
    wpath <- paste0(npath, "/wac")
    dir.create(npath)
    dir.create(opath)
    dir.create(rpath)
    dir.create(wpath)
    url <- paste0("http://lehd.ces.census.gov/data/lodes/LODES7/", stusab)
    url <- paste0(url, "/")
    ourl <- paste0(url, "od/")
    rurl <- paste0(url, "rac/")
    wurl <- paste0(url, "wac/")
    setwd(npath)
    dfile <- paste0(url, fname)
    download.file(dfile, destfile = mname)
    md5sum71 <- read.table(mname, sep = " ", header = F, colClasses = "character")
    md5sum71 <- md5sum71[, -c(2)]
    nobs <- dim(md5sum71)[1]
    for (n in 1:nobs) {
       target <- md5sum71[n, 2]
        if ((length(grep("_od_", target)) > 0)&(length(grep(year, target)) > 0)) {
            setwd(opath)
            oname <- paste0(target, ".gz")
            ofile <- paste0(ourl, oname)
            download.file(ofile, destfile = oname)
        } else if ((length(grep("_rac_", target)) > 0)&(length(grep(year, target)) > 0)) {
            setwd(rpath)
            rname <- paste0(target, ".gz")
            rfile <- paste0(rurl, rname)
            download.file(rfile, destfile = rname)
        } else if ((length(grep("_wac_", target)) > 0)&(length(grep(year, target)) > 0)) {
            setwd(wpath)
            wname <- paste0(target, ".gz")
            wfile <- paste0(wurl, wname)
            download.file(wfile, destfile = wname)
        } 
    }
}

```
## 
```


