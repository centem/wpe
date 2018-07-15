This week's exercise combines a few different things:
Using "requests" to retrieve data
Turning JSON strings into Python data structures
Turning Python data structures into CSV
Fortunately, all of this can be done with very little code, taking advantage of Python libraries -- both those that come with the language, and those that we can download from PyPI.

Let's start with downloading the data.  I'm a big fan of the "requests" package on PyPI; once you start to use it, you'll wonder why and how they still include "urllib" in Python.  Maybe people need the complexity or fine-grained control that it provides, but I still see "requests" as one of best designed packages out there.

How do I use "requests"?  After downloading and installing it with "pip", we can use it as follows in our programs:
    In [1]: import requests

    In [2]: requests.get('https://lerner.co.il')
    Out[2]: <Response [200]>

As you can see, retrieving from a URL is as easy as calling "requests.get" and passing the URL as an argument. We get back a response object, as we can see:
    In [3]: response = requests.get('https://lerner.co.il')

    In [4]: type(response)
    Out[4]: requests.models.Response

Once we get that response, we can do a number of things with it.  What's relevant to us is the content that we received, which is (not surprisingly) available in the "content" attribute.  For example:
    In [6]: type(response.content)
    Out[6]: str

    In [7]: len(response.content)
    Out[7]: 24462

For this exercise, we wanted to retrieve the list of cities. Never mind that the list of cities is in JSON format; the key thing is that we're retrieving a document with "requests", and that the content of everything in an HTTP request is returned as a string.  Thus, we can say:
    In [8]: url = 'https://gist.githubusercontent.com/reuven/77edbb0292901f35019f17edb9794358/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json'

    In [9]: response = requests.get(url)

Let's look at the first part of the response:
    In [12]: type(response.content)
    Out[12]: str

    In [13]: len(response.content)
    Out[13]: 244212

    In [14]: response.content[:100]
    Out[14]: '[\n    {\n        "city": "New York", \n        "growth_from_2000_to_2013": "4.8%", \n        "latitude"'

We can see that the content is a string, and it certainly looks like the string contains JSON.  We could use the "json" module to decode the JSON into a data structure for us, but I prefer to use the "json" method that "requests" provides for precisely this purpose.  In other words:
    In [17]: cities =response.json()

    In [18]: type(cities)
    Out[18]: list

    In [19]: cities[0]
    Out[19]:
    {u'city': u'New York',
     u'growth_from_2000_to_2013': u'4.8%',
     u'latitude': 40.7127837,
     u'longitude': -74.0059413,
     u'population': u'8405837',
     u'rank': u'1',
     u'state': u'New York'}

    In [20]: type(cities[0])
    Out[20]: dict

In other words, we clearly see that "response.json()" has returned a list of dictionaries. Each dictionary represents one city that was on the Web site from which we retrieved things.

Now we want to create a CSV file, separated by tab characters, with four of the columns (city, state, rank, and population) in the file.

One way to do this would be to use the "str.join" method. We use "str.join" all of the time, and constructing CSV files seems like a natural use for it.  However, you should avoid this temptation, not only because the "csv" module in the standard library is faster than anything you're likely to write, but because it also handles many of the corner cases that you are unlikely to have considered.

The "csv" module is actually fairly straightforward to use: If we're looking to write to a CSV file, then we can use the "csv.writer" class, which we can create with a working file object:
    In [26]: import csv

    In [29]: with open('cities.csv', 'w') as outfile:
        ...:     w = csv.writer(outfile)
        ...:     w.writerow(['abc', 'def', 'ghijk'])
        ...:     w.writerow(['w', 'xyz', '!@#$'])
        ...:     w.writerow(['123', '4', '5678'])
        ...:
        ...:

    In [30]: %cat cities.csv
    abc,def,ghijk
    w,xyz,!@#$
    123,4,5678

While we don't have to use "with" in the above code, it guarantees that our file is closed, and that its contents have thus been flushed to disk.

Given our list of dictionaries, maybe we can write them all to disk using "csv.writerow".  We can also specify that we want to separate our fields with tabs, rather than with commas, as follows:
    In [31]: with open('cities.csv', 'w') as outfile:
        ...:     w = csv.writer(outfile, delimiter='\t')
        ...:     for one_city in cities:
        ...:         w.writerow(one_city)

Seems good, except:
    ---------------------------------------------------------------------------
    Error                                     Traceback (most recent call last)
    <ipython-input-31-d0fad866b40b> in <module>()
          2     w = csv.writer(outfile, delimiter='\t')
          3     for one_city in cities:
    ----> 4         w.writerow(one_city)
          5

    Error: sequence expected

It turns out that "csv.writerow" only works if you give it a sequence -- which means, in the Python world, a string, list, or tuple. This means that a dictionary won't work.

However, if we can transform our list of dicts into a list of tuples, then we'll be just fine. And whenever we think about transforming a sequence into another sequence, we should aim to then use a comprehension -- in this case, a list comprehension.

Think about it: We have a list of dicts, and we want to grab four elements from that dicts. (Too bad we don't have a list of SlicedDicts from last week, right?) We can set up a list comprehension to grab the four fields that are of interest to us.  Let's start with a simple list comprehension that returns the dict that we already have:
    In [34]: [one_city
        ...: for one_city in cities]
    
Now let's change the expression such that it produces a tuple of the four fields we want:
    In [35]: [(one_city['city'], one_city['state'], one_city['rank'], one_city['population'])
        ...: for one_city in cities]
    
Sure enough, we now get a list of tuples:
    [(u'New York', u'New York', u'1', u'8405837'),
     (u'Los Angeles', u'California', u'2', u'3884307'),
     (u'Chicago', u'Illinois', u'3', u'2718782'),
     (u'Houston', u'Texas', u'4', u'2195914'),
     (u'Philadelphia', u'Pennsylvania', u'5', u'1553165'),

CSV files are all about text; if we were going to use the data in any serious numeric way before writing it to CSV, I'd probably want to use int() to turn the numeric fields into actual integers.  But we can ignore that for now.

Instead of iterating over "cities" in our loop, we can now iterate over the result of our list comprehension:
    In [36]: city_tuples = [(one_city['city'], one_city['state'],
                             one_city['rank'], one_city['population'])
        ...: for one_city in cities]

    In [37]: with open('cities.csv', 'w') as outfile:
        ...:     w = csv.writer(outfile, delimiter='\t')
        ...:     for one_city in city_tuples:
        ...:         w.writerow(one_city)
    
Great news; this seems to work!

But I think that we can do better.

For example, there's a method "writerows" that takes a list of sequences, thus allowing us to avoid the "for" loop. So:
    In [38]: with open('cities.csv', 'w') as outfile:
        ...:     w = csv.writer(outfile, delimiter='\t')
        ...:     w.writerows(city_tuples)

You could make a good argument that we're done at this point; the program works, and didn't require much code. I've enclosed the solution as solution.py.

But if you think about it, doesn't it seem weird that the "csv" module doesn't know how to work with dicts? I mean, is this the any time that Python developers would ever have a list of dicts that they want to write as a CSV file?  

It turns out that the "csv" module includes not just "csv.writer", but also "csv.DictWriter".  As the name implies, it allows us to take a sequence of dicts and write them to a CSV file.

You would think, from a quick read of the documentation, that we could do something like this:
    with open('cities.csv', 'w') as outfile:
        output = csv.DictWriter(outfile,
                                ['city', 'state', 'rank', 'population'],
                                delimiter='\t')

        output.writerows(requests.get(url).json())

First, we create our instance of "csv.DictWriter", indicating the file object to which we want to write, the keys from the dict we want to store, and the delimiter.  However, this fails, because "csv.DictWriter" assumes that you want all of the dictionary's keys to be stored. If you are willing to ignore this error -- and why it's considered an error is somewhat beyond me -- then you need to define the "extrasaction" parameter, giving it a value of "ignore".  This tells "csv.DictWriter" to ignore any additional keys.

This means that our entire program, from start to finish, can be:
    #!/usr/bin/env python

    import requests
    import csv

    url = 'https://gist.githubusercontent.com/reuven/77edbb0292901f35019f17edb9794358/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json'

    with open('cities.csv', 'w') as outfile:
        output = csv.DictWriter(outfile,
                                ['city', 'state', 'rank', 'population'],
                                delimiter='\t',
                                extrasaction='ignore')

        output.writerows(requests.get(url).json())

Not bad for a few lines of code, I'd say!

Questions or comments?  Discuss them here:

    https://forum.weeklypythonexercise.com/t/exercise-5-big-cities/217

And please, as always, provide your feedback (which should now allow you to answer multiple times):

        https://www.surveymonkey.com/r/wpe-jan2018

Until next time,

Reuven



#!/usr/bin/env python

import requests
import csv

url = 'https://gist.githubusercontent.com/reuven/77edbb0292901f35019f17edb9794358/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json'

with open('cities.csv', 'w') as outfile:
    output = csv.writer(outfile, delimiter='\t')

    cities = [(d['city'], d['state'], d['rank'], d['population'])
              for d in requests.get(url).json()]

    output.writerows(cities)
 
