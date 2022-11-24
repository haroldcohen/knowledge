# Breaking changes: When software isn't soft anymore

I noticed that a lot of developers heard or used the term "breaking change" with a sense of confusion.

This article aims to provide with a more extensive knowledge on the topic, as well as tools to prevent and handle
breaking changes.

## What is a breaking change ?

> In software development, a breaking change is a software modification that will force a change of implementation on
> the client's (or consumer) side. <b>In other words, it is a software update with backward incompatible API
> changes.</b>

There are different kinds of breaking changes. Some are tied to the version release of a programming language
or framework, some to a REST API or message version.

> In every case, the breaking change is the result of a modification in a contract between the provider and consumer.

Below an example.

In Python 2.x, printing a message to a standard output device required to use a statement called print:

```
print "Some message"
```

In Python 3.x, the same feature requires to use not a statement but a function called print:

```python
print("Some message")
```

As you noticed, the breaking change happened when Python 3 was released, and for a good reason.

> Semantic versioning (an incremental versioning system using a number MAJOR.MINOR.PATCH) aims to notify of a breaking
> change through the release of a MAJOR version, where API changes are backward incompatible.

### Differentiating an enrichment from a breaking change

The most common mistake is to confuse an enrichment and a breaking change. Not every API modification translates into a
breaking change.

#### The anatomy of an enrichment/added functionality

Given we have a service for car rental company, with a route GET /cars/{id}.

In the version 1.0.0 of the API, the response looked like this:

```json
{
  "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
  "model": "Cadillac Miller-Meteor Sentinel",
  "manufacturerId": "1f24ccba-4e3b-4c94-8e4d-7e156e318091"
}
```

In the version 1.1.0 of the API, where a new feature to handle license plates was added, the response will look like
this:

```json
{
  "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
  "model": "Cadillac Miller-Meteor Sentinel",
  "manufacturerId": "1f24ccba-4e3b-4c94-8e4d-7e156e318091",
  "licensePlate": "ECTO-1"
}
```

Consequently, the MINOR segment of the semantic version was incremented.

#### The anatomy of a breaking change

Given we have the same car rental service API.

<b>Example 1</b>

In the version 1.1.0 of the API, the response looked like this:

```json
{
  "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
  "model": "Cadillac Miller-Meteor Sentinel",
  "manufacturerId": "1f24ccba-4e3b-4c94-8e4d-7e156e318091",
  "licensePlate": "ECTO-1"
}
```

In the version 2.0 of the API, where consumers need to retrieve not only the manufacturer ID but it's name and slug name
as well, the response will look like this:

```json
{
  "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
  "model": "Cadillac Miller-Meteor Sentinel",
  "manufacturer": {
    "id": "1f24ccba-4e3b-4c94-8e4d-7e156e318091",
    "name": "Cadillac",
    "slug": "cadillac"
  },
  "licensePlate": "ECTO-1"
}
```

Depending on the context (whether the system is monolithic or not, if bounded context are isolated or not), this new
feature might actually not make sense, but it is a larger topic that I won't address here.
---

<b>Example 2</b>

After releasing, the version 2.0 of the API, it was requested to change the way manufacturers are exposed:

```json
{
  "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
  "model": "Cadillac Miller-Meteor Sentinel",
  "manufacturer": {
    "id": "1f24ccba-4e3b-4c94-8e4d-7e156e318091",
    "name": "Cadillac",
    "slugName": "cadillac"
  },
  "licensePlate": "ECTO-1"
}
```

----

<b>Example 3</b>

After releasing, the version 3.0 of the API, it was requested to decommission the route GET /cars/available-cars.

The response that previously looked like the example below, will now return a 404 HTTP error.

```json
{
  "cars": [
    {
      "id": "cb12ac3e-0a71-49e7-b3a6-13f6f2e93692",
      "model": "Cadillac Miller-Meteor Sentinel",
      "manufacturer": {
        "id": "1f24ccba-4e3b-4c94-8e4d-7e156e318091",
        "name": "Cadillac",
        "slugName": "cadillac"
      },
      "licensePlate": "ECTO-1"
    },
    {
      "id": "575b98b8-8f17-47b7-985f-f3bfe6fa2607",
      "model": "Pontiac Trans-Am",
      "manufacturer": {
        "id": "c57a0f2f-78f4-4e02-a7a6-5dfa4b7884e7",
        "name": "Pontiac",
        "slugName": "pontiac"
      },
      "licensePlate": "KNIGHT"
    },
    etc...
  ],
  "page": {
    "previous": "YXZhaWxhYmxlX2NhcnNfXzAx",
    "next": "YXZhaWxhYmxlX2NhcnNfXzAy",
    "total": 100
  }
}
```

----

> In all 3 examples, consumers will most likely experience critical bugs and will have to update their code base to keep
> communicating with the Provider.

You might think that incrementing the major version of the API for each of those changes was excessive, but releasing a
minor version instead would prevent consumers to be aware of changes that might break their software.

You would be right on the other hand to assume that simply releasing major version on a regular basis is bad.

In a perfect world, there should be no breaking change. Ever.

In the world we live in, there should be a consensus that breaking changes are to be the exception, and that when they
happen, they should be handled properly through automated notifications, tests and validation, thus decreasing the risk
of incidents.

There are many ways to both prevent breaking changes and handle them. Some are specific, others universal. Either way, I
will make my best to address as many as possible in the sections below.

## Why do breaking changes happen ?

### Lacking skills & experience

As a first universal cause for breaking changes and at the risk of sounding condescending, I would put the lacking
knowledge of computer programming fundamentals at the top of my list.

As weird as it may sound, most developers do not master the principles that make a software "soft", meaning robust and
easily maintainable (the list below is not extensive):

- An extensive understanding of the 3 primary programming paradigms (structural, functional, object oriented)
- S.O.L.I.D. principles
    - S.R.P (Single Responsibility Principle)
    - O.C.P (Open Close Principle)
    - L.S.P (Liskov Substitution Principle)
    - I.S.P (Interface Segregation Principle)
    - D.I.P (Dependency Inversion Principle)
- Demeter law
- C.C.P (Component Cohesion Principle)

The resulting software will usually prove to be frail, with an increased risk of volatility.

### Volatility vs Variability

Developers often confuse Volatility with Variability. Although linked, they are not mutually inclusive.

> Variability describes an aspect that is subject to a conditional factor and can be handled by a clean design.
>
> Volatility is by definition a variable that invalidates the very core of the system design and can only be handled at
> the cost of heavy and expensive design changes.

Depending on what you build, methods for identifying Volatility may vary but will come down to the one question.
How is the software used ?

Whether you are providing developers with a library of functions or a system with a set of features, understanding what
problem you are meant to solve is key to identifying areas of Volatility.

This all sound very abstract, but again the nature of your software will drive the tools and methods you should resort
to in order to map the areas that are subject to change.

A concrete method to identify and prevent volatility in an Event Driven business system are EventStorming workshops,
that I will address later in this article.

A way to tackle business complexity and change over time on a larger scale is to implement a Domain Driven Design (AKA
DDD). EventStorming was actually invented in the context of DDD.

## Preventing breaking changes

### When tackling business software

#### Write an effective use case

Writing an effective use case is key to ensure an alignment between the user, the Domain experts and the software.
Failing to identify the actual user intention will most likely result in a product that does not provide with a
sustainable UX and be highly subject to Volatility and breaking changes.

<b> Who, What & Why </b>

> As I often repeat, answering the 3 questions below is the starting point of a healthier system:
> - Who ? (The use case's actor)
> - What ? (A clear intent of the "Who")
> - Why ? (A reason that validates the "What")

Example:

**As a customer (Who), I want to retrieve a list of my orders (What), in order to follow their payment and shipping
status (Why).**

> Failing to answer one of the questions above is a red flag and something might be off. You might want to challenge the
> feature.

Note that "User" is never a valid answer to the question of "Who".

<b> Business rules </b>

A user intention usually comes tied with business rules. Listing and detailing them is essential, since they drive the
very core of the system. The system coherence will be directly affected by how those rules are implemented or not. An
incoherent system will push the teams to patch it over and over, leading to a more fragile architecture.

#### Adopt a decoupled architecture

One of the reasons why contracts often break is a high coupling and low cohesion.
Translation: the most of important part of your system, and what is most likely to evolve over time is tied to a
technology (A web framework, an ORM, etc...).
Consequently, what could have been a simple Variability will become a Volatility.

## REST Web APIs

### Handling breaking changes

> <b>Reminder</b>\
> REST is a software architecture allowing machines to communicate with each other.


