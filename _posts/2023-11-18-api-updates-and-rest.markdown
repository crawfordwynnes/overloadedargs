---
layout: post
title:  "Api Updates"
date:   2023-11-18 13:00:00 +0000
categories: jekyll update
---

I originally wanted to consider delivering this blog post as a talk, but the belief that it would need to be held to quite high standards has instead transformed it into writing instead. It's about crafting APIs, and before I go into some of the points I'm trying to make, I want to say there are a lot of web APIs out there, which have 100s of endpoints, I'm not sure how useful this will be to designing those type of APIs but instead it's going into a bit more detail of a specific type of crafting APIs in REST by using the PUT/PATCH HTTP verb, so also by extension this doesn't really apply to GraphQL APIs, of which in my opinion, are difficult to understand when acting on a number of different resources, e.g. should a list contain an array or not. So sticking with what has been shown to work time and time again let's dive into the different ways we can use PUT and PATCH through Rails controllers in the `update` action.

Let's start with some of the real basics about this. If we call an endpoint via an PUT update e.g. `(put 'example', to: 'users#example')`, what are some of the actual things we can do with the values at a code level. Well let's imagine we have a model and that it has a state field, once you receive the fact that you want to change the state, there are a number of ways to modify it, we can directly send the state we want to change in the payload, for example we could send state: :loaded and then in the update directly set it to the value of :loaded and that's what the API responds with, we could also transition a value, so if we send :loaded, we could transition the saved value from :new, to :load and the API would respond with ':load_transitioned', and there is an intermediary way to change the value which would we send :loaded and the value can only transition to that state if that state change is allowed, in this case the API could respond with ':load_transitioned' if the laoded status is allowed.

Let's move onto the next, more important explanation of PUT/PATCH, let's imagine that we've got some JSON for an instructions API and we're going to send an update with the JSON, an example payload my look like:

```
{
    id: 340,
    description: 'updated description',
    instruction: 'modify description later'
},
{
    description: 'recently updated description',
    instruction: 'modify description today'
}
```

Notice how there is a JSON object with an id and one without, and we find out that to build this API the behaviour should be to find the record with id: 340, update the fields and then move onto the next object and create this record. That seems all well and good, but what happens first if the id of 340 does not exist, should we go ahead and create this object? Well, probably not because of AUTO INCREMENTING ids in the database, we never create with an id, if we are using UUID, then maybe it makes some sense, however what about the grey area, where we can't find id of 340 but we still go ahead and create the second object, can the API just fail silently? Well as it turns out yes, and this is a good way to design an API for an application that consumes JSON to use.

Let's think about this a bit more:

  * We want to design an API that can receive an UPDATE, on an id that exists.
  * We want to design an API that can receive an UPDATE, and creates data on objects that don't have ids.
  * We want an API that can ignore objects that have ids but can't be found in the database.

So following on from above what happens if we send an UPDATE and we can't find the record with the id, but we allow this to fail (i.e. not respond with a validation message), and then the second object is created.
  
In this case we should respond with a JSON payload with the id's of the objects that were created/updated, and this is fine aslong as we have communicated the API behaviour to the application consuming the API, because the alternatives are that we change the behaviour of the API and do not allow any of the update if there is an object with an id that does not exist, there is also not allowing creation of all only when some of the objects contain other invalid data. This is why API documentation is incredibly important, and in the Rails world we have tools like Swagger which we can use to combine with testing, (Swagger can generate specs for you), which communicate the structure of API calls and their responses. Swagger can even be expanded to use `require` or `require_relative` to load response examples per file, which allows a more maintainable generation of the OpenAPI documentation that you create, and you can keep your spec files DRY.

With all that out the way it's left to say that we are not finished yet, let's go back to our API design and add another field with an ID, we decide to send our JSON like:

```
{
    id: 340,
    description: 'updated description',
    instruction: 'modify description later'
},
{
    id: 341,
    description: '2nd updated description',
    instruction: 'modify description much later'
},
{
    description: 'recently updated description',
    instruction: 'modify description today'
}
```

We get our payload from our params, and we send this to be updated, at this point something strange is going on, we're going to have to lookup in the Database the records for 340 and 341, let's assume that we find both, we end up with two objects, the data that we want to update and the data that we already have, even if we wrap all of this in a transaction so that we can say hang on, don't do anything with this update if it fails, what happens if it goes ahead, and the object containing the array of records to be updated looks slightly different, i.e.

```
{
    id: 341,
    description: 'old description',
    instruction: 'old instruction,
},
{
    id: 340,
    description: 'old description',
    instruction 'old instruction',
}
```

notice how the ID's are in a different order, we do definitely need to guarentee the order so that the correct records are updated with the correct id's. To do this all we are left to do is to sort both of the objects by id, so we can make sure there are no issues. Why do we need to sort a hash which has the ids in it I here you ask, this is because if you want to extract useful error messages about which record the validation has failed on you will need to loop through updating your ActiveRecord response in a transaction, which will not guarentee that the id's are in order. This has profound implications, not just for GDPR if it's important data, but also for all sorts of APIs that just need to guarentee correctness.

I will leave you with this; when you consider trying to define the behaviour of UPDATE in your API and that if you've managed to get everything else understood and the client comes along and requests that for some reason your index action needs to include JSON from a related object, and you get your head around the N+1 problem, from API level to code level with nicely formatted pagination etc, what happens if the client requests you to change the API to update this new complex object, well my advice is, I've not been it that situation yet, but I advise you not to update objects with a relationship (you may well end up using Rails build and nested params in API mode, which I guess would be strongly discouraged) and to try to design ways around this. 