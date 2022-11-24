# Breaking changes: When software isn't soft anymore

I noticed that a lot of developers heard or used the term "breaking change" with no actual knowledge of what it meant.

This article aims to provide with a more extensive knowledge on the topic, as well as tools to handle various changes.

## What is a breaking change ?

> In software development, a breaking change is a software modification that will force a change of implementation on
> the client's (or consumer) side. <b>In other words, it is a software update with backward incompatible API
> changes.</b>

There are different kinds of breaking changes. Some are tied to the version release of a programming language
or framework, some to a REST API or message version.

> In every case, the breaking change is the result of a modification in a contract between the provider and consumer.

Below an example.

In Python 2.x, printing a message to a standard output device required to use a statement called print:

```python
print
"Some message"
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

Given the same car rental service API with the same route GET /cars/{id}.

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

After releasing, the version 2.0 of the API, it war requested to change the way manufacturers are exposed:

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

In both examples, consumers will face potential critical bugs and will have to update their code base to keep
communicating with the Provider.

You might think that incrementing the major version of the API for each of those changes is excessive, but releasing a
minor version instead would prevent any consumer to be aware of a change that might break their software.

You would be right on the other hand to assume that simply releasing major version on a regular basis is bad.

In a perfect world, there should be no breaking changes. Ever.

In the world we live in, there should be a consensus that breaking changes should be the exception, and that when they
happen, it should be handled properly through automated notifications, tests and validation, thus decreasing the risk of
incidents.

There are many ways to both prevent breaking changes and handle them. I will try to address as many as possible in the
sections below.

## REST Web APIs

### Prevention

### Handling breaking changes

> <b>Reminder</b>\
> REST is a software architecture allowing machines to communicate with each other.



