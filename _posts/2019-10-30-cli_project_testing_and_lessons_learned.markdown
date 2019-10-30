---
layout: post
title:      "CLI Project: Testing and Lessons Learned"
date:       2019-10-30 08:34:21 -0400
permalink:  cli_project_testing_and_lessons_learned
---


*In my last blog post, "[To Scrape, or not to Scrape](https://jessesbyers.github.io/toscrapeornottoscrape)", I laid out the plan for my first Command Line Application project and the challenges I encountered in accessing daily weather data for an entire year. This post will update how the project has come together, and share some of the lessons learned from testing and troubleshooting the program.*

[Explore the project on gihub at this link](https://github.com/jessesbyers/climate_pattern_generator/tree/master)

* Use the **master branch** to see the full version (full year of data, taking 20 minutes to generate a knitting pattern)


* Use the **demo branch** to see the demo version (see all features within a few seconds, with fewer days of data)


# Climate Pattern Generator
I successfully created a CLI application that would automatically generate a knitting pattern using weather data from a location and year chosen by the user. Here's how it works:

 **Get Search Terms**: User inputs a valid U.S. zip code and year
 
![](https://i.imgur.com/q1CxSU2.png)

 **Weather Preview**: The program uses the zip code and year to scrape 5 days of weather data and matches each day's maximum temperature with a corresponding yarn color. This allows the user to confirm this is the data they are looking for, or they may choose to start a new search at this point.
 
![](https://i.imgur.com/V27seIY.png)

 **Create Knitting Pattern**: If the preview looks correct, the user can choose to generate a pattern for the entire year. This process takes approximately 20 minutes in the full version (master branch), since 365 separate URLs from the Old Farmer's Almanac Weather History Archive are scraped. Users can watch a YouTube video tutorial about the knitting project while they wait for their pattern to be generated and printed. *In the demo version (demo branch), this process takes only a few seconds and scrapes a smaller sample of data.*
 
 ![](https://i.imgur.com/z0j7MQX.png)
 
 ...
 
 ![](https://i.imgur.com/Z0CfU4t.png)


**Daily Weather Snapshot**: The user can choose any day from the year, and see a snapshot of temperature and precipitation data from that day.

![](https://i.imgur.com/Oj7lrHo.png)

# How it works
The project includes 4 main classes: 

* The **CLI class** is responsible for interacting with the user and displaying data based on user input. It includes a simple call method, a get_search_terms method, menus, and the logic to format and display the data requested by the user.


* The **Scraper class** is responsible for scraping data from the Old Farmer's Almanac, and assigning attributes to each scraped object. It includes an initalize and scrape_attributes method, a get_color method to assign color attributes, and a convert_temp method to convert temperature values.


* The **Weather class** is responsible for calling the Scraper.new method to create each weather data object, each representing a day. It includes the methods responsible for creating a collection of weather objects to represent an entire year (self.year), or a shorter preview period (self.preview).


* The **Color class** is responsible for transforming a hard-coded chart of temperature-to-color conversions (residing in the bin file) to individual color objects, which are then assigned as attributes for each day by the Scraper class.

```
color_chart = [
  ["Currant", 121, 1000],
  ["Garnet", 116, 120],
  ["Rooibos", 111, 115],
  ["Hollyberry", 106, 110],
  ["Firecracker", 101, 105],
  ["Cranberry", 96, 100],
  ["Red", 91, 95],
  ["Victorian", 86, 90],
  ["Papaya", 81, 85],
  ["Orange", 76, 80],
  ["Conch", 71, 75],
  ["Caution", 66, 70],
  ["Semolina", 61, 65],
  ["Creme Brulee", 56, 60],
  ["Green Tea", 51, 55],
  ["Peapod", 46, 50],
  ["Grass", 41, 45],
  ["Everglade", 36, 40],
  ["Tranquil", 31, 35],
  ["Calypso", 26, 30],
  ["Whirlpool", 21, 25],
  ["Baltic", 16, 20],
  ["Fjord", 11, 15],
  ["Ciel", 6, 10],
  ["Celestial", 1, 5],
  ["Sapphire", -4, 0],
  ["Solstice", -9, -5],
  ["Lullaby", -14, -10],
  ["Amethyst", -19, -15],
  ["Indigo", -24, -20],
  ["Blackberry", -29, -25],
  ["Coal", -1000, -30]
]

color_chart.each do |color_array|
  color = color_array[0]
  min = color_array[1]
  max = color_array[2]
  ClimatePatternGenerator::Color.new(color, min, max)
end
```

```
  def get_color
    ClimatePatternGenerator::Color.all.each do |color_row|
      if self.max_temp.to_f.round >= color_row.min && self.max_temp.to_f.round <= color_row.max
        self.color = color_row.color
      end
    end
   self.color
  end
```

The methods within these classes were fairly straightforward to set up initially to develop a functioning program. However, as I began testing the program, I ran into a number of issues which required me to make the code more complex, through the use of conditionals and re-assignment of object attributes.

# Takeaways and Lessons Learned

**1. A CLI application only works if it can accommodate the extreme and irregular cases, not just the ordinary cases.**

I ran into a number of irregular conditions to account for in this project, and devised the following solutions:

Most years have 365 days, while leap years have 366 days. I used the built-in  .leap? method in the Ruby Date class to address this.

```
  def self.year
    ClimatePatternGenerator::Scraper.clear
    @@all.clear
    if Date.leap?(ClimatePatternGenerator::CLI.year.to_i)
      366.times do 
        @@all << ClimatePatternGenerator::Scraper.new
        sleep 4
      end
    else
      365.times do
        @@all << ClimatePatternGenerator::Scraper.new
        sleep 4
      end
    end
  end
```

When data is unavailable for a particular day, the Old Farmer's Almanac automatically pulls data from the next closest weather station. As I explored data from my own town, I found that in various years the data came from Vermont, New York, or Quebec. When the data came from Canadian weather stations, all of the measurements were in metric units. I added a convert_temp method to check the units attribute and convert any Celsius measurements to Fahrenheit.

```
def convert_temp
    if self.temp_units == "°C"
      self.max_temp = ((self.max_temp.to_f * 1.8) + 32).round
      self.min_temp = ((self.min_temp.to_f * 1.8) + 32).round
      self.mean_temp = ((self.mean_temp.to_f * 1.8) + 32).round
      self.temp_units = "°F"
    end
  end
```


**2. A program can break at any interaction point, and each problem may require a different custom error message or strategy.**


Addressing possible break points and errors impacted the CLI class the most. My code needed to not only account for people inputting invalid menu choices, but also inputting invalid zip codes and years. After experimenting with various approaches, I found that the best solution was to use results of the Scraper class to uncover whether user input was invalid. Since the Farmers Almanac had a built-in error message for URLs with invalid year and location input, I could use that to detect errors and then code my own error message.

```
def scrape_attributes
    html = open(self.url)
    doc = Nokogiri::HTML(html)

    if doc.css("p").first.text == "Weather history data is not available for the date you have selected."
      attributes = {}
    else
      attributes = {
      * #... attributes and css selectors for scraped data *
        }
  
    end
  end
```

We can then check whether the Scraper.all array is empty in order to print an error message from the CLI class:

```
  def print_preview
    if ClimatePatternGenerator::Scraper.all == nil
      puts ""
      puts "  ERROR: Please try again. Weather history data is not available for the date and location you have selected."
      puts ""
      get_search_terms
    else
		......
```

This approach was very efficient in that I did not need to code all of the criteria for a valid year or zip code, but instead could rely on the website, which was already accounting for all of those details internally.

**3. The data in a CLI application will always be limited by the quality of the original source data.**


After careful consideration ([see blog post](https://jessesbyers.github.io/to_scrape_or_not_to_scrape)), I decided to scrape my data from the Old Farmer's Almanac Weather History site rather than from an API. The primary reason for this decision was that I wanted to make it as simple as possible for a user to input their location, and it was the best choice in that regard. The APIs that I explored were limited in that searches were made primarily by weather station, but not all weather stations collected the necessary data for my program. 

The Old Farmer's Almanac eliminated this issue, but was a second-hand source of data. As a result, I found several irregularities that likely resulted from the transfer of data from the original source, the National Climatic Data Ceter, to the Farmer's Almanac (such as a reported 999 mph wind gust, or many instances of missing data points).  I decided to detect whether data was missing on a particular day, and reassign the remaining data attributes from that day, because those were the days that tended to have the inconsistencies in the data.

```
    if self.max_temp == "No data."
      self.max_temp = "No data available"
      self.min_temp = "No data available"
      self.mean_temp = "No data available"
      self.precipitation = "No data available"
      self.temp_units = ""
      self.color = "No data available"
    end
```

Since my application was designed for craft projects, the user-friendlyness of the data source was more important than extreme accuracy.  If I were to collect data for a more scientific project in the future, I would go straight to the API.

**Want to check out my project to learn more?**

[Explore the project on gihub at this link](https://github.com/jessesbyers/climate_pattern_generator/tree/master)

* Use the **master branch** to see the full version (full year of data, taking 20 minutes to generate a knitting pattern)


* Use the **demo branch** to see the demo version (see all features within a few seconds, with fewer days of data)
