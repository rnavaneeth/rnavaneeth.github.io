---
layout: post
title: "[Capybara] Infinite Scroll Automation"
date:   2015-09-26 22:06:00
categories: [protip, capybara, ruby]	
---

Most modern websites have gone the way of having cleaner, uncluttered views.
One of the concepts that goes into buidling a clutter free views is the term "unpagination".

To Quote,
	> Infinite scrolling, also known as "endless scrolling," "unpagination," and others, is a technique where additional content for a web page is appended dynamically to the bottom of the page as the user approaches the end of the content. 

In terms of automation, what this means is when you are to assert a set of data, only part of it is available in the view for you to assert. The rest of them only loads when you scroll.

To achieve this, we have make use of some Javascript.
for example, consider the following html

```
	<html>
		...
		<body>
			<div class='.item'> Item 1 </div>
			<div class='.item'> Item 2 </div>
			<div class='.item'> Item 3 </div>
			<div class='.item'> Item 4 </div>
			<div class='.footer'> Footer message here </div>
		</body>
	</html>
```

In this case, only the first few items are loaded at Screen1. On reaching the end of the page, the next set loads and so on. How do u say, reach Item 50

The idea is to use scrollIntoView() method. This method can be applied to any JQuery Object. 
Most pages have a footer (Our example has one). Scroll into view on Footer will trigger the call to pull the next set of items. Now, we would not want to keep scrolling till heaven meets earth, right?
So, we bring in an end point. A point at which our code stops scrolling. The code snippet below will scroll till we reach any selector with a specific keyword.

```ruby
def scroll_until_element_exist(selector,keyword)
	
	script=%{
		function checkID(){ var allSections = $('#{selector}');
		var result = false;
		allSections.each(function(i, sec) {
			if (!result) {
				result = sec.innerHTML.contains('#{keyword}');
			}
		});
	return result;}
	return checkID();
	}

	meeting_present=false
	count=0
	while(!meeting_present&&count<=100)
		page.execute_script("$('div.footer')[0].scrollIntoView(true)")
		meeting_present=page.execute_script(script)
		count+=1
	end
end
```

To call the method

```ruby
 scroll_until_element_exist('.item',"Item50")
```
will scroll the page till we reach Item 50


**Protip**
![protip](https://github.com/rnavaneeth.github.io/public/protip-logo.jpg "Protip")
Use this method in your base page (if you follow Page Object model) and that you won't have to repeat this code anywhere else.



