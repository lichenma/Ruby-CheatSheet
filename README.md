# Ruby Cheat Sheet 

A collection of useful ruby commands and functions which was collected from sources across the internet. 


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







