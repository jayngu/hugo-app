---
title: "Web App Load Testing with Loader.io"
date: 2018-06-17T15:20:04-04:00
subtitle: ""
categories: ["devops"] 
tags: ["nginx", "kubernetes", "hugo"]
bigimg: [{src: "/img/triangle.jpg"}, {src: "/img/sphere.jpg"}, {src: "/img/hexagon.jpg"}]
---

The other day I came across a medium post which introduced me to the site: [**loader.io**](https://loader.io/). Their service is free (baseline) and allows me to easily test up to several hundred concurrent connections a second. It was pretty convenient that I found this randomly because I was trying to figure out a simple way of stress testing my blog. My current cluster is set up with 5 `nginx:alpine` servers with default configurations being load balanced by a `nginx ingress controller`. I was curious as to what this basic setup could handle. I want to reiterate that the blog setup is definitely overkill for a simple `hugo` generated site without a backend. My goal was to have a fun lab environment to play with and potentially break. 

The below table summarizes the results of the tests done through their GUI:

---

| Clients/min   | Avg Response Time | Successful Responses | Data sent by Loader | Data Received by Clients |
| ------------- |:-----------------:| --------------------:|--------------------:|------------------------: |
| 250           | 33 ms             | 249                  | 51.2 KB             | 3.55 MB                  |
| 500           | 33 ms             | 500                  | 103 KB              | 7.13 MB                  |
| 2500          | 24 ms             | 2500                 | 513 KB              | 35.6 MB                  |         
| 10000         | 63 ms             | 9996                 | 2 MB                | 143 MB                   |        

---

The below animation is footage of 30 seconds of the 10 000 client test. This should give an easily digestable summary:

![loader.io 10,000 clients](/img/loaderio.gif)

---

I'm impressed at how well default round-robin load-balancing was performing on nginx. What's interesting is that during the 5-6 second mark, the response time spiked to 1539 ms with about 400 simulated concurrent connections at that point in time. Towards the end of the animation, you can see that the response time quickly shot back down to normal levels and the average response time only suffered minorly with an impressive `63 ms` average. 

As a free service I was somewhat satisfied with what they had to offer - I'd imagine things would be different if I opted in and became a paying customer. Unfortunately, I had one major issue where my tests would refuse to actually run. The UI would give a loading bar that would never seem to succeed. I decided to abort those tests and wait a few hours.

It appeared to me that their service was under high-load during parts of the day and it was almost as if my tests were in some sort of queue. There are many other features they have such as scheduling load tests and an API that I haven't looked into. 

In the end I got to see my web app come across one hiccup, and the caveat with using the free service is that I'm capped to a load test of `10000 clients/min`. 