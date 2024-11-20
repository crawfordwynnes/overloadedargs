---
layout: post
title:  "Rails Enums"
date:   2024-11-20 16:00:00 +0000
categories: jekyll update
---

This is a quick post about the use of enums in Rails. The main point of it is to show that there can be problems if you don't set them up properly. Enums are everywhere, especially from older languages like C, if you've not used them before it may be easier to quickly try and get some enums going in a programming language without Rails. Basically an enum is a way of creating a collection of states that a variable can be in. It allows programs to be more easily understood because if your using something with an enum type the programmer knows that it's only ever going to be one of those types. The extensions to enums, which are used very regularly are the fact you can add small wrapper methods which modify the state of the enum, and that you can assign integers to the enum. This is used to decrease storage space in the database and potentially to improve database access. Just because they are simple to use dosen't mean that they should be a go-to solution as sometimes you want something a bit more complex to give you a bit of extra flexibility later on. However using enums can help you think of parts of your code base as state machines, if this is what you need. State machines are a good solution to some problems.

An Enum declaration in C:

```enum State {Working = 1, UnpaidTaxes = 0}; ```

## Enums in Rails

Rails introduced enums, way back in Rails 3, the first step is to declare a migration to store the integer value of your enum in the database:

```bundle exec rails g migration AddStatusToPost status:integers```

which will give you:

```
class AddStatusToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :status, :integer, default: 0
  end
end
```

the final step is to add `enum` to your ApplicationModel like:

```enum :status, [ :draft, :live, :archived ]```

you can also use the %i syntax to declare your symbols without the : symbol if you have a long list of states,
and that's it. Rails will automatically give you wrapper methods to change the state of your model.

For example if you want to change your post into the live state, you use the name of the state you wan't to move into with the ! symbol on the end like: @post.live!, you can also use the convention for boolean methods to check what state your model is in, like: @post.live?. This allows you to create very simple state machines, for example if you only want to ever change your Post from draft to live, and from live to archived, you would create guard methods which would contain these checks, e.g.

```
def make_live!
  self.live! if self.draft?
end
```

This would rely on other programmers knowing that they were not supposed to call the self.live! method directly.

## Adding an Enum

Previously mentioned was that there could be problems if you don't set your enums up properly, so is the enum declaration above not correct? Well yes it is, and it will work but there is an extra step if you want to ensure your not making some extra work for yourself later on. The problem is that sometimes customer and clients make requests for extra features, and suddenly you've got to add an extra state which is represented by the order that the states progress. For example you may need a pre_live state. At this point for general good house-keeping and data understandability you would want draft to be 1, pre_live to be 2, live to be 3 and archived to be 4. If the system has already been live, then your database is going to contain a load of state: 2 and 3, for live and archived. If you added the extra pre_live state on the end then, all new pre_live states would need to be represented by 4, which is ok as long as you remember that 4 represented pre live. If you don't include the integer matches for each of the enum states then your probably going to get some issues because you can forget what each state was.

You can do this by including the integer with the label for the enum status:
```enum :status, [ draft: 1, live: 2, archived: 3, pre_live: 4 ]```

This is especially in the case of a Developer working on a new feature and testing it all fully, but not realising that the states were different on the live database. This would save you the extra headache of having to go in afterwards and either write data migrations or SQL updates based on date in case you accidently released the new feature to the customer. You can use any integers for each state if you want.

Once enums are setup correctly, you can directly use the enum in ActiveRecord lookups:

```
Post.new(title: "Title", content: "New Content", status: 4)
Post.new(title: "Title", content: "New Content", status: :archived)
```

Sometimes you may know in advance that you will need a full state machine, as noted above you wouldn't be able to just change the state to `live!` without the record being in the correct state. A great solution for state machines is the [statesman gem](https://github.com/gocardless/statesman), it allows you to specify each of your states, transitions and even extra hook methods which can be run on each state transition. If it's not immediately obvious that your problem domain is suitable for a state machine then see if you can use something else first.