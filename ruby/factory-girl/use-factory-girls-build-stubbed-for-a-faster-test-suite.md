# [Use Factory Girl's `build_stubbed` for a Faster Test Suite](https://robots.thoughtbot.com/use-factory-girls-build-stubbed-for-a-faster-test)

* `build_stubbed` instantiates and assigns attributes just like `build`
* Code should typically depend less on the state of the data in relation to the database, and more on its state in relation to other objects
* Not useful in every situation (like when testing uniqueness constraints or scopes)
* Imagine an `OrderProcessor` that accepts an `Order` and `CreditCard` instance
* Let's say `OrderProcessor` hits the Braintree API and returns a boolean value that indicates the success state of the charge
  * Let's also say that if when processing the order, operations occur on the `Order` and `CreditCard` instances to add errors, send emails, or track Braintree's transaction ID
* The test would look something like

```ruby
describe OrderProcessor do
  let(:transaction_id) { '1234' }
  let(:order) { build_stubbed(:order) }
  let(:credit_card) { build_stubbed(:credit_card) }

  subject { OrderProcessor.new(order, credit_card) }

  context 'when the Braintree result is valid' do
    before do
      MockBraintree.stub_successful_customer_sale(transaction_id: transaction_id)
    end

    it 'assigns the transaction id to the order' do
      subject.process
      order.transaction_id.should == transaction_id
    end
    # More success tests
  end

  # Test unsuccessful Braintree call
end
```
