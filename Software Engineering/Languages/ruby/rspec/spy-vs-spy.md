# [Spy vs. Spy](https://robots.thoughtbot.com/spy-vs-spy)

```ruby
# Spy-based approach
describe PostsController do
  it 'shows the given post on GET show' do
    post = stub('a post', to_param: '1')
    Post.stubs(find: post)

    get :show, id: post.to_param

    Post.should have_received(:find).with(post.to_param)
    should render_template(:show)
    should assign_to(:post).with(post)
  end
end

# Expectation-based approach
describe PostsController do
  it 'shows the given post on GET show' do
    post = stub('a post', to_param: '1')
    Post.expects(:find).with(post.to_param).returns(post)

    get :show, id: post.to_param

    should render_template(:show)
    should assign_to(:post).with(post)
  end
end
```

* Traditional unit tests have a four-stage approach
  * Setup: create necessary preconditions for the tests
  * Exercise: Run code you're trying to test
  * Verification: Ensure expected result
  * Teardown: Clean up after test
* With expectation-based approach, verification statements are made during setup phase
  * If they are called unexpectedly, will fail in exercise phase
    * Will fail with the same message
  * If never called, will fail in teardown phase

```ruby
describe PostsController, 'on GET show' do
  before(:each) do
    @post = stub('a post', to_param: '1')
    Post.stubs(find: @post)

    get :show, id: @post.to_param
  end

  it { should render_template(:show) }

  it 'should find and assign the given post' do
    Post.should have_received(:find).with(@post.to_param)
    should assign_to(:post).with(@post)
  end
end
```

## Sharing Stubs Between Tests

* Problem with mocks is that unable to share stub without sharing built-in verification
* This is because when the mock is created, the expectation is also defined
* So in other tests where the expectation is unimportant, it still needs to be defined

```ruby
module PostHelpers
  def stub_post(post_attrs = {})
    post_attrs = {
      to_param: '1',
      :published? => true,
      title: 'a title'
    }.update(post_attrs)
    stub('a post', post_attrs)
  end

  def stub_post!(post_attrs = {})
    returning stub_post do |post|
      Post.stubs(find: post)
    end
  end
end

describe PostsController do
  include PostHelpers

  it 'shows a published post on GET show' do
    post = stub_post!
    get :show, id: post.to_param
    Post.should have_received(:find).with(post.to_param)
    post.should have_received(:published?)
    should render_template(:show)
    should assign_to(:post).with(post)
  end
end

describe '/posts/show' do
  include PostHelpers

  it 'displays a post' do
    assigns[:post] = stub_post
    render '/posts/show'
    template.should have_tag('h1', assigns[:post].title)
  end
end
```
