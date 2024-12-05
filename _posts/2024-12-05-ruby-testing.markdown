---
layout: post
title:  "Ruby Testing"
date:   2024-12-05 11:30:00 +0000
categories: jekyll update
---

## Ruby Testing

This is an overview of testing techniques for Ruby, if you've not seen them before or if your getting back into testing, don't worry, but it's pretty ancient stuff now. The general ideas are transferrable to other languages including Node.

## Black Box Testing

Black Box Testing is testing a class without looking into it, like a black box. We can't really do integration testing with black box testing, the two don't really go together.

Out of all of the testing techniques, why does black box testing stand out as a good technique? It seems obvious right, you just take a class, only observe what it outputs and test against those expectations.
Doing it this way says; I'm not going to care about they way you implement this method, even I'm not completely sure what my class structure should be.

In Ruby it's basically stating let's not look into the code and create mock objects in the tests which are duplicating the logic in the code. The idea is to reduce coupling between classes, and from the outset it seems counter-intuitive. 

Surely we need test objects which isolate classes to reduce coupling. Black Box testing works well, when classes and their interfaces are designed well. 

For example if you've ever tried to test a class with a line of code with multiple demeter violations, e.g. long.extra.methods then you will find very quickly that you have to specifically test in your code for each of these methods and return a value for each of these method calls, unless you go for more of an integration testing approach, which can make your factories very complicated. To do Black Box testing well we wan't to isolate our classes in our tests and not create really complex tests that create test objects that are looking into our code.

The following are the techniques that we can use to try to decrease coupling in our integration tests and decrease coupling between the test suite and code in black box testing:

## Doubles & Mocks, Expect, Allow, Stubs & Spies

This will be hard to describe because the current view is that a mock is a double with expectations. However it wasn't always like this. 

I think that the initial idea was that a double was an object you could create in your test suite which was lightweight and could be used to send to methods which expected a variable, and perhaps you didn't care about what it did with the value, just that the method received it. Additionally you *could* add expectations to it, e.g. stubs. A double was an Object, but it was just an Object and it was lightweight, it wasn't any *specific* instance of an Object. A Mock on the other hand was a very specific duplication of an Object that existed in the system, e.g a Product. If you created a Mock of that object you would get all of the methods attached to it and then you could add additional stubs to it. Also a mock object would directly replace the thing you were trying to test in your system by hooking into it and replacing it, while a Double would not do this.

In RSpec 3 there is also `instance_double` which is known as verifying double, which has a guard where you cannot stub a method that does not already exist on the class.

Spies are like stubs but they are designed to help you to get your head around testing expectations. They change the order of the declaration of the expectation to after the method has been run. You say that an object has a spy object attached to it, then you run your method, and then afterwards you check that the method received the other method call you wanted. When expectations are afterwards it can be a lot easier to understand.

Examples are important here, let's start with a bit of code that we want to isolate and test:

```
  def build_cart
    @cart = UserCart.retrieve(params[:cart_id])
    @cart.save_cart_abandoned_stat
    @cart.add(cart_objects)
    
    if @cart.save
      :201
    else
      :400
    end
  end
```

specs with different types of expectations:
```
  it 'saves a cart' do
    cart = mock_model(UserCart)
    cart_objects = Double("Cart Objects")
    allow_any_instance_of(UserCart).to receive(:retrieve).with(cart_id).and_return(cart)

    allow(cart).to receive(:save_cart_abandoned_stat)
    expect(cart).to receive(:add).with(cart_objects)
    Cart.build_cart
  end
```

### Difference between expect and allow.

Expect and Allow are both additional behaviour that you can add to your objects to isolate your method calls in testing. Once expect is attached to an object and called with something it will only pass if that method definately receives that message call. Allow is different and a spec can pass even if that message call was not received. Allow is used when we want to stub a method call but we are not interested in knowing that the method was called, usually to test an additional line in a method that is not the focus of the test, or to advance a test that would otherwise not know what to do with the save_cart_abandoned_stat if @cart was a Double.

## Pure Functions

Pure functions are ones which don't have any side effects. This means that if you call them they are going to
directly calculate a result from the input you've given and they don't call any external methods in your code base.

This isn't taken to the extreme that the methods don't call anything from the standard library, but it's 
something to keep in mind as a pattern if your attempting to make your code base easier to test. My opinion
is that it's much better to have lots of methods which are easier to test, but some people don't have that opinion and would rather that the code base does not contain hundreds of one line methods. If your using a style checker it will probably warn you straight away if you have a really long method so finding a method like that is a good reason to refactor it.

## let vs let!

When using the most popular testing framework for Ruby and Ruby On Rails, there is a really important syntactic difference, which if noone tells you what it means can severely slow you down. In an RSpec test it is very common to create test data like:

with an array:

```
  let(:expectation_result) { [:404, "Resource Not Found"] }
```

with a hash:

```
  let(:expectation_result_hash_with_block_syntax) do
    { foo: bar }
  end
```

the above will create variables in your RSpec so you can test with them, 
```
  it 'returns foo bar' do
    expect(foo.bar).to eq expectation_result
  end
```

with a normal let the expectation_result variable will only be built when it is called, because RSpec uses
let to lazy evaluate the data in the test suite.

Instead with let!
```
  let!(:expectation_result { [:201, "Created"] })
```

The most typical use case for a let! is that your creating a factory that contains test data that needs to be called. 

The most obvious example to understand is:

```
  let(:cart_info) { create(:cart_info) }
  let(:user_with_association) { create(:user, cart_info: cart_info )}
```

If we went ahead and created a test which uses the cart_info variable but had an integration with user data the user data would be missing and the test wouldn't pass.

This case would look like:

```
  it 'returns foo bar' do
    expect(cart_info.duplicate_from_user).to eq { { user_cart: [ cart_id: 33, user_cart: 23 ]}}
  end
```

The person running the test would assume that the user factory created in the second let had associated the cart_info to it, but because we are using let, this didn't happen. To get around this problem if we want to use complex factories with associations we need to make sure that the second let is called. 

What we would need to do is to change:

```
  let(:user_with_association) { create(:user, cart_info: cart_info )}
```

to:

```
  let!(:user_with_association) { create(:user, cart_info: cart_info )}
```
so that it is not lazyily evaluated, and probably we would change the first let to a let! also for consistency.

Apparently let on it's own also returns the memoized value, so atleast you know that let will return a consitent value.

## Style & Not nesting context

Most people who use RSpec a lot are quite strongly opinionated not to nest multiple contexts, to reduce deep nesting and to make specs more readable:

``` 
context "cart_specs" do
  context "cart_full_specs" do
  end
end
```
Another important style consideration for Rspec, from the styleguidelines is, one expectation per it block:

instead of:

```
describe ".instance_method" do
  it "updates messages" do
    expect(user.update_friend_message).to eq (:foo_bar)
    expect(user.update_friend_message).to eq ([:foo_bar, :foo_bar])
    expect(user.all_messages).to eq([:foo_bar, :foo_bar])
  end
end
```

we use:

```
describe ".update_friend_message" do
  it "updates a message"
    expect(user.update_friend_message).to eq (:foo_bar)
  end

  it "updates multiple messages"
    user.update_friend_message
    expect(user.update_friend_message).to eq (:foo_bar)
  end
end

# before all became before each
describe ".all_messages" do
  before(:each) do
    2.times do
      user.update_friend_message
    end
  end

  it "gets all messages" do
    expect(user.all_messages).to eq([:foo_bar, :foo_bar])
  end
end
```

Also you can use instance variables or helper methods in tests, these can even be shared between test files if set up properly in RSpec, but these are not used as much.

e.g.
```
describe "fooooobar" do
  before(:each) do
    @instance_var = "fooobar"
  end

  it "uses foobar"
    expect(User.class_foo_bar(@instance_var)).to be >= "fooo"
  end
end
```

## Multi Env Tests (qa, uat)

I've never seen multi env tests before in an application, but it's just one of those strange ideas that may or may not happen.

It's a little bit more than, let's just run the BDD tests on the UAT environment only. 
The idea here is not to test the environment configuration but that your UAT environment may have better test data in it's database. If your using a lot of factories then it won't even be a consideration, but I like to build some tests top down with integration testing, this can reduce the amount of mocks and stubs you use but couple you to the bahviour in another class, so it can be a consideration. You can easily tag tests in RSpec if you want to have custom behaviour, you can use guard to run your tests every time you save and you can also run individual specs by giving a specific line number to the test.

## An RSpec Test with a tag

it "RSpec test with a tag", :focus => true do; end

to run all tests with that tag use rspec . --tag focus

## An RSpec Test for one specific spec or context

rspec spec/examples/user_spec: 32    

which would point to a describe line or within.

this can also be used for contexts:

rspec spec/examples/user_spec: 28  

which will run all specs within that context.

### Note on history of testing in Ruby on Rails

Originally a lot of people favored UnitTest in Rails, because it was already built into the Rails framework, but gradually RSpec became a lot more popular because it's DSL was easier to read and understand. Minitest was also used but was not as popular.