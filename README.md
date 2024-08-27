# Geog 418 Fall 2024 - Assignment 1
All assignments for this course will be completed in the coding language R. There are two software that you will need to install: (1) [R version 4.4.1](https://www.r-project.org/), and (2) [R Studio version 2024.04.2](https://posit.co/download/rstudio-desktop/). R is an open-source programming language fpr statistical analysis, while R Studio is the graphical user interface that provides you with editing and visualization capabilities. You can visit the video in the Read Me First Page on the course Brightspace site to learn how to download these software. 

R operates by running through lines of code that a user (e.g. you) creates. Like all languages, R is sensitive to spelling, upper/lower cases, and punctuation, so it is best to approach your coding in a slow and deliberate manner to ensure that you are not making any mistakes. In addition to code, you can enter comments in R that serve as plain text for explaining what the code is suppose to do.

There are really only three basic elements to coding. 
1. objects: this is the thing that you are doing something to with your code. It could be a number, a name, or a dataset (amongst other things)
2. variables: each object can have multiple variables that describe something about it. For example, if the object is a list of trees, the variable could be height
3. functions: a function is applied to do something to an object or a variable. For example, the function "mean" would return the average height of all the trees in your list. Functions can be short and simple or they can be very long and confusing. Sometimes we will ignore some of the elements of a function and just focus on the essentials.

Whatever your experience is with coding, please have the confidence that will learn some new skills and will walk away from this course with a new or polished tool in your analytical toolbelt. You can do it, we can help. Let's get started with Assignment 1.


## Instructions
### Installing Packages
Open R Studio and create a new project and give it an appropriate name (e.g. Geog418_Assignment_1). You should see multiple panels in the interface. The top-left panel is where you create and save your code. When you run your code you will see it being executed in the bottom-left panel. Any outputs are shown in the Plots panel on the right.

R has many generic functions that are built into its main download "package". However, most often you will want to use a specific function that is part of some specific package that does not come with the main download, and so you will need to install these packages onto your machine. Here is a list of packages that you will need to install for this assignment. Copy and paste this code into your R Studio editor panel. Highlight the code and select "Run"

```
install.packages("terra")
install.packages("lubridate")
install.packages("e1071")
install.packages("gridExtra")
install.packages("gtable")
install.packages("grid")
install.packages("ggplot2")
install.packages("dplyr")
install.packages("bcmaps")
install.packages("tmap")
install.packages("sf")
```
You will see all the packages being installed in the execution panel. Note that you will only need to install packages once on your machine.

Next, you will need to activate these packages by calling them into a "library". This step will need to be done each time you open R for your assignment. 
```
library("terra")
library("lubridate")
library("e1071")
library("gtable")
library("grid")
library("gridExtra")
library("ggplot2")
library("dplyr")
library("bcmaps")
library("raster")
library("maps")
library("tmap")
```

### Opening and Reading Data
R needs to know where to get data from. We call the location of where your files are stored (and where your outputs will be stored) the "working directory". Here we are going to create an object called "dir" and assign it our working directory. We use the <- symbol whenever we are assigning something to an object or variable. "setwd(dir)" is a function that assigns our pathname to "dir", and "getwd()" will print our working directory in the execution panel so that we can make sure it is correct.
```
dir <- "C:/Users/Chris/OneDrive - University of Victoria/Courses/Geog 418 Spatial Analysis/2024/Assignment 1"
setwd(dir)
getwd()
```
*Note: it is good practice to save and run your code each time you add a line or a section of code. This way you can make sure that each chunk of code is working appropriately. 

Next, you will read in the shapefile of wildfires that you downloaded from the BC Data Catalogue. You are going to create an object called "shp", and use the function "vect" to create a vector dataset in R of this shapefile. Be sure to use the same punctuation in the filename below.
```
shp <- vect("./prot_current_fire_points.shp") #read in shp file from current (".") working directory
```
Shapefiles are clunky files to work with in R, so a common practice is to transform it to a file class called a dataframe. This is a very common data class to work with in R. You should be able to understand from the code below that we are creating a new object called "df" and assigning our vector shapefile as a dataframe to this object. We call also make sure it is a dataframe by asking R to tell us what class it is.

```
df <- as.data.frame(shp) 
class(df)
```

### Inspecting and Preparing the Data
There are several quick tools for exploring your data, which is especially handy since we cannot necessarily see the dataset like we can in a software like Excel or ArcGIS. The "name" and "head" functions allow us to see the name of the columns and the first six rows of the dataset, respectively.

```
names(df) 
head(df) 
```
You can see what is the data type of each column (i.e. variable in the dataset). The "typeof" function is applied to the ignition date of the fire (the IGNITIT_DT column) in the df dataframe. The "range" function below looks at the FIRE_YEAR column and gets the range of years in the data. It should be clear that the "$" symbol indicates that IGNITN_DT is from the dataframe df.
```
typeof(df$IGNITN_DT) 
range(df$FIRE_YEAR)
```
_What is the data type of the IGNITIN_DT column?_
_What is the range of years in the dataset?_


You will see that there are multiple years of data in this dataset. For this assignment you only want to use data for 2024. In the code below I use data for 2023; you need to make sure to change this for 2024. The code takes "df" and assigns it a subset of the data based on the fire year.

```
df <- subset(df, df$FIRE_YEAR == 2023)
```
You are going to compute descriptive statistics on the entire 2024 season as well as just the summer months (more on the summer months below). Therefore, we need to know what day and month each fire took place. Unfortunately, the date of the fire ignition is stored as a single string; we want to create an object for the Day, Month, and Year of each fire. To do this, we will first make sure that our INIGNITN_DT columns is in a Date format, then create a new column for each of these three variables and extract the appropriate information to fill these new columns. Try to read through the code to see if you can make sense of what is happening.

```
df$IGNITN_DT<- as.Date(df$IGNITN_DT, format = "%Y/%m/%d")
df$IGN_Day <- yday(df$IGNITN_DT) 
df$IGN_Month <- month(df$IGNITN_DT, label = TRUE, abbr = TRUE) 
df$IGN_Year <- year(df$IGNITN_DT) 
```
Now you can change the range of days and years, and see what months are included in the dataset. 

```
range(df$IGN_Day) 
unique(df$IGN_Month) 
range(df$IGN_Year)
```
_What do all these outputs refer to?_

We are now going to create two separate dataframes in order to be able to compare fires from across 2024 to fires during the summer months of 2024. First we create one for the year.
```
df_year <- df[which(df$IGN_Year == 2023),] 
```
Then we create one for only the summer months. Here we use the day of the year (Jan 1 = 1 and December 31 = 365).
```
df_Summer <- subset(df, IGN_Day >= 182 & IGN_Day <= 243) #Make new variable to hold summer data
```

### Calculating Statistics
You are now ready to calculate statistics. 

_Mean_
```
meanPop <- mean(df_year$CURRENT_SZ)
meanSummer <- mean(df_Summer$CURRENT_SZ)
```
_Standard Deviation_
```
sdPop <- sd(df_year$CURRENT_SZ, na.rm = TRUE) 
sdSummer <- sd(df_Summer$CURRENT_SZ, na.rm = TRUE) 
```

_Mode_
```
#Mode
modePop <- as.numeric(names(sort(table(df_year$CURRENT_SZ), decreasing = TRUE))[1]) 
modeSummer <- as.numeric(names(sort(table(df_Summer$CURRENT_SZ), decreasing = TRUE))[1])
```
Note: This code makes a frequency table of fire size variable and sorts it in desending order and extract the first row (i.e. the most frequent value)

_Median_
```
medPop <- median(df_year$CURRENT_SZ, na.rm = TRUE)
medSummer <- median(df_Summer$CURRENT_SZ, na.rm = TRUE)
```

_Skewness_
```
skewPop <- skewness(df_year$CURRENT_SZ, na.rm = TRUE)[1]
skewSummer <- skewness(df_Summer$CURRENT_SZ, na.rm = TRUE)[1]
```

_Kurtosis_
```
kurtPop <- kurtosis(df_year$CURRENT_SZ, na.rm = TRUE)[1]
kurtSummer <- kurtosis(df_Summer$CURRENT_SZ, na.rm = TRUE)[1]
```

_Coefficient of Variation___
```
CoVPop <- (sdPop / meanPop) * 100
CoVSummer <- (sdSummer / meanSummer) * 100
```

_Normal Distribution Test_
```
normPop_PVAL <- shapiro.test(df_year$CURRENT_SZ)$p.value
normSummer_PVAL <- shapiro.test(df_Summer$CURRENT_SZ)$p.value
```

### Creating a Table for Your Results

Creating a table and inputing your results is a little tricky. Do your best to understand how this code works, but do not get stuck on understanding every single detail.

We first create an object of the two labels that we will use in the table
```
samples = c("Population", "Summer") #Create an object for the labels
```

Next, we create an object that contains the statistic for the year and summer
```
means = c(meanPop, meanSummer) #Create an object for the means
sd = c(sdPop, sdSummer) #Create an object for the standard deviations
median = c(medPop, medSummer) #Create an object for the medians
mode <- c(modePop, modeSummer) #Create an object for the modes
skewness <- c(skewPop, skewSummer) #Create an object for the skewness
kurtosis <- c(kurtPop, kurtSummer) #Create an object for the kurtosis
CoV <- c(CoVPop, CoVSummer) #Create an object for the CoV
normality <- c(normPop_PVAL, normSummer_PVAL) #Create an object for the normality PVALUE
```

We can change the number of values after the decimal to make sure that our numbers are within reason. 
```
means <- round(means, 3)
sd <- round(sd, 3)
median <- round(median, 3)
mode <- round(mode,3)
skewness <- round(skewness,3)
kurtosis <- round(kurtosis,3)
CoV <- round(CoV, 3)
normality <- round(normality, 5)
```

Next, we will create two tables and include specific results in each. We start by creating an object for each table that contains the relevant data and output all the results into a .csv file.
```
data.for.table1 = data.frame(samples, means, sd, median, mode)
data.for.table2 = data.frame(samples, skewness, kurtosis, CoV, normality)
outCSV <- data.frame(samples, means, sd, median, mode, skewness, kurtosis, CoV, normality)
write.csv(outCSV, "./FireDescriptiveStats_2023.csv", row.names = FALSE)
```

Next, we use several functions in R to create the first table.
```
table1 <- tableGrob(data.for.table1, rows = c("","")) #make a table "Graphical Object" (GrOb) 
t1Caption <- textGrob("Table 1: CAPTION FOR YOUR TABLE", gp = gpar(fontsize = 09))
padding <- unit(5, "mm")

table1 <- gtable_add_rows(table1, 
                          heights = grobHeight(t1Caption) + padding, 
                          pos = 0)

table1 <- gtable_add_grob(table1,
                          t1Caption, t = 1, l = 2, r = ncol(data.for.table1) + 1)

```

And repeat these steps for table 2.
```
table2 <- tableGrob(data.for.table2, rows = c("",""))
t2Caption <- textGrob("Table 2: CAPTION FOR YOUR TABLE", gp = gpar(fontsize = 09))
padding <- unit(5, "mm")

table2 <- gtable_add_rows(table2, 
                          heights = grobHeight(t2Caption) + padding, 
                          pos = 0)

table2 <- gtable_add_grob(table2,
                          t2Caption, t = 1, l = 2, r = ncol(data.for.table2) + 1)
```

To print these tables, we first add each one to a new page.
```
grid.arrange(table1, newpage = TRUE)
grid.arrange(table2, newpage = TRUE)
```

Next, we create an empty png file in our working directory, and then assign Table 1 to that png file, and then print the table by calling the "dev.off" function.
```
png("Output_Table1.png")
grid.arrange(table1, newpage = TRUE)
dev.off() 
```
And repeat for Table 2
```
png("Output_Table2.png") 
grid.arrange(table2, newpage = TRUE)
dev.off()
```


### Plot Your Data in a Histogram

The text below creates a simple histogram following the same process as making a table that is described above. First an empty png file is create, next a histogram is created from your data (the example below includes some extra parameters for the histogram design), and finally printing the histogram to the png file. Examine the code and try to understand what the various components do.
```
png("Output_Histogram.png")
hist(df_year$CURRENT_SZ, breaks = 30, main = "Frequency of Wild Fire Sizes", xlab = "Size of Wild Fire (ha)") #Base R style
dev.off()
```


### Plot Your Data in a Bar Graph

Next, we will create a bar graph showing the sum of wildfires in each month. The first step is to create a new object for each month and then assigning the sum of fires to that object. In addition, the last line of code creates a set of labels for the x-axis of the graph.
```
sumMar = sum(subset(df_year, IGN_Month == "Mar")$CURRENT_SZ, na.rm = TRUE) 
sumApr = sum(subset(df_year, IGN_Month == "Apr")$CURRENT_SZ, na.rm = TRUE) 
sumMay = sum(subset(df_year, IGN_Month == "May")$CURRENT_SZ, na.rm = TRUE) 
sumJun = sum(subset(df_year, IGN_Month == "Jun")$CURRENT_SZ, na.rm = TRUE) 
sumJul = sum(subset(df_year, IGN_Month == "Jul")$CURRENT_SZ, na.rm = TRUE) 
sumAug = sum(subset(df_year, IGN_Month == "Aug")$CURRENT_SZ, na.rm = TRUE) 
sumSep = sum(subset(df_year, IGN_Month == "Sep")$CURRENT_SZ, na.rm = TRUE) 
months = c("Mar","Apr","May","Jun","Jul", "Aug", "Sep") h
```


The bar graph is printed using the familiar three-step process.
```
png("Output_BarGraph.png") 
barplot(c(sumMar,sumApr,sumMay, sumJun, sumJul, sumAug, sumSep), names.arg = months, 
        main = "TITLE FOR BAR GRAPH", ylab = "AXIS TITLE", xlab = "AXIS TITLE") #Create the bar graph
dev.off() 
```

You can create an advanced bar graph by using the the popular ggplot library. This is a little more involved, and so we have provide comments in each line to explain a little more about what is going on in the code.
```
barGraph <- df_year %>% #store graph in bar graph variable and pass data frame as first argument in next line
  group_by(IGN_Month) %>% #use data frame and group by month and pass to first argument in next line
  summarise(sumSize = sum(CURRENT_SZ, na.rm = TRUE)) %>% #sum up the total fire size for each month and pass to GGplot
  ggplot(aes(x = IGN_Month, y = sumSize)) + #make new GGPLOT with summary as data and month and total fire size as x and y
  geom_bar(stat = "identity") + #make bar chart with the Y values from the data (identity)
  labs(title = "Total Burned Area by Month 2020", x = "Month", y = "Total Burned Area (ha)", caption = "Figure 2: Total fire size by month in 2020") + #label plot, x axis, y axis
  theme_classic() + #set the theme to classic (removes background and borders etc.)
  theme(plot.title = element_text(face = "bold", hjust = 0.5), plot.caption = element_text(hjust = 0.5)) #set title to center and bold
barGraph

png("Output_BarGraph_GG.png")
barGraph
dev.off()

```

### Creating Maps
Now we get to the fun but complex part of all the cody. Making maps is not an easy task in R as there are multiple key steps that go into preparing the data into a spatial format that can be accurately mapped. Again, we provide some comments to help explain what the code is doing, but you are not expected to understand how everything works.

```
bc <- as_Spatial(bc_neighbours()) #Get shapefile of BC boundary
raster::crs(bc)
bc <- spTransform(bc, CRS("+init=epsg:4326")) #Project your data to WGS84 geographic (Lat/Long)
bc <- bc[which(bc$name == "British Columbia" ),] #Extract just the BC province

png("FirstMap.png")
map(bc, fill = TRUE, col = "white", bg = "lightblue", ylim = c(40, 70)) #Make map of province
points(df_year$LONGITUDE ,df_year$LATITUDE , col = "red", pch = 16) #Add fire points
dev.off()
```


### Creating Maps Using the tm Package
There are many mapping packages available in R. The tm package is a fairly accessible package that can help you take your maps to the next level, but they require a slightly different set of steps than the "map" function used above. First we define the coordinates, establish a projection and add the locations of the wildfires.

```
coords <- df_year[, c("LONGITUDE", "LATITUDE")] #Store coordinates in new object
crs <- CRS("+init=epsg:4326") #store the coordinate system (CRS) in a new object
firePoints <- SpatialPointsDataFrame(coords = coords, data = df_year, proj4string = crs) #Make new spatial Points object using coodinates, data, and projection
```
Next we create the map.
```
map_TM <- tm_shape(bc) + #make the main shape
  tm_fill(col = "gray50") +  #fill polygons
  tm_shape(firePoints) +
  tm_symbols(col = "red", alpha = 0.3) +
  tm_layout(title = "BC Fire Locations 2020", title.position = c("LEFT", "BOTTOM"))

map_TM
```


### Calculating and Adding the Mean Centre to a Map
This is a complex task, but by now you should be able to make sense of what the code is doing (at least at a very general level). Follow the steps below to add the mean centre to your map and try your best to explain what each line of code is doing.


```
meanCenter <- data.frame(name = "Mean Center of fire points", long = mean(df_year$LONGITUDE), lat = mean(df_year$LATITUDE))

coords2 <- meanCenter[, c("long", "lat")]
crs2 <- CRS("+init=epsg:4326")
meanCenterPoint <- SpatialPointsDataFrame(coords = coords2, data = meanCenter, proj4string = crs2)

map_TM <- tm_shape(bc) + 
  tm_fill(col = "gray50") +  
  tm_shape(firePoints) +
  tm_symbols(col = "red", alpha = 0.3) +
  tm_shape(meanCenterPoint) +
  tm_symbols(col = "blue", alpha = 0.8) +
  tm_add_legend(type = "symbol", labels = c("Fire Points", "Mean Center"), col = c(adjustcolor( "red", alpha.f = 0.3), adjustcolor( "blue", alpha.f = 0.8)), shape = c(19,19)) +
  tm_layout(title = "BC Fire Locations 2020", title.position = c("LEFT", "BOTTOM"), legend.position = c("RIGHT", "TOP"))

map_TM

png("TmMap.png")
map_TM
dev.off()
```

That's it! Congratulations on completing Assignment 1.




