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


#####
#Read in data and extract attribute table
shp <- vect("./Data/prot_current_fire_points.shp") #read in shp file from current (".") working directory
shp

df <- as.data.frame(shp) #Extract the attribute table as a dataframe, store in variable
class(df) #ensure new variable is a dataframe



#####
#Inspect data
names(df) #see column names
head(df) #see first 6 rows of data



typeof(df$IGNITN_DT) #what is the data type of year?
range(df$FIRE_YEAR) #How many years of data is there?


#We will need to subset the data into an given year
df <- subset(df, df$FIRE_YEAR == 2023)

#We will also need to know the date and the month of each fire
df$IGNITN_DT<- as.Date(df$IGNITN_DT, format = "%Y/%m/%d") #Convert to date string
df$IGN_Day <- yday(df$IGNITN_DT) #Make new column with day of year for the ignition date
df$IGN_Month <- month(df$IGNITN_DT, label = TRUE, abbr = TRUE) #create new column with month of ignition
df$IGN_Year <- year(df$IGNITN_DT) #Make new column with day of year for the ignition date

#Check the range of day and month
range(df$IGN_Day) #Everything seem ok?
unique(df$IGN_Month) #Any months seem odd?
range(df$IGN_Year)

##Remove values with an NA date
#df3 <- subset(df, !is.na(df$IGNITN_DT))

##Check the range of IGN Date
#range(df$IGN_Day) #Anything odd?



#####
#Clean data to appropriate year and calculate descriptive statistics
df_year <- df[which(df$IGN_Year == 2023),] #subset only your year

##Mean
#meanPop <- mean(df$CURRENT_SZ) #This could produce a wrong value (NA) due to a single NA value in data
#meanPop <- mean(df$CURRENT_SZ, na.rm = TRUE) #Use na.rm = TRUE to ignore NA values in calculation

#Check this website for the correct day numbers of your year: https://miniwebtool.com/day-of-the-year-calculator/
#meanSummer <- mean(subset(df_year, IGN_Day >= DDD & IGN_Day <= DDD)$FIRESIZE) #Calculate the mean fire size between July 1 and Aug 31
#meanSummer <- mean(subset(df_year, IGN_Day >= 182 & IGN_Day <= 243)$CURRENT_SZ) #Calculate the mean fire size between July 1 (182) and Aug 31 (243)

df_Summer <- subset(df, IGN_Day >= 182 & IGN_Day <= 243) #Make new variable to hold summer data

meanPop <- mean(df_year$CURRENT_SZ)
meanSummer <- mean(df_Summer$CURRENT_SZ)

#Standard Deviation
sdPop <- sd(df_year$CURRENT_SZ, na.rm = TRUE) #Calculate the SD, ignoring NA values
sdSummer <- sd(df_Summer$CURRENT_SZ, na.rm = TRUE) #Calculate the SD, ignoring NA values
sdSummer

#Mode
modePop <- as.numeric(names(sort(table(df_year$CURRENT_SZ), decreasing = TRUE))[1]) #make frequency table of fire size variable and sort it in desending order and extract the first row (Most Frequent)
modeSummer <- as.numeric(names(sort(table(df_Summer$CURRENT_SZ), decreasing = TRUE))[1]) #make frequency table of fire size variable and sort it in desending order and extract the first row (Most Frequent)

#Median
medPop <- median(df_year$CURRENT_SZ, na.rm = TRUE)
medSummer <- median(df_Summer$CURRENT_SZ, na.rm = TRUE)
medSummer

#Skewness
skewPop <- skewness(df_year$CURRENT_SZ, na.rm = TRUE)[1]
skewSummer <- skewness(df_Summer$CURRENT_SZ, na.rm = TRUE)[1]
skewSummer

#Kurtosis
kurtPop <- kurtosis(df_year$CURRENT_SZ, na.rm = TRUE)[1]
kurtSummer <- kurtosis(df_Summer$CURRENT_SZ, na.rm = TRUE)[1]
kurtSummer 

#CoV
CoVPop <- (sdPop / meanPop) * 100
CoVSummer <- (sdSummer / meanSummer) * 100
CoVSummer

#Normal distribution test
normPop_PVAL <- shapiro.test(df_year$CURRENT_SZ)$p.value
normSummer_PVAL <- shapiro.test(df_Summer$CURRENT_SZ)$p.value
normSummer_PVAL

#####
#Create a table of descriptive stats

samples = c("Population", "Summer") #Create an object for the labels
means = c(meanPop, meanSummer) #Create an object for the means
sd = c(sdPop, sdSummer) #Create an object for the standard deviations
median = c(medPop, medSummer) #Create an object for the medians
mode <- c(modePop, modeSummer) #Create an object for the modes
skewness <- c(skewPop, skewSummer) #Create an object for the skewness
kurtosis <- c(kurtPop, kurtSummer) #Create an object for the kurtosis
CoV <- c(CoVPop, CoVSummer) #Create an object for the CoV
normality <- c(normPop_PVAL, normSummer_PVAL) #Create an object for the normality PVALUE

##Check table values for sigfigs?

means <- round(means, 3)
sd <- round(sd, 3)
median <- round(median, 3)
mode <- round(mode,3)
skewness <- round(skewness,3)
kurtosis <- round(kurtosis,3)
CoV <- round(CoV, 3)
normality <- round(normality, 5)


data.for.table1 = data.frame(samples, means, sd, median, mode)
data.for.table2 = data.frame(samples, skewness, kurtosis, CoV, normality)

outCSV <- data.frame(samples, means, sd, median, mode, skewness, kurtosis, CoV, normality)
write.csv(outCSV, "./FireDescriptiveStats_2020.csv", row.names = FALSE)


#Make table 1
table1 <- tableGrob(data.for.table1, rows = c("","")) #make a table "Graphical Object" (GrOb) 
t1Caption <- textGrob("Table 1: CAPTION FOR YOUR TABLE", gp = gpar(fontsize = 09))
padding <- unit(5, "mm")

table1 <- gtable_add_rows(table1, 
                          heights = grobHeight(t1Caption) + padding, 
                          pos = 0)

table1 <- gtable_add_grob(table1,
                          t1Caption, t = 1, l = 2, r = ncol(data.for.table1) + 1)


table2 <- tableGrob(data.for.table2, rows = c("",""))
t2Caption <- textGrob("Table 2: CAPTION FOR YOUR TABLE", gp = gpar(fontsize = 09))
padding <- unit(5, "mm")

table2 <- gtable_add_rows(table2, 
                          heights = grobHeight(t2Caption) + padding, 
                          pos = 0)

table2 <- gtable_add_grob(table2,
                          t2Caption, t = 1, l = 2, r = ncol(data.for.table2) + 1)



grid.arrange(table1, newpage = TRUE)
grid.arrange(table2, newpage = TRUE)

#Printing a table (You can use the same setup for printing other types of objects (see ?png))
png("Output_Table1.png") #Create an object to print the table to
grid.arrange(table1, newpage = TRUE)
dev.off() #Print table

png("Output_Table2.png") #Create an object to print the table to
grid.arrange(table2, newpage = TRUE) #Create table
dev.off()

#####
#Create and Print a histogram
png("Output_Histogram.png")
hist(df_year$CURRENT_SZ, breaks = 30, main = "Frequency of Wild Fire Sizes", xlab = "Size of Wild Fire (ha)") #Base R style
dev.off()

histogram <- ggplot(df_year, aes(x = CURRENT_SZ)) + #Create new GGplot object with data attached and fire size mapped to X axis
  geom_histogram(bins = 50, color = "black", fill = "white") + #make histogram with 30 bins, black outline, white fill
  labs(title = "Frequency of Wild Fire Sizes", x = "Size of Wild Fire (ha)", y = "Frequency", caption = "Figure 1: Fire size histogram 2020") + #label plot, x axis, y axis
  theme_classic() + #set the theme to classic (removes background and borders etc.)
  theme(plot.title = element_text(face = "bold", hjust = 0.5), plot.caption = element_text(hjust = 0.5)) + #set title to center and bold
  scale_y_continuous(breaks = seq(0, 700, by = 100)) # set y axis labels to 0 - 700 incrimenting by 100

png("Output_Histogram_ggplot.png")
histogram
dev.off()


#LOOK FOR AND CORRECT AN ERROR IN THE CODE BELOW
#histogram <- ggplot(df_year, aes(x = SizeOfFire)) + #Create new GGplot object with data attached and fire size mapped to X axis
#  geom_histogram(bins = 30, color = "black", fill = "white") + #make histogram with 30 bins, black outline, white fill
#  labs(title = "TITLE OF HISTOGRAM", x = "AXIS TITLE", y = "AXIS TITLE", caption = "Figure X: MAKE CAPTION FOR FIGURE") + #label plot, x axis, y axis
#  theme_classic() + #set the theme to classic (removes background and borders etc.)
#  theme(plot.title = element_text(face = "bold", hjust = 0.5), plot.caption = element_text(hjust = 0.5)) + #set title to center and bold
#  scale_y_continuous(breaks = seq(0, 700, by = 100)) # set y axis labels to 0 - 700 incrimenting by 100

#png("Output_Histogram_ggplot.png")
#histogram
#dev.off()

#####
#Creating bar graph
#LOOK FOR AND CORRECT AN ERROR IN THE CODE BELOW
sumMar = sum(subset(df_year, IGN_Month == "Mar")$CURRENT_SZ, na.rm = TRUE) #create new object for March
sumApr = sum(subset(df_year, IGN_Month == "Apr")$CURRENT_SZ, na.rm = TRUE) #create new object for April
sumMay = sum(subset(df_year, IGN_Month == "May")$CURRENT_SZ, na.rm = TRUE) #create new object for May
sumJun = sum(subset(df_year, IGN_Month == "Jun")$CURRENT_SZ, na.rm = TRUE) #create new object for June
sumJul = sum(subset(df_year, IGN_Month == "Jul")$CURRENT_SZ, na.rm = TRUE) #create new object for July
sumAug = sum(subset(df_year, IGN_Month == "Aug")$CURRENT_SZ, na.rm = TRUE) #create new object for August
sumSep = sum(subset(df_year, IGN_Month == "Sep")$CURRENT_SZ, na.rm = TRUE) #create new object for September
months = c("Mar","Apr","May","Jun","Jul", "Aug", "Sep")  #Create labels for the bar graph

png("Output_BarGraph.png") #Create an object to print the bar graph 
barplot(c(sumMar,sumApr,sumMay, sumJun, sumJul, sumAug, sumSep), names.arg = months, 
        main = "TITLE FOR BAR GRAPH", ylab = "AXIS TITLE", xlab = "AXIS TITLE") #Create the bar graph
dev.off() #Print bar graph

#Total Size by Month GGPLOT
#LOOK FOR AND CORRECT ERRORS IN THE CODE BELOW
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



#####
#Creating maps
bc <- as_Spatial(bc_neighbours()) #Get shp of BC bounds
raster::crs(bc)
bc <- spTransform(bc, CRS("+init=epsg:4326")) #project to WGS84 geographic (Lat/Long)

bc <- bc[which(bc$name == "British Columbia" ),] #Extract just the BC province

png("FirstMap.png")
map(bc, fill = TRUE, col = "white", bg = "lightblue", ylim = c(40, 70)) #make map of province
points(df_year$LONGITUDE ,df_year$LATITUDE , col = "red", pch = 16) #add fire points
dev.off()

#####
#####
#Making Maps with tm package
#Make spatial object (Spatial points dataframe) out of data
coords <- df_year[, c("LONGITUDE", "LATITUDE")] #Store coordinates in new object
crs <- CRS("+init=epsg:4326") #store the coordinate system (CRS) in a new object

firePoints <- SpatialPointsDataFrame(coords = coords, data = df_year, proj4string = crs) #Make new spatial Points object using coodinates, data, and projection

map_TM <- tm_shape(bc) + #make the main shape
  tm_fill(col = "gray50") +  #fill polygons
  tm_shape(firePoints) +
  tm_symbols(col = "red", alpha = 0.3) +
  tm_layout(title = "BC Fire Locations 2020", title.position = c("LEFT", "BOTTOM"))

map_TM


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


#How would you calculate the mean center point location and add it to a map?




