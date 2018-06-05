footer: @yigenana
slidenumbers: true

# Digital Publishing for Scale

## The Economist and Go

### Jonas, Content Platform Lead Engineer

^ Hi everyone. My name is Jonas, I’m the Lead Engineer for the Content Platform team at the Economist. What I want to share with you today is how The Economist implemented Go to write services responsible for delivering content across our products and platforms. Go has enabled our teams to quickly adapt to business needs and scale for growing demand, but it has not been without its own challenges. This talk will cover the struggles and victories in transitioning to Go and how Go has uniquely fit The Economist’s digital publishing goals.

---

# Setting the Scene

Monolithic Drupal CMS

![](drupal.jpg)

^ Let me set the scene to help illustrate the change we were undertaking. Economist content was being authored and displayed in a Monolithic Drupal CMS. For those less familiar with Drupal, it was a one-in-all solution primarily using PHP, MySQL, JS, and then CSS and HTML for design. 

# The Goal

Distributed Content API

![](gopher.jpg)

^Our goal was to break up the monolith and develop a platform that consumed content, standardized it, and delivered it via an API. This platform would be hosted in AWS, use Docker containers, and be written primarily in Go.

# Why Golang Microservices

Key factors
* Built in concurrency support
* Strong networking and API support
* Compiled language, more reliable for distributed content system

# The Process

