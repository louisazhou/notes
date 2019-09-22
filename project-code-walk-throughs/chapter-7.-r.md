# R

R严格意义不是OOP 所以list可以被用来做container

reg&lt;- lm\(\) 就能极其简单的regression，reg$就有所有hints 知道该用哪个parameter

dataframe也是一个list。

```r

```

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

R里对于variable的定义

character、numeric（NaN和Inf都是numeric）、integer（以L结尾的才是integer）、factor、logical（NA算logical）

```r
# view dimension of a dataframe, first row, second column
dim(lunch)

#look at column names
names(lunch)

#summary, median mean quantiles 
summary(lunch)

# dplyr 的package里
glimpse(lunch)

# view the top, can specify n
head(lunch)
tail(lunch) # bottom

#draw histograms
hist(lunch$per_free_red, breaks=20)
#scatter plot
plot(,)
#boxplot
boxplot(x, horizontal=TRUE)

#The followings are from tidyr package

#gather function gets key-value pairs
gather(df, key, value, -namethiscolumn) #all colomns except for the namethiscolumn

#spread function spread the key-value pairs
spread(df, key, value)

#separate a colmn to multiple columns
separate(df, columnname, c('this', 'that'), sep= '')

#unite multiple columns to one
unite(df, unitedname, columname1, columname2, sep="-")


# lubridate package 是用来处理datetime variable的
ymd("2015-08-25") ymd("2015 August 25") mdy("August 25, 2015")
hms("13:33:09") ymd_hms("2015/08/25 13.33.09")


#stringr 用来处理string
str_trim("") #删前后空格
str_pad("",width=number, side="left"， pad="number") #填充内容
str_detect(vectorname, "string") #在vector中找到指明的string return的是和vector size等长的logical
str_replace(vectorname, "oldstringname", "newname")
tolower("string")
toupper("string")

#NA的处理
is.na(df) #返回和df一样dimension的df，里面写着logical 
any(is.na(df)) #返回一个logical 
sum(is.na(df)) #返回一个total number
which(is.na(df$columnname))
complete.cases(df) #以row为单位扫df，如果有na就false
df[complete.cases(df),] #只保留所有没NA的行
na.omit(df) #也可以remove有missing的row
#table/summary也可以间接告诉NA

#一次性的mutate
mutate_all()
mutate_at(weather5, vars(CloudCover:WindDirDegrees), funs(as.numeric))
mutate_if() 

transmute_all()
transmute_at()
transmute_if()
```

### Network Science in R \(Notes\)

```r
library(readr)
library(igraph)

#build a network from data frame
g <- graph_from_data_frame(d=ties, directed = FALSE, vertices = nodes)

#add name attribute to the network
g$name <- "Name"

# explore the set of nodes, and print the number of nodes
V(g) #v for vertices
vcount(g)

# explore the set of ties and print the number of ties
E(g) #e for edges
ecount(g)

# add node attribute id and print the node id attribute
V(g)$id <- 1:vcount(g)

# add cluster information 
V(g)$cluster <- nodes$cluster

# print the tie 'weight' attribute
E(g)$weight

library(ggraph)
library(dplyr)
library(ggplot2)

ggraph(g, layout = 'with_kk')+  #Kamada-Kawai layout placed tied nodes at equal distances, so that all ties had roughly the same length.
    geom_edge_link(aes(alpha=weight))+ #draws edges as a straight line between nodes
    geom_node_point(ase(size=degree))   #draws each node as a point,
                    #geom_node_text() draws a text label on each node.
    geom_node_text(aes(label = id), repel = TRUE) #The label aesthetic should map to the id field.
#Set the repel argument to TRUE to prevent the labels from overlapping.
ggraph:::igraphlayouts #看到各种layout方式

# Plot with Kamada-Kawai layout
ggraph(filtered_network, layout="with_kk") + 
  # Add an edge link geom, mapping transparency to similarity
  geom_edge_link(aes(alpha=similarity))

geom_edge_link(aes(alpha = betweenness, filter = betweenness > median_betweenness))

'in_circle' #places nodes on a circle, and
"on_grid" #which places nodes on a grid.

#degree 一个node有几个edges igraph funciton
degree(g)

#strength 一个node边上edges的所有weight的加和 
strength(g)

nodes_with_centrality <- nodes %>%
  # Add a column containing the degree of each node
  mutate(degree = degree(g), strength = strength(g)) %>%
  # Arrange rows by descending degree
  arrange(desc(degree))

#how often a node lies on the shortest path between other nodes.
betweenness()
# how close a node is to all other nodes in the network in terms of shortest path distance.
closeness()

#betweeness of ties: the number of shortest paths going through a tie.
# 1. inverse of weight, call it dist_weight, then apply edge_betweeness()
dist_weight = 1/E(g)$weight
edge_betweeness(g, weights=dist_weight)

#a weak tie as a tie with a weight equal to 1
#find number and percentage of weak ties
ties %>%
  group_by(weight)%>%
  summarise(n=n(), p=n/nrow(ties))%>%  #n=n() means number of nodes
  arrange(-n)
  
tie_counts_by_weight <- ties %>% 
  # Count the number of rows with each weight
  count(weight) %>%
  # Add a column of the percentage of rows with each weight
  mutate(percentage = 100 * n / nrow(ties)) 
  
#connection patterns: using the adjacency matrix
#undirected symmetric. 
A = as_adjacency_matrix(g, attr="weight")

A[1,] #first row
A[,1] #first column

#pearson similarity: [-1,1]
ties_swapped <- ties %>%
  mutate(temp = to, to = from, from = temp) %>% 
  select(-temp)
ties_bound <- bind_rows(ties, ties_swapped)

# Using ties_bound, plot to vs. from, filled by weight
ggplot(ties_bound, aes(x = from, y = to, fill = factor(weight))) +
  # Add a raster geom
  geom_raster() + #draw a point in the plot if there is a tie 
  # Label the color scale as "weight"
  labs(fill = "weight")
  
# visualize correlation
ggplot(data=nodes, mapping=aes(x=degree, y=strength))+
  geom_point()+
  geom_smooth(method="lm", se=FALSE)
  
# graph to matrix
A<-as_adjacancy_matrix(g)
# matrix to graph
g<-graph_from_adjacency_matrix(A, mode="undirected", weighted=TRUE)
# graph to dataframe
df = as_data_frame(g, what='both')
sim_df <- igraph::as_data_frame(h, what = "edges")
# dataframe to graph
g<-graph_from_data_frame(df$ties, vertices=df$nodes)
# matrix to dataframe
df = as_data_frame(graph_from_adjacency_matrix(A), what="both")
# dataframe to matrix
A<- as_adjacency_matrix(graph_from_data_frame(df$ties, vertices=df$nodes))
# Convert df to a tibble
sim_tib <- as_tibble(sim_df)

# hierarchical clustering in R
# distance matrix from similarity matrix 
D<- 1-S #distance is defined as the complement to 1 of similarity
# distance object from distance matrix
d<- as.dist(D)
# average-linkage clustering method
cc<- hclust(d, method="average")
# cut dendrogram at 4 clusters
cutree(cc,k=4)
#Plot the dendrogram of cc with plot().
plot(cc)
#the first pair that was merged 
cc$merge[1,]

#在R里有点像SQL的操作：
nodes %>%
  # Filter rows for cluster 1
  filter(cluster==1) %>% 
  # Select the name column
  select(name)
# Calculate properties of each cluster
nodes %>%
  # Group by cluster
  group_by(cluster) %>%
  # Calculate summary statistics
  summarize(
    # Number of nodes
    size = n(), 
    # Mean degree
    avg_degree = mean(degree),
    # Mean strength
    avg_strength = mean(strength)
  ) %>% 
  # Arrange rows by decreasing size
  arrange(desc(size))
  
  
# 画图的时候也可以像ggplot的facet_wrap一样，不同panel
  facet_nodes(~cluster, scales="free")
  
#visNetwork, interactive plot
data <- toVisNetworkData(g)
head(data$nodes)
head(data$edges)
visNetwork(nodes = NULL, edges = NULL, dot = NULL, gephi = NULL,
  width = NULL, height = NULL, main = NULL, submain = NULL,
  footer = NULL, background = "rgba(0, 0, 0, 0)", ...)
#还可以为它加不同的layout
ls("package:igraph", pattern = "^layout_.")
 # Change the layout to be in a circle %>%
  visIgraphLayout(layout = "layout_with_kk") 
#还可以在选中的时候再变颜色 %>%
  visOptions(highlightNearest=TRUE)
#增加bar选择
  visOptions(nodesIdSelection=TRUE)
#增加不同颜色 用cluster类别来为颜色划分
V(g)$color <- V(g)$cluster
  visOptions(selectedBy = "group")
```

### Predictive Analytics using Networked Data in R \(notes\)

```r
library(igraph)
plot.igraph(g, edge.label=, edge.color=, layout= , vertex.label=, vertex.color=,)

#The relational Neighbor Classifier
RelationalNeighbor <- Avector/(Avector+Bvector)
```

