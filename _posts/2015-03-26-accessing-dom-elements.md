---
layout: post
title: "Accessing DOM elements in a HTML 5, CSS 3 setup"
date:   2015-03-26 19:30:00
categories: jekyll update
---
In the recent times, to bring about a Fluid UI, CSS3 and Html 5 are being used prominently these days. Some of the practices is having a colorful html element which will not have an action, but will be bound to a regular html element like a checkbox. The user will only see this fancy element and checkbox stays hidden.
Now coming to automating this: Capybara for all the right purposes, does not allow actions on hidden elements. And not always will the hidden element be accessible. So what is the next best thing? Using 
Javascript.

So, Capybara will use an implementation of a JS friendly back-end (selenium web-driver, phantomjs, etc). Use the execute script functionality to do achieve this.
Example.

Given our element looks something like this.

```
<div class="toggle-switch"><input id="all_notifications_enabled" type="checkbox" checked="checked"><label for="all_notifications_enabled"></label></div>
```

The label element is the UI part and the input element contains the action, and yes, the input element is hidden. 

```ruby
page.execute_script("document.querySelector('[id^=#{id}]').checked=true")
```

where execute_script is an extension of selenium-webdriver’s driver.execute_script method.