---
layout: post
title: "Cucumber - Single column tables"
date:   2015-09-09 19:30:00
categories: [cucumber, ruby]
---

Many a times, we come across a situation, where we need to send out a list of values to a step definition.

The most common way of doing that is using a comma-seperated list.
Example:

```
Given I visit a page 
When I pass value1, value2, value 3 
Then the action must be successful
```

A more cleaner way of passing arguments will be to use Single column tables.
Re-writing the earlier step

```
	Given I visit a page
	When I pass the following values 
		| value 1 | 
		| value 2 | 
		| value 3 | 
```
Then the action must be successful

This in turn can be handled from the code as

```ruby
When /^I pass the following values$/ do |table|
    table.raw.flatten
end
```