---
layout: post
title: "When indices carry more information than expected"
date: 2020-07-26 19:19:35 -0300
tags: statistics
---

If you walk down Buenos Aires' streets you may find one of these bike racks:

{:refdef: style="text-align: center;"}
![A bike rack](/assets/2020-07-26-bikes.jpg)
{: refdef}

These are free-to-use bikes provided by the city, which anyone can ride after downloading a mobile app and signing up. Whenever you want to use one of them, you just request one using the app and get a code which lets you unlock one. You are free to return it to any of the racks scattered around the city.

The bikes' usage information is published [yearly][bike-data] as a .csv file containing a row for each single trip. All of the users' information is properly anonymized but at least one gets a unique, numerical user ID associated to each account, as well as gender and age data for each ID.

One thing that surprised me after toying a bit with the 2018 dataset is that user IDs were inversely correlated with age: their correlation coefficient is -0.206929, which is too large to ascribe to random noise. At first I thought that this could be explained by the fact that IDs were handed out sequentially, so that probably older and less tech-savvy users started using bikes only after a younger family member or friend showed them how to use the app, and thus generally ended up with larger user IDs. But this is nonsense, since these two variables are not correlated but _inversely_ correlated, so that being older correlates with having a _smaller_ ID.

At this point I decided to look at a scatterplot to try to understand what was happening:

{:refdef: style="text-align: center;"}
![A scatterplot](/assets/2020-07-26-scatterplot.png)
{: refdef}

As you can see, points on the lower left corner appear to slant to the right, which explains the correlation. Can you see why this happens? A closer look at the graph shows that there are no users younger than about 18, and the data confirms that all users must be over the age of 17 at the time they sign up. Therefore, all users having an ID close to zero must have been at least 17 at the time the bike racks were installed, and so by 2018 they should be at least in their mid-20s. In this way, the slope of the asymptote on the lower left corner gives a rough estimate of how many users sign up per year. 

The following table shows the lowest user ID associated to each age:

| Age | Lowest user ID |
|-----|---------|
| 17 | 181123 |
| 18 | 184646 |
| 19 | 153897 |
| 20 | 123799 |
| 21 | 93360 |
| 22 | 41690 |
| 23 | 5259 |
| 24 | 4089 |
| 25 | 3462 |
| 26 | 506 |
| 27 | 401 |

The fact that the lowest ID of 17-year-olds is smaller than that of 18-year-olds seems to be a data-entry mistake -- the next lowest 17-year-old ID is 240540. You can even see this point (very faintly!) in the scatterplot and check that it is far lower than any point lying on the same vertical line.

Just by looking at the table above, we can guess that the bike program started in about 2009, which seems to be backed up by this [article](bike-article), and that user registration started to pick up momentum in about 2013.

Sometimes IDs are more than what meets the eye!

[bike-data]: https://data.buenosaires.gob.ar/dataset/bicicletas-publicas
[bike-article]: https://www.buenosaires.gob.ar/noticias/ecobici-llega-las-80-estaciones-y-800-bicicletas