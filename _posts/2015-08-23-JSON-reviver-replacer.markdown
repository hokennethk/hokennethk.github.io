---
title:  "JSON Reviver and Replacer"
date:   2015-08-23 23:14:00
description: Revive values while parsing JSON or replace values while stringifying JSON
---

If you're familiar with JSON and javascript, then you're probably familiar with the functions JSON.stringify() and JSON.parse(). A quick description: JSON.stringify takes a Javascript value and converts it into a string in JSON format, and JSON.parse takes a JSON string and parses it as JSON.

### JSON.parse reviver
Aside from the JSON string, JSON.parse also takes an optional parameter known as the reviver. This reviver function allows you to transform a value before it is returned.

```javascript
JSON.parse(string [,reviverFn]);
```

#### Use Case for reviver
When it comes to dates, Javascript has a Date constructor that is used to make date instances that represent a moment in time. These instances have access to useful methods, including getting its current hours, minutes, etc.

{% highlight javascript %}
var now = new Date();
now.getHours();  // 10
now.getMinutes();  // 26
now.toISOString();  //  "2015-08-20T17:26:16.062Z"
now.toString();  // "Thu Aug 20 2015 10:26:16 GMT-0700 (PDT)"
{% endhighlight %}

JSON is good for storing information and data. The JSON format does not store things like functions or methods, so if you decided to stringify your date object, you'd be left with a string representation of that date. In my experience, you get an ISO string representation:

{% highlight javascript %}
var jsondate = JSON.stringify(now) // "2015-08-20T17:26:16.062Z"
{% endhighlight %}

You can store this JSON however you'd like, or pass it as part of an HTTP request/response.

But what happens when you parse it? Unfortunately, you'd only get the ISO string again. We've lost all date methods for this date.

{% highlight javascript %}
var parsedDate = JSON.parse(jsondate)  // "2015-08-20T17:26:16.062Z"
parsedDate.getTime(); // Uncaught TypeError: parsedDate.getTime is not a function
{% endhighlight %}

Fortunately, we know about the JSON parse reviver, which allows us to transform the parsed values before returning. The reviver takes two arguments: a key parameter and a value parameter. We can look at the "value" of our parsed JSON and determine if its an ISO string with a Regex. If it is, then we can return a new date object using this ISO string date, and if not, just return the value unchanged.

{% highlight javascript %}
parsedDate = JSON.parse(jsondate, function(key, value) {
  // define pattern to match
  var re = /\d{4}-[01]\d-[0-3]\dT[0-2]\d:[0-5]\d:[0-5]\d\.\d+([+-][0-2]\d:[0-5]\d|Z)/;
  if (typeof value === 'string' && value.match(re)) {
    return new Date(value);
  } else {
    return value; // unchanged
  }
});

parsedDate instanceof Date; // true
{% endhighlight %}

And voila, your stringified Date has been revived into a date object!

But wait, that regex looks complicated! Read on for an alternative solution.

###JSON.stringify replacer
JSON.stringify also takes an optional parameter, known as the replacer function. This replacer function allows you to override the default JSON stringification. If your replacer function returns a number, string, or boolean, then that value is used as the value for the property during stringification. If you return an object or array, then that object/array is recursively stringified. If you return undefined, then that value is not included in the stringification.

```
JSON.stringify(value [,replacerFn]);
```

#### Use Case for replacer
When we stringify our date object, instead of letting JSON.stringify perform its default behavior (converting the date to an ISO string), we can define our own stringification method to avoid using a complicated regex during parsing/reviving. In this example, we'll store the date as the number of milliseconds since epoch time, which we can get with Date.prototype.getTime(). We can define our replacer like so:

{% highlight javascript %}
var dateToJSON = function(key, val) {
  if (this[key] instanceof Date) {
    return this[key].getTime();
  } else {
    return val; // returns value unchanged if not a date
  }
};
{% endhighlight %}

Now, when we stringify, we can pass in the replacer.

{% highlight javascript %}
jsondate = JSON.stringify(now, dateToJSON) // "1440091576062"
{% endhighlight %}

We have successfully stringified our object to a JSON string, but the date is represented in milliseconds since epoch time instead of the default ISO string.

Now, when we parse and want to revive our date object, we can use a much simpler matching pattern. Assuming any stringified number that is 13 characters/digits long is a date, we can use the following reviver:

{% highlight javascript %}
parsedDate = JSON.parse(jsondate, function(key, value) {
  if (typeof value === 'number' && value.toString().match(/[\d]{13}/)) {
    return new Date(value);
  } else {
    return value; // unchanged
  }
});

parsedDate instanceof Date; // true
parsedDate.toString(); // "Thu Aug 20 2015 10:26:16 GMT-0700 (PDT)"
{% endhighlight %}
