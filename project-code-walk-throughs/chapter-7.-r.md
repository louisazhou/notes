# R

R严格意义不是OOP 所以list可以被用来做container

reg&lt;- lm\(\) 就能极其简单的regression，reg$就有所有hints 知道该用哪个parameter

dataframe也是一个list。

### Introduction to the Tidyverse \(Notes\)

```r
#arrange(xx)序，arrange（desc（xx））降序

# ggplot 画彩色、不同size的scatter plot
ggplot(gapminder_1952, aes(x = pop, y = lifeExp, color = continent, size = gdpPercap)) +
  geom_point() +
  scale_x_log10()
  expand_limits(y=0)
  
# ggplot+用facet来画不同的panel，并排显示
ggplot(gapminder_1952, aes(x=pop, y=lifeExp))+
geom_point()+
scale_x_log10()+
facet_wrap(~ continent)

# bar plot
by_continent<-gapminder%>%
filter(year==1952)%>%
group_by(continent)%>%
summarize(medianGdpPercap=median(gdpPercap))
ggplot(by_continent, aes(x=continent, y=medianGdpPercap))+
geom_col()

# 用groupby和summarize 有点像SQL的aggregation function和groupby
gapminder%>%
  group_by(year)%>%
    summarize(medianLifeExp=median(lifeExp), maxGdpPercap=max(gdpPercap))

# histogram 
ggplot(gapminder_1952, aes(x=pop_by_mil))+
geom_histogram(bins=50)

#boxplot
ggplot(gapminder_1952, aes(x = continent, y = gdpPercap)) +
  geom_boxplot() +
  scale_y_log10()+
  ggtitle("Comparing GDP per capita across continents")        
```

### Import data in R 1 \(Notes\)

```r
# read.table对于header的default就是false
path <- file.path("data", "hotdogs.txt")

# Import the hotdogs.txt file: hotdogs
hotdogs <- read.table(path, 
                      sep = '', 
                      col.names = c("type", "calories", "sodium")
                      colClasses = c("factor", "NULL", "numeric"))

#readr 默认第一行是header 用的是delm=‘’（必须指明） class是"tbl_df" "tbl" "data.frame"
read_delim("potatoes.txt", delim='\t',col_names=properties)

# Import swimming_pools.csv default header是TRUE 
pools <- read.csv( "swimming_pools.csv", stringsAsFactors=FALSE)
# read.csv , read.csv2 ;

#readr reads tibble 不需要写stringsAsFactors 读出来多一行对数据的描述
read_csv


# default header是TRUE   如果要skip几行，那么header也会被skip 所以要设置header=FALSE
hotdogs <- read.delim("hotdogs.txt", header=FALSE)

#readr reads tibble 不需要写stringsAsFactors
potatoes <- read_tsv("potatoes.txt", col_names=properties)


#fread function 更快, 还能spicify行列， class是"data.table" "data.frame"
library(data.table)
potatoes <- fread("potatoes.csv", select = c(1, 5))


#readexcel： readxl
library(readxl)
excel_sheets(" .xlsx") #给出不同的sheets的名字
read_excel(" .xlsx") #把excel的第一个sheet读入，存成tible
read_excel(" .xlsx", sheet=2) #spicify第二个sheet 或者sheet='name'
cols <- c("country", paste0("year_", 1960:1966))
pop_b <- read_excel("urbanpop_nonames.xlsx", col_names=cols, sheet=1)


#可以用lapply一次读完所有sheet并存成list
pop_list <- lapply(excel_sheets("urbanpop.xlsx"), read_excel, path="urbanpop.xlsx")


#gdata 用perl把excel转成了csv，然后借助了read.table的wrapper read.csv()
library(gdata)
urban_pop<-read.xls("urbanpop.xls", col.names= c('country', "1967", "1968", "1969", "1970", "1971", "1972", "1973", "1974"), sheet=2)



# XLConnect   在R里操作excel
install.packages(XLConnect) #也会装上rJAVA
library(XLConnect)
my_book <- loadWorkbook("urbanpop.xlsx")
getSheets(my_book) #和上面的excel_sheets的效果一样
readWorksheet(my_book, sheet = 2, startCol=1, endCol=1)
createSheet(my_book, name="xx")
writeWorksheet(my_book, xx, sheet ="xx")
saveWorkbook(my_book, file="xx")
renameSheet(my_book, "original name", "newname")
saveWorkbook(my_book, file="xx")
removeSheet(my_book, sheet="")

my_book <- loadWorkbook("urbanpop.xlsx")
sheets <- getSheets(my_book)
all <- lapply(sheets, readWorksheet, object = my_book)
str(all)


# which可以选择某个column中的最大最小值
lily <- hotdogs[which.min(hotdogs$calories), ]

# 打印一个tibble里的每一行 注意有个，
print(urbanpop_sel[1,])

# 不要一个tibble里的第一个column [-1]
urban <- cbind(urban_sheet1, urban_sheet2[-1], urban_sheet3[-1])

# 不要NA的行
urban_clean <- na.omit(urban)

```

### Import data in R 1 \(Notes\)

```r
# Load the DBI package
library(DBI)

# Edit dbConnect() call   出来的是一个MySQL object
con <- dbConnect(RMySQL::MySQL(), 
                 dbname = "tweater", 
                 host = "courses.csrrinzqubik.us-east-1.rds.amazonaws.com", 
                 port = 3306,
                 user = "student",
                 password = "datacamp")
                 
dbListTables(con) #给出这里的所有relational table

dbReadTable(con, "") #给出真正的data

dbDisconnect(con) #用完之后disconnect

dbGetQuery(con, "SELECT name FROM employees WHERE started_at > "xx"") #注意SQL里的WHERE后是1个=
#这和下面的等价
subset(employees, subset=started_at>"xx", select = name)
#也和下面的等价
res <- dbSendQuery(con, "")
dbFetch(res)  #优势是可以specify数量 (res=2)


#用完之后clearresult
dbClearResult(res)
#SQL里在Where后可以用CHAR_LENGTH这个function，return string里的字母数

#R也可以直接用read.csv直接load http或者https的csv 
# 但是类似的用read_excel读xlsx暂时还不行 可以先下载再load
dest_path <- file.path("")
download.file(url, dest_path)

#还可以download RData file 现在之后只用 load("name")就行

#httr & GET
library(httr)
resp <- GET(url)
# Get the raw content of resp: raw_content
raw_content<- content(resp, as='raw') 
# content的其他格式，比如as text 可以给出内容，default可以给出R list

#jsonlite  JSON可以是一个JSON FILE、JSON ARRAY
install.packages("jsonlite")
library(jsonlite)
fromJSON("somejsonurl") #括号里的可以是jsonurl 也可以是一个keyvalue pair的dictionary
toJSON()
pretty_json=toJSON(mtcars, pretty=TRUE)

prettify()
minify()


#haven   SAS, STATA, SPSS
library(haven)
read_sas("xx")
read_stata(), read_dta()
# 注意stata出来的格式有label，给了他们顺序 在处理时需要把它们转成R的factor  
#比如 as.charcater(as_factor(xx$xx)) sugar$Date<-as.Date(as_factor(sugar$Date))
read_spss()  read_por() read_sav()

#foreign   SAS, STATA, SPSS,Systat, Weka
# 用这个package 有一个参数是convert.factor 默认是TRUE by default converte.dates True by default, 
# missing.type False by default， 如果是False，见到missing就是NA 如果是TRUE 显示missing的attributes(27种)
read.sas()

#use.value.labels= TRUE by default, to.data.frame = FALSE by default
read.spss()


nrow(subset()) # returns the number of the rows that satisfy the condition in the parenthesis 
```

### Cleaning Data in R \(Notes\)

