# [That's Not Yours](https://8thlight.com/blog/eric-smith/2011/10/27/thats-not-yours.html)

* Mocks are meant to improve the expressiveness of code while reducing coupling

```ruby
# mocked
it "should update the attributes of the blog post" do
  mock_blog = mock_model(Blog)
  Blog.stub!(:find).with('9').and_return(mock_blog) 
  
  mock_blog.should_receive(:update_attribute).with({'title' => 'Eric', 
                                                    'text' => 'A unified theory'})

  post "update", {'id' => '9', 
                  'title' => 'Eric', 
                  'text' => 'A unified theory'}

# using model objects
it "should update the attributes of the blog post" do
blog = Blog.create!(:title => 'Not Eric', 
                    :text => 'a hackneyed theory')
  
  post "update", {'id' => blog.id, 
                  'title' => 'Eric', 
                  'text' => 'A hackneyed theory'}

  blog.reload
  blog.title.should == 'Eric'
  blog.text.should == 'A unified theory'
```

* Both the mocked test code and the model test code are coupled to ActiveRecord. Any object used in the mocked test will have to match ActiveRecord's interface.
* Can redesign the implementation to write the test with mocks that expresses the design better if you don't use ActiveRecord objects

```ruby
it "should update the attributes of the blog post" do
  BlogEngine.should_receive(:update_entry).with({:id => '9', 
                                                 :title => 'Eric', 
                                                 :text => 'A unified theory'})
  
  post "update", {'id' => '9', 
                  'title' => 'Eric', 
                  'text' => 'A unified theory'}
```

* Tests for `BlogEngine` will use real ActiveRecord objects where needed, however, everywhere else will just talk to BlogEngine and won't need to worry about ActiveRecord / the database

## A Networked Example

* Logic that monitored the status of various computers in a network
  * Network object, computer object, and the comptuer object depended on ping (which was a system call)
* Changed the Computer object to depend on the `Sonar` interface instead of the `ping` system call directly
  * There were various `Sonar` objects where `PingSonar` represented executing the `ping` system call, while `FakeSonar` was a fake implementation for testing purposes

* A framework or third party object often has to provide a large interface to solve a large number of needs, of which, you may only require a subset
  * When writing your wrapper, don't try and write everything it does, write the operations you need, with the interfaces you want
