
// Consider if structure should be highs then lows, or a back and for. IE Static is a pro but made dynamic processing and html cleanup hard.

footer: @yigenana
slidenumbers: true

# Digital Publishing for Scale

## The Economist and Go

### Jonas, Content Platform Lead Engineer

---
# Print Pressing Forward

^ When I tell people that I work for The Economist, they assume I'm some kind of economists or numbers person and my work is a little something like this (old british men, economics graphs). Then I tell them I'm a Lead Engineer for the Content Platform and program primarily with Go and I get a surpised reaction.  That's because for a long time, The Economist with primarily conerned with print.
---

# Print Pressing Forward

^ I'm going to assume however, in a room full how technologists, that's not how most of us read the news any more. Share of hands, who still preferes to read their news in print? And how about online or mobile? Exactly. The Economist, like most publishered, realized they needed to modernize and digitize in order to keep reaching our readers, and that's when I jumped into the picture

---

# Print Pressing Forward

^ I joined The Economist to take part in a project that would reshape our technology stack and modernize in order to better support the digital distribution of content. At that time,  content was being authored and displayed in a Monolithic Drupal CMS. For those less familiar with Drupal, it was a one-in-all solution primarily using PHP, MySQL, JS, and then CSS and HTML for design.  Our goal was to break up the monolith and develop a platform that consumed content, standardized it, and delivered it via an API. This platform would be hosted in AWS, use Docker containers, and be written primarily in Go.

---

# Why Go

The Platform
* Event Messaging
* Worker microservices
* Distributed
* S3, DynamoDB, and ElasticSearch data storage
* Eventual Consistency
* RESTful API & GraphQL

^ So why did we choose Go? Well first, it's helpful to understand the overall architeture for the new system. The Content Platform is a event based system. It response to events from our different content authoring platforms and triggers a stream of processes run in descrete worker miscroservices. These services perform functions such as data standardization, sematic tagging analysis, indexing in our ElasticSearch database, formating and pushing content to external platforms like apple news or facebook, and much more. We also have a RESTful API, which combined with GrahpQL, is used to query content, and this is how our products, such as the website and mobile apps, fetch content to be displayed.

---
# Why Go

Key factors
* Built in concurrency support
* Strong networking and API support
* Compiled language, more reliable for distributed content system

^ Understanding the type of architecture we were aiming for, we then had to find the right language for our Worker microservices. There 

# The Happy Path

APIs: Quick implementation of scalable API endpoints. No frameworks needed. We started with one, but it was more bloat, found we could do everthing we needed with net/http and encoding/json.
Each request on a handler is a goroutine, baked in, no customization needed

 ---

# The Happy Path

Static: Dependency and reliability in a distributed, enterprise environment.
Content is kind. Every product and consumer needs consistency from our API. A static language helps us enforce that.


# The Happy Path

Testing: A first class feature, critical for successful microservice integration. Fast compile times make TDD easy as well.

 ---

# The Happy Path

CAP: GoRoutines improve eventual consistency in a distributed data system

 ---

# The Happy Path

Failing Fast: Compiled, static language and error handling enabling fast failure in a CI/CD environment
 ---

# The Happy Path
Visibility Tools: Go has many tools that make it easy to have visibility into your applications

 ---

# The Challenges

Handling messy, broken HTML gracefully. Had to use JS.

---

# The Challenges

Managing dynamic content from unpredictable backends. Reflection approach worked for our use case, but may not be appropriate for everyone

---

# The Challenges

Initially limited usage in AWS, where we hosted. This has improved and we’ve done more things, like Lambdas, using Go
Highlight how it's possible but very verbose. Use example of lambda that used JS to get the key and how it would have to have been done in Go. For simple tasks that involve manipulating strings, JS was better.

---

# The Challenges

Limited tools when handing audio and video metadata. Had to use Python, but hopeful that we can add functionality to the Go libs

---

# The Challenges

Dependency management (Good is that dependancies aren't as caotic because of the way they are all compiled into a single binary) 
"Golang statically links all of its modules and dependency libraries into one binary file. So, it does not demand to install any dependencies on a server, all you are required to do is to upload a compiled file for your application to start working at earliest."

---

# Key Takeaways

Go has generally worked well for us and we enjoy writing it
Numbers on scale and performance (run some go benchmarks)

---

# Key Takeaways

Lamaar success story, products faster to market.

---

# Key Takeaways

Sometimes it's not always the right tool, and that's fine. We have a polyglot platform and use different languages where it makes sense. Go is liekly never going to be our top choice when we have to mess around with a lot of text and dynamic content, and that's always going to be something we need at The Economist. So we keep Node in our toolset. But Go's strengths are the backbone that allows are systems to scale quickly. We know it's the tool to turn to when we're adding new features and delivering to new products and it's made our development team a family of happy Gophers.



