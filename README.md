# Ruby Cheat Sheet 

A collection of useful ruby commands and functions which was collected from sources across the internet. 


### Colons 

Colon variable refers to `:abc` type variables that you might have seen in Ruby. They are referred to as `Ruby symbols`. In comparison to other languages a Ruby symbol is not a variable since it is not assigned a value instead they behave much like an `Enum` in Java and C++. Most common use is to use it in a map variable. 



### Map Method 

Map is a method in Ruby which is used with Arrays, Hashes, and Ranges. The main use for map is to `TRANSFORM` data. 

Given an array of strings it allows us to iterate through every string and make every character UPPERCASE. 


```ruby 
array = ["a", "b", "c"]
array.map {|string| string.upcase}
# ["A", "B", "C"]
```

We call the `map` with a block where the block is the thing between the `{...}` which says HOW we want to transform every element in the array. Map returns a new array with the transformed element - it does the same thing as `each` except each always **returns the original unchanged object.**



# Closures in Ruby: Blocks, Procs and Lambdas

In programming languages with **first-class functions**, functions can be stored in variables and passed as arguments to other functions. Functions can even use other functions as their return values. 

A **closure is a first-class function with an environment**. The environment is a mapping to the variables that existed when the closure was created. The closure will retain its access to these variables, even if they are defined in another scope. 


Ruby doesn't have first-class functions, but it does have closures in the form of blocks, procs and lambdas. **Blocks are used for passing blocks of code to methods**, and **procs/lambdas allow storing blocks of code in variables**. 



## Blocks 

In Ruby, blocks are snippets of code that can be created to be executed later. Blocks are passed to methods that yield them within the `do` and `end` keywords. One of the many examples is the `#each` method which loops over enumerable objects. 


```Ruby 
[1,2,3].each do |n|
    puts "#{n}!"
end 

[1,2,3].each { |n| puts "#{n}!"} # one line equivalent 
```






# RSpec 

### Expecting messages 

Use `expect().to receive()` to expect a message on a test double. Unfulfilled message expectations trigger failures when the example completes. The negative is `expect().not_to receive()`. 





### RSpec Mocks 

`Rspec-mocks` helps to control the context in a code example by letting you set known return values, fake implementations of methods, and even set expectations that specific messages are received by an object. 


A test double is an **object that stands in for another object** in your system during a code example. Use the double method, passing in an optional identifier, to create one: 

```ruby 
book = double("book")
```



### The Basics 

RSpec gives users a way to encapsulate what you are testing via the `describe` block and its friend `context`. In a general unit testing sense, we use `describe` to describe the behavior of a class: 

```ruby 
describe Hash do 

end 
```

Tests are written using the `it` block. Here is an example of how you might write a spec for the `Hash` class: 


```ruby 
describe Hash do 
    it "should return a blank instance" do 
        Hash.new.should == {}
    end 
end 
```

Those are the basics of writing some rspec examples. You can run the above by installing rspec via `gem install rspec` and putting that code in a file called `hash_spec.rb` and running 

```bash 
$ rspec hash_spec.rb
```

We can also set up the test state using the `before` and `after` directives. This will apply to anything in your describe block: 

```ruby 
describe Hash do 
    before do 
        @hash = Hash.new({:hello => 'world'})
    end 

    it "should return a blank instance" do 
        Hash.new.should == {}
    end 

    it "hash the correct information in a key" do 
        @hash[:hello].should == 'world' 
    end 
end 
```

The above code will creating the `@hash` variable before each test is run. There will be two arguments for before - `all` and `each`. Using the `all` argument, the setup will be done once before all of the tests in the block, and `:each` will be done before each individual test. The `after` directive has the same options and runs the same way, only after tests are completed. This is most useful when tearing down the previous state of the tests. 


**RSpec Idioms** 

We usually use the `describe` keyword to describe methods. Using a "." will signify that you are testing **a class method**, and using a "#" will signify that it is **an instance method**. Here is how it might look for a made up class: 


```ruby 
describe MyClass do 
    describe ".class_method_1" do 
    end 

    describe "#instance_method_1" do 
    end 
end 
```

The `context` method does the same thing by letting you contextualize a block of your tests. This is extremely powerful for test states when you add more complicated setup and teardown code to really get in to your objects. Let's look at a real life scenario by building a burger class:


Say that in our `Burger` class we are trying to test the `#apply_ketchup` method as some people may not want ketchup on their burger. We will write a test for the class to not apply ketchup if someone does not want it. 


```ruby 
describe Burger do 
    describe "#apply_ketchup" do 
        context "with ketchup" do 
            before do 
                @burger = Burger.new(:ketchup => true)
                @burger.apply_ketchup
            end 

            it "sets the ketchup flag to true" do 
                @burger.has_ketchup_on_it?.should_be_true
            end 
        end 

        context "without ketchup" do 
            before do 
                @burger = Burger.new(:ketchup => false)
                @burger.apply_ketchup
            end 

            it "sets the ketchup flag to be false" do
                @burger.has_ketchup_on_it?.should be_false
            end 
        end
    end 
end 
```


**Cleaning Upa a Bit** 

The `let` keyword allows us to reuse the same object in all tests under the same describe block. 


The above pattern works but can be a bit tiresome to repeat all the time. RSpec gives us some helper methods to generalize it. We could rewrite the above using the `let` keyword to make the variable automatically. The variable would then get created the first time it is accessed: 


```ruby 
describe Burger do
    describe "#apply_ketchup" do 
        context "with ketchup" do 
            let(:burger) { Burger.new(:ketchup => true) }
            before { burger.apply_ketchup }

            it "sets the ketchup flag to true" do 
                burger.has_ketchup_on_it?.should be_true
            end 
        end 

        context "without ketchup" do 
            let(:burger) { Burger.new(:ketchup => false) }
            before { burger.apply_ketchup }

            it "sets the ketchup flag to false" do 
                burger.has_ketchup_on_it?.should be_false
            end 
        end 
    end 
end 
```

This is nice but we can clean it up even further using the `subject` method. The `subject` method tells rspec what we are doing the tests on. We are going to combine the specify method in the next example. The `specify` method is just like the `it` method except the `specify` method takes the code block as the description of the test: 

```ruby 
describe Burger do 
    describe "#apply_ketchup" do 
        subject { burger }
        before  { burger.apply_ketchup }

        context "with ketchup" do 
            let(:burger) { Burger.new(:ketchup => true) }
            
            specify { subject.has_ketchup_on_it?.should be_true }
        end 

        context "without ketchup" do 
            let(:burger) { Burger.new(:ketchup => true) }

            specify { subject.has_ketchup_on_it?.should be_false}
        end 
    end 
end 
```


One neat thing about rspec is that the built in matchers will let you declaratively specify methods in your tests if they conform to a certain naming convention. RSpec will look for methods that are named with `has` and end in a question mark to let you write declarative test code. Here is what the final Burger class can look like with that idiom. Here is what our `burger_spec.rb` will look like: 


```ruby 
describe Burger do 
    describe "#apply_ketchup" do 
        subject { burger }
        before { burger.apply_ketchup }

        context "with ketchup" do 
            let(:burger) { Burger.new(:ketchup => true) }
            it { should have_ketchup_on_it }
        end 

        context "without ketchup" do 
            let(:burger) { Burger.new(:ketchup => false) }
            
            it { should_not have_ketchup_on_it}
        end 
    end 
end 
```


**Getting Started** 

RSpec is a testing tool for Ruby, created for behavior-driven development (BDD). It is the most frequently used testing library for Ruby in production applications. Even through it has a very rich and powerful DSL (domain-specific language), at its core is a simple tool which can be used rather quickly. 

Behavior-driven development is a concept built on top of TDD. The idea is to write tests as specifications of system behavior. 


        
        
**RSpec Stubs** 

In RSpec, a stub is often called a Method Stub and its a special type of method that **"stands in" for an existing method or for a method that doesn't even exist yet**. 

```ruby 
describe ClassRoom do 
    it 'the list student names method should work correctly' do 
        student1 = double('student')
        student2 = double('student')

        allow(student1).to receive(:name) { 'John Smith'}
        allow(student2).to receive(:name) { 'Jill Smith'}

        cr = ClassRoom.new [student1, student2]
        expect(cr.list_student_names).to eq('John Smith,Jill Smith')
    end 
end 
```


**Allow method** 

Suppose we have the following code block: 

```ruby 
book = double("book")
allow(book).to receive(:title) { "The RSpec Book" }
expect(book).to receive(:title) { "The RSpec Book" }
```

In the case of the `expect` statement, we expect that when the book object calls title method it will return the string "The RSpec Book". However for the  `allow` statement we **assume that book object has a method title and it will return "The RSpec Book** when the method is called. 



**Fabrication** 

Fabrication generates objects in Ruby. Fabricators are schematics for your objects and can be created as needed anywhere in your app or specs. 

Fabrication can generate anything, but has specific support for ActiveRecord Models, Mongoid Documents, Sequel Models, and DataMapper Resources. 


To use it with Bundler just add it to your gemfile

```ruby 
gem 'fabrication'
```

You can define a schematic for generating objects by defining Fabricators as 

`spec/fabricators/**/*fabricator.rb`


Fabricators are loaded automatically - so as long as they are in the right place we are good to go. 

Let's say that we have a `Person` model with the usual fields and some associations: 


```ruby 
class Person < ActiveRecord::Base 
    belongs_to :neighborhood 
    has_many :houses
end 
```

You could then create a `Fabricator` to automatically generate copies of `Person` for your test suite. 

```ruby 
# Located in spec/fabricators/person_fabricator.rb 

Fabricator(:person) do 
    neighborhood 
    houses(count: 2)
    name { Faker::Name.name }
    age 45
    gender { %w(M F).sample }
end 
``` 

Every time you fabricate a person, you will get a brand-new instance of a person model persisted to the database and containing the fields you specified. In the case above, `neighborhood` and `houses` would automatically expand out to use the fabricators for those models and be persisted as well. 



**Fabricating Instances** 

Once we have defined some fabricators, we can use them anywhere in the application. This is especially useful in populating scripts for development and staging environments as well as in the test suite. 


We can Fabricate a new instance of the person object we defined above every time we call: 

`Fabricate(:person)`

We can also provide overrides to the default options at `Fabricate` time with a hash or the same block syntax you used to define the Fabricator. 


```ruby 
Fabricate(:person, name: 'Paul Elliott', gender: 'M') do 
    houses { [Fabricate(:house, location: 'the beach')] }
end 
```

If we don't want to persist the object in the database we can use `Fabricate.build` and skip the save step. All the normal goodness when Fabricating is available for building as well. 


```ruby 
Fabricate.build(:person)
```




# Constructors

A constructor is a special method of the class that gets automatically invoked whenever an instance of the class is created. Like methods, a constructor may also contain the group of instructions or a method which will execute at the time of object creation. 

Whenever an object of the class is created using new method, internally it calls the `initialize` method on the new object. Also all arguments passed to `new` will automatically pass the method initialize. 



```ruby 
class Class_Name 
    def initalize(parameter_list) 
    end 
end 
```


# Attributes 

In OOP, the `member access control` concept is important as it allows programmers to control the access to encapsulated variables. **Member Access Controls separates the public interface of a class from the private implementation details and the protected members.** In many programming languages this concept is implemented by using `getters` and `setters` for each member. In Ruby, the `attr_*` methods are in charge of the member access control.   















