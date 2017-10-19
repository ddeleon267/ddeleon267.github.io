---
layout: post
title:      "CLI Gem Project: Whats On Tap!"
date:       2017-10-18 21:21:52 -0400
permalink:  cli_gem_project_whats_on_tap
---


As I approach the end of my CLI Gem project I feel relieved, excited, and ready to be done! (*So* ready to be done) It feels like I have been working on it for ages, tweaking things here and there, fussing over certain things but also taking some time away from my code to let things breathe. I’m looking forward to sending it out into the world and moving forward. 

<br> ![](http://s.quickmeme.com/img/81/8102ca6afa528219ae16873c044d346f1e0757c30cb0c5fd4e4c685f61931f53.jpg)

**WARNING:** This is going to be long. I would encourage you to grab some tea or coffee before you begin, or perhaps run as fast as you can in the opposite direction. 

If you’ve read my previous blog post on my CLI Gem project, WhatsOnTap, you have a sense of what its objective is. If not, you can watch a brief video overview of my gem **[here](https://www.youtube.com/watch?v=uXCTd8gPlBw&feature=youtu.be)**. You’re also welcome to look at my code [in its entirety](https://github.com/ddeleon267/ddeleon-cli-app). 

Also worth noting that this blog post is more of a beakdown of my code and what it's doing. My [last post](http://ddeleon.com/cli_gem_project_initial_thoughts) is more of a reflection piece (initial thoughts, how I got started), and my [next blog post](http://ddeleon.com/cli_gem_project_final_thoughts) will be as well (final thoughts, areas for improvement, etc.)

Let's dive in.

**WhatsOnTap**

While most of the magic happens in the CLI and Scraper classes, lets talk briefly about the two objects that play a major role in this gem: locations and beers. After all, the whole point of this gem is to give the user data about specific beers and where to find them!

<br>![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTK1rM4o-gZtbT-5XejMiUi9bHLGlnfe2Na0QHlOgV4JR9ZPdRE)

**Location.rb and Beer.rb**
The only significant differences between the two classes are their attributes.

**Location.rb**
```
attr_accessor :name, :num_beers_on_tap, :type
```

**Beer.rb**
```
attr_accessor :name, :brewery, :brewery_location, :type, :abv, :full_description
```

The functionality of these classes is nearly identical:
```
  @@all = []

  def initialize(name)
    @name = name
    @@all << self
  end

  def self.all
    @@all
  end
```

Short and sweet. Each of the two classes has a class variable, @@all, that points to an array. Both objects must be initialized with a name and are then pushed to the @@all array for their respective classes. Each class also has a class reader for @@all.

I will say that while there is obvious repetition here, it is minor enough for me to feel comfortable leaving these two classes as they are. It wouldn’t make sense for the beer class to inherit from the location class, for example. Any other refactoring seems like it would either not make logical sense or would be more trouble than it’s worth based on the small amount of code both classes contain.  

Let’s move on to the code that’s doing all the heavy lifting. 

**Cli.rb**
**#call**

The call method controls the flow of the CLI, and I wanted to be very descriptive of what was happening:

```
def call
    welcome
    get_locations
    list_locations
    get_beers
    list_beers
    get_a_beer_and_list_details
    restart_or_exit
    goodbye
  end
```

While it’s not ideal to have so many method calls, I appreciate that a brief glance tells me exactly what my program is doing, and it doesn't seem to significantly slow things down.

**#welcome**
```
def welcome
    puts ""
    puts "--------------------------------------------------------------------------------"
    puts "                            Welcome to What's On Tap!"
    puts "--------------------------------------------------------------------------------"
    puts ""
 end

```
This method greets the user. Short, sweet, and simple, with a little fluff to help things display nicely.

**#get_locations**

In general, my CLI and my scraper classes do a lot of interacting. This method prompts the user to enter a city, then uses that input with the scraper methods ::get_beer_menu_site and ::make_locations to access a list of location results for the city chosen (all data taken from BeerMenus.com), scrape for relevant data about each location, and instantiate objects (locations) based off of that data. 

```
def get_locations
    puts "Please enter your city to find places offering craft beers on tap in your city."
    puts ""
    city = gets.strip
    WhatsOnTap::Scraper.get_beer_menu_site(city)
    puts ""
    puts " #{city.upcase}"
    puts "#{"-" * (city.length + 2)}"
    WhatsOnTap::Scraper.make_locations
 end
```

**#list_locations**

Does what it says! Having objects to work with makes working with code much easier. This method iterates over the collection of location objects stored in @@all to deliver a numbered list of the name, establishment type, and number of beers on tap for the first five locations in the city that has been chosen. Five seemed like the right number; too many options can be overwhelming. Because my project is potentially scraping a large number of objects, the scraping doesn’t always go as planned, so in some cases data is missing. The “unless” conditionals here help to keep things formatted nicely (and keep things from breaking!) even when this is the case. 

```
def list_locations
    WhatsOnTap::Location.all.each.with_index(1) do |location_object, i|
      print " #{i}. #{location_object.name}"
      print " (#{location_object.type})" unless location_object.type == nil
      puts " ---> #{location_object.num_beers_on_tap}" unless location_object.num_beers_on_tap == nil
      puts ""
    end
 end
```

**#get_beers**

This method is similar in purpose to #get_locations: it gets an input from the user (in this case a number that references a location listed in the method above), then interacts with the scraper class and uses that input to access the BeerMenus results for that location, scrape for data, and instantiate beer objects.

```
def get_beers
    puts ""
    puts "Enter the number of the location for which you'd like to see the beer menu."
    puts "You can also type 'back' to try a new city, or type 'exit' to exit"
    puts ""

    input = gets.strip
    if input.to_i > 0 && input.to_i <= WhatsOnTap::Location.all.size
      location_number = input.to_i - 1
      WhatsOnTap::Scraper.get_beer_list_page(location_number)
      WhatsOnTap::Scraper.make_beers

      name = WhatsOnTap::Location.all[location_number].name
      puts ""
      puts "Location: #{name}"
      puts "#{"-" * (name.length + 11)}"
      puts ""
    elsif input.downcase == "exit"
      exit
    elsif input.downcase == "back"
      puts ""
      get_locations
      list_locations
      get_beers
    else
      puts ""
      puts "Please try again."
      puts ""
      get_beers
    end
 end
```

This method differs from #get_locations in two significant ways, though. It is much more controlled in that the input should be an integer that is greater than zero and no greater than the number of beer objects being listed. This made it easy to control the outcome of this method and to prompt the user to try again if the given input is invalid. There are a number of method calls in this method, and a number of conditionals used, but it was important to me that 1) the user be able to go back or exit at any point in the CLI and 2) that the experience from that point go as smoothly and naturally as possible given the original intention and flow outlined in #call. It took a lot of testing to get this just right, but it was well worth it. 

**#List_beers**

This method is similar in nature to #list_locations in that it is just giving a formatted list of object names (in this case, beers). In the event that a location doesn’t list information about individual beers —or, more likely, the location’s beer data is structured differently and so does not scrape correctly — there is a conditional statement to keep from “breaking” the program and to give a message to the user that more information is not available. In that event, the user is returned to the list of locations generated by his or her “city” input.

```
def list_beers
    if WhatsOnTap::Beer.all.empty?
      puts " There is no individual beer data for this location. Please try again."
      puts ""
      restart_from_location_list
    else
      WhatsOnTap::Beer.all.each.with_index(1) do |beer_object, i|
        puts " #{i}. #{beer_object.name}"
      end
    end
  end
```

**#get_a_beer_and_list_details**

This method takes an integer input from the user; the input refers back to one of the beers that has been listed. If the user input is valid, the method interacts with the scraper class to access the BeerMenu page for the individual beer chosen, scrape for some additional information about that beer, then add this data (as attributes) to the corresponding beer object. If the input is not valid, the user is prompted to try again. The user is also able to exit, go back to the previous list of locations, or to start over with a new city.

```
def get_a_beer_and_list_details
    puts ""
    puts "Enter the number of a beer you'd like to learn more about. You can also type 'list' to see the list of locations again, type 'back' to start over, or type 'exit' to exit."
    puts ""

    beer_input = gets.strip
    if beer_input.to_i > 0 && beer_input.to_i <= WhatsOnTap::Beer.all.size
      beer_number = beer_input.to_i - 1
      WhatsOnTap::Scraper.get_beer_info_page(beer_number)
      WhatsOnTap::Scraper.get_and_set_beer_attributes(beer_number)
      beer_object = WhatsOnTap::Beer.all[beer_number]

      puts ""
      puts "#{beer_object.name}"
      puts "#{"-" * (beer_object.name.length + 2)}"
      puts "Brewery: #{beer_object.brewery}"
      puts "Brewery Location: #{beer_object.brewery_location}"
      puts "Type: #{beer_object.type}"
      puts "ABV: #{beer_object.abv}"
      puts "Description: #{beer_object.full_description}"
      puts ""
    elsif beer_input.downcase == "exit"
      exit
    elsif beer_input.downcase == "back"
      puts ""
      get_locations
      restart_from_location_list
    elsif beer_input.downcase == "list"
      restart_from_location_list
    else
      puts ""
      puts "Please try again."
      puts ""
      get_a_beer_and_list_details
    end
  end
```

**#restart_or_exit**
This method presents a list of options to the user after a full “cycle” of the CLI. The user can exit the program or return to any point in the CLI (the previous list of beers, list of locations, or back to the beginning to try again with a new city.) This seemed more elegant that abruptly exiting the program; as a user I would find it annoying to run the program from the beginning over and over again, particularly if I just wanted to revisit options that were previously listed. Again, you can see that I’m pretty big on maximizing options for the user.

```
def restart_or_exit
    puts "What would you like to do next? Choose one of the following:"
    puts ""
    puts "     Enter 'beers' to see the  beer list for this location again."
    puts "     Enter 'list' to see the previous list of locations for this city."
    puts "     Enter 'restart' to start over with a different city."
    puts "     Enter 'exit' to exit."
    puts ""

    input = gets.strip.downcase
    case input
    when "beers"
      puts ""
      list_beers
      get_a_beer_and_list_details
      restart_or_exit
    when "list"
      puts ""
      restart_from_city
    when "restart"
      puts ""
      restart_after_welcome
    when "exit"
      goodbye
      exit
    else
      puts ""
      puts "Please enter a valid input."
      puts ""
      restart_or_exit
    end
  end
```

**#goodbye**

Short and sweet, this method gives an exiting message to the user.
```
def goodbye
    puts ""
    puts "Until next time, happy drinking!"
  end
```
**#restart_from_location_list, #restart_from_city, and #restart_after welcome (helper methods)**

If it wasn't clear already, I’m pretty fussy about my CLIs -- I want to maximize what the user can do at any point. That being said, I still want to minimize repetition and clunky code. These helper methods each encompass a few of the methods called in #call. Because I wanted the user to experience a smooth and intuitive “flow” of the CLI regardless of where the user is in the program and what input is given, I felt strongly about laying things out *just so* -- even if it meant that there are a ton of method calls going on. Not ideal, but it’s a give-and-take sort of situation, and I find that the speed of my program is not significantly affected. Having these helper methods does help keep the rest of the CLI cleaner and less repetitive, though. 

```
def restart_from_location_list
    list_locations
    get_beers
    list_beers
    get_a_beer_and_list_details
  end

  def restart_from_city
    list_locations
    get_beers
    list_beers
    get_a_beer_and_list_details
    restart_or_exit
  end

  def restart_after_welcome
    get_locations
    list_locations
    get_beers
    list_beers
    get_a_beer_and_list_details
    restart_or_exit
    goodbye
  end
```


**Scraper.rb**

Let's move on to the scraper class. 


![](https://i.pinimg.com/736x/19/cc/7d/19cc7df47ea1f7a3f693b23b819f2bec--so-funny-funny-stuff.jpg)

The scraper class does most of the heavy lifting for this gem, as it should.

**::get_beer_menu_site**
```
def self.get_beer_menu_site(city)
    encoded_city = URI::encode(city)
    @@page = Nokogiri::HTML(open("https://www.beermenus.com/search?q=#{encoded_city}"))
end
```

This class method takes in the modified city input from CLI #get_locations. This input is url-encoded to prevent unexpected inputs, like special characters, from breaking the program or causing unintended results. That encoded input is then used to modify the url argument that is being passed to the Open-URI method #open, which will ultimately store the HTML retrieved from that page. 

Why modify the url at all? Well, the BeerMenus site I’m working off of actually has a search box that makes the rest of this gem possible — it turns out that all city searches are structured with a consistent url ending, e.g. https://www.beermenus.com/search?q=bronx, https://www.beermenus.com/search?q=philadelphia, etc. So by interpolating here, I’m able to easily modify the url with the student’s city input so that the appropriate page is accessed. The results are then stored in the @@page class variable. I also built a class reader ::page for this variable, to keep things streamlined in the code where I’ll  be using this data. 


**::make_locations (and helper methods)**

This method is putting in work! The ultimate goals of this method are 1) to scrape for data based on the web page being accessed (a list of locations serving beer in the city the user has chosen) and 2) instantiate location objects with appropriate attributes based on what has been scraped.  

```
def self.make_locations
    WhatsOnTap::Location.all.clear 
    self.scrape_location_names.each.with_index do |location_name,i|
      location_object = WhatsOnTap::Location.new(location_name)
      location_object.type = self.scrape_location_types[i]
      location_object.num_beers_on_tap = self.scrape_num_beers_on_tap[i]
    end
end
```

Before scraping for anything, though, I first clear the array of location instances being stored in my Location class. This might seem like a weird thing to do in the event of a user going through the program for the first time, but if the user is going back and forth through different stages of the program by trying different cities, for example— doing this helps to prevent duplicate objects and unexpected results.

This method then iterates over the array of scraped location names using each.with_index; this allows me to instantiate objects with a name argument, then access the corresponding values for the location’s establishment type and number of beers on tap (using the index number to then set those attributes for each object). 

There are three helper methods called here that handle the scraping:

```
def self.scrape_location_names
    self.page.css("h3.mb-0.text-normal a").take(5).map {|p| p.text}
  end

  def self.scrape_location_types
    self.page.css("h3.mb-0.text-normal span").take(6).map {|p| p.text.gsub("· ", "")}
  end

  def self.scrape_num_beers_on_tap
    self.page.css("p.caption.text-gray.mb-small").take(6).map {|p| p.text}
  end
```

**::get_beer_list_page (and helper methods)**

This class method is similar to ::get_beer_menu_site, although its behavior is modified based on an integer input from the user, rather than a city, and that integer corresponds to the location the user has chosen. Nokogiri and open-uri are used again to access the page, which shows the beer list for that particular location. To modify the BeerMenus cite appropriately, I call the helper method ::scrape_location_urls. This method scrapes for url data for each location and stores these as an array of strings, similar to the scraping methods mentioned earlier. Since this method returns an array, I use the user’s input (location_number) to access only the url data that corresponds to his/her chosen location in order to modify the url appropriately. I also chose to build a class reader for the class variable @@beer_list_page. 

```
def self.get_beer_list_page(location_number)
    modified_page_url = "https://www.beermenus.com#{self.scrape_location_urls[location_number]}"
    @@beer_list_page = Nokogiri::HTML(open(modified_page_url))
  end

  def self.scrape_location_urls
    self.page.css("h3.mb-0.text-normal a").take(5).map { |link| link['href']}
  end

  def self.beer_list_page
    @@beer_list_page
  end
```

**::make_beers (and helper method ::scrape_beer_names**

This method is similar  to ::make_locations, but its job is to create beer objects.

```
def self.make_beers
    WhatsOnTap::Beer.all.clear 
    self.scrape_beer_names.each do |beer_name|
      WhatsOnTap::Beer.new(beer_name) unless beer_name == ""
    end
  end

  def self.scrape_beer_names
      self.beer_list_page.css("h3.mb-0.text-normal a").take(10).map {|p| p.text}
  end
```

This method first clears the list of beer objects to prevent duplicates and any other funny business. It then calls ::scrape_beer_names, which will scrape the beer list for a given location, storing this data as an array of beer names. It then iterates over this array, using the name of each beer to instantiate a new beer object, provided that a valid name is returned. No other attributes are added yet — more on that later. 

**::self.get_beer_info_page (with helper methods)**

Similar to ::get_beer_list_page, this method modifies the url argument for Nokogiri / open-uri based on an integer input from the user, although in this case that integer corresponds to a chosen beer rather than a chosen location. The helper method #scrape_beer_urls scrapes for url data for each beer listed and stores these as an array of strings. The user’s input is used to access only the url data that corresponds to his/her chosen beer in order to modify the url appropriately. There is a class variable reader for this data as well.

```
def self.get_beer_info_page(beer_number)
    modified_page_url = "https://www.beermenus.com#{self.scrape_beer_urls[beer_number]}"
    @@beer_page = Nokogiri::HTML(open(modified_page_url))
  end

  def self.beer_page
    @@beer_page
  end

  def self.scrape_beer_urls
    self.beer_list_page.css("h3.mb-0.text-normal a").take(5).map { |link| link['href']}
  end
```

**::get_and_set_beer_attributes and ::scrape_individual_beer_data**

Let’s talk about ::scrape_individual_beer_data first, even though it is really a helper method for ::get_and_set_beer_attributes. 

This method is putting in *work* It's also not easy on the eyes, and I’m sure it needs a lot of tinkering. But, this method ultimately helps give the user what he or she wants: more information about tasty beers. It does have a number of different parts, as well as conditionals and bits and pieces of things here and there to keep things from breaking. Most of the troubleshooting in the last stages of my project happened in this method; if something broke, it was probably fixed somewhere in here.

```
def self.scrape_individual_beer_data
    brewery = self.beer_page.css("div.pure-f-body a").text
    brewery_location = self.beer_page.css("p.mt-tiny.mb-0").text

    #formatting type and abv attributes ---> prone to breaking otherwise / behaving unexpectedly
    type_and_abv_data = self.beer_page.css("li.caption.lead-by-icon p").text.split("\n")[1]
    if type_and_abv_data == nil || type_and_abv_data == []
      type_and_abv = ""
    else
      type_and_abv = type_and_abv_data.strip.split("·")
    end
    type = type_and_abv[0].strip unless type_and_abv[0] == nil
    abv = type_and_abv[1].strip.gsub(" ABV", "") unless type_and_abv[1] == nil

    #formatting full_description attribute ---> prone to breaking otherwise / behaving unexpectedly
    notes = self.beer_page.css("div.caption p")[0].text unless self.beer_page.css("div.caption p")[0] == nil
    description = self.beer_page.css("div.caption.beer-desc p").text
    if notes == nil
      full_description = description
    elsif description == nil
      full_description = notes
    elsif notes == nil && description == nil
      full_description = ""
    elsif description.include?("#{notes}")
      full_description = description
    else
      full_description = notes+description
    end

    beer_data = [brewery, brewery_location, type, abv, full_description]
    beer_data.map do |attribute|
       if attribute == nil || attribute == ""
         attribute = "No information available"
       else
         attribute = attribute
       end
     end
  end #end ::scrape_individual_beer_data
```

YOWZA. This method is primarily concerned with scraping for beer data and setting appropriate attributes based on this data. If this data was found on the same page as the beer name data (i.e. on a location’s list of beers), this could have been done in the ::make_beers method. Since it’s being scraped for after that point, though, I had to set these attributes after the objects have already been instantiated, which is a bit messy. 

Scraping for brewery data and brewery location information is relatively straightforward, and those data are both stored as a string that represents the beer’s brewery or brewery location. The beer type and abv are scraped for in a similar way, but these are in the same element and I wanted them separate, so there was a little extra work involved in separating those data and then formatting them nicely like the other examples.

The notes and description data for the beers initially seemed easy to scrape for, but I saw in a significant number of examples that either something would break, or a beer was missing one or both pieces of data, etc. I opted to combine these into one attribute for simplicity, although the number of conditionals involved in doing this should you a sense that in some cases data was missing, in the wrong place, duplicated, etc. There were a number of things that could go wrong which suggests that something here could stand to be more controlled.

Each of these strings (which will ultimately become attributes) is then stored as an element in the array beer_data; ie. the chosen. If anything funky happens, (the element is an empty string, or worse, nil) that attribute is reassigned with en error message as a string. This helps to keep things from breaking, and at least provides a helpful message if something is not what it should be. In most cases, though, things are fine, and the appropriate element is returned. By using map, the return value of this method is an array where each element is a string, even if the string is just to let us know data is missing. 

After all of this madness, ::get_and_set_beer_attributes  finishes the job:

```
def self.get_and_set_beer_attributes(beer_number)
    beer_attribute_array = self.scrape_individual_beer_data
    beer_object = WhatsOnTap::Beer.all[beer_number]

    beer_object.brewery = beer_attribute_array[0]
    beer_object.brewery_location = beer_attribute_array[1]
    beer_object.type = beer_attribute_array[2]
    beer_object.abv = beer_attribute_array[3]
    beer_object.full_description = beer_attribute_array[4]
  end
```

This class method calls on the ::Scrape_individual_beer_data to do its magic, then stores the return value (an array) in a variable. The user's input (beer number) is then used to access the corresponding beer object. Each attribute writer / setter is then called on that beer object, with the argument for each being the corresponding string, accessed by the appropriate index.


And that's my gem! That's all, folks! 




