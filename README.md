# Search-Engine

 ## Specification
The system must provide a function search, with the following specification:

def search(query, ordering = 'normal', count = 10):
  ...
It prints out the results of the search, subject to the following rules:

It selects from the set of all recipes that contain all of the words in the query (the positions/order of the words in the recipe are to be ignored).
It orders them based on the provided ordering (a string, meaning defined below).
It prints the top count matches only, preserving the order from best to worst. Must print just their title, one per line. Must be less than count if the search is specific enough that less than count recipes match.
As an aside, do not worry about memory usage. If duplicating some data can make your code faster/neater then feel free.

Data set
A file, recipes.json is provided, containing 17K recipes. It can be parsed into a Python data structure using the json module. It is a list, where each recipe is a dictionary containing various keys:

title : Name of recipe; you can assume these are unique
categories : A list of tags assigned to the recipe
ingredients : What is in it, as a list
directions : List of steps to make the recipe
rating : A rating, out of 5, of how good it is
calories : How many calories it has
protein : How much protein is in it
fat : How much fat is in it
Note that the data set was obtained via web scrapping and hence is noisy - every key except for title is missing from at least one recipe. Your code will need to cope with this.

You will probably want to explore the data before starting, so you have an idea of what your code has to deal with.

Data set came from https://www.kaggle.com/hugodarwood/epirecipes/version/2 though note it has been cleaned it up, by deleting duplicates and removing the really dodgy entries.

Search
The search should check the following parts of the recipe (see data set description below):

title
categories
ingredients
directions
For instance, given the query "banana cheese" you would expect "Banana Layer Cake with Cream Cheese Frosting" in the results. Note that case is to be ignored ("banana" matches "Banana") and the words do not have to be next to one another, in the same order as the search query or even in the same part of the recipe ("cheese" could appear in the title and "banana" in the ingredients). However, all words in the search query must appear somewhere.

Ordering
There are three ordering modes to select from, each indicated by passing a string to the search function:

normal - Based simply on the number of times the search terms appear in the recipe. A score is calculated and the order is highest to lowest. The score sums the following terms (repeated words are counted multiple times, i.e. "cheese cheese cheese" is  3  matches to "cheese"):

8×  Number of times a query word appears in the title
4×  Number of times a query word appears in the categories
2×  Number of times a query word appears in the ingredients
1×  Number of times a query word appears in the directions
The rating of the recipe (if not available assume  0 )
simple - Tries to minimise the complexity of the recipe, for someone who is in a rush. Orders to minimise the number of ingredients multiplied by the numbers of steps in the directions.

healthy - Order from lowest to highest by this cost function:
|𝚌𝚊𝚕𝚘𝚛𝚒𝚎𝚜−510𝑛|510+2|𝚙𝚛𝚘𝚝𝚎𝚒𝚗−18𝑛|18+4|𝚏𝚊𝚝−150𝑛|150
 
Where  𝑛∈ℕ+  is selected to minimise the cost ( 𝑛  is a positive integer and  𝑛=0  is not allowed). This can be understood in terms of the numbers  510 ,  18  and  150  being a third of the recommended daily intake (three meals per day) for an average person, and  𝑛  being the number of whole meals the person gets out of cooking/making the recipe. So this tries to select recipes that neatly divide into a set of meals that are the right amount to consume for a healthy, balanced diet. Try not to overthink the optimisation of  𝑛 , as it's really quite simple to do!

To clarify the use of the ordering string, to get something healthy that contains cheese you might call search('cheese', 'healthy'). In the case of a recipe that is missing a key in its dictionary the rules are different for each search mode:

normal - Consider a missing entry in the recipe (e.g. no ingredients are provided) to simply mean that entry can't match any search words (because it has none!), but the item is still eligible for inclusion in the results, assuming it can match the search with a different entry.
simple - If a recipe is missing either ingredients or directions it is dropped from such a search result. Because the data is messy if either of these lists is of length  1  it should be assumed that the list extraction has failed and the recipe is to also be dropped from the search results.
healthy - If any of calories, protein or fat is missing the recipe should be dropped from the result.
