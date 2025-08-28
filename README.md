# Geog 418 Fall 2025 - Assignment 1
All assignments for this course will be completed in the coding language R. There are two software that you will need to install: (1) [R version 4.5.1](https://muug.ca/mirror/cran/), and (2) [R Studio version 2025.05.1-513](https://posit.co/download/rstudio-desktop/). R is an open-source programming language for statistical analysis, while R Studio is the graphical user interface that provides you with editing and visualization capabilities. You can visit the video in the Read Me First Page on the course Brightspace site to learn how to download these software. 

R operates by running through lines of code that a user (e.g. you) creates. Like all languages, R is sensitive to spelling, upper/lower cases, and punctuation, so it is best to approach your coding in a slow and deliberate manner to ensure that you are not making any mistakes. In addition to code, you can enter comments in R that serve as plain text for explaining what the code is suppose to do.

There are really only three basic elements to coding. 
1. objects: this is the thing that you are doing something to with your code. It could be a number, a name, or a dataset (amongst other things)
2. variables: each object can have multiple variables that describe something about it. For example, if the object is a list of trees, the variable could be height
3. functions: a function is applied to do something to an object or a variable. For example, the function "mean" would return the average height of all the trees in your list. Functions can be short and simple or they can be very long and confusing. Sometimes we will ignore some of the elements of a function and just focus on the essentials.

Whatever your experience is with coding, please have the confidence that you will learn some new skills and will walk away from this course with a new or polished toolset in your analytical toolbelt. You can do it, we can help. Let's get started with Assignment 1.


## Instructions
### Installing Packages
Open R Studio and create a new project and give it an appropriate name (e.g. Geog418_Assignment_1). You should see multiple panels in the interface. The top-left panel is where you create and save your code. When you run your code you will see it being executed in the bottom-left panel. Any outputs are shown in the Plots panel on the right.

R has many generic functions that are built into its base "package". However, most often you will want to use a specific function that is part of a specific package that is seperate from the base package. In this case you will need to install these packages onto your machine. Below is a list of packages that you will need to install for this assignment. Copy and paste this code into your R Studio editor panel. If your R Studio does not automatically open with a editor panel, you can open one with the 'New File' <img width="44" height="21" alt="image" src="https://github.com/user-attachments/assets/9788d843-b96b-4127-af54-27a8ef392dc3" />
button, and selecting 'R Script'.


Once you have pasted the code below into the editor you need to run it. Highlight the code and either click on the "Run" button <img width="58" height="27" alt="image" src="https://github.com/user-attachments/assets/4920d5e9-4848-4c65-8c28-ae713693eec8" /> or use ctrl + enter (windows)/ cmd + enter (mac) to execute the selected code.


```
install.packages("terra")
install.packages("lubridate")
install.packages("e1071")
install.packages("gridExtra")
install.packages("gtable")
install.packages("ggplot2")
install.packages("dplyr")
install.packages("bcmaps")
install.packages("tmap")
install.packages("sf")
```
You will see all the packages being installed in the execution panel, keep an eye out for any major errors (warnings are often ok). 

Note that you will only need to install packages once on your machine. After you have installed them successfully you can comment out the lines by adding a '#' to the start of each line. If you want to do multiple rows at once, you can highlight the rows you want to comment out and press ctrl + shift + c (windows) / cmd + shift + c (mac).
```
# install.packages("terra")
# install.packages("lubridate")
# install.packages("e1071")
# install.packages("gridExtra")
# install.packages("gtable")
# install.packages("grid")
# install.packages("ggplot2")
# install.packages("dplyr")
# install.packages("bcmaps")
# install.packages("tmap")
# install.packages("sf")
```

Next, you will need to activate these packages by calling them into a "library". This step will need to be done each time you open a new R session (opening RStudio for the first time or restarting). 

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
library("tmap")
```

### Opening and Reading Data
R needs to know where to get your data from. We call the location of where your files are stored (and where your outputs will be stored) the file path. Once you point R to a specific file path it is known as the "working directory". Here we are going to create an object called "dir" and assign it our working directory. We use the <- symbol whenever we are assigning something to an object or variable. "setwd(dir)" is a function that assigns our file path (stored in the object "dir") as the working directory. Alternativly, "getwd()" will print our working directory in the execution panel so that we can make sure it is correct.
```
dir <- "C:/Users/Jason/OneDrive - University of Victoria/Geog418_202509/A1/"
setwd(dir)
getwd()
```
*Note: it is good practice to save and run your code each time you add a line or a section of code. This way you can make sure that each new piece of code is working appropriately. 

Next, you will read in the shapefile of wildfires that you downloaded from the BC Data Catalogue. You are going to create an object called "shp", and use the function "read_sf()" to create a vector dataset in R of this shapefile. Be sure to use the same punctuation in the filename below.
```
shp <- read_sf("./H_FIRE_PNT_point.shp") #read in shp file from current (".") working directory
```
Sometimes we don't need all of the extra spatial information included with a Shapefile, so a common practice is to transform it to a file class called a dataframe. This is a very common data class to work with in R. Looking at the code below you will see that we are using the st_drop_geometry() function with the shp object to remove the spatial information. The result of this function is then used inside the as.data.frame() function to ensure what ends up in the df object is a data frame class as we intended. We call also make sure it is a dataframe by asking R to tell us what class it is.

```
df <- as.data.frame(st_drop_geometry(shp)) 
class(df)
```

### Inspecting and Preparing the Data
There are several handy functions for doing checks and exploring your data, which is especially handy since we cannot necessarily see the dataset like we can in a software like Excel or ArcGIS. The "name" and "head" functions allow us to see the name of the columns and the first six rows of the dataset, respectively.

```
names(df) 
head(df) 
```

You can also see what the data type is for each column (i.e. variable in the dataset). Below, the typeof() function is applied to the ignition date of the fire (the IGN_DATE column) in the df object. The range() function looks at the FIRE_YEAR column and gets the range of years in the data. It should be clear that the "$" symbol indicates that we are referring to specific columns within the df object (IGN_DATE & FIRE_YEAR).

```
typeof(df$IGN_DATE)
range(df$FIRE_YEAR)
```

_What is the data type of the IGN_DATE column?_

_What is the range of years in the dataset?_


You will see that there are multiple years of data in this dataset. For this assignment you only want to use data for 2017 and 2023. In the code below I use data for 2017; you need to copy this code and make appropriate changes to also get this for 2023. The code takes "df" and assigns it a subset of the data based on the fire year.

```
df_17 <- subset(df, df$FIRE_YEAR == 2017)
```
You are going to compute descriptive statistics on both the 2017 and 2023 seasons for just the main wildfire season (April through October). Therefore, we need to know what day and month each fire took place. Unfortunately, the date of the fire ignition is stored as a single character string; we want to create a variable for the Day, Month, and Year of each fire. To do this, we will first make sure that our IGN_DATE columns are in a Date format, then create a new column for each of these three variables and extract the appropriate information to fill these new columns. Try to read through the code to see if you can make sense of what is happening and then repeat it for the 2023 data.

```
df_17$IGN_DATE <- as.Date(df_17$IGN_DATE, format = "%Y%m%d")
df_17$IGN_DAY <- yday(df_17$IGN_DATE) 
df_17$IGN_MONTH <- month(df_17$IGN_DATE, label = TRUE, abbr = TRUE) 
df_17$IGN_YEAR <- year(df_17$IGN_DATE) 
```
Now you can check the range of days and years, and see what is included in each dataset.

```
range(df_17$IGN_DAY) 
unique(df_17$IGN_MONTH) 
range(df_17$IGN_YEAR)
```

_What do all these outputs refer to?_

You may notice that some of these checks returned values you were not expecting, this indicates that some rows have not been filtered out properly. First start by removing any rows that do not have a valid IGN_DATE. These will be the rows that show an NA value for IGN_DATE. In the code below we use the subset() function to remove the NA rows from the df_17 object by using the is.na() function to identify all the NA rows. Note that the '!' symbol indicates 'not', so in combination the is.na() function identifies rows with NA and the '!' symbol switches it to identify all the 'not-NA' rows.

```
df_17 <- subset(df_17, !is.na(df_17$IGN_DATE))
```

If you repeat your checks you should find that some have changed.

```
range(df_17$IGN_DAY) 
unique(df_17$IGN_MONTH) 
range(df_17$IGN_YEAR)
```

While we no longer have NA show up for IGN_DAY and IGN_YEAR it appears that the Year column includes more than our intended year. You can clean that up in a similar way.

```
df_17 <- subset(df_17, df_17$IGN_YEAR == 2017))
```

At this point you should make sure that both the 2017 data and 2023 data have been cleaned up and contain only valid dates for their respective years

We are now going to subset to only the main fire season (April 1 through October 31) in order to compare fires between seasons. Note that the data in the IGN_DAY column refers to day of the year so April 1 is day 91 and October 31 is day 304.

```
df_17 <- subset(df_17, df_17$IGN_DAY >= 91 & df_17$IGN_DAY <= 304)
```

### Calculating Statistics
You are now ready to calculate statistics. 

_Mean_
```
mean_17 <- mean(df_17$VARIABLE)
mean_23 <- mean(df_23$VARIABLE)
```
_Standard Deviation_
```
sd_17 <- sd(df_17$VARIABLE, na.rm = TRUE) 
sd_23 <- sd(df_23$VARIABLE, na.rm = TRUE) 
```

_Mode_
```
mode_17 <- as.numeric(names(sort(table(df_17$VARIABLE), decreasing = TRUE))[1]) 
mode_23 <- as.numeric(names(sort(table(df_23$VARIABLE), decreasing = TRUE))[1])
```
Note: This code makes a frequency table of fire size variable and sorts it in desending order and extract the first row (i.e. the most frequent value)

_Median_
```
med_17 <- median(df_17$VARIABLE, na.rm = TRUE)
med_23 <- median(df_23$VARIABLE, na.rm = TRUE)
```

_Skewness_
```
skew_17 <- skewness(df_17$VARIABLE, na.rm = TRUE)[1]
skew_23 <- skewness(df_23$VARIABLE, na.rm = TRUE)[1]
```

_Kurtosis_
```
kurt_17 <- kurtosis(df_17$VARIABLE, na.rm = TRUE)[1]
kurt_23 <- kurtosis(df_23$VARIABLE, na.rm = TRUE)[1]
```

_Coefficient of Variation___
```
CoV_17 <- (sd_17 / mean_17) * 100
CoV_23 <- (sd_23 / mean_23) * 100
```

_Normal Distribution Test_
```
norm_17_PVAL <- shapiro.test(df_17$VARIABLE)$p.value
norm_23_PVAL <- shapiro.test(df_23$VARIABLE)$p.value
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
png("Output_Histogram.pngg")
hist(df_year$VARIABLE, breaks = 30, main = "Frequency of Wild Fire Sizes", xlab = "Size of Wild Fire (ha)") #Base R style
dev.off()
```


### Plot Your Data in a Bar Graph

Next, we will create a bar graph showing the sum of wildfires in each month. The first step is to create a new object for each month and then assigning the sum of fires to that object. In addition, the last line of code creates a set of labels for the x-axis of the graph.
```
sumMar = sum(subset(df_year, IGN_Month == "Mar")$VARIABLE, na.rm = TRUE) 
sumApr = sum(subset(df_year, IGN_Month == "Apr")$VARIABLE, na.rm = TRUE) 
sumMay = sum(subset(df_year, IGN_Month == "May")$VARIABLE, na.rm = TRUE) 
sumJun = sum(subset(df_year, IGN_Month == "Jun")$VARIABLE, na.rm = TRUE) 
sumJul = sum(subset(df_year, IGN_Month == "Jul")$VARIABLE, na.rm = TRUE) 
sumAug = sum(subset(df_year, IGN_Month == "Aug")$VARIABLE, na.rm = TRUE) 
sumSep = sum(subset(df_year, IGN_Month == "Sep")$VARIABLE, na.rm = TRUE) 
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
  summarise(sumSize = sum(VARIABLE, na.rm = TRUE)) %>% #sum up the total fire size for each month and pass to GGplot
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

map_T
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

map_RM

png("TmMap.png")
map_RM
dev.off()
```


That's it! Congratulations on completing the tasks for generating the results for Assignment 1. You now need to take your outputs and present them in a document using the guidelines specified in the Assignment 1 page in Brightspace.




