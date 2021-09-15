# Spotify-EDA

---
title: "Spotify"
knit: (function(input_file, encoding) {
  out_dir <- 'docs';
  rmarkdown::render(input_file,
 encoding=encoding,
 output_file=file.path(dirname(input_file), out_dir, 'index.html'))})
author: "Vishal Moradia"
date: "26/08/2021"
output: html_document
---


```{r}
spotify <- read.delim(file.choose(), sep = '#')
```

```{r}
library(tidyverse)
library(ggplot2)
library(lubridate)
```
##### Segmenting the data into different years. (2017 - 2020)

```{r}
spotify <- mutate(spotify, Year = year(spotify$Date))
spotify2017 <- spotify[spotify$Year==2017,]
spotify2018 <- spotify[spotify$Year==2018,]
spotify2019 <- spotify[spotify$Year==2019,]
spotify2020 <- spotify[spotify$Year==2020,]
```

##### taking glimpse of the data.

```{r}
head(spotify2017)
str(spotify2017)
```

##### taking look at the number of songs and artists present in the data.


```{r}
paste("Number of Songs", length(unique(spotify$Track.Name)))
paste("Number of artists", length(unique(spotify$Artist)))
```


#### Plotting a graph for top 5 artists with number of streams.


```{r}
stream_df <- aggregate(x = spotify$Streams, by = list(spotify$Artist), FUN = sum)
colnames(stream_df) <- c("Artist", "NumberofStreams")
stream_df <- stream_df[order(stream_df$NumberofStreams, decreasing = T),]
```

##### looking at the new data derived.

```{r}
head(stream_df)
```

## Spotify streams of an Artist.

```{r}
ggplot(data = stream_df[1:5,], aes(x = Artist, y = NumberofStreams, fill = Artist)) +
  geom_bar(stat = "identity") + geom_text(aes(label = NumberofStreams), size = 2.5) +
  xlab("Artist") + 
  ylab("Total Streams") + 
  ggtitle("Artist VS Total Streams")
```

##### Post Melone was the most listened to artist.


## Number of days a song stays at No.1 in TOP 200

```{r}
top_song <- spotify[spotify$Position==1,]
number_1 <- aggregate(x = top_song$Position, by = list(top_song$Track.Name, top_song$Artist), sum)
colnames(number_1) <- c("Song", "Artist", "Numberofdays_1")
number_1 <- number_1[order(number_1$Numberofdays_1, decreasing = T),]
```

```{r}
ggplot(number_1[1:5,], aes(x = Song, y = Numberofdays_1, fill = Artist))+
  geom_bar(stat = "Identity") +
  ggtitle("Number of Days at #1 VS Song title") +
  xlab("Song Title") +
  ylab("Number of days at #1")+
  geom_text(aes(label = Numberofdays_1), size = 2.5)
  
```


##### Dance Monkey from Tones and I stayed at the top for most number of days. (120 days).


## Artist with most Number one Songs.


```{r}
best_artist <- as.data.frame(table(number_1$Artist))
colnames(best_artist) <- c("Artist", "Numberofsongs_1")
best_artist <- best_artist[order(best_artist$Numberofsongs_1, decreasing = T),]
```

```{r}
ggplot(best_artist[1:5,], aes(x = Artist, y = Numberofsongs_1, fill = Artist)) + geom_bar(stat="Identity") +
  geom_text(aes(label = Numberofsongs_1), size = 2.5)+
  ggtitle("Artist VS The number of songs at #1")+
  xlab("Artist")+
  ylab("Number of songs at #1")
```


##### Drake came out be the best artist with the most number 1 songs in the database. No wonder why he is the best !!

## Artist that spent most days at Number 1


```{r}
number_1_days <- aggregate(x = number_1$Numberofdays_1, by = list(number_1$Artist), sum)
colnames(number_1_days) <- c("Artist", "numberofdays_1")
number_1_days <- number_1_days[order(number_1_days$numberofdays_1, decreasing = T),]
```

```{r}
ggplot(number_1_days[1:5,], aes(x = Artist, y = numberofdays_1, fill = Artist))+
  geom_bar(stat = "Identity") +
  ggtitle("Number of Days at #1 VS Artist")+
  geom_text(aes(label = numberofdays_1), size = 2.5)+
  xlab("Artist")+
  ylab("Number of days at #1")
```

##### Post strikes again !! With stagering 174 days at the top of the industry. 
