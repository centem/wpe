<p>
This week, we'll explore the built-in data types, seeing how we can store information in them, and then extract information from them, without having to create a new class.
</p>
<p>
The idea is that we want to organize a list of places to which someone has traveled. That is: We'll ask the user to enter, one at a time, a city and country to which they have traveled. The city and country should be separated by a comma. If there is no comma, then the user is given an error message, and given another chance. If the user enters a city-country combination, then this information is recorded, and then they're asked again.  Indeed, the user is asked again and again for a city-state combination, until they provide an empty response. When that happens, the questioning phase ends, and the reporting phase begins.
</p>
<p>
In the report, we'll want to see a list of all of the places visited, organized by country. That is, we'll get a list of the visited countries, presented in alphabetical order, and for each country, we'll see a list of visited cities, also in alphabetical order.  If the city was visited more than once, then we'll see a number next to its name.
</p>

For example, this is how the interaction could look:

    Tell mewhere you went: New York, USA
    Tell me where you went: London, England
    Tell me where you went: Shanghai, China
    Tell me where you went: Chicago, USA
    Tell me where you went: Beijing, China
    Tell me where you went: Chicago, USA
    Tell me where you went: Beijing, China
    Tell me where you went: lalala
    That's not a legal city, state combination
    Tell me where you went: Boston, USA
    Tell me where you went: <user presses "enter" here>

    You visited:
    China
        Beijing (2)
        Shanghai
    England
        London
    USA
        Boston
        Chicago (2)
        New York
    
