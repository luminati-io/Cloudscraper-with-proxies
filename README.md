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

![Bright Data's proxies and scraping infrastructure control panel](https://brightdata.com/wp-content/uploads/2025/01/image-7-1024x493.png)

3. Activate the proxies by clicking the toggle:

![Turning on the residential zone](https://brightdata.com/wp-content/uploads/2025/01/image-8-1024x137.png)

This is what you should now be seeing:

![The residential zone turned on](https://brightdata.com/wp-content/uploads/2025/01/image-9-1024x137.png)

> **Note**:\
> Bright Data’s residential proxies rotate automatically.

4. In the “Access Details” section, copy the proxy host, username, and password:

![The access details for your residential proxies zone](https://brightdata.com/wp-content/uploads/2025/01/image-10.png)

Your Bright Data proxy URL will look like this:

```
http://<PROXY_USERNAME>:<PROXY_PASSOWRD>@brd.superproxy.io:33335
```

5. Integrate the proxy into Cloudscraper as follows:

```python
import cloudscraper

# Create CloudScraper instance
scraper = cloudscraper.create_scraper()

# Define the Bright Data proxy
proxies = {
   "http": "http://<PROXY_USERNAME>:<PROXY_PASSOWRD>@brd.superproxy.io:33335",
   "https": "http://<PROXY_USERNAME>:<PROXY_PASSOWRD>@brd.superproxy.io:33335"
}

# Perform a request using the proxy
response = scraper.get("https://httpbin.io/ip", proxies=proxies)

# Print the repsonse
print(response.text)
```

The CloudScraper proxy integration is done.

## Conclusion

Bright Data controls the best proxy servers in the world, serving Fortune 500 companies and over 20,000 customers. Its worldwide proxy network involves:

*   [Datacenter proxies](https://brightdata.com/proxy-types/datacenter-proxies) – Over 770,000 datacenter IPs.
*   [Residential proxies](https://brightdata.com/proxy-types/residential-proxies) – Over 72M residential IPs in more than 195 countries.
*   [ISP proxies](https://brightdata.com/proxy-types/isp-proxies) – Over 700,000 ISP IPs.
*   [Mobile proxies](https://brightdata.com/proxy-types/mobile-proxies) – Over 7M mobile IPs.

[Create a free Bright Data account](https://brightdata.com/#popup-155639) today to try our proxy servers.