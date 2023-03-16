# [Testing Guidelines](https://graphql-ruby.org/schema/testing.html)

* Don't test the schema and instead test the other objects and services that the schema delegates behavior to
  * Extract behavior to service classes or other objects that you can test separately that you then call in GraphQL schema definition

```ruby
# Instead of this...
class PostType
  def is_trending
    // execute some logic
  end
end

# Do this...
class Post
  def is_trending
    // executes previous logic
  end
end

class PostType
  def is_trending
    Post.is_trending
  end
end
```

* Also create end-to-end or integration tests for the schema

```ruby
RSpec.describe MySchema do
  // Override in more specific scopes
  let(:context) {}
  let(:variables) {}
  let(:result) do
    MySchema.execute(query_string, context: context, variables: variables)
  end

  describe "some query" do
    let(:query_string) { "some query" }
    
    context "when no current user" do
      it "is nil" do
        expect(result["data"]["viewer"]).to eq(nil)
      end
    end
  end
end
```
