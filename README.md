# Cosmos SQL API demo

## Prerequisites:

Create a CosmosDB Account of type SQL API and database/collection named Tweets
Insert the data inside the folder Tweets to CosmosDB using the Data Migration Tool


 ## Query 1 : Select All

Type this query into the query editor:

```SQL
SELECT *
FROM tweets
```

...and click on **Run It!** to get all tweets.


 ## Query 2 : Filter Path

When referring to fields you must use the alias you define in the `FROM` clause. We have to provide the full "path" to the properties of the objects within the database.


```SQL
SELECT tweets.RetweetCount
FROM tweets
```

 ## Query 3 : Join

Lets see how we can find out the hashtags that have been used in all the tweets. We can use the `JOIN` keyword to join to our hashtags array in each tweet. We can also give it an alias and inspect its properties.

Let's see the `JOIN` in action. Try this query:

```SQL
SELECT hashtags
FROM tweets
JOIN hashtags IN tweets.Hashtags
```

Now that we know how to join to our child array we can use it for filtering. Lets find all other hashtags that have been used along with the known hashtags (#Azure, #CosmosDB):

```SQL
SELECT hashtags
FROM tweets
JOIN hashtags IN tweets.Hashtags
WHERE hashtags.text NOT IN ("CosmosDB", "Azure")
```

Similarly we can use OR,IN,DISTINCT,GROUPBY etc.


 ## Query 4 : KEYWORDS (IN,OR etc)

To return the entire tweet where the indices of the hashtag is between 11 and 18 simply by selecting the tweets rather than the indices

```SQL
SELECT tweets
FROM tweets
JOIN hashtags IN tweets.Hashtags
JOIN indices IN hashtags.indices
WHERE indices BETWEEN 11 AND 18
```

## Query 5 : ORDERBY and TOP

We can also order our query so we can find the most recent tweet(s) and retrieve the top 5 tweets. (use ASC for ascending and DESC for Descending) :

```SQL
SELECT TOP 5 tweets
FROM tweets
JOIN hashtags IN tweets.Hashtags
JOIN indices IN hashtags.indices
WHERE indices BETWEEN 21 AND 28
ORDER BY tweets
```

## Query 6 : PROJECTION

We can use a feature called **Projection** to create an entirely new result set. We could use this to create a common structure or to make it match a structure we already have.

Try this query:

```SQL
SELECT tweets.CreatedBy.Name AS Name,
		tweets.FullText AS Text,
		tweets.CreatedAt AS CreatedTime,
        tweets.TweetDTO.metadata.iso_language_code AS LanguageCode
FROM tweets
```

## Query 7 : USER DEFINED FUNCTION

The SQL API supports javascript User defined functions, there that you can use on this server called displayDate which removes the time parts of a UTC date string.

This is the function :

```javascript
function displayDate(inputDate) {
    return inputDate.split('T')[0];
}
```

Let's have a go at using it

```SQL
SELECT tweets.CreatedAt,
    udf.displayDate(tweets.CreatedAt) AS FormattedDate
FROM tweets
```

The SQL API also supports stored procedures written in javascript which enables you to perform ACID transactions over multiple records. This allows scalable and almost unlimited expandability on the functionality Azure Cosmos DB can offer.

### Further References

[Get Started with Azure Cosmos DB - http://aka.ms/cosmosdb](http://aka.ms/cosmosdb)

[DocumentDB SQL Query Syntax](https://azure.microsoft.com/en-us/documentation/articles/documentdb-sql-query)

[DocumentDB SQL Query Playground](https://www.documentdb.com/sql/demo)