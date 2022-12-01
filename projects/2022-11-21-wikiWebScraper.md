*2022-11-21*

*Most recent developments can be found on the [GitHub Repo](https://github.com/ATinyFish3/wikiWebScraper) (this page will be updated asap with each new development)*

# Wikipedia Web Scraper Project

Using the BeautifulSoup Python module I plan to build a web scraper for wikipedia pages. I think my aim for this will be to get user input on what information the user wants. This could include summary information e.g. get the first paragraph of a page, the contents table, search for strings the user specifies and anything else I think could be useful functionality.

## Basic Setup

I started with a very simple implementation that just got the contents headers.

```
import requests
from bs4 import BeautifulSoup

def scraper():
    #Base URL to use, get from user input later
    URL = "https://en.wikipedia.org/wiki/Web_scraping"

    #Get page html
    page = requests.get(URL)

    #Get soup object
    soup = BeautifulSoup(page.content, "html.parser")

    #Get headers from contents table || get from user input later
    contentsHeaders = soup.find(id="toc")

    print(contentsHeaders.prettify())

    print(contentsHeaders.text)

if __name__ == '__main__':
    scraper()
```

I need to update this to be able to easily work with user input as well as refactor the code into modules - e.g. contents module that can be called later only if the user wants that data. Also storing soup results rather than just printing them and using the .split() method to get each part of the result - currently each header is all part of one long string object.

*2022-12-01*

## First Improvements and Adding Functionality

### Modularisation

I added improvements by splitting the code into different functions as some parts were needing to be used multiple times. I now have the scraper() function, getHTML() function - that gets the page content of a given url, and added functionality in the userSearch() and summary() functions.

### User Search Functionality

userSearch():

```
def userSearch(searchTerm, numResults):
    searchReq = "https://en.wikipedia.org/w/index.php?search=" + searchTerm + "&title=Special:Search&profile=advanced&fulltext=1&ns0=1"
    soup = getHTML(searchReq)

    allResults = soup.find_all('div', class_='mw-search-result-heading')

    #Need to add validation

    if len(allResults) != 0:
        return allResults[:numResults]
    else:
        return False
```
Allows the user to enter a search term and returns the closest matches that Wikipedia has - at the moment just the top match is then used, future development would allow a user to select the best match.

This means the user does not have to find the relevant URL themselves (which would somewhat defeat the purpose of the web scraper.

### Summary Function

summary():

```
def summary(pageURL):
    soup = getHTML(pageURL)
    firstStuff = soup.find('div', class_='mw-parser-output')
    if firstStuff == None:
        sys.exit('Error in extracting summary info')
    para1 = firstStuff.find('p').text
    sent1 = para1.split('.')[0] + '.'

    return para1, sent1
```
Returns the first paragraph and sentance of a Wikipedia page to act as summary information.

This is now the main area for improvement as this code is not very 'intelligent' e.g.:

Searching for 'web scraping' returns: `Web scraping, web harvesting, or web data extraction is data scraping used for extracting data from websites.[1] Web scraping software may directly access the World Wide Web using the Hypertext Transfer Protocol or a web browser. While web scraping can be done manually by a software user, the term typically refers to automated processes implemented using a bot or web crawler. It is a form of copying in which specific data is gathered and copied from the web, typically into a central local database or spreadsheet, for later retrieval or analysis.` This is the desired result.

Searching for 'red' returns an error.

Searching for 'web' returns: `Web most often refers to:'

These are not the desired results.

### Getting the Desired Results

I found that you can get all the <p> blocks using .select('p') which returns all the paragraphs which I could then get the first item to get the first paragraph.
    
```
def summary(pageURL):
    soup = getHTML(pageURL)
    #firstStuff = soup.find('div', class_='mw-parser-output')
    try:
        paras = soup.select('p')
        #para1 = firstStuff.find('p').text # sometimes this is not first paragraph
        #sent1 = para1.split('.')[0] + '.'
    except:
        sys.exit('Error in extracting summary info')

    return paras
```

Now 'web scraping' still returns: `Web scraping, web harvesting, or web data extraction is data scraping used for extracting data from websites.[1] Web scraping software may directly access the World Wide Web using the Hypertext Transfer Protocol or a web browser. While web scraping can be done manually by a software user, the term typically refers to automated processes implemented using a bot or web crawler. It is a form of copying in which specific data is gathered and copied from the web, typically into a central local database or spreadsheet, for later retrieval or analysis.`

Searching for 'red' returns: `Red is the color at the long wavelength end of the visible spectrum of light, next to orange and opposite violet. It has a dominant wavelength of approximately 625–740 nanometres.[1] It is a primary color in the RGB color model and a secondary color (made from magenta and yellow) in the CMYK color model, and is the complementary color of cyan. Reds range from the brilliant yellow-tinged scarlet and vermillion to bluish-red crimson, and vary in shade from the pale red pink to the dark red burgundy.[2]`

However 'web' still returns an undesirable result as the first "paragraph" is actually an unordered list. This is probably better to fix by getting the unordered lists as well and figuring out if the first paragrpah tag or unordered list tag is the first "paragraph".

Testing on other search results reveals that sometimes the actual first paragraph is actually paras[1] not paras[0].

## QoL Improvements

I also decided to put the print statements in the summary() function not the main scraper() function, outputting the summary in the future will depend on user input.
    
```
def summary(pageURL):
    soup = getHTML(pageURL)
    # firstStuff = soup.find('div', class_='mw-parser-output')
    try:
        paras = soup.select('p')
    except:
        sys.exit('Error in extracting summary info')  # keep to handle exceptionos
    numParas = len(paras)  # don't access summarResult[5] if only 2 elements
    # print(numParas)
    if numParas != 0:
        para1 = paras[0].text
        print(para1)
    if numParas >= 2:
        para2 = paras[1].text
        print(para2)
        # print(summaryResult[1].text)
```

Then improved this to only print out the correct "first paragraph":

```
    if numParas != 0:
        para1 = paras[0].text
        print(len(para1))
        if len(para1) <= 1:
            if numParas >= 2:
                para2 = paras[1].text
                print('p2')
                print(para2)
        else:
            print('p1')
            print(para1)
```

Then got rid of bad nested IFs:

```
    if numParas >= 2 and len(paras[0]) <= 1:
        print(paras[1].text)
    elif numParas != 0:
        print(paras[0].text)
    else:
        print('Summary unavailable')
```

Improve userSearch() return, from:

```
    if len(allResults) != 0:
        return allResults[:numResults]
    else:
        return False
```

to:

```
    if allResults:
        return allResults[:numResults]
    else:
        return False
```

## Get All text from a Wikipedia Page

This was easy to adapt from the summary() function:

```
def allText(pageURL):  # Return all text from wiki page
    soup = getHTML(pageURL)
    try:
        paras = soup.select('p')
    except:
        sys.exit('Error in extracting summary info')  # keep to handle exceptionos
    for i in paras:
        print(i.text)
```
