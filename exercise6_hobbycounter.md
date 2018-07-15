This week, we're going to try to create some reports with Python's built-in data structures. For the purposes of this exercise, I'm going to assume that we have a list of dictionaries, in which each dict represents a person. 

That dict will then have three key-value pairs:

* name

* age

* hobbies


The final value, "hobbies", will be a list of strings describing the person's hobbies.

For example, here is a list of myself and my children:

    In [4]: people = [{'name':'Reuven', 'age':47, 'hobbies':['Python', 'cooking', 'reading']},
       ...:           {'name':'Atara', 'age':16, 'hobbies':['horses', 'cooking', 'art']},
       ...:           {'name':'Shikma', 'age':14, 'hobbies':['Python', 'piano', 'cooking']},
       ...:           {'name':'Amotz', 'age':11, 'hobbies':['biking', 'cooking']}]

You'll first want to create a list that looks like this. Note that we have a complex data structure here -- a list of dicts, in which one of the dict's values is a list.

Given this data (or data like it), I'd like you to produce the following reports:
Calculate the average age of people under 25.
Print all of the different hobbies that people in our database have.
Show how many people have each hobby.  That is, how many people are interested in Python, how many enjoy cooking, and so forth.
What are the three most common hobbies?
What are the three most common hobbies, among people who have more than two hobbies?
Hint: Nested list comprehensions and the Counter class will make this much easier!

I'll be back on Monday with a solution.

Meanwhile, here's the forum link for this week's exercise:

        https://forum.weeklypythonexercise.com/t/exercise-6-hobby-counter/219

Reuven
 
