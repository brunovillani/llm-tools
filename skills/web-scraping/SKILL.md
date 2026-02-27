---
name: Web Scraping
description: Extract data from websites systematically and reliably
version: 1.0.0
author: LLM Tools Project
tags: [web, scraping, data-extraction, automation]
---

# Web Scraping Skill

This skill provides comprehensive guidance for extracting data from websites using various techniques and tools. Use this when you need to gather information from web pages programmatically.

## Overview

Web scraping involves extracting data from websites by parsing HTML, handling pagination, and managing various web technologies. This skill covers both simple static page scraping and more complex scenarios requiring JavaScript execution.

## When to Use This Skill

Use this skill when:
- ✅ You need to extract structured data from websites
- ✅ No API is available for the data you need
- ✅ You need to monitor website content changes
- ✅ You're gathering data for research or analysis
- ✅ You need to automate data collection tasks

Don't use this skill when:
- ❌ The website provides an API (use api-integration skill instead)
- ❌ The website's robots.txt disallows scraping
- ❌ The data is behind authentication you don't have permission for
- ❌ The website has anti-scraping measures that would require circumvention

## Prerequisites

**Required Libraries**:

For basic scraping:
```bash
pip install requests beautifulsoup4 lxml
```

For JavaScript-heavy sites:
```bash
pip install selenium webdriver-manager
```

For advanced scraping:
```bash
pip install scrapy playwright
```

**Tools**:
- Python 3.7+
- Chrome/Firefox browser (for Selenium)
- Basic HTML/CSS knowledge

## Instructions

### Step 1: Analyze the Target Website

Before scraping, understand the website structure:

1. **Inspect the HTML**: Use browser DevTools (F12) to examine the page structure
2. **Check robots.txt**: Visit `https://example.com/robots.txt` to see scraping rules
3. **Identify data location**: Find where your target data appears in the HTML
4. **Note pagination**: Determine how to navigate multiple pages
5. **Check for APIs**: Look for hidden APIs in Network tab (might be easier)

### Step 2: Choose the Right Tool

**For Static HTML Pages** (content visible in "View Source"):
- Use `requests` + `BeautifulSoup`
- Fast and lightweight
- Works for most simple sites

**For JavaScript-Rendered Content** (content not in "View Source"):
- Use `Selenium` or `Playwright`
- Slower but handles dynamic content
- Can interact with page elements

**For Large-Scale Scraping**:
- Use `Scrapy` framework
- Built-in concurrency and rate limiting
- Robust error handling

### Step 3: Implement Basic Scraping

```python
import requests
from bs4 import BeautifulSoup
import time

def scrape_page(url):
    """Scrape data from a single page."""
    # Set a user agent to avoid being blocked
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    try:
        # Make the request
        response = requests.get(url, headers=headers, timeout=10)
        response.raise_for_status()
        
        # Parse HTML
        soup = BeautifulSoup(response.content, 'lxml')
        
        # Extract data (customize selectors for your target)
        items = []
        for element in soup.select('.item-class'):
            item = {
                'title': element.select_one('.title').text.strip(),
                'price': element.select_one('.price').text.strip(),
                'link': element.select_one('a')['href']
            }
            items.append(item)
        
        return items
        
    except requests.RequestException as e:
        print(f"Error fetching {url}: {e}")
        return []
```

### Step 4: Handle Pagination

```python
def scrape_multiple_pages(base_url, max_pages=10):
    """Scrape data from multiple pages."""
    all_items = []
    
    for page_num in range(1, max_pages + 1):
        url = f"{base_url}?page={page_num}"
        print(f"Scraping page {page_num}...")
        
        items = scrape_page(url)
        if not items:
            break  # No more items, stop pagination
        
        all_items.extend(items)
        
        # Be polite: wait between requests
        time.sleep(1)
    
    return all_items
```

### Step 5: Handle JavaScript-Rendered Content

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service

def scrape_dynamic_page(url):
    """Scrape JavaScript-rendered content."""
    # Setup Chrome driver
    service = Service(ChromeDriverManager().install())
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')  # Run in background
    driver = webdriver.Chrome(service=service, options=options)
    
    try:
        driver.get(url)
        
        # Wait for content to load
        wait = WebDriverWait(driver, 10)
        wait.until(EC.presence_of_element_located((By.CLASS_NAME, 'item-class')))
        
        # Extract data
        items = []
        elements = driver.find_elements(By.CLASS_NAME, 'item-class')
        
        for element in elements:
            item = {
                'title': element.find_element(By.CLASS_NAME, 'title').text,
                'price': element.find_element(By.CLASS_NAME, 'price').text
            }
            items.append(item)
        
        return items
        
    finally:
        driver.quit()
```

## Examples

### Example 1: Scraping Product Listings

**Scenario**: Extract product information from an e-commerce site

**Implementation**:
```python
import requests
from bs4 import BeautifulSoup
import csv

def scrape_products(url):
    """Scrape product listings and save to CSV."""
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.content, 'lxml')
    
    products = []
    for product in soup.select('.product-item'):
        data = {
            'name': product.select_one('.product-name').text.strip(),
            'price': product.select_one('.product-price').text.strip(),
            'rating': product.select_one('.rating')['data-rating'],
            'url': product.select_one('a')['href']
        }
        products.append(data)
    
    # Save to CSV
    with open('products.csv', 'w', newline='', encoding='utf-8') as f:
        writer = csv.DictWriter(f, fieldnames=['name', 'price', 'rating', 'url'])
        writer.writeheader()
        writer.writerows(products)
    
    return products

# Usage
products = scrape_products('https://example.com/products')
print(f"Scraped {len(products)} products")
```

### Example 2: Scraping with Error Handling and Retries

**Scenario**: Robust scraping with automatic retries

**Implementation**:
```python
import requests
from bs4 import BeautifulSoup
import time
from typing import Optional

def scrape_with_retry(url: str, max_retries: int = 3) -> Optional[BeautifulSoup]:
    """Scrape with automatic retries on failure."""
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    for attempt in range(max_retries):
        try:
            response = requests.get(url, headers=headers, timeout=10)
            response.raise_for_status()
            return BeautifulSoup(response.content, 'lxml')
            
        except requests.RequestException as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # Exponential backoff
                print(f"Retrying in {wait_time} seconds...")
                time.sleep(wait_time)
            else:
                print(f"Failed to scrape {url} after {max_retries} attempts")
                return None

# Usage
soup = scrape_with_retry('https://example.com')
if soup:
    # Process the data
    pass
```

## Best Practices

### Do's
- ✅ **Respect robots.txt**: Check and follow the site's scraping rules
- ✅ **Use delays**: Add `time.sleep()` between requests to avoid overwhelming servers
- ✅ **Set User-Agent**: Identify your scraper with a proper User-Agent header
- ✅ **Handle errors**: Use try-except blocks and implement retries
- ✅ **Cache responses**: Save HTML to avoid re-scraping during development
- ✅ **Parse efficiently**: Use CSS selectors or XPath for precise targeting
- ✅ **Validate data**: Check extracted data for completeness and correctness

### Don'ts
- ❌ **Don't scrape too fast**: Respect rate limits and add delays
- ❌ **Don't ignore errors**: Handle exceptions properly
- ❌ **Don't scrape personal data**: Respect privacy and legal boundaries
- ❌ **Don't bypass authentication**: Only scrape public data
- ❌ **Don't ignore Terms of Service**: Check if scraping is allowed
- ❌ **Don't use default User-Agent**: Set a descriptive User-Agent
- ❌ **Don't scrape if an API exists**: APIs are more reliable and ethical

## Common Pitfalls

### Pitfall 1: Not Handling Dynamic Content

**Problem**: Data appears in browser but not in scraped HTML

**Cause**: Content is loaded via JavaScript after initial page load

**Solution**: Use Selenium or Playwright instead of requests

```python
# Instead of requests
from selenium import webdriver
driver = webdriver.Chrome()
driver.get(url)
time.sleep(2)  # Wait for JS to execute
html = driver.page_source
```

### Pitfall 2: Getting Blocked by Anti-Scraping Measures

**Problem**: Receiving 403 errors or CAPTCHAs

**Cause**: Website detects automated scraping

**Solution**: 
- Add realistic User-Agent headers
- Use delays between requests
- Rotate IP addresses (use proxies)
- Consider using scraping services

```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Referer': 'https://www.google.com/'
}
```

### Pitfall 3: Fragile Selectors

**Problem**: Scraper breaks when website layout changes

**Cause**: Using overly specific CSS selectors

**Solution**: Use more robust selectors and add fallbacks

```python
# Fragile
title = soup.select_one('div.container > div.row > div.col-md-8 > h1')

# Better
title = soup.select_one('h1.product-title') or soup.select_one('h1')
```

## Error Handling

```python
import requests
from bs4 import BeautifulSoup
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def safe_scrape(url):
    """Scrape with comprehensive error handling."""
    try:
        response = requests.get(url, timeout=10)
        response.raise_for_status()
        
    except requests.Timeout:
        logger.error(f"Timeout while fetching {url}")
        return None
        
    except requests.HTTPError as e:
        logger.error(f"HTTP error {e.response.status_code} for {url}")
        return None
        
    except requests.RequestException as e:
        logger.error(f"Request failed for {url}: {e}")
        return None
    
    try:
        soup = BeautifulSoup(response.content, 'lxml')
        return soup
        
    except Exception as e:
        logger.error(f"Failed to parse HTML from {url}: {e}")
        return None
```

## Performance Considerations

- **Use connection pooling**: Reuse HTTP connections with `requests.Session()`
- **Concurrent scraping**: Use `asyncio` or `threading` for multiple URLs
- **Parse efficiently**: Use `lxml` parser (faster than `html.parser`)
- **Limit data extraction**: Only extract what you need
- **Cache responses**: Save HTML during development to avoid re-scraping

```python
import requests

# Use session for connection pooling
session = requests.Session()
session.headers.update({'User-Agent': 'My Scraper'})

for url in urls:
    response = session.get(url)
    # Process response
```

## Security Considerations

- **Validate URLs**: Ensure URLs are from expected domains
- **Sanitize data**: Clean extracted data before use
- **Respect privacy**: Don't scrape personal information
- **Follow laws**: Comply with GDPR, CCPA, and other regulations
- **Check Terms of Service**: Ensure scraping is permitted

## Related Skills

This skill works well with:
- **api-integration** - Use APIs when available instead of scraping
- **data-processing** - Clean and transform scraped data
- **database-operations** - Store scraped data efficiently

## Additional Resources

- [BeautifulSoup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Selenium Documentation](https://www.selenium.dev/documentation/)
- [Scrapy Tutorial](https://docs.scrapy.org/en/latest/intro/tutorial.html)
- [Web Scraping Best Practices](https://www.scrapehero.com/web-scraping-best-practices/)

## Version History

- **1.0.0** (2026-02-16): Initial version
