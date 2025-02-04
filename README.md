# Using CloudScraper with Proxies

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

This guide covers setting up a CloudScraper proxy integration, rotating IPs, and using authenticated proxies for seamless scraping.

- [What Is CloudScraper?](#about-cloudscraper)
- [Why Use Proxies with CloudScraper?](#why-use-proxies-with-cloudscraper)
- [Setting Up a Proxy With CloudScraper](#setting-up-a-proxy-with-cloudscraper)
- [Implementing Proxy Rotation](#implementing-proxy-rotation)
- [Using Authenticated Proxies in CloudScraper](#using-authenticated-proxies-in-cloudscraper)
- [Integrating Premium Proxies in CloudScraper](#integrating-premium-proxies-in-cloudscraper)
- [Conclusion](#conclusion)

## About CloudScraper

[CloudScraper](https://github.com/VeNoMouS/cloudscraper) is a Python module designed to bypass Cloudflare's anti-bot page (commonly known as "I'm Under Attack Mode" or IUAM). Under the hood, it is implemented using Requests, one of the most popular Python HTTP clients.

## Why Use Proxies with CloudScraper?

Cloudflare may block your IP if you make too many requests or trigger more sophisticated defenses that are difficult to bypass. The combination of proxies and CloudScraper for scraping websites hosted by Cloudflare offers two key benefits:

- **Enhanced security and anonymity**: By routing requests through a proxy, your true identity remains hidden, reducing the risk of detection.
- **Avoiding blocks and interruptions**: Proxies allow you to rotate IP addresses dynamically, which helps you bypass blocks and rate limiters.

## Setting Up a Proxy With CloudScraper

### Step #1: Install CloudScraper

Install the `cloudscraper` pip package:

```bash
pip install -U cloudscraper
```

The `-U` option ensures that you are getting the latest version of the package with the latest workarounds for Cloudflare's anti-bot engine.

### Step #2: Initialize CloudScraper

Import CloudScraper:

```python
import cloudscraper
```

Create a CloudScraper instance using the `create_scraper()` method:

```python
scraper = cloudscraper.create_scraper()
```

The `scraper` object works similarly to the `Session` object from the `requests` library. In particular, it enables you to make HTTP requests while bypassing Cloudflare's anti-bot measures.

### Step #3: Integrate a Proxy

Define a `proxies` dictionary and pass it to the `get()` method as below:

```python
proxies = {
    "http": "<YOUR_HTTP_PROXY_URL>",
    "https": "<YOUR_HTTPS_PROXY_URL>"
}

# Perform a request through the specified proxy
response = scraper.get("<YOUR_TARGET_URL>", proxies=proxies)
```

The `proxies` parameter in the `get()` method is passed down to Requests. This allows the HTTP client to route your request through the specified HTTP or HTTPS proxy server, depending on the protocol of your target URL.

### Step #4: Test the CloudScraper Proxy Integration Setup

For demonstration purposes, let's target the `/ip` endpoint of the HTTPBin project. This endpoint returns the caller's IP address. If everything works as expected, the response should display the IP address of the proxy server.

Assuming that the URL for the proxy server is `http://202.159.35.121:443`, this will be the script code:

```python
import cloudscraper

# Create a CloudScraper instance
scraper = cloudscraper.create_scraper()

# Specify your proxy
proxies = {
    "http": "http://202.159.35.121:443",
    "https": "http://202.159.35.121:443"
}

# Make a request through the proxy
response = scraper.get("https://httpbin.org/ip", proxies=proxies)

# Print the response from the "/ip" endpoint
print(response.text)
```

You should see a response like this:

```json
{
    "origin": "202.159.35.121"
}
```

The IP in the response matches the IP of the proxy server, as expected.

> **Note**:\
> Free proxy servers are often short-lived. It's best to obtain a new IP address for a proxy when testing the script.

## Implementing Proxy Rotation

Retrieve a list of proxies from a reliable provider and store them in an array:

```python
proxy_list = [
    {"http": "<YOUR_PROXY_URL_1>", "https": "<YOUR_PROXY_URL_1>"},
    # ...
    {"http": "<YOUR_PROXY_URL_n>", "https": "<YOUR_PROXY_URL_n>"},
]
```

Next, use the `random.choice()` method to randomly select a proxy from the list:

```python
import random

random_proxy = random.choice(proxy_list)
```

Set the randomly selected proxy in the `get()` request:

```python
response = scraper.get("<YOUR_TARGET_URL>", proxies=random_proxy)
```

If everything is set up correctly, the request will use a different proxy from the list at each run. Here is the complete code:

```python
import cloudscraper
import random

# Create a Cloudscraper instance
scraper = cloudscraper.create_scraper()

# List of proxy URLs (replace with actual proxy URLs)
proxy_list = [
    {"http": "<YOUR_PROXY_URL_1>", "https": "<YOUR_PROXY_URL_1>"},
    # ...
    {"http": "<YOUR_PROXY_URL_n>", "https": "<YOUR_PROXY_URL_n>"},
]

# Randomly select a proxy from the list
random_proxy = random.choice(proxy_list)

# Make a request using the randomly selected proxy
# (replace with the actual target URL)
response = scraper.get("<YOUR_TARGET_URL>", proxies=random_proxy)
```

## Using Authenticated Proxies in CloudScraper

To authenticate a proxy in CloudScraper, include the required credentials directly in the proxy URL. The format for username and password authentication is as follows:

`<PROXY_PROTOCOL>://<YOUR_USERNAME>:<YOUR_PASSWORD>@<PROXY_IP_ADDRESS>:<PROXY_PORT>`
    
With that format, the CloudScraper proxy configuration would look like this:

```python
import cloudscraper

# Create a Cloudscraper instance
scraper = cloudscraper.create_scraper()  

# Define your authenticated proxy
proxies = {
   "http": "<PROXY_PROTOCOL>://<YOUR_USERNAME>:<YOUR_PASSWORD>@<PROXY_IP_ADDRESS>:<PROXY_PORT>",
   "https": "<PROXY_PROTOCOL>://<YOUR_USERNAME>:<YOUR_PASSWORD>@<PROXY_IP_ADDRESS>:<PROXY_PORT>"
}

# Perform a request through the specified authenticated proxy
response = scraper.get("<YOUR_TARGET_URL>", proxies=proxies)
```

## Integrating Premium Proxies in CloudScraper

For reliable results in production scraping environments, use proxies from top-tier providers like [Bright Data](https://brightdata.com/). To integrate Bright Data’s proxies in CloudScraper:

1. Create an account or log in.

2. Reach the dashboard and click on the “Residential” zone in the table:

![Bright Data's proxies and scraping infrastructure control panel](https://github.com/luminati-io/Cloudscraper-with-proxies/blob/main/image-7.png)

3. Activate the proxies by clicking the toggle:

![Turning on the residential zone](https://github.com/luminati-io/Cloudscraper-with-proxies/blob/main/image-8.png)

This is what you should now be seeing:

![The residential zone turned on](https://github.com/luminati-io/Cloudscraper-with-proxies/blob/main/image-9.png)
> **Note**:\
> Bright Data’s residential proxies rotate automatically.

4. In the “Access Details” section, copy the proxy host, username, and password:

![The access details for your residential proxies zone](https://github.com/luminati-io/Cloudscraper-with-proxies/blob/main/image-10.png)
Your Bright Data proxy URL will look like this:

```
http://<PROXY_USERNAME>:<PROXY_PASSWORD>@brd.superproxy.io:33335
```

5. Integrate the proxy into Cloudscraper as follows:

```python
import cloudscraper
# Create a CloudScraper instance
scraper = cloudscraper.create_scraper()
# Define the premium proxy
proxies = {
"http": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST>:<PROXY_PORT>",
"https": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST>:<PROXY_PORT>"
}
# Perform a request using the premium proxy
response = scraper.get("https://httpbin.org/ip", proxies=proxies)
# Print the response to verify the proxy is working
print(response.text)
```

The CloudScraper proxy integration is done. Now, we need to test and verify. To ensure the proxy is working correctly, you can test it against a service like [https://httpbin.org/ip](https://httpbin.org/ip), which returns the IP address of the caller. If the setup is correct, the response should display the IP address of the proxy server instead of your local IP.


## Putting Everything Together 

```python
import cloudscraper
import random
import time

# Step 1: Define a list of proxies (authenticated and non-authenticated)
# Replace <PROXY_USERNAME>, <PROXY_PASSWORD>, <PROXY_HOST>, and <PROXY_PORT> with actual values
proxy_list = [
    {"http": "http://<PROXY_HOST_1>:<PROXY_PORT_1>", "https": "http://<PROXY_HOST_1>:<PROXY_PORT_1>"},
    {"http": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_2>:<PROXY_PORT_2>", 
     "https": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_2>:<PROXY_PORT_2>"},
    {"http": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_3>:<PROXY_PORT_3>", 
     "https": "http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_3>:<PROXY_PORT_3>"}
]

# Step 2: Create a CloudScraper instance
scraper = cloudscraper.create_scraper()

# Step 3: Define the target URL
target_url = "https://httpbin.org/ip"  # This endpoint returns the caller's IP address

# Step 4: Implement proxy rotation and make requests
def fetch_with_proxy_rotation(proxy_list, target_url, num_requests=5):
    """
    Fetch the target URL using proxy rotation.
    
    Args:
        proxy_list (list): A list of proxy configurations.
        target_url (str): The URL to scrape.
        num_requests (int): Number of requests to make.
    """
    for i in range(num_requests):
        # Randomly select a proxy from the list
        proxy = random.choice(proxy_list)
        
        try:
            # Make a request using the selected proxy
            print(f"Using proxy: {proxy}")
            response = scraper.get(target_url, proxies=proxy, timeout=10)
            
            # Print the response (IP address of the proxy)
            print(f"Response {i + 1}: {response.text}")
        
        except Exception as e:
            # Handle errors (e.g., connection timeout, proxy failure)
            print(f"Error with proxy {proxy}: {e}")
        
        # Wait a bit before the next request to mimic human behavior
        time.sleep(random.uniform(1, 3))

# Step 5: Run the function
fetch_with_proxy_rotation(proxy_list, target_url, num_requests=5)
```

### Output Example

```python
Using proxy: {'http': 'http://<PROXY_HOST_1>:<PROXY_PORT_1>', 'https': 'http://<PROXY_HOST_1>:<PROXY_PORT_1>'}
Response 1: {
    "origin": "203.0.113.1"
}
Using proxy: {'http': 'http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_2>:<PROXY_PORT_2>', 'https': 'http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_2>:<PROXY_PORT_2>'}
Response 2: {
    "origin": "198.51.100.2"
}
Using proxy: {'http': 'http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_3>:<PROXY_PORT_3>', 'https': 'http://<PROXY_USERNAME>:<PROXY_PASSWORD>@<PROXY_HOST_3>:<PROXY_PORT_3>'}
Response 3: {
    "origin": "192.0.2.3"
}
...
```

## Conclusion

Bright Data controls the best proxy servers in the world, serving Fortune 500 companies and over 20,000 customers. Its worldwide proxy network involves:

*   [Datacenter proxies](https://brightdata.com/proxy-types/datacenter-proxies) – Over 770,000 datacenter IPs.
*   [Residential proxies](https://brightdata.com/proxy-types/residential-proxies) – Over 72M residential IPs in more than 195 countries.
*   [ISP proxies](https://brightdata.com/proxy-types/isp-proxies) – Over 700,000 ISP IPs.
*   [Mobile proxies](https://brightdata.com/proxy-types/mobile-proxies) – Over 7M mobile IPs.

[Create a free Bright Data account](https://brightdata.com) today to try our proxy servers.
