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


## RSpec 

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












