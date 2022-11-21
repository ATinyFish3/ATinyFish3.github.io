*2022-11-21*

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
