# Employee_Survey_R
Exploring employee survey responses with R.
## BACKGROUND: This is an initial analysis of an anonymous qualitative survey.  The survey is anonymous to prompt respondents to freely share 
#  their thoughts. The intent of the survey is to get a pulse on an at risk subset of the employee population. This is a way to understand 
#  "What" makes them excited, and "What" makes them unhappy about the organization. This is a way to understand "if" they are thinking of 
#  leaving, if so, "What" could make them stay. This is a small dataset for demonstration purposes.

## LETS GET STARTED
# Install "readxl" to read Excel files
# Installation: The install.packages() command is only needed once. Once a package is installed, you don't need to install it again 
# unless you are updating it or have changed your R environment.
# Loading the Package: Use library() each time you start a new R session and want to use functions
# More information on reading and importing Excel files into R: https://www.datacamp.com/tutorial/r-tutorial-read-excel-into-r
install.packages("readxl")
library(readxl)

# Install tidyverse package (collection of R packages: ggplot2, dplyr, tidyr, readr, purr, tibble and stringr)
# install.packages("tidyverse")
library(tidyverse)

# Read in excel file and assign it to employee_survey variable (NOTE: Make sure the file is closed, i.e. on desktop)
employee_survey <- read_excel("C:/Users/mprev/OneDrive/R Code/Stay.Retention Analysis/Stay.Retention Analysis/Data/GitHub.Survey Data.xlsx")

## DATA EXPLORATION
# See first 6 rows of employee survey data
head(employee_survey)

# See last few rows of data
tail(employee_survey)

# See the structure of the dataframe, original data loaded in as chr (characters).  For the columns that contain numeric data, I had to
# change them from character to numeric data types
str(employee_survey)

# If you want to check the structure of a particular column, i.e. confirm that "Rate_Satisfaction1To10" is numeric
# str(employee_survey$Rate_Satisfaction1To10)

# Summary of dataframe. This will give you quick Min, Median, Mean, Quartiles, Max and NAs for any numeric responses.
summary(employee_survey)

## DATA CLEANING
# If you need to remove rows (ex. If first 9 columns included unnecessary data - like IP addresses.  This wouldn't be needed as 
# this sample survey is anonymous, therefore any identifying data isn't necessary).Remove the first 9 columns.  
# Pass data back to employee_survey variable. See sample code to remove the columns below. 
# employee_survey <- employee_survey[ , -c(1:9)]
# View() or head() data to confirm changes made.

# Delete row(s) of dataframe (if you have unneeded data rows). Our sample data had a first row of data that 
# repeated "Response" across the row which isn't needed. Sample code below deletes 1st row. 
# employee_survey <- employee_survey[-1, ]
# View() or head() data to confirm changes made.

# NOTES: For simplicity, I opted to standardize my column names in the excel file prior to import.The original column names were 
# questions so I renamed them to shortened versions that would be easier to manipulate (See Best Practices for Renaming).  For my original
# data, I imported a mapping file to rename my column headers. Note: if you need to make additional updates to your column headers, maintain
# sequential mapping files. Each subsequent mapping file should take the last set of updated names as its starting point. For instance, the  
# second mapping file should map the already updated names (from the first renaming) to the new set of names you want to apply.

# If you want to change the column names in R, see comments below for code.
# This will print out the list of column names in your R console.  Used this list to create my mapping file.
# print(names(employee_survey))

# Automated Column Renaming Example: 
# 1. Assuming the mapping file is loaded into a dataframe called name_mapping
# If not, import the mapping file, replace with the correct path and sheet name
# name_mapping <- read_excel("path/to/your/name_mapping.xlsx", sheet = "YourSheetName")

# 2. Assuming the dataframe has two columns: originalName and newName (Make sure the column names match the names in your data)
# for(i in 1:nrow(name_mapping)) {
  # colnames(employee_survey)[colnames(employee_survey) == name_mapping$originalName[i]] <- name_mapping$newName[i]
# }

# Validate that the column names were replaced correctly and create a back up file (ex. post_colRename_employee_survey)
# print(names(employee_survey))

# Back-up employee_survey (Do this before and after any major change) View() or head() data to confirm data looks good
original_employee_survey <- employee_survey

# If numeric data imported as chr (character), you will need to convert chr to numeric. This happens if you import data from CSV file.
# If you have numerous columns that you need to convert, you can automate the process with a loop or apply function
# (use ChatGPT or other similar tool to help you write this code)
# Here is sample code to convert "Rate_Satisfaction1To10" from chr to numeric. 
# employee_survey$Rate_Satisfaction1To10 <- as.numeric(as.character(employee_survey$Rate_Satisfaction1To10))

# Check for NAs in Rate_Satisfaction1To10 column
sum(is.na(employee_survey$Rate_Satisfaction1To10))

# Filter the rows with NA to decide how to handle data in the Rate_Satisfaction1To10 column.  
# Windows Keyboard shortcut for pipe operator (%>%) is Ctrl + Shift + M
# MacOS Keyboard shortcut for pipe operator (%>%) is Cmd + Shift + M
rows_with_NA <- employee_survey %>% filter(is.na(Rate_Satisfaction1To10))

# View NA rows in Rate_Satisfaction1To10 column. I decided to leave NAs.  (Note: you can also view by selecting the dataframe, etc.
# in the Environment tab.)
View(rows_with_NA)

## DESCRIPTIVE STATISTICS: Calculate measures like mean, median, and standard deviation for numerical data to get a feel for your data
# Summary() to get quick view of min, max, median, mean and quartiles of a specific column. Note: summary(employee_survey) like above
# will show summary for all columns in survey.
summary(employee_survey$Rate_Satisfaction1To10)

# Standard deviation - sd() to see how much variation or spread in the values.  Small SD, means data points are close together
sd(employee_survey$Rate_Satisfaction1To10, na.rm = TRUE)

# Variance - var() to see the variance (average of the squared difference from the mean). Small variance means data points are generally close to mean
var(employee_survey$Rate_Satisfaction1To10, na.rm = TRUE)

## NUMERICAL DATA VISUALIZATION: Visual representations of data can reveal trends, patterns, and outliers very quickly compared to looking at raw data.
# Histogram of Rate_Satisfaction1To10
# Other blue options "midnightblue", "navy", "darkblue" (Yes, I love blue! :))
hist(employee_survey$Rate_Satisfaction1To10,
     main = "Histogram of Satisfaction Ratings",
     xlab = "Satisfaction Rating",
     breaks = 10,
     col = "blue",
     border = "black")

# Boxplot of Rate_Satisfaction1To10
# Boxplot: Visualizes numerical data distribution based on 5 values - Minimum, 1st quartile (Q1), median, 3rd quartile (Q3),and maximum values.
# Boxplot can help you quickly identify if your data has data points that are outside the minimum and maximum values ("whiskers").
# Data points outside the whiskers (outliers) are often indicated with dots or asterisks on the plot. An outlier is defined as a data point that is
# located outside the whiskers of the box plot, typically 1.5 times the interquartile range above the upper quartile or below the lower quartile
# This visual helps identify data points that fall significantly outside the overall pattern of the data.
boxplot(employee_survey$Rate_Satisfaction1To10,
        horizontal = TRUE,
        main = "Boxplot of Satisfaction Ratings",
        xlab = "Satisfaction Rating",
        col = "cyan")

#_____________________________________________________________________________________________
# CATEGORICAL DATA VISUALIZATION 
# View dataframe to identify categorical columns for analysis.  NOTE: The sorting/filtering features in view don't change
# actual underlying data
View(employee_survey)

# COLUMN: CATEGORICAL DROPDOWN_RISEORFALLOVERTENURE - LETS LOOK AT WHETHER SATISFACTION HAS RISEN OR FALLEN OVER THE COURSE 
# OF THE EMPLOYEE'S TENURE
# Replace blanks in employee_survey with "No Response" - this isn't standard but for visualization, wanted "No Response" vs "NA"

# Change "NA" to "No Response" in 'DropDown_RiseOrFallOverTenure'Column, then save as "employee_w_no_resp_survey for visualization 
# purposes only for this column.  
employee_w_no_resp_survey <- employee_survey %>%
  mutate(DropDown_RiseOrFallOverTenure = ifelse(is.na(DropDown_RiseOrFallOverTenure), "No Response", DropDown_RiseOrFallOverTenure))

# Use 'count()' to count occurrences of each value in 'DropDown_RiseOrFallOverTenure' including "No Response".
rise_or_fall_count_summary <- employee_w_no_resp_survey %>%
  count(DropDown_RiseOrFallOverTenure, name = "Count")

# Display the summary
print(rise_or_fall_count_summary)

# GGPLOT BAR CHART: DropDown_RiseOrFallOverTenure
# Convert counts to percentages
rise_or_fall_count_summary <- rise_or_fall_count_summary %>%
  mutate(Percentage = (Count / sum(Count)) * 100)

# Reorder levels based on your specified order
rise_or_fall_count_summary$DropDown_RiseOrFallOverTenure <- factor(
  rise_or_fall_count_summary$DropDown_RiseOrFallOverTenure,
  levels = c("Rise", "Fall", "No Response")
)

# OPTION 1: Create the RiseOrFallBarChart and include all formatting (NOTE: This includes 3 color palette for bars and a legend)
RiseOrFallBarChart <- ggplot(rise_or_fall_count_summary, aes(x = DropDown_RiseOrFallOverTenure, y = Percentage, fill = DropDown_RiseOrFallOverTenure)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("Rise" = "#483D8B", "Fall" = "#4682B4", "No Response" = "#B0C4DE")) +  # Color vision deficiency friendly palette
  labs(title = "Satisfaction Rising or Falling", x = "Satisfaction Direction", y = "Percentage") +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    axis.text.y = element_text(margin = margin(r = 5)),
    plot.margin = unit(c(1, 1, 1, 1), "lines")
  ) +
  scale_y_continuous(
    labels = function(x) paste0(x, "%"),  # Add % symbol to y-axis labels
    breaks = seq(0, max(rise_or_fall_count_summary$Percentage) + 10, by = 10),  # Extend y-axis beyond last value
    expand = expansion(mult = c(0.05, 0.1))  # Add space between axis and data representation
  )

# Print the plot to view it in RStudio's Plots pane
print(RiseOrFallBarChart)

# OPTION 2:Create the ggplot object and include all formatting (NOTE: This includes monochromatic bar chart and no legend which I prefer)
RiseOrFallBarChart2 <- ggplot(rise_or_fall_count_summary, aes(x = DropDown_RiseOrFallOverTenure, y = Percentage, fill = DropDown_RiseOrFallOverTenure)) +
  geom_bar(stat = "identity", fill = "darkslateblue") +  # Use a single color for all bars since bars are labeled
  labs(title = "Satisfaction Rising or Falling", x = "Satisfaction Direction", y = "Percentage") +
  theme_minimal() +
  theme(
    legend.position = "none",  # Remove the legend
    axis.text.x = element_text(angle = 0, vjust = 0.5, hjust = 0.5),  # Horizontal x-axis labels
    axis.text.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis labels
    axis.title.x = element_text(margin = margin(t = 10)),  # Increase space for x-axis title
    axis.title.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis title
    plot.margin = unit(c(1, 1, 1, 1), "lines")
  ) +
  scale_y_continuous(
    labels = function(x) paste0(x, "%"),  # Add % symbol to y-axis labels
    breaks = seq(0, max(rise_or_fall_count_summary$Percentage) + 50, by = 10),  # Extend y-axis beyond last value
    limits = c(0, max(rise_or_fall_count_summary$Percentage) + 50),  # Extend y-axis limit if needed
    expand = expansion(mult = c(0.05, 0.1))  # Add space between axis and data representation
  )

# Print the plot to view it in RStudio's Plots pane
print(RiseOrFallBarChart2)
#___________________________________________________________________________________________________

# CATEGORICAL 
# COLUMN: DROPDOWN_GROWTHOPP

# CREATE new column with abbreviations for DropDown_GrowthOpp to make it easier to work with data.  Data length is too long.
# Create a named vector of abbreviations
abbreviations_vector <- c("I am confident that I will be able to grow in my role." = "Confident will Grow",
                          "I don’t see myself growing my career at EcoByte in the future." = "No Future Growth",
                          "I would like to grow at EcoByte but I’m not certain that I can." = "Uncertain Growth"
)

# Mutate() to create NEW column in dataframe based on abbreviated vector.  Recode() will match responses to the abbreviations.
#employee_survey <- employee_survey %>%
  #mutate(DropDown_GrowthOpp_AbbreviatedResponse = recode(DropDown_GrowthOpp, !!!abbreviations_vector)) # New column name = DropDown_GrowthOpp_AbbreviatedResponse

# Or Mutate() to OVERWRITE the DropDown_GrowthOpp column data with the abbreviated responses. I opt to overwrite
# the column since we are creating a new dataframe. This keeps the original data in employee_survey unaltered.
employee_survey_plus_abbrev <- employee_survey %>%
  mutate(DropDown_GrowthOpp = recode(DropDown_GrowthOpp, !!!abbreviations_vector)) # New column name = DropDown_GrowthOpp_AbbreviatedResponse

# View df to confirm abbreviated column added
View(employee_survey_plus_abbrev)

# Use 'count()' to count occurrences of each value in DropDown_GrowthOpp
DropDown_GrowthOpp_Summary <- employee_survey_plus_abbrev %>% 
  count(DropDown_GrowthOpp, name = "Count")

# Display the count
print(DropDown_GrowthOpp_Summary)

# GGPLOT BAR CHART: DropDown_DropDown_GrowthOpp
# Convert counts to percentages in a new column
DropDown_GrowthOpp_Summary <- DropDown_GrowthOpp_Summary %>%
  mutate(Percentage = (Count / sum(Count)) * 100)

# Display to confirm that percentage column has been added
print(DropDown_GrowthOpp_Summary)

# Create the ggplot Bar Chart and include all formatting (NOTE: This includes monochromatic bar chart and no legend which I prefer)
DropDown_GrowthOppChart <- ggplot(DropDown_GrowthOpp_Summary, aes(x = DropDown_GrowthOpp, y = Percentage, fill = DropDown_GrowthOpp)) +
  geom_bar(stat = "identity", fill = "midnightblue") +  # Use a single color for all bars since bars are labeled
  labs(title = "Employee Growth Opportunities", x = "Response", y = "Percentage") +
  theme_minimal() +
  theme(
    legend.position = "none",  # Remove the legend
    axis.text.x = element_text(angle = 0, vjust = 0.5, hjust = 0.5),  # Horizontal x-axis labels
    axis.text.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis labels
    axis.title.x = element_text(margin = margin(t = 10)),  # Increase space for x-axis title
    axis.title.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis title
    plot.margin = unit(c(1, 1, 1, 1), "lines")
  ) +
  scale_y_continuous(
    labels = function(x) paste0(x, "%"),  # Add % symbol to y-axis labels
    breaks = seq(0, max(DropDown_GrowthOpp_Summary$Percentage) + 50, by = 10),  # Extend y-axis beyond last value
    limits = c(0, max(DropDown_GrowthOpp_Summary$Percentage) + 50),  # Extend y-axis limit if needed
    expand = expansion(mult = c(0.05, 0.1))  # Add space between axis and data representation
  )

# Print the plot to view it in RStudio's Plots pane
print(DropDown_GrowthOppChart)

#______________________________________________________________________________________________
# COLUMN:  "DropDown_ThoughtAboutLeaving" 
# Replace blanks in employee_survey with "No Response" - this isn't standard but for visualization, wanted "No Response" vs "NA"

# Change "NA" to "No Response" in 'DropDown_ThoughtAboutLeaving'Column, then save as "emp_w_no_resp_Leaving_survey" for visualization 
# purposes only for this column.  
emp_w_no_resp_Leaving_survey <- employee_survey %>%
  mutate(DropDown_ThoughtAboutLeaving = ifelse(is.na(DropDown_ThoughtAboutLeaving), "No Response", DropDown_ThoughtAboutLeaving))

# Use 'count()' to count occurrences of each value in 'DropDown_ThoughtAboutLeaving'.
DropDown_ThoughtAboutLeaving_Summary <- emp_w_no_resp_Leaving_survey %>%
  count(DropDown_ThoughtAboutLeaving, name = "Count")

# GGPLOT BAR CHART: DropDown_ThoughtAboutLeaving
# Convert counts to percentages
DropDown_ThoughtAboutLeaving_Summary <- DropDown_ThoughtAboutLeaving_Summary %>%
  mutate(Percentage = (Count / sum(Count)) * 100)

# Reorder levels based on your specified order
DropDown_ThoughtAboutLeaving_Summary$DropDown_ThoughtAboutLeaving <- factor(
  DropDown_ThoughtAboutLeaving_Summary$DropDown_ThoughtAboutLeaving,
  levels = c("Yes", "No", "No Response")
)

# Create the ggplot object and include all formatting (NOTE: This includes monochromatic bar chart and no legend which I prefer)
DropDown_ThoughtAboutLeavingBarChart <- ggplot(DropDown_ThoughtAboutLeaving_Summary, aes(x = DropDown_ThoughtAboutLeaving, y = Percentage, fill = DropDown_ThoughtAboutLeaving)) +
  geom_bar(stat = "identity", fill = "cyan") +  # Use a single color for all bars since bars are labeled
  labs(title = "% Who Thought of Leaving Recently", x = "Response", y = "Percentage") +
  theme_minimal() +
  theme(
    legend.position = "none",  # Remove the legend
    axis.text.x = element_text(angle = 0, vjust = 0.5, hjust = 0.5),  # Horizontal x-axis labels
    axis.text.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis labels
    axis.title.x = element_text(margin = margin(t = 10)),  # Increase space for x-axis title
    axis.title.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis title
    plot.margin = unit(c(1, 1, 1, 1), "lines")
  ) +
  scale_y_continuous(
    labels = function(x) paste0(x, "%"),  # Add % symbol to y-axis labels
    breaks = seq(0, max(DropDown_ThoughtAboutLeaving_Summary$Percentage) + 40, by = 10),  # Extend y-axis beyond last value
    limits = c(0, max(DropDown_ThoughtAboutLeaving_Summary$Percentage) + 40),  # Extend y-axis limit if needed
    expand = expansion(mult = c(0.05, 0.1))  # Add space between axis and data representation
  )

# Print the plot to view it in RStudio's Plots pane
print(DropDown_ThoughtAboutLeavingBarChart)

#______________________________________________________________________________________________________________

# COLUMN:  "DropDown_StillConsideringLeaving" 
# Replace blanks in employee_survey with "No Response" - this isn't standard but for visualization, wanted "No Response" vs "NA"
# Change "NA" to "No Response" in 'DropDown_StillConsideringLeaving'Column, then save as "emp_w_no_resp_still_Leaving_survey" for visualization 
# purposes only for this column.  
emp_w_no_resp_still_Leaving_survey <- employee_survey %>%
  mutate(DropDown_StillConsideringLeaving = ifelse(is.na(DropDown_StillConsideringLeaving), "No Response", DropDown_StillConsideringLeaving))

# Use 'count()' to count occurrences of each value in 'DropDown_StillConsideringLeaving'.
DropDown_StillConsideringLeaving_Summary <- emp_w_no_resp_still_Leaving_survey %>%
  count(DropDown_StillConsideringLeaving, name = "Count")

# GGPLOT BAR CHART: DropDown_StillConsideringLeaving
# Convert counts to percentages
DropDown_StillConsideringLeaving_Summary <- DropDown_StillConsideringLeaving_Summary %>%
  mutate(Percentage = (Count / sum(Count)) * 100)

# Reorder levels based on your specified order
DropDown_StillConsideringLeaving_Summary$DropDown_StillConsideringLeaving <- factor(
  DropDown_StillConsideringLeaving_Summary$DropDown_StillConsideringLeaving,
  levels = c("Yes", "No", "No Response")
)

# Create the ggplot object and include all formatting (NOTE: This includes monochromatic bar chart and no legend which I prefer)
DropDown_StillThinkingAboutLeavingBarChart <- ggplot(DropDown_StillConsideringLeaving_Summary, aes(x = DropDown_StillConsideringLeaving, y = Percentage, fill = DropDown_StillConsideringLeaving)) +
  geom_bar(stat = "identity", fill = "cyan") +  # Use a single color for all bars since bars are labeled
  labs(title = "% Still Thinking of Leaving", x = "Response", y = "Percentage") +
  theme_minimal() +
  theme(
    legend.position = "none",  # Remove the legend
    axis.text.x = element_text(angle = 0, vjust = 0.5, hjust = 0.5),  # Horizontal x-axis labels
    axis.text.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis labels
    axis.title.x = element_text(margin = margin(t = 10)),  # Increase space for x-axis title
    axis.title.y = element_text(margin = margin(r = 10)),  # Increase space for y-axis title
    plot.margin = unit(c(1, 1, 1, 1), "lines")
  ) +
  scale_y_continuous(
    labels = function(x) paste0(x, "%"),  # Add % symbol to y-axis labels
    breaks = seq(0, max(DropDown_StillConsideringLeaving_Summary$Percentage) + 50, by = 10),  # Dynamically extends y-axis beyond last value
    limits = c(0, max(DropDown_StillConsideringLeaving_Summary$Percentage) + 50),  # Extend y-axis limit if needed
    expand = expansion(mult = c(0.05, 0.1))  # Add space between axis and data representation
  )

# Print the plot to view it in RStudio's Plots pane
print(DropDown_StillThinkingAboutLeavingBarChart)

View(employee_survey)
#______________________________________________________________________________________________________________

# QUALITATIVE ANALYSIS USING TM PACKAGE
# Group Qualitative data by themes to create grouped corpus for efficient text mining. Omit NAs, It's helpful to 
# document the original full length questions, the truncated version of the questions and it's themed group.
# You can complete text analysis on each individual question if you prefer.  

# Group1_Positive_Ques (Interpetation: Group1_WhatGeneratesPostiveFeelings)
employee_survey$Group1_Positive_Ques <- apply(employee_survey[, c("Open_WhatAboutJobJumpOutBed", "Open_WhatChangedMindLeaving")],
                                          1, function(x) paste(na.omit(x), collapse = " "))

# Group2_Negative_Ques (Interpetation: Group2_WhatGeneratesNegativeFeelings)
employee_survey$Group2_Negative_Ques <- apply(employee_survey[, c("Open_WhatAboutJobHitSnooze", "Open_WhatMakeYouSearchNewJob",
                                                                "Open_WhatPromptedThoughtsLeaving")], 1, function(x) paste(na.omit(x), collapse = " "))

# Group3_Change_Ques (Interpetation: Group3_WhatToChange)
employee_survey$Group3_Change_Ques <- apply(employee_survey[, c("Open_WhatChangeAboutWork", "Open_Change1ThingIncreaseSatisf",
                                                              "Open_SuggestionsForImplementation", "Open_WhatMakeYouStay",
                                                              "Open_WhatEnableGrowth")], 
                                        1, function(x) paste(na.omit(x), collapse=" "))

# Group4_Unknown_PosOrNeg (Interpetation: Group4_ResponseCanBePosOrNegative)
employee_survey$Group4_Unknown_PosOrNeg <- apply(employee_survey[, c("Open_HowFeelNow",
                                                                   "Open_WhatCauseRiseOrFall", "Open_AnythingShareAboutExperience")], 
                                             1, function(x) paste(na.omit(x), collapse=" "))

# Backup Qualitative Groups
original_employee_survey$Group1_Positive_Ques <- employee_survey$Group1_Positive_Ques
original_employee_survey$Group2_Negative_Ques <- employee_survey$Group2_Negative_Ques
original_employee_survey$Group3_Change_Ques <- employee_survey$Group3_Change_Ques
original_employee_survey$Group4_Unknown_PosOrNeg <- employee_survey$Group4_Unknown_PosOrNeg

#  View Backup Qualitative Group
View(original_employee_survey$Group1_Positive_Ques)

# Install TM package specifically designed for text mining tasks like corpus creation, cleaning, analysis, and visualization.
# install.packages("tm")
library(tm)

# Install SnowballC package for stemming (reduces words to their base form)
# install.packages("SnowballC")
library(SnowballC)

# Create corpus groups for preproccessing steps (Needed to get data ready for frequency counts and word clouds)
corpus_group1 <- Corpus(VectorSource(employee_survey$Group1_Positive_Ques))
corpus_group2 <- Corpus(VectorSource(employee_survey$Group2_Negative_Ques))
corpus_group3 <- Corpus(VectorSource(employee_survey$Group3_Change_Ques))
corpus_group4 <- Corpus(VectorSource(employee_survey$Group4_Unknown_PosOrNeg))

# Create back-ups of corpus_groups prior to performing transformation
original_corpus_group1 <- corpus_group1
original_corpus_group2 <- corpus_group2
original_corpus_group3 <- corpus_group3
original_corpus_group4 <- corpus_group4

# Preprocessing for groups
# Transformation Group1: Preprocess text to convert text to lowercase to ensure uniformity
# Transformation Group1: Remove numbers, punctuations, and common stopwords
# Transformation Group1: Strip whitespace to clean up the text
# NOTE: Received warning messages that transformation dropped documents. Reloaded data to compare before and after.  
# No data was lost besides the intended data removed in preproccessing steps.  You may see the same warning. In this case, you can ignore.

corpus_group1 <- tm_map(corpus_group1, content_transformer(tolower)) 
corpus_group1 <- tm_map(corpus_group1, removeNumbers)
corpus_group1 <- tm_map(corpus_group1, removePunctuation)
corpus_group1 <- tm_map(corpus_group1, removeWords, stopwords("english"))
corpus_group1 <- tm_map(corpus_group1, stripWhitespace)

# NOTE: Truncate words to stem form - saved to a separate corpus. Want to compare groups with/without stemming to see if meaningful context 
# lost with stemming. Opting not to perform stemming on the corpus_groups because in this case, lost meaningful context.
# corpus_group1stem <- tm_map(corpus_group1, stemDocument)

# Transformation Group2: Preprocess text to convert text to lowercase to ensure uniformity
# Transformation Group2: Remove numbers, punctuations, and common stopwords
# Transformation Group2: Strip whitespace to clean up the text
corpus_group2 <- tm_map(corpus_group2, content_transformer(tolower)) 
corpus_group2 <- tm_map(corpus_group2, removeNumbers)
corpus_group2 <- tm_map(corpus_group2, removePunctuation)
corpus_group2 <- tm_map(corpus_group2, removeWords, stopwords("english"))
corpus_group2 <- tm_map(corpus_group2, stripWhitespace)

# NOTE: Truncate words to stem form - saved to a separate corpus. Want to compare groups with/without stemming to see if meaningful context 
# lost with stemming. Opting not to perform stemming on the corpus_groups because in this case, lost meaningful context.
# corpus_group2stem <- tm_map(corpus_group2, stemDocument)

# Transformation Group3: Preprocess text to convert text to lowercase to ensure uniformity
# Transformation Group3: Remove numbers, punctuations, and common stopwords
# Transformation Group3: Strip whitespace to clean up the text
corpus_group3 <- tm_map(corpus_group3, content_transformer(tolower)) #Received a warning message that transformation dropped documents. Reload data to compare before and after.  Also make sure to save corpus groups prior to running transformation.
corpus_group3 <- tm_map(corpus_group3, removeNumbers)
corpus_group3 <- tm_map(corpus_group3, removePunctuation)
corpus_group3 <- tm_map(corpus_group3, removeWords, stopwords("english"))
corpus_group3 <- tm_map(corpus_group3, stripWhitespace)

# NOTE: Truncate words to stem form - saved to a separate corpus. Want to compare groups with/without stemming to see if meaningful context 
# lost with stemming. Opting not to perform stemming on the corpus_groups because in this case, lost meaningful context.
# corpus_group3stem <- tm_map(corpus_group3, stemDocument)

# Transformation Group4: Preprocess text to convert text to lowercase to ensure uniformity
# Transformation Group4: Remove numbers, punctuations, and common stopwords
# Transformation Group4: Strip whitespace to clean up the text
corpus_group4 <- tm_map(corpus_group4, content_transformer(tolower)) #Received a warning message that transformation dropped documents. Reload data to compare before and after.  Also make sure to save corpus groups prior to running transformation.
corpus_group4 <- tm_map(corpus_group4, removeNumbers)
corpus_group4 <- tm_map(corpus_group4, removePunctuation)
corpus_group4 <- tm_map(corpus_group4, removeWords, stopwords("english"))
corpus_group4 <- tm_map(corpus_group4, stripWhitespace)

# NOTE: Truncate words to stem form - saved to a separate corpus. Want to compare groups with/without stemming to see if meaningful context 
# lost with stemming. Opting not to perform stemming on the corpus_groups because in this case, lost meaningful context.
# corpus_group4stem <- tm_map(corpus_group4, stemDocument)

# Inspect groups
# Inspect the first few entries of the grouped-question corpus
inspect(corpus_group4[1:5])
# inspect(corpus_group4stem[1:5])

# Create the Document-Term Matrix (transforms the corpus into a structured format suitable for analysis)
dtm_group1 <- DocumentTermMatrix(corpus_group1)
dtm_group2 <- DocumentTermMatrix(corpus_group2)
dtm_group3 <- DocumentTermMatrix(corpus_group3)
dtm_group4 <- DocumentTermMatrix(corpus_group4)

# Create Document-Term Matrix with stem groups (Will compare word clouds with/without stemming)
# dtm_group1stem <- DocumentTermMatrix(corpus_group1stem)
# dtm_group2stem <- DocumentTermMatrix(corpus_group2stem)
# dtm_group3stem <- DocumentTermMatrix(corpus_group3stem)
# dtm_group4stem <- DocumentTermMatrix(corpus_group4stem)


# Create backup of dtm_groups
original_dtm_group1 <- dtm_group1
original_dtm_group2 <- dtm_group2
original_dtm_group3 <- dtm_group3
original_dtm_group4 <- dtm_group4

# Create backup of dtm stem groups
# original_dtm_group1stem <- dtm_group1stem
# original_dtm_group2stem <- dtm_group2stem
# original_dtm_group3stem <- dtm_group3stem
# original_dtm_group4stem <- dtm_group4stem

# Inspect the DTM for the grouped corpus 
inspect(original_dtm_group3[1:5, 1:5])

# Inspect the stem DTM for the grouped corpus 
# inspect(original_dtm_group3stem[1:5, 1:5])

# Sum word frequencies for groups 
# Convert DTM to matrix 
mat_group1 <- as.matrix(dtm_group1)
mat_group2 <- as.matrix(dtm_group2)
mat_group3 <- as.matrix(dtm_group3)
mat_group4 <- as.matrix(dtm_group4)

# Sum word frequencies for stem groups 
# Convert DTM to matrix for stem groups
# mat_group1stem <- as.matrix(dtm_group1stem)
# mat_group2stem <- as.matrix(dtm_group2stem)
# mat_group3stem <- as.matrix(dtm_group3stem)
# mat_group4stem <- as.matrix(dtm_group4stem)

# Sum word frequencies across all documents for each term 
word_freq_group1 <- colSums(mat_group1)
word_freq_group2 <- colSums(mat_group2)
word_freq_group3 <- colSums(mat_group3)
word_freq_group4 <- colSums(mat_group4)

# Sum stem word frequencies across all documents for each term 
# word_freq_group1stem <- colSums(mat_group1stem)
# word_freq_group2stem <- colSums(mat_group2stem)
# word_freq_group3stem <- colSums(mat_group3stem)
# word_freq_group4stem <- colSums(mat_group4stem)

# Now create the dataframe with proper names and frequencies 
word_freq_group1_df <- data.frame(word = colnames(mat_group1), freq = word_freq_group1, row.names = NULL)
word_freq_group2_df <- data.frame(word = colnames(mat_group2), freq = word_freq_group2, row.names = NULL)
word_freq_group3_df <- data.frame(word = colnames(mat_group3), freq = word_freq_group3, row.names = NULL)
word_freq_group4_df <- data.frame(word = colnames(mat_group4), freq = word_freq_group4, row.names = NULL)

# Now create the stem dataframe with proper names and frequencies 
# word_freq_group1stem_df <- data.frame(word = colnames(mat_group1stem), freq = word_freq_group1stem)
# word_freq_group2stem_df <- data.frame(word = colnames(mat_group2stem), freq = word_freq_group2stem)
# word_freq_group3stem_df <- data.frame(word = colnames(mat_group3stem), freq = word_freq_group3stem)
# word_freq_group4stem_df <- data.frame(word = colnames(mat_group4stem), freq = word_freq_group4stem)

# Ensure the data frame is ordered correctly 
word_freq_group1_df <- word_freq_group1_df[order(word_freq_group1_df$freq, decreasing = TRUE), ]
word_freq_group2_df <- word_freq_group2_df[order(word_freq_group2_df$freq, decreasing = TRUE), ]
word_freq_group3_df <- word_freq_group3_df[order(word_freq_group3_df$freq, decreasing = TRUE), ]
word_freq_group4_df <- word_freq_group4_df[order(word_freq_group4_df$freq, decreasing = TRUE), ]

# Ensure the stem data frame is ordered correctly 
# word_freq_group1stem_df <- word_freq_group1stem_df[order(word_freq_group1stem_df$freq, decreasing = TRUE), ]
# word_freq_group2stem_df <- word_freq_group2stem_df[order(word_freq_group2stem_df$freq, decreasing = TRUE), ]
# word_freq_group3stem_df <- word_freq_group3stem_df[order(word_freq_group3stem_df$freq, decreasing = TRUE), ]
# word_freq_group4stem_df <- word_freq_group4stem_df[order(word_freq_group4stem_df$freq, decreasing = TRUE), ]

# View top rows of df
head(word_freq_group1_df)
head(word_freq_group2_df)
head(word_freq_group3_df)
head(word_freq_group4_df)

# View top rows of stem df
# head(word_freq_group1stem_df)
# head(word_freq_group2stem_df)
# head(word_freq_group3stem_df)
# head(word_freq_group4stem_df)

# Back up word_freq_group1_df
original_word_freq_group1_df <- word_freq_group1_df
original_word_freq_group2_df <- word_freq_group2_df
original_word_freq_group3_df <- word_freq_group3_df
original_word_freq_group4_df <- word_freq_group4_df

# Back up word_freq_group1stem_df
# original_word_freq_group1stem_df <- word_freq_group1stem_df
# original_word_freq_group2stem_df <- word_freq_group2stem_df
# original_word_freq_group3stem_df <- word_freq_group3stem_df
# original_word_freq_group4stem_df <- word_freq_group4stem_df

# NEW #Combine the word_freq_group_dfs to one dataframe for further analysis or visualizations - USE FOR TIDY TEXT SENTIMENT ANALYSIS
# Stack the data frames vertically
total_word_freq_df <- bind_rows(word_freq_group1_df, word_freq_group2_df, 
                                word_freq_group3_df, word_freq_group4_df)

# Sum the frequencies for each word
total_word_freq_groups_df <- total_word_freq_df %>%
  group_by(word) %>%
  summarise(total_freq = sum(freq)) %>% 
  ungroup() # remove the grouping structure in case additional analysis is needed on entire df

# Order the combined dataframe by total_freq
total_word_freq_groups_df <- total_word_freq_groups_df %>%
  arrange(desc(total_freq))

# View(total_word_freq_groups_df)

# VISUALIZATION (WORDCLOUDS)
# Install wordcloud
# install.packages("wordcloud") (Generates basic word clouds.  We will try Wordcloud2 later that has more features and customization options)
library(wordcloud)
# install.packages("RColorBrewer")
# library(RColorBrewer)

# Check structure of word_freq_group1_df (positive questions)
str(word_freq_group1_df)

# Wordcloud for word_freq_group1_df (positive question group) & word_freq_group1stem_df (positive question group with stemming) 
# (min.freq = 1)(min freq of words = 1)(rot.per - rotation percentage)
# 0.35 means 35% of words in cloud will be rotated by 90 degrees, remaining 65% will be horizontal.  #8 refers to number of different colors to be used
# in specified RColorBrewer palette Dark2)
# NOTE: Determined min.freq = 1 is too cluttered, high level of words freq = 1, For word_freq_group1_df min.freq = 3 or 2 is best
# wordcloud(words = word_freq_group1_df$word, freq = word_freq_group1_df$freq, min.freq = 1,
          #max.words = 100, random.order = FALSE, rot.per = 0.35,
          #colors = brewer.pal(8, "Dark2"))
# wordcloud(words = word_freq_group1stem_df$word, freq = word_freq_group1stem_df$freq, min.freq = 1,
          #max.words = 100, random.order = FALSE, rot.per = 0.35,
          #colors = brewer.pal(8, "Dark2"))
wordcloud(words = word_freq_group1_df$word, freq = word_freq_group1_df$freq, min.freq = 2,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))
# wordcloud(words = word_freq_group1stem_df$word, freq = word_freq_group1stem_df$freq, min.freq = 2,
          #max.words = 100, random.order = FALSE, rot.per = 0.35,
          #colors = brewer.pal(8, "Dark2"))
wordcloud(words = word_freq_group1_df$word, freq = word_freq_group1_df$freq, min.freq = 3,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))
# wordcloud(words = word_freq_group1stem_df$word, freq = word_freq_group1stem_df$freq, min.freq = 3,
          #max.words = 100, random.order = FALSE, rot.per = 0.35,
          #colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group2_df (negative question group).  Min.freq = 2. Check out difference between min.freq set to 2 and 3
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 2,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group2_df (negative question group).  Min.freq = 3.
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 3,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group3_df (Change question group).  Min.freq = 2. Check out difference between min.freq set to 2 and 3
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 2,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group3_df (Change question group).  Min.freq = 3.
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 3,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group4_df (Unknown question group).  Min.freq = 2. Check out difference between min.freq set to 2 and 3
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 2,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# Wordcloud for word_freq_group4_df (Unknown question group).  Min.freq = 3.
wordcloud(words = word_freq_group2_df$word, freq = word_freq_group2_df$freq, min.freq = 3,
          max.words = 100, random.order = FALSE, rot.per = 0.35,
          colors = brewer.pal(8, "Dark2"))

# SENTIMENT ANALYSIS - FOCUSED ON POSITIVE AND FULL TEXT SENTIMENT. USE AS TEMPLATE FOR ALL QUESTION GROUPS
# Install packages
# install.packages("syuzhet")
library(syuzhet)

# Create vector for positive themed group from employee survey positive ques
corpus_positive_text <- Corpus(VectorSource(employee_survey$Group1_Positive_Ques))

# Apply the as.character function to convert each entry to character string
positive_text_vector <- sapply(corpus_positive_text, as.character)

# View positive_text vector
View(positive_text_vector)

# Perform sentiment analysis
positive_text_sentiment_scores <- get_sentiment(positive_text_vector, method = "syuzhet")

# View first few sentiment scores
head(positive_text_sentiment_scores)

# Calculate average sentiment score for the positive group
average_positive_text_sentiment_scores <- mean(positive_text_sentiment_scores)

# Summarize sentiment in more detail
positive_text_sentiment_summary <- summary(positive_text_sentiment_scores)

# Print  the average sentiment and summary
print(average_positive_text_sentiment_scores)
print(positive_text_sentiment_summary)

# EXPLORE OUTLIERS
# Create a df that includes the text(vector) and the sentiment scores
positive_text_sentiment_df <- data.frame(text = positive_text_vector, sentiment = positive_text_sentiment_scores)

# View positive_text_sentiment_df
View(positive_text_sentiment_df)

# Find minimum sentiment score and it's associated text
min_positive_text_sentiment <- min(positive_text_sentiment_scores)
min_positive_text <- positive_text_sentiment_df$text[which(positive_text_sentiment_scores == min_positive_text_sentiment)]

# Find maximum sentiment score and it's associated text
max_positive_text_sentiment <- max(positive_text_sentiment_scores)
max_positive_text <- positive_text_sentiment_df$text[which(positive_text_sentiment_scores == max_positive_text_sentiment)]

# Print texts with most extreme sentiment scores
cat("Most Negative Text:", min_positive_text, "\n")
cat("Most Positive Text:", max_positive_text, "\n")

# VISUALIZE THE SENTIMENT
# Histogram of positive scores
hist(positive_text_sentiment_scores, main = "Positive Sentiment Score Distribution", xlab = "Sentiment Score", breaks = 10)

# Boxplot of positive sentiment scores
# boxplot(positive_text_sentiment_scores, horizontal = TRUE, main = "Positive Sentiment Score Distribution", xlab = "Sentiment Score")

# Boxplot of sentiment scores with added color and adjusted frequency axis
boxplot(positive_text_sentiment_scores, 
        horizontal = TRUE, 
        main = "Positive Sentiment Score Distribution", 
        xlab = "Sentiment Score", 
        col = "orange", # Add color
        ylim = c(min(positive_text_sentiment_scores) - 1, max(positive_text_sentiment_scores) + 1)) # Adjust frequency markers

# Histogram of sentiment scores with adjusted frequency axis and color
hist(positive_text_sentiment_scores, 
     main = "Positive Sentiment Score Distribution", 
     xlab = "Sentiment Score", 
     breaks = 10, 
     ylim = c(0, 14), # Adjust the frequency axis
     col = "skyblue") # Add color

# create a histogram with a color palette
# ggplot(data.frame(sentiment = positive_text_sentiment_scores), aes(x = sentiment)) +
# geom_histogram(binwidth = 0.5, fill = "skyblue", color = "black") +
# labs(title = "Positive Sentiment Score Distribution", x = "Sentiment Score", y = "Frequency") +
# theme_minimal()

# Lets combine all comments from the positive, negative, change and unknown_PosORNeg groups into one field.
employee_survey <- employee_survey %>% unite("FullText", 19:22, na.rm = TRUE, remove = FALSE, sep  = " ")

# Create vector
corpus_FullText <- Corpus(VectorSource(employee_survey$FullText))

# Preprocess the text: remove stop words
corpus_FullText <- tm_map(corpus_FullText, content_transformer(tolower))  # Convert to lower case
corpus_FullText <- tm_map(corpus_FullText, removePunctuation)  # Remove punctuation
corpus_FullText <- tm_map(corpus_FullText, removeNumbers)  # Remove numbers
corpus_FullText <- tm_map(corpus_FullText, removeWords, stopwords("en"))  # Remove stop words

# Apply the as.character function to convert each entry to character string
FullText_vector <- sapply(corpus_FullText, as.character)

# Perform sentiment analysis
FullText_sentiment_scores <- get_sentiment(FullText_vector, method = "syuzhet")

# View first few sentiment scores
head(FullText_sentiment_scores)

# Calculate average sentiment score for the FullText group
average_FullText_sentiment_scores <- mean(FullText_sentiment_scores)

# Summarize sentiment in more detail
FullText_sentiment_summary <- summary(FullText_sentiment_scores)

# Print  the average sentiment and summary
print(average_FullText_sentiment_scores)
print(FullText_sentiment_summary)

# EXPLORE OUTLIERS
# Create a df that includes the text(vector) and the sentiment scores
FullText_sentiment_df <- data.frame(text = FullText_vector, sentiment = FullText_sentiment_scores)

# View FullText_sentiment_df
# View(FullText_sentiment_df)

# Find minimum sentiment score and it's associated text
min_FullText_sentiment <- min(FullText_sentiment_scores)
min_FullText <- FullText_sentiment_df$text[which(FullText_sentiment_scores == min_FullText_sentiment)]

# Find maximum sentiment score and it's associated text
max_FullText_sentiment <- max(FullText_sentiment_scores)
max_FullText <- FullText_sentiment_df$text[which(FullText_sentiment_scores == max_FullText_sentiment)]

# Print texts with most extreme sentiment scores
cat("Most Negative Text:", min_FullText, "\n")
cat("Most Positive Text:", max_FullText, "\n")

# VISUALIZE THE SENTIMENT
# Histogram of Full Text scores
hist(FullText_sentiment_scores, main = "Full Text Sentiment Score Distribution", xlab = "Sentiment Score", breaks = 10)

# Boxplot of Full Text sentiment scores
# boxplot(FullText_sentiment_scores, horizontal = TRUE, main = "Full Text Sentiment Score Distribution", xlab = "Sentiment Score")

# Boxplot of sentiment scores with added color and adjusted frequency axis
boxplot(FullText_sentiment_scores, 
        horizontal = TRUE, 
        main = "Full Text Sentiment Score Distribution", 
        xlab = "Sentiment Score", 
        col = "orange", # Add color
        ylim = c(min(FullText_sentiment_scores) - 1, max(FullText_sentiment_scores) + 1)) # Adjust frequency markers

# Histogram of sentiment scores with adjusted frequency axis and color
hist(FullText_sentiment_scores, 
     main = "Full Text Sentiment Score Distribution", 
     xlab = "Sentiment Score", 
     breaks = 10, 
     ylim = c(0, 14), # Adjust the frequency axis
     col = "skyblue") # Add color


# SENTIMENT ANALYSIS USING TIDYTEXT (Another sentiment analysis tool)
# install packages (if you haven't already)
# Load libraries (load at each new session)
# install.packages(tidytext)
library(tidytext)
library(stopwords)

# We will take this new column and the satisfaction score.
FullTextData = subset(employee_survey, select = c(FullText, Rate_Satisfaction1To10))

# Tokenize and unnest the data so you can conduct sentiment analysis.  This splits a column into "tokens", basically one word per row.
# Also removes stop words
FullTextDataUnnest <- FullTextData %>% 
  unnest_tokens(word, FullText) %>% 
  anti_join(stop_words)

# Do a wordcount so we can begin to visualize sentiment 
WordCounts <- FullTextDataUnnest %>% count(word) %>% arrange(desc(n))

# Load "bing" sentiment lexicon.  "Bing" lexicon categorizes words as negative or positive sentiment. This is best for general purpose
# sentiment analysis.  
bing_lexicon <- get_sentiments("bing")

# Join word counts with the "bing" sentiment lexicon.
WordSentiments <- WordCounts %>% inner_join(bing_lexicon, by = "word")

# Calculate overall sentiment.  If you have employee IDs, you can calculate sentiment by employee ID.  
# Positive words = 1, Negative words = -1
OverallSentiment <- WordSentiments %>% 
  mutate(sentiment = ifelse(sentiment == "positive", 1, -1),
         sentiment_score = sentiment * n) %>% 
  summarise(total_sentiment = sum(sentiment_score))

# Print overall sentiment score
print(OverallSentiment)

# Calculate the total frequencies of positive and negative sentiments
PosNegSentimentTotal <- WordSentiments %>%
  group_by(sentiment) %>%
  summarise(total = sum(n))

# Visualize the distribution of positive and negative words
PosNegSentimentTotal %>%
  ggplot(aes(x = sentiment, y = total, fill = sentiment)) +
  geom_bar(stat = "identity") +
  theme_minimal() +
  theme(legend.position = "none", # Removes the legend
    axis.title.x = element_text(margin = margin(t = 10)),  # Adjust the top margin of x-axis title
    axis.title.y = element_text(margin = margin(r = 10))) + # Adjust the right margin of y-axis title
  expand_limits(y = c(0, max(PosNegSentimentTotal$total) * 1.2)) + # Expand y-axis
  labs(title = "Distribution of Sentiments", x = "Sentiment", y = "Total Frequency")

# Let's use Wordclould again to get a picture of all the words.
# Let's install Wordcloud2 package
# install.packages("wordcloud2")
library(wordcloud2)

# Create a Circle word cloud of all the words
employee_surveyWordCloud1 <- wordcloud2(WordCounts, color = "random-dark", shape = 'circle')

# Save as html to open in browser (It will save in the same place as your R code file.)
htmlwidgets::saveWidget(employee_surveyWordCloud1, "employee_surveyWordCloud1.html")

# Filter WordCounts to only include words with freq >= 5. Check your df to confirm name of freq column.
WordCountsFiltered = subset(WordCounts, n >= 5)

# Create a Circle word cloud of filtered words
employee_surveyWordCloud2 <- wordcloud2(WordCountsFiltered, color = "random-dark", shape = 'circle')

# Save as html to open in browser (It will save in the same place as your R code file.)
htmlwidgets::saveWidget(employee_surveyWordCloud2, "employee_surveyWordCloud2.html")
