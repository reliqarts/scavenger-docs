# Release Notes

- [Features](#features)
- [Glossary](#glossary)
- [Acknowledgements](#acknowledgements)


![Laravel Scavenger](./docs/images/inline-preview.png "Laravel Scavenger")

A highly flexible Laravel 5.x scraper package.


---

<a name="features"></a>
## Top Features

Scavenger provides the following features and more out of the box.

- Ease of use
    - Scavenger is super-easy to configure. Simple publish the config file and set your targets.
- Scrape data from multiple sources at once.
- Convert scraped data into useable Laravel model objects.
    - eg. You may scrape an article and have it converted into an object of your choice and saved in your database. Immediately available to your viewers.
- You can easily perform one or more operations to each property of any scraped entity.
    - eg. You may call a paraphrase service from a model or package of your choice on data attributes before saving them to your database.
- Data integrity constraints
    - Scavanger uses a hashing algorithm of your choice to maintain data integrity. This hash is used to ensure that one scrap (source article) is not converted to multiple output objects (model duplicates).
- Console Command
    - Once scavenger is configured, a simple artisan command launches the seeker. Since this is a console command it is more efficient and timeouts are less likely to occur.
    - Artisan command: `php artisan scavenger:seek`
- Schedule ready
    - Scavenger can easily be set to scrape on a schedule. Hence, creating a someone autonomous website is super easy!

<a name="glossary"></a>
## Glossary

The following words may appear in this documentation.

- `Daemon`: User instance to be used by the scavenger service.
- `Scrap`: Scraped data before being converted to the target object.
- `Target`: Configured source-model mapping for a single entity. 
- `Target Object`: Eloquent model object to be generated from scrap. 

<a name="acknowledgements"></a>
##Acknowledgements

#### Author

Patrick Reid (ReliQ) - <reliq@reliqarts.com> - <http://twitter.com/iamreliq>

#### Major Third-Party Libraries

- ##### Guzzle

    This package is heavily inspired by and dependent on the [Guzzle](https://github.com/guzzle/guzzle)
    library, although several concepts may have been adjusted.