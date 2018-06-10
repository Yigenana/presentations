footer: @yigenana
slidenumbers: true

# Digital Publishing for Scale

## The Economist and Go

### Jonas, Content Platform Lead Engineer

---
# Print Pressing Forward

^When I tell people that I work for The Economist, they assume I'm some kind of economics or numbers person and my work is a little something like this (old british men, economics graphs). Then I tell them I'm a Lead Engineer for the Content Platform and program primarily with Go and I get a surpised reaction.  That's because for a long time, The Economist with primarily conerned with print.

---

# Print Pressing Forward

^I'm going to assume however, in a room full how technologists, that's not how most of us read the news any more. Share of hands, who still preferes to read their news in print? And how about online or mobile? Exactly. The Economist, like most publishered, realized they needed to modernize and digitize in order to keep reaching our readers, and that's when I jumped into the picture

---

# Print Pressing Forward

^I joined The Economist to take part in a project that would reshape our technology stack and modernize in order to better support the digital distribution of content. At that time,  content was being authored and displayed in a Monolithic Drupal CMS. For those less familiar with Drupal, it was a one-in-all solution primarily using PHP, MySQL, JS, and then CSS and HTML for design.  Our goal was to break up the monolith and develop a platform that consumed content, standardized it, and delivered it via an API. This platform would be hosted in AWS, use Docker containers, and be written primarily in Go.

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
* Built in concurrency support enables performance at scale
* Strong networking and API support
* Compiled language simple to deploy across platforms

^ Understanding the type of architecture we were aiming for, we then had to find the right language for our Worker microservices. Go was compared against Python, Ruby, Node, PHP, and Java. While every language had its strengths , Go fit best with our architecture and use cases. Go's baked in concurrency and apis and it's design as a static, compiled language would enable a distributed, eventing systems that could scale quickly. Additionaly, the relatively simple syntax of Go made it easy to pick up and start writing working code, which was a quick win for a team going through so much technology transition.

---

# Fail Fast: Language Design

Application Priciples
* Minimize startup time
* Fail fast
* Continuous Integration & Delivery

^ Failing Fast: Compiled, static language and error handling enabling fast failure in a CI/CD environment

---

# Fail Fast: Errors vs Exceptions

Errors vs Exception
* No exceptions
* Error as a type
* Failing fast the responsbility of the developer

^ A major difference people quickly notice in Go is that is does not have exceptions, rather it has an Error Type. 

---

# Fail Fast: Error Type

```
type error interface {
    Error() string
}

// New returns an error that formats as the given text.
func New(text string) error {
    return &error{text}
}

func Sqrt(f float64) (float64, error) {
    if f < 0 {
        return 0, errors.New("math: square root of negative number")
    }
    // implementation
}
```

^ In Go, all errors are values. Calling Error on an error type returns the error as a string, often used for logging. You can also easily create errors like with the New function above.

---

# Fail Fast: Error Handling

```
func fetchContent(id string) (string, error) {
    content, err := fetch(id)
    if err != nil {
    	// handle the error.
	}
	// happy path continues.
}
```

^ In Go, functions allow multiple return values, so if your function or method can fail, it will likely return an error value. The language encourages you to explicitly check for errors where they occur. 

---

# Fail Fast: Error Handling

```
package net

type Error interface {
    error
    Timeout() bool   // Is the error a timeout?
    Temporary() bool // Is the error temporary?
}

if nerr, ok := err.(net.Error); ok && nerr.Temporary() {
    time.Sleep(1e9)
    continue
}

if err != nil {
    log.Fatal(err)
}
```

<sub>[Error Handling in Go](https://blog.golang.org/error-handling-and-go)</sub>

^ The Go authors argue that not all exceptions are exceptional. Not all errors should crash your app. If you can gracefully recover from an error, you should do so. Error as values enables you to use the error to simplify your error handling. In a distributed  system for example, we can easily enable retries by wrapping our errors. Network issues are always going to be encountered in our system, whether we're sending data to other internal services or pushing to third party tools like Apple News or Facebook. This simple example above highlight how we can take advantage of error as a type to build in retries and evetual backoffs into our system, so that we don't fail on minor, temporary distruptions. We've used similar error wrapping for handling HTTP errors and better using HTTP status code to communicate the type of errors to clients.

# Consistency: Language Design

The Canonical Article
* Standard data model for all content
* Aligned to schema.org standards
* Querable via GraphQL

^Static: Dependency and reliability in a distributed, enterprise environment.
Content is king. Every product and consumer needs consistency from our API. Our products primarily use GraphQL to query our API, which uses a static schema. 
If was critical that we delivered our content consistently. A static language helps us enforce that from the start. Go's design as a static language and it's strong type checking during compilation aligns with our goals. There were also several great tools, like json schema validation, we could implement in our testing suite.

---

# Consistency: Testing

main.go
```
package main

func Sum(x int, y int) int {
	return x + y
}
```

^Testing is a critical tool for successful microservice integration. Fast compile times combined with testing  as a first class feature in Go enables strong testing practices and quick failures in our build pipelines.
^Go comes with a testing package in the standard library, as well as several great contributed packages for testing. Go Tooling allows for tests to be easily setup and run. Create a test file by adding "underscore test" to the file and run the built in command "go test" in the directory to run the tests.

---

# Consistency: Testing

main_test.go
```
package main

import "testing"

func testSum(t *testing.T) {
	total := Sum(1,2)
	if total != 3 {
		t.Errorf("Sum incorrect, got: %d, want: %d.", total, 3)
	}
}
```

^My test file imports the standard library testing package. All tests are functions that take a pointer to a T type that manages the test state and ensures the function is run when the "go test" command is run. From there you write the test that verifies the function runs as expected. 

---

# Consistency: Testing

## Go Test Features
* Cover: Code test coverage
* Bench: Runs benchmark tests (Benchxxx)
* TestMain: Add extra setup or teardown for tests
* Table tests and mocks

^Additionally the test command has several helpful feature flags. The "cover" flag  provides a detailed report on code coverage, which is a helpful tool for you development process. The "bench" test runs benchmark tests with are denoted by starting with the word Bench rather than test. The TestMain function allows extra setup for test, such as a mock metadata or authentication server. Additionally, Go makes it quite easy to use table test with anonymous structs and mocks with interfaces, making your tests more robust.

---

# Consistency: The Challenges

```
TODO: PHP empty int vs string example
```

^One of the first major challenges for our platform was managing dynamic content from unpredictable backends. We consumed content from source CMS systems primarily via JSON endpoints, but we couldn't guarantee the data types would be consistent. This meant we couldn't easily use Go's encoding/json package, which supports unmarshalling JSON into structs, but panics if the types do not match. 

---

# Consistency: Serializing Dynamic Content

```
TODO: Canonical example vs JSON/Unmarshal
```

^To overcome this challenge, we needed a custom way to map our backends to a standard format. Ater a few iterations on the approach, we decided to implement a custom unmarshalling process. In some ways, this felt a bit like rebuilding the a standard lib package, but it gave us fine grained control of how we handled source data. It enabled us to decide when to apply defaults if we had invalid data and when to error.

---

# Consistency: Scaling Content Standardization

```
TODO: Benchmark tests of custom vs standard marshalling and scale
```

^As you can see in this first set of metrics, our custom unmarshalling tool performs at gererally the same rate as the Go library. In the second set of data you can see that this performance scales as more content is processed. What this highlights is that while the approach is a bit heavy handed in the implementation, interfaces and reflectioned worked well to acheive our goals and didn't prevent us from scaling. This may not be the right approach for every situation, but it's helped us quickly provide stardized, consistent data to our consumers.


# Scale: HTTP and APIs

```
HTTP endpoint in go
```

^Part of this scaling was also enable with the strong support for networking and APIS in Go. In Go, you can quickly implement scalable http endpoints with no frameworks needed. In this example you can see I've imported the net/http package and setup a handler, with takes an request and response writer. We started with one, but it was more bloat, found we could do everthing we needed with net/http and encoding/json.

# Scale: HTTP and concurrency

```
Go http package sample
```

^Additionally, these endpoints can scale because each request on a handler is a goroutine, baked in, no customization needed

---

# Scale: Content Guarantees

## CAP Theorum

Pick two
* Consistency
* Availiabity
* Partition Tolerance

^Working with distributed data means wrestling with the gaurantees we promise consumers. As per the CAP theorem, it is impossible to simultaneously provide more than two out of the following three guarantees: Consistency. Availability. Partition tolerance. In our platform, we chose Eventual Consistency, meaning that we guarentee reads from our data sources will eventually be consistent, we tolerate moderate delays in all data sources reaching a consistent state. One of the ways we minimize that gap is by taking advantage of GoRoutines.

---

#Scale: Go Routines

```
go routine example with ES
```
^One of the data sources in our system in Elasticsearch. When content is updated in the system, one of our processes is to be sure that all content referencing that item is updated and reindexed. With Goroutines we can improve the time it takes run this reprocessing, thus ensuring the items are all consisten faster. In the example above you can see that after querying all the items that need reprocessing, we run each reprocess event in a goroutine. With channels, gather the responses from ES as the arrive and end the routine once each item has responded. 

#Scale: Go Routines

```
benchmark with and without goroutines
```

^This is a somewhat oversimplified example of the performnce differences when I run this reindexing process synchronously vs asyncronsly with Goroutines. The improvements are relatively small but in a distributed systems with 1 content change triggering hundreds of events, these improvements add up and have enables use to reach a consistent state with minimal impact or delay for consumers.

# Scale: The Happy Path

```
PPROF example
```

Go has many tools that make it easy to have visibility into your applications, and these are especially helpful as you start focusing on your system's preformance and using more advanced features like GoRoutines and Channels. One of the more powerful tools is PPROF, which collects CPU profiles, traces, heap profiles, and mutex profiles and exposes these on a port. These metrics can also be interactively viewed in the command line, or exported into visualizations. I've used PPROF both to understand the performance of my application and to debug the application when I've encountered issues like memory leaks. Using these tools to improve visibility into your programs helps you better understand how different features of Go work and where they can bring you the most benefit.

---

# Scale: Dependencies

^In you you can easily import 3rd packages via the "go import" command. The files are stored in blah... Dependencies are statically linked and compiled alongside your application's code into the single application binary. This is helpful with distributed systems because it limits the complexity in installing and associating dependencies. It's made it easier for us to scale services as needed and add new services without cimplicated build processes.

# Scale: The Saga of Dependencies

^When go was released it had no dependencies management system. Within the community several tools were developed to meet this need. Within our own systems, we used Git Submodules. This made sense at the time as the community was actively pushing for a standard dependency management tool, and so we wanted to use a non-Go tool until the chosen tool arrived. It's been 2 years and while the community is closer to an aligned approach and tool for dependency management, it's not there yet. At The Economist, we've had minimal issues with our current approach, and so it hasn't posed specific challenges for us, but it certianly has been challnging for others and it something to be aware when transitioning to Go. 

---

# Scale: The Challenges

Limited tools when handing audio and video metadata. Had to use Python, but hopeful that we can add functionality to the Go libs

---

# Consistency: The Challenges

```
TODO: Crazy html example
```

^Handling messy, broken HTML gracefully. Had to use JS.

---

# Scale: The Challenges

```
TODO: JS lambda example
```

Serverless and AWS. Highlight how it's possible but very verbose. Use example of lambda that used JS to get the key and how it would have to have been done in Go. For simple tasks that involve manipulating strings, JS was better.

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

---
