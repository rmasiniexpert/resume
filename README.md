---
title: "Scraping my future company"
subtitle: "Scraping example project"
author: "Rodrigo Masini"
output: rmarkdown::github_document
---

```{r, echo = FALSE}
knitr::opts_chunk$set(
  fig.path = "README_figs/README-"
)
```

  I have been working **as an independent contractor Data Scientist for companies from healthcare, retail, educational, financial, marketing and law industry for more than 3 years**. Unfortunately, after COVID, the number of new contracts drop it a lot.  

  Because of that, I decided to apply for a data scientist position. For a hire manager this can be an opportunity to check my skills in a small project that I called: *"What does a market wants from a data scientist?"*

  I will be attempting to clarify what the market are searching and requiring from a Data Scientist by developing a web scraper for [Indeed job postings](https://indeed.com).

  Web scraping Indeed jobs with R and can easily be accomplished with the [rvest](https://cran.r-project.org/web/packages/rvest/index.html) package. With this package, I will get the relevant information from Indeed’s website is a straight forward process.

  First, I will be loading the required packages.

```{r}
pacman::p_load("tidyverse","rvest","xml2","stringi","strex")
```

  Then I’ll have a look at how to get job titles from the web page. I want to look for Data Scientist jobs in Orlando, Fl.

## Getting data from Indeed jobs

  After we are done with that, we will copy the link address and store the URL in a variable called URL. Then we will use the xml2 package and the read_html function to parse the page. In short, this means that the function will read in the code from the webpage and break it down into different elements (div, span, p, etc.) for you to analyze it.

In the code below, I will show you how to get the page into R for you to analyze it.

### Extracting html site structure

I will extract the html structure from Indeed using the xml2 package.

```{r}
url <- "https://www.indeed.com/jobs?q=Data+Scientist&l=Orlando%2C+FL"
page <- xml2::read_html(url)
```

After, I'm ready to extract relevant nodes from the XML object.

I call elements like divs (div), spans (span), paragraphs (p) or anchors (a) nodes, after they have been parsed by the xml2::read_html() function. Besides element nodes there are also attribute nodes and text nodes.

But wait…how do I know where to find all the relevant information I'm looking for? Well, that is the hard part of developing a successful scraper. Luckily, Indeed’s website is not very hard to scrape.

So, what I will be doing is looking at the source code of the website and also inspecting individual elements.

### Scraping job titles

The **job titles are located under the anchor tag**. When I look more into it I can also see it is located under the job title CSS selector and the xpath **a[@class="jobtitle"]**. This makes it so much easier to find individual pieces on a website. Now you might ask yourself what CSS Selectors are and what an xpath is.

**Xpath:** a path to specifically extract certain parts from a tree-structured document such as XML or HTML. The path can be very specific and makes it possible to grab certain parts from a website easily.

**CSS Selectors:** A CSS selector has a similar function to xpath. Namely, locating certain nodes in a document and extracting information from these nodes. Every CSS selector can be translated into an equivalent xpath but not the other way around.

So let’s construct the xpath:

**//a[@data-tn-element = "jobTitle"]**. Bam! I get all job titles. Notice that if I have included //* instead of //a in my code below, the star acts as a wildcard and selects all elements or nodes not just the anchor node. For Indeed’s website, the **attribute data-tn-element** is always under the anchor node so the wild card symbol wouldn’t be necessary.

In the code below I'm selecting all div nodes and specify the xpath from where we grab the attribute title to get all job titles from the website.

```{r}
page %>% 
    rvest::html_nodes("div") %>%
    rvest::html_nodes(xpath = '//*[@data-tn-element = "jobTitle"]') %>%
    rvest::html_attr("title")
```


