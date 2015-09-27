---
layout: post
title: "Data Transformation between different Codebases"
date:   2015-09-27 21:48:00
categories: [pro, ruby, .net, json]	
---

I came across an interesting situation. My application under test requires to send out emails/ICS and I had to write automation tests to ensure the emails/ICS reach the recipient.

Abiding by the "Every test is Independent" rule, data is seeded as part of every test (cucumber scenarios) and destroyed after the test is run. And I also require my framework to re-validate the response from the Inbox back into the application.

To solve this situation, I made use of the Hooks in cucumber and the almightly "JSON".

### How?

Lets take an example data of a Order Summary for an ecommerce website. Considering that as our AUT (application under test), we will then know that an order summary will have details like 
	
- Product Details   
	- Product Name  
	- Product Quanitity  
	- Product Cost 
- Sender Details  
- Recipient Details  

There can be multiple products under Product Details. The email is sent with these details to the buyer needs to be asserted. As a best practice,
	
> Always create a class to store your more frequent datasets. For an ecommerce site, Order is the most frequent dataset and when automating, this data set can be used to pass along data across context.

So, a class Order is created as 

```ruby
class Order
	attr_accessor(:order_id, :purchased_date,:recipient,:order_status,:delivery_address,:products)
	
	def initialize
     self.products = [] # on object creation initialize this to an array
	end

	def to_hash
    	hash = {}
    	instance_variables.each {|var| hash[var.to_s.delete("@")] = instance_variable_get(var) }
    	hash
	end
end

class Product
	attr_accessor(:product_id, :product_name, :quantity, :cost, :seller)
end
```

Do note that I have created a method *to_hash* in Order class.
This method will convert all instance variables into a hash with Keys as the attribute names.

#### Cucumber Hooks
The *Before* and *After* Hooks in cucumber gets called right before and after every scenario.
Add a hook say *@hold_on* to those scenarios where you do not want to clear the data after scenario completion.

The After hook will have a code to move this order from deletion list (the teardown) to a save list

At the end of all scenarios (at_exit), move all these orders to a list.

```ruby	
def at_exit
	@saved_orders << current_order
	orders_json = @saved_orders.each{|order| order.to_hash}.to_json
	f=File.new("orders.json","w+")
	f.write(order_json).close
end
```
The orders are now hashed and converted into a JSON and saved.

Now, you can have a different code base, read this JSON and convert it to an Order object at the other end (best practice to manipulate data)


