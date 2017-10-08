# Configuration

- [Structure](#structure)
- [Target Breakdown](#target-breakdown)

Scavenger is highly configurable. These configurations remain for use the next time around. 

<a name="structure"></a>
## Structure

Below is an example of a typical config file structure, with explaining comments.

```php
<?php

return [
    // debug mode?
    'debug' => false,
    
    // whther log file should be written
    'log' => true,

    // How much detail is expected in output, 1 being the lowest, 3 being highest.
    'verbosity' => 1,

    // Set the database config
    'database' => [
        // Scraps table
        'scraps_table' => env('SCAVENGER_SCRAPS_TABLE', 'scavenger_scraps'),
    ],

    // Daemon config - used to build daemon user
    'daemon' => [ 
        // Model to use for Daemon identification and login
        'model' => 'App\\User',

        // Model property to check for daemon ID
        'id_prop' => 'email',

        // Daemon ID
        'id' => 'daemon@scavenger.reliqarts.com',

        // Any additional information required to create a user:
        // NB. this is only used when creating a daemon user, there is no "safe" way 
        // to change the daemon's password once he has been created.
        'info' => [
            'name' => 'Scavenger Daemon',
            'password' => 'pass'
        ]
    ],

    // Hashing algorithm to use
    'hash_algorithm' => 'sha512',

    // storage
    'storage' => [
        // This directory will live inside your application's storage directory.
        'dir' => env('SCAVENGER_STORAGE_DIR', 'scavenger'),
    ],

    // Different entities and mapping information
    'targets' => [
        'rooms' => [
            'model' => 'App\\Room',
            'source' => 'http://roomssite.demo.com/showads/section/rooms',
            'search' => [
                // keywords
                'keywords' => ['school'],
                // input element name for search term/keyword
                'keyword_input' => 'keyword',
                // form markup, used to locate search form
                'form_markup' => 'div.content',
                // text on submit button
                'submit_button_text' => 'Search'
            ],
            'pager' => 'div.content #page .pagingnav',
            'markup' => [
                'title' => 'div.content section > table tr h3',
                // content to be found upon clicking title link
                '_inside' => [
                    'title' => '#ad-title > h1 > a',
                    'body' => 'article .adcontent > p[align="LEFT"]:last-of-type',
                    // focus detail on the following section
                    '_focus' => 'section section > .content #ad-detail > article'
                ],
            ],
            // split single attributes into multiple based on regex
            'dissect' => [
                'body' => [
                    'email' => '(([eE]mail)*:*\s*\w+\@(\s*\w)*\.(net|com))',
                    'phone' => '((([cC]all|[[tT]el|[Pp][Hh](one)*)[:\d\-,\sDL\/]*\d)|(\d{3}\-?\d{4}))',
                    'money' => '((US)*\$[,\d\.]+[Kk]*)',
                    'beds' => '([\d]+[\d\.\/\s]*[^\w]*([Bb]edroom|b\/r|[Bb]ed)s?)',
                    'baths' => '([\d]+[\d\.\/\s]*[^\w]*([Bb]athroom|bth|[Bb]ath)s?)',
                    '_retain' => true
                ],
            ],
            // modify attributes by calling functions
            'preprocess' => [
                // takes a callable
                // optional third parameter of array if callable method needs an instance
                'title' => ['App\\Status', 'foo', true],
                'body' => 'bar'
            ],
            // remap entity attributes to model properties
            'remap' => [
                'title' => 'name',
                'body' => 'description'
            ],
            // scraps containing any of these words will be rejected
            'bad_words' => [
                'car',
                'bar',
                'land',
                'loan',
                'club',
                'shop',
                'sale',
                'store',
                'lease',
                'plaza',
                'condo',
                'seeks',
                'garage',
                'barber',
                'office',
                'company',
                'mortgage',
                'business',
                'wholesale',
                'commercial',
                'short term',
            ],
        ],
    ],

];

```

<a name="target-breakdown"></a>
### Target Breakdown

The `targets` array is to contain a list of scrapable entities keyed by a unique target identifier. The structure is as follows.

- `model`: Laravel DB model to create from target.
- `source`: Source URL to scrape.
- `search`: Search settings. Use if a search is to be performed before target data is shown. (optional)
    - `keywords`: Array of keywords to search for.
    - `keyword_input`: Keyword input text markup.
    - `form_markup`: CSS selector for search form.
    - `submit_button_text`: The text on the form's submit button.
- `pager`: Next button CSS selector. To skip to next page.
- `markup`: Array of attributes to scrape from main list. `[attributeName => CSS selector]`
    - `_inside`: Sub markup for detail page. Markeup for page which shows when article title is clicked/opened. (optional)
- `dissect`: Split compound attributes into smaller attributes via REGEX. (optional)
- `preprocess`: Array of attributes which need to be preprocessed. `[attributeName => callable]` (optional)
- `remap`: Array of attributes which need to be renamed in order to be saved as target objects. `[attributeName => newName]` (optional)
- `bad_words`: Any scraps found containing these words will be discarded. (optional)