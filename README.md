# Useful R programming language code snippets

## No Scientific Numeric digits 
```
options(scipen = 999)
```

## Treating directory name on Windows
```
pathPrep <- function(path = "clipboard") {
  y <- if (path == "clipboard") {
    readClipboard()
  } else {
    cat("Please enter the path:\n\n")
    readline()
  }
  x <- chartr("\\", "/", y)
  writeClipboard(x)
  return(x)
}
```

## Setting working directory to directory of R script
Obs: only works in RStudio, doens't work in command line/scheduled task
```
setwd(dirname(rstudioapi::getActiveDocumentContext()$path))
```

## Obtaining session info only with loaded packages
```
pacman::p_load(sessioninfo,gt)
sessioninfo::session_info(include_base = F)$packages %>% 
  as.data.frame() %>% 
  filter(attached == TRUE) %>% 
  select(package,loadedversion) %>% 
  tibble::remove_rownames() %>% 
  gt()
```
This way, we extract current session info and filter only loaded external packages.


## Loading all Excel files from a folder
```
setwd("dir to look files in")
file.list <- list.files(pattern='.xlsx')
df.list <- lapply(file.list, read.xlsx)
dfnames=c(202112:202113,202201:202208)
names(df.list)=dfnames #how to identify each dataframe
rbindlist(df.list,idcol = "periodo") #combining all DFs
```

## Triggering RMD report with parameters
```
rmarkdown::render('Report.Rmd',params = list(
  semana = semana,
  semanaNum = semanaNum
),output_file = paste0('./',folder,'/report ',semana,'.html'))
```

# Openxslx

## % Formatting
```
pct = createStyle(numFmt="0.00%",border = "TopBottomLeftRight")
```

## Conditional formatting
```
conditionalFormatting(wb, sheet = 1, cols = c(2:((length(Activos)-6))),
                      rows = 71:75,
                      style = c("#ff684c","#ffda66","#8ace7e"),
                      rule = c(min(valoresGxb),median(valoresGxb),max(valoresGxb)),
                      type="colourScale")
```

## Conditional formatting Openxlsx - part 2
https://stackoverflow.com/questions/55615781/openxlsx-fill-row-row-based-on-text-in-another-column



# SQL querying

## SQL Server
```
Source   <- odbcConnect(dsn = 'database',      uid = 'user',  pwd = 'password')
Query <- paste0("select * from something")
RODBC::sqlQuery(Source,Query)
```

## Oracle

### Setting up timezone
```
Sys.setenv(TZ = "GMT")
Sys.setenv(ORA_SDTZ = "GMT")
```

### Connecting to Oracle database
```
Conexao<-function(){
  require(data.table)
  library(ROracle)
  drv     <- dbDriver("Oracle")
  host    <- "blabla.com"
  port    <- 1530
  service <- "servicename"
  drv     <- dbDriver("Oracle")
  connect.string <- paste("(DESCRIPTION=","(ADDRESS=(PROTOCOL=tcp)(HOST=", host, ")(PORT=", port, "))","(CONNECT_DATA=(SERVICE_NAME=", service, ")))", sep = "")
  odbcvar <- dbConnect(drv, "readonly", "readonly", dbname = connect.string)
  assign('odbcvar',odbcvar,envir = .GlobalEnv)    
 }
 
 con=Conexao()
 query=paste0("Select * from something")
 ROracle::dbGetQuery(con, query)
```

## Joining with between dates
```
library(sqldf)
sqldf("SELECT tabela1.*,tabela2.* FROM tabela1
            LEFT JOIN calendar
            ON  tabela1.date_field BETWEEN calendar.StartDate  AND calendar.EndDate") 
```

## Validate barcode check digit
As done in: https://www.gs1.org/services/how-calculate-check-digit-manually
```
gtin13 <- function(n) {
  s <- as.character(n)
  check.sum <- 0
  for (i in 1:nchar(s)) {
    digit <- substr(s, nchar(s) - i + 1, nchar(s) - i + 1)
    check.sum <- check.sum + as.numeric(digit) * ifelse(i %% 2, 3, 1)
  }
  if (is.na(check.sum)){
    return(NA)
  }
  finalcheck = 10 - check.sum %% 10
  if (finalcheck == 10){
    return(0)
  }else {
    return(finalcheck)
  }
}
```

## Load several JSON files 
https://www.linkedin.com/posts/mattdancho_i-was-working-with-662-json-files-today-and-activity-6978886400737693696-rBhA?utm_source=share&utm_medium=member_desktop
[<img src="https://media-exp1.licdn.com/dms/image/C4E22AQFzkRhL0iE0Kg/feedshare-shrink_800/0/1663896177933?e=1666828800&v=beta&t=bNU9qrH4bDVJzUca76TCLlCUS7THZ6AGziH64Njj7NM">](https://www.linkedin.com/posts/mattdancho_i-was-working-with-662-json-files-today-and-activity-6978886400737693696-rBhA?utm_source=share&utm_medium=member_desktop)

## convert back "lost" diacritic accents
```
df$colname = iconv(df$colname, from="UTF-8", to="LATIN1")
```

## Filter only the first N groups of a Dplyr dataframe
```
first_group = function(x,n=1){
  x %>% nest %>% ungroup %>% slice(1:n) %>% unnest(data)
}
```

## GGplot stuff
https://www.mitchhenderson.org/2020/04/how-sports-scientists-can-use-ggplot2-in-r-to-make-better-visualisations/

* Adicionando imagem à plot  - imagem livre, com base na posição
* Tema escuro

https://drsimonj.svbtle.com/label-line-ends-in-time-series-with-ggplot2

* Label no fim de lineplot

https://dm13450.github.io/2021/02/01/PointsToImages.html
https://statsbylopez.netlify.app/post/nfl-team-logos-using-ggimage/

* Adicionando imagem à scatter plot
