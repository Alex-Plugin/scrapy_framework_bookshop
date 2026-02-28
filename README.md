# Scrapy Bookshop Spider

### Project Description:
A Scrapy spider for collecting book information from [books.toscrape.com](https://books.toscrape.com).  
Completed as part of training at [Mate Academy Python Program](https://mate.academy/).

### Tech Stack:
- Python 3.12  
- Scrapy  

## Architecture Overview

- The spider uses a two-level scraping approach:
- The catalog page is parsed to extract basic book data and detail page URLs.
- Each detail page is requested to extract extended book information.
- Pagination is handled recursively until all 50 pages (1000 books) are processed.
- Scrapy's internal request scheduler manages the crawling queue asynchronously.

### Short logigal schema
```
Catalog page
    ↓
Extract basic info
    ↓
Follow detail link
    ↓
Extract additional info
    ↓
Merge data
    ↓
Save item
    ↓
Follow next page
```
### Spider execution flow
```
START
  │
  ▼
Scrapy creates initial Request from start_urls
  │
  ▼
parse(response: catalog page)
  │
  ├── Extract book cards (article.product_pod)
  │
  ├── For each book:
  │       ├── Extract:
  │       │       title
  │       │       price
  │       │       rating
  │       │
  │       └── yield Request(detail_page)
  │               callback = parse_book
  │               meta = {title, price, rating}
  │
  └── Find next_page
          │
          └── yield Request(next_page, callback=parse)
                  (pagination continues)
  
Queue handled by Scrapy scheduler
  │
  ▼
parse_book(response: detail page)
  │
  ├── Extract:
  │       amount_in_stock
  │       category
  │       description
  │       upc
  │
  ├── Combine with:
  │       response.meta["title"]
  │       response.meta["price"]
  │       response.meta["rating"]
  │
  └── yield final item (dictionary)
  
  │
  ▼
Scrapy exports data to books.jl
  │
  ▼
END (after 1000 books)
```


## Task
Here you will scrape https://books.toscrape.com/ website.
For each of 1000 books you need to parse this information:
- title
- price
- amount_in_stock
- rating
- category
- description
- upc

In this task you should use `scrapy` framework for parsing.
And implement only 1 spider to do such job.

When completed it - save all books into `books.jl` file and commit it.
This task doesn't have auto-tests, so test it manually.

Hints:
- use scrapy documentation for searching for all required information;
- use scrapy best practices & learn how to learn new frameworks;
- make your code as clean as possible;
- separate scraping for different steps to make code cleaner.
