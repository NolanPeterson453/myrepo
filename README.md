---
title: "HW 1 Stat 433"
author: "Nolan Peterson"
date: "9/17/2021"
output: github_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
library(rvest)
```
## Intro 

For homework one I choose to scrape the UW Madison faculty page. Below is my code. 
```{r}
## Load in the html of the webpage 
html <- read_html("https://guide.wisc.edu/faculty/")

## Using class uw-people to scrape the proper part of page 
faculty <- html_nodes(html, ".uw-people")

## store html as character vector 
list <- faculty %>% 
  html_children() %>% 
  as.character()
  
## Remove html tags at start of each line 
list <- gsub("<li>", " ", list) 
list <- gsub("<p>", " ", list) 

## replace <br> tags with | as deliminator 
list <- gsub("<br>", " | ", list) 

## remove all other html tags
list <- gsub("<.*?>", " ", list)

## remove an additional  special characters 
list <- gsub("\n", " ", list) 
list <- gsub("\"", " ", list) 


## replace &amp; with and 
list <- gsub("&amp;", "and", list)

## Convert char vector into data frame
 # note that the professors who are missing education information will create NAs in the 4th column.
data <- read_delim(list, delim = "|", col_names = FALSE)

## drop 5th unused column  
data <- data[,-5]


## add column names 
names <- c("Name", "Title", "Dept", "Education")
colnames(data) <- names

## Call head  and structure functions as proof of successful scraping
head(data)
str(data)

```

Additionally I thought it might be interesting to look more closely at the composition of the statistics dept. Below is the code doing that.

```{r}

# finding the subset of all statistics professors and set up a data frame
stats.prof <- data %>% 
  filter(Dept == " Statistics ") %>% 
  as.data.frame()

## setting title as a factor 
stats.prof$Title <- stats.prof$Title %>% as.factor()

## counting occurrences of levels 
counts <- count(stats.prof, Title)$n

## Create plot to show counts of professor types in stat dept 

  # make room for legend 
  par(mar = c(4, 19, 4, 4))
  
  # plot data
  barplot(counts, horiz = TRUE, col = terrain.colors(8), main = "Types of Professors in Stat Dept")
  
  # allow legend to sit off of plot 
  par(xpd=TRUE)
  
  # code for legend 
  legend("bottomleft", inset=c(-1.2,0) ,legend = rev(levels(stats.prof$Title)), fill = terrain.colors(8, rev = TRUE), cex = 1.2, bg = "white")


```

