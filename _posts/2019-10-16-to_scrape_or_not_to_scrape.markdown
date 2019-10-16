---
layout: post
title:      "To Scrape, or Not to Scrape?"
date:       2019-10-16 14:34:52 +0000
permalink:  to_scrape_or_not_to_scrape
---


This week, I have been working on my first project, a CLI application. The goal is to build a CLI that allows users to make choices, then either scapes data from a website or an API and stores, manipulates, and presents that data.

# **My Inspiration - The Tempestry Project**# 
Given my science teaching background and goals, I wanted to create something that incorporated science data. I am also a knitter, and I decided to create a project that combined both of these interests. I had recently come across the [Tempestry Project](https://www.tempestryproject.com/), which combines climate data and fiber arts.

> "A Tempestry is a wallhanging, or temperature tapestry, that represents the daily high temperature for a given year and location, with January at the bottom and December at the top – think of it like a bar graph.

> All Tempestries use the same yarn colors and temperature ranges, creating an immediately recognizable and globally comparable mosaic of shifting temperatures over time."
> 
> *- The Tempestry Project, https://www.tempestryproject.com*

![](https://i2.wp.com/www.tempestryproject.com/wp-content/uploads/2019/06/New-Normal-Kit-Pic.jpg?zoom=2&fit=900%2C2016&ssl=1)

*You can read more about the Tempestry Project [here](https://www.tempestryproject.com/about/).*
# **My Plan - Knitting Pattern Generator**# 
While the [Files](https://www.tempestryproject.com/files/) page on the Tempestry website gives detailed directions for creating a custom knitting pattern based on a given year and location, the process is complicated. Knitters must visit a search page, work through multiple menus, and are eventually emailed a spreadsheet of temperature data for their chosen year and location. They must then copy the data into a google sheets template to associate each yarn color with a given day's temperature. After collecting my own data in this way, I immediately realized that this process could be automated through a CLI application.

My initial goal was to create a CLI application that would allow users to input a zip code and a year, and the application would automatically generate a knitting pattern in the form of a table. 

```
// ♥ ./bin/climate_pattern_generator
Welcome to the Climate Pattern Generator

Enter a zip code
12193

Enter a year
2000

Please wait while we generate your pattern

What would you like to do next?
1. Print pattern
2. Read more about the Tempestry Project and yarn colors
Type a number to make your choice.
Type exit or back at any time.

1

Here is your daily maximum temperature data for zip code 12193, year 2000.
Complete?  Date         Temperature     Yarn Color
________   2000-01-01   45 deg. F       Grass
________  2000-01-02   51 deg. F       Green Tea
________  2000-01-03   55 deg. F       Green Tea
________  2000-01-04   60 deg. F       Creme Brulee
```

Each table row would represent a day during the chosen year, and the table columns would include that day's maximum temperature, and the associated yarn color representing that temperature. Temperature data would either be collected through an API, or scraped from a website. Using Object Oriented Ruby, I would create a Scraper class to create an object representing each day's data.

# **My First Challenge - Historical Weather Data Access**# 
After setting up my file structure and gems for the project, I needed to figure out a source for my weather data. I thought this would be fairly easy, since current weather data and weekly forecasts are so easy to access! However, as it turns out, multi-day historical weather data is much more difficult to access. I tried out three different data sources, each with its own set of benefits and limitations.

***1. Weather Underground - Scraping***

Weather Underground looked promising primarily because it could display a month's worth of daily maximum temperatures in one page, allowing me to iterate over 12 webpages rather that 365 pages (see example at the bottom of the page [here](https://www.wunderground.com/history/monthly/KFSO/date/2018-1?req_city=Saint%20Albans&req_state=VT&req_statename=Vermont&reqdb.zip=05478&reqdb.magic=1&reqdb.wmo=99999). Another benefit was that the url could be easily modified using string interpolation to include the data I was trying to scrape:
```
"https://www.wunderground.com/history/monthly/#{location}/KPBG/date/#{year}-#{month}"
```
However, when I started trying to access specific data points using css selectors, I realized that the data could not be scraped because the site was using javascript to load the data points after the shell of the page was loaded. As a result of the javascript, all of my css selectors were returning empty arrays when I should have been returned temperature values.

***2. Dark Sky - API***

I started exploring API options, since most other websites I had explored only allowed one day of temperature data per page. I quickly learned that while current and forecast data was easily accessed through free APIs, access to historical data was much more limited and generally blocked by paywalls. The [Dark Sky API](https://darksky.net/dev) seemed most promising. With a free account, I could make up to 1000 data requests a day using a url structured in this way:
```
https://api.darksky.net/forecast/#{key},#{location},#{date}
```

However, I encountered many drawbacks. Location needed to be structures as latitude and longitude coordinates, which would be very challenging for users to input into the CLI. That date structure was equally challenging, as seen below. 
```
REQUEST
https://api.darksky.net/forecast/4a0c86c0489ba2f3b974542fe117ba4b/44.8107,-73.0836,1514782800?exclude=[currently,minutely,hourly,alerts,flags] 

RESULTS
{"latitude":44.8107,
"longitude":-73.0836,
"timezone":"America/New_York",

"daily":{
  "data":[
        {"time":1514782800,"summary":"Clear throughout the day.","icon":"clear-day","sunriseTime":1514809868,"sunsetTime":1514841791,"moonPhase":0.5,"precipIntensity":0,"precipIntensityMax":0,"precipProbability":0,**"temperatureHigh"**:1.77,"temperatureHighTime":1514836800,"temperatureLow":-15.95,"temperatureLowTime":1514894400,"apparentTemperatureHigh":-0.45,"apparentTemperatureHighTime":1514840400,"apparentTemperatureLow":-15.3,"apparentTemperatureLowTime":1514894400,"dewPoint":-15.23,"humidity":0.68,"pressure":1027.15,"windSpeed":2.1,"windGust":9.67,"windGustTime":1514833200,"windBearing":322,"cloudCover":0.12,"uvIndex":1,"uvIndexTime":1514826000,"visibility":8.008,"temperatureMin":-13.67,"temperatureMinTime":1514808000,"temperatureMax":1.77,"temperatureMaxTime":1514836800,"apparentTemperatureMin":-13.02,"apparentTemperatureMinTime":1514808000,"apparentTemperatureMax":-0.45,"apparentTemperatureMaxTime":1514840400}
]
},
"offset":-5}
```
The greatest obstacle was that I would need to make 365 requests to complete one knitting pattern, and only 1000 requests were allowed per day using the free account. This was clearly not feasible, and other APIs that I explored were more expensive and had similar limitations.

***3. The Old Farmers Almanac - Scraping***

Having struck out again, I looked for a website that might include the data I needed, without the javascript issue I had run into with Weather Underground. The [Old Farmer's Almanac Weather History Archive](https://www.almanac.com/weather/history) offered many of the benefits of the previous site, but required scraping a separate web page to access each day's data (see example [here](https://www.almanac.com/weather/history/zipcode/05478/2018-01-01)). I was able to write a method to scrape the first day of the year, and then another method to scrape the next day of the year, as follows. 
*(Note: This code snippet is pre-refactoring, as this is still a work in progress).*

```
def self.scrape_first_day
    zip = ClimatePatternGenerator::CLI.search_terms[0]
    year = ClimatePatternGenerator::CLI.search_terms[1]
    next_year = (year.to_i + 1).to_s

    url = "https://www.almanac.com/weather/history/zipcode/#{zip}/#{year}-01-01"
    html = open(url)
    doc = Nokogiri::HTML(html)
    day = self.new
    day.date = doc.css("div.print-no form").attr("action").value.split("/")[-1]
    day.temperature = doc.css("table.weatherhistory_results td p span.value").children[2].text
    day.color = day.get_color
    day.url = url
    day.next_day_url = "https://www.almanac.com" + doc.css("td.nextprev_next a").attribute("href").value
    @@year_data << day
  end
	
	def self.scrape_next_day
    zip = ClimatePatternGenerator::CLI.search_terms[0]
    year = ClimatePatternGenerator::CLI.search_terms[1]
    next_year = (year.to_i + 1).to_s

    url = self.all[-1].next_day_url
    html = open(url)
    doc = Nokogiri::HTML(html)
    day = self.new
    day.date = doc.css("div.print-no form").attr("action").value.split("/")[-1]
    day.temperature = doc.css("table.weatherhistory_results td p span.value").children[2].text
    day.color = day.get_color
    day.url = url
    day.next_day_url = "https://www.almanac.com" + doc.css("td.nextprev_next a").attribute("href").value
    @@year_data << day
  end

```

When these methods were incorporated into my CLI, I was able to create a working application that generated and printed out a knitting pattern just as I had planned... until I started getting 429 Too Many Request errors. These errors would break my program after collecting anywhere from 40 to 120 days worth of data, out of the 365 that I needed. Ever after experimentation with looping and building in several seconds of sleep between each scrape, I was not able to resolve the problem.

# **So...to scrape, or not to scrape?**# 
That is the question. Having invested so much time into my CLI application, and having created a program that functions the way it was designed, part of me is tempted to continue figuring out how alter my code to get around the limits imposed by the websites I am trying to scrape. But another part of me is concerned about the ethics of scraping in general. I started exploring the issue of ethics, reading reflections by [Roberto Rocha](http://robertorocha.info/on-the-ethics-of-web-scraping/), a journalist who uses scraping  in his research and reporting, as well as [James Densmore](https://towardsdatascience.com/ethics-in-web-scraping-b96b18136f01), a data scientist and consultant.  Rocha offered a set of guiding questions around the ethics of scraping:

> "Can I take this data?
> 
> Can I republish this data?
> 
> Am I overloading the website’s servers?
> 
> What can I use this data for?"
> 
> *- Roberto Rocha, http://robertorocha.info/on-the-ethics-of-web-scraping/*

Densmore included a set of principles that should guide the ethical scraper. I am quoting a few that feel most relevant to my challenge:

> "I, the web scraper will live by the following principles:
> 
> If you have a public API that provides the data I’m looking for, I’ll use it and avoid scraping all together.
> 
> I will request data at a reasonable rate. I will strive to never be confused for a DDoS attack.
> 
> I will respect any content I do keep. I’ll never pass it off as my own.
> 
> I will scrape for the purpose of creating new value from the data, not to duplicate it."
> 
> *- James Densmore, https://towardsdatascience.com/ethics-in-web-scraping-b96b18136f01*

So, will I continue trying to work around my 429 error and scrape 365 different urls to create the project I envision, or will I need to go back to the drawing board to find a more ethically accessible source of data for my project? I am going to take some time to investigate, and will update in the next blog post. 

However, it seems somewhat fitting that I have run into an ethical data access issue with this particular project. The Tempestry Project began around the same time as, and in response to, [fears of government climate change data being lost](https://www.tempestryproject.com/why-the-tempestry-project/) after the 2016 presidential election. As a middle school science teacher, I lost access to many of the climate teaching resources I had always used from sites such as the EPA or NOAA. If we are to continue raising awareness and action around our changing climate, we need free access to historical data. This project is showing that we are not there yet.





