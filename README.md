# Intro to Rspec

## Overview

Ever wanted to write some RSpec tests for a Ruby project, but didn't know where to start? This is for you!

## Objectives

+ Understand the purpose of testing our code
+ Setup a test environment using RSpec
+ Write tests for existing code

## Why Write Tests?

Pretend you’ve been given a code challenge. You have to write a method that takes in a number. If the number is divisible by 3, the method should return ‘Fizz’. If it’s divisible by 5, it should return ‘Buzz’. If it’s divisible by both, it should return ‘FizzBuzz’. If none of the above is true, it should just return the number.

To start out, you decide to spend some time writing down exactly how this method should behave. You want to make sure you’re not missing anything. This gives you a chance to really think about what the method should do. You jot down some notes.

```
#fizz_buzz
  when divisible by 3
    returns 'Fizz'
  when divisible by 5
    returns 'Buzz'
  when divisible by 3 and 5
    returns 'FizzBuzz'
  when not divisible by 3 or 5
    returns the number
```

Now that you have your expectations written out, you write out your method definition and test it out in IRB or by running the file. You define the method, run it with the given inputs and see if it returns what you expect.

The problem is, you’re a programmer. Wouldn’t it be nice if you could write out your expectations AND test your code all in one go? Let’s do it!

## Implementing a Test Framework from Scratch

Imagine writing a method that would run your code for you and output a message based on the result. Say we wanted a method to check that calling fizz_buzz(3) actually returns "Fizz" .

```ruby
require_relative './fizz_buzz.rb' # pretend we have a `fizz_buzz` method defined in this file

def it_returns_fizz_when_divisible_by_three
  print 'returns Fizz when divisible by three: '
  if fizz_buzz(3) == 'Fizz'
    puts 'Passed!'
  else
    puts 'Failed!'
  end
end

it_returns_fizz_when_divisible_by_three
```

Now, when we call `it_returns_fizz_when_divisible_by_three` , we’ll get some output to the console to see if our `fizz_buzz` method is returning what we want.

```bash
$ ruby long_test.rb

returns three when divisible by three: Passed!
```

Assuming that our code works as expected, we’ll see a passing test. If we break our code somehow, we should get notified.

```bash
$ ruby long_test.rb

returns three when divisible by three: Failed!
```

This is great! Let’s repeat this process to test for multiples of five.

```ruby
require_relative './fizz_buzz.rb'

def it_returns_fizz_when_divisible_by_three
  print 'returns Fizz when divisible by three: '
  if fizz_buzz(3) == 'Fizz'
    puts 'Passed!'
  else
    puts 'Failed!'
  end
end

def it_returns_buzz_when_divisible_by_five
  print 'returns Buzz when divisible by five: '
  if fizz_buzz(5) == 'Buzz'
    puts 'Passed!'
  else
    puts 'Failed!'
  end
end

it_returns_fizz_when_divisible_by_three
it_returns_buzz_when_divisible_by_five
```

We run this and output:

```bash
$ ruby long_test.rb

returns Fizz when divisible by three: Passed!
returns Buzz when divisible by five: Passed!
```

Awesome! We now have the expectations about how our code works and some code to check it all in one place. Still, this approach to testing has some problems.

1. The test methods that we’re defining aren’t very readable.

2. They’re a bit of a pain to setup.

3. There is a lot of duplicated code.

Let’s create a simple test-framework that we can reuse for many different cases. We can create some methods that read like plain english, test our code, and give us a nice looking output to the console. Here’s what I’d like our test file to look like.

```ruby
describe '#fizzbuzz' do
  it 'returns Fizz for a multiple of three' do
    fizz_buzz(3) == 'Fizz' && fizz_buzz(9) == 'Fizz'
  end

  it 'returns Buzz for a multiple of 5' do
    fizz_buzz(5) == 'Buzz' && fizz_buzz(10) == 'Buzz'
  end

  it 'returns FizzBuzz for multiple of 3 and 5' do
    fizz_buzz(15) == 'FizzBuzz' && fizz_buzz(45) == 'FizzBuzz'
  end

  it 'returns the number if not a multiple of three or five' do
    fizz_buzz(7) == 7 && fizz_buzz(11) == 11
  end
end
```

Notice how similar this looks to the original notes we wrote about `fizz_buzz` at the beginning. The only differences:

1. We’re calling two methods, describe and it . Each method takes a string.

2. Both describe and it take in a block. The block returns true or false depending on what our fizz_buzz method returns.

Implementing this is actually pretty simple. Let’s put these methods in a module that we can include in our test file. We’ll start with `describe`. This method simply takes in a string, prints that string to the console, and then runs whatever code is passed into the block. The purpose here is to just format our output a little bit.

```ruby
module MyTestFramework

  def describe(condition)
    puts condition
    yield
  end

end
```

Now, let’s add a method called it . This method is a bit more complex. First, we’ll take in a string and output that to the console. Again, this is just to make sure that our test output reads nicely.
```ruby
module MyTestFramework

  def describe(description)
    puts description
    yield
  end

  def it(description)
    puts '*' * 20
    print description
  end
end
```

Finally, we’ll yield to a block. For our purposes, we’ll simply check if the block returns true or false . We’ll print ‘Passed!’ if it returns trueand ‘Failed!’ if it returns false .

```ruby
module MyTestFramework

  def describe(description)
    puts description
    yield
  end

  def it(description)
    puts '*' * 20
    print description + ': '
    outcome = yield ? "Passed!" : "Failed!"
    puts outcome
  end
end
```

That’s pretty much it! Let’s include the module in the file above and run it!
```ruby
require_relative './fizz_buzz.rb'
include MyTestFramework

describe '#fizzbuzz' do
  it 'returns Fizz for a multiple of three' do
    fizz_buzz(3) == 'Fizz' && fizz_buzz(9) == 'Fizz'
  end

it 'returns Buzz for a multiple of 5' do
    fizz_buzz(5) == 'Buzz' && fizz_buzz(10) == 'Buzz'
  end

it 'returns FizzBuzz for multiple of 3 and 5' do
    fizz_buzz(15) == 'FizzBuzz' && fizz_buzz(45) == 'FizzBuzz'
  end

it 'returns the number if not a multiple of three or five' do
    fizz_buzz(7) == 7 && fizz_buzz(11) == 11
  end
end
```

```
$ ruby shorter_test.rb

#fizzbuzz

********************

returns Fizz for a multiple of three: Passed!

returns Buzz for a multiple of 5: Passed!

********************

returns FizzBuzz for multiple of 3 and 5: Passed!

********************

returns the number if not a multiple of three or five: Passed!
```

Not too shabby for only a few lines of code!

**Using Rspec**

Luckily, we don’t actually have to implement these methods ourselves. We can use RSpec, an awesome testing library that comes with a bunch of formatting options and a really nice syntax for checking our expectations.

To get setup, we’ll first install the rspec gem, either using bundler or manually.

`$ gem install rspec`

Next, let’s setup RSpec for our project. In the folder that you’re working in, run `rspec --init` . This will create a couple of things in this directory:

1. A .rspec file, where we can add some configuration options.

1. A spec directory, where we’ll write our specs.

1. A spec_helper.rb file inside of the spec directory.

Running `rspec` from the command line will automatically run any tests in any files that are inside of the spec directory and end in `_spec.rb` Let’s create a file for our fizz_buzz specs.

Inside of the `spec_helper.rb` file, we'll load any files that our test environment will need to run. In a very simple example, this might just be the file where your `fizz_buzz` method is defined. In a more complex project, you may have an environment file that you can load in.

`$ touch spec/fizz_buzz_spec.rb`

Copy/paste our existing specs. From the command line, run rspec .

```
$ rspec

#fizzbuzz

returns Fizz for a multiple of three

returns Buzz for a multiple of 5

returns FizzBuzz for multiple of 3 and 5

returns the number if not a multiple of three or five

Finished in 0.00138 seconds (files took 0.16459 seconds to load)

4 examples, 0 failures
```

Boom! Beautiful! Finally, we can update our specs to use RSpecs built-in matchers. This will make our tests even more readable, and also provide us with a more helpful output in case of a failing test. For example: compare this:

```ruby
it 'returns the number if not a multiple of three or five' do
  fizz_buzz(7) == 7
end
```

to this:

```ruby
it 'returns the number if not a multiple of three or five' do
  expect( fizz_buzz(7) ).to eq(7)
end
```

Much more readable. Now, if this test were to fail, we’ll get this output:

```
$ rspec

#fizzbuzz

returns Fizz for a multiple of three

returns Buzz for a multiple of 5

returns FizzBuzz for multiple of 3 and 5

returns the number if not a multiple of three or five (FAILED - 1)

Failures:

1) #fizzbuzz returns the number if not a multiple of three or five

Failure/Error: expect(fizz_buzz(7)).to eq(7)

expected: 7

got: "Something wrong"

(compared using ==)

# ./spec/fizz_buzz_spec.rb:20:in `block (2 levels) in <top (required)>'

Finished in 0.14421 seconds (files took 0.28575 seconds to load)

4 examples, 1 failure
```
Voila!

```ruby
require_relative './spec_helper'

describe '#fizzbuzz' do
  it 'returns Fizz for a multiple of three' do
    expect(fizz_buzz(3)).to eq('Fizz')
    expect( fizz_buzz(9)).to eq('Fizz')
  end

it 'returns Buzz for a multiple of 5' do
    expect(fizz_buzz(5)).to eq('Buzz')
    expect( fizz_buzz(10)).to eq('Buzz')
  end

it 'returns FizzBuzz for multiple of 3 and 5' do
    expect(fizz_buzz(15)).to eq('FizzBuzz')
    expect( fizz_buzz(45)).to eq('FizzBuzz')
  end

it 'returns the number if not a multiple of three or five' do
    expect(fizz_buzz(7)).to eq(7)
    expect( fizz_buzz(11)).to eq(11)
  end

end
```
Very nice!

## Conclusion

Hopefully, you now see the benefits of TDD using Rspec. Learning the Domain-Specific-Language can take some time, but once you break it down, it makes your life a lot easier!
