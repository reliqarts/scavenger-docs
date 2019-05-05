# Configuration

- [Structure](#structure)
- [Target Breakdown](#target-breakdown)

<a name="structure"></a>
## Structure

Scavenger is highly configurable. Below is an example of a typical config file structure, with explaining comments.

```php
<?php

return [
    // debug mode?
    'debug' => false,

    // whether log file should be written
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
            'password' => 'pass',
        ],
    ],

    // guzzle settings
    'guzzle_settings' => [
        'timeout' => 60,
    ],

    // hashing algorithm to use
    'hash_algorithm' => 'sha512',

    // storage
    'storage' => [
        // This directory will live inside your application's log directory.
        'log_dir' => env('SCAVENGER_LOG_DIR', 'scavenger'),
    ],

    // different model entities and mapping information
    'targets' => [
        // NB. the "rooms" target shown below is for example purposes only. It has all posible keys explicitly.
        'rooms' => [
            'example' => true,
            'serp' => false,
            'model' => 'App\\Room',
            'source' => 'http://myroomslistingsite.1demo/section/rooms',
            'search' => [
                // keywords
                'keywords' => ['professional'],
                // form markup
                'form' => [
                    // search form selector (important)
                    'selector' => '#form',
                    // input element name for search term/keyword
                    'keyword_input_name' => 'keyword',
                    'submit_button' => [
                        // text on submit button (optional)
                        'text' => null,
                        // submit element id, use if button doesn't have text (optional)
                        'id' => null,
                    ],
                ],
            ],
            'pager' => [
                // link (a tag) selector
                'selector' => 'div.content #page a.pagingnav',
            ],
            // max. number of pages to scrape (0 is unlimited)
            'pages' => 0,
            // content markup: actual data to be scraped
            'markup' => [
                'title' => 'div.content section > table tr h3',
                // inside: content to be found upon clicking title link
                '__inside' => [
                    'title' => '#ad-title > h1 > a',
                    'body' => 'article .adcontent > p[align="LEFT"]:last-of-type',
                    // focus: focus detail on the following section
                    '__focus' => 'section section > .content #ad-detail > article',
                ],
                // wrapper/item/result: wrapping selector for each item on single page.
                // If inside special key is set this key becomes invalid (i.e. inside takes preference)
                '__result' => null,
            ],
            // split single attributes into multiple based on regex
            'dissect' => [
                'body' => [
                    'email' => '(([eE]mail)*:*\s*\w+\@(\s*\w)*\.(net|com))',
                    'phone' => '((([cC]all|[[tT]el|[Pp][Hh](one)*)[:\d\-,\sDL\/]*\d)|(\d{3}\-?\d{4}))',
                    'beds' => '([\d]+[\d\.\/\s]*[^\w]*([Bb]edroom|b\/r|[Bb]ed)s?)',
                    'baths' => '([\d]+[\d\.\/\s]*[^\w]*([Bb]athroom|bth|[Bb]ath)s?)',
                    // retain:  whether details should be left in source attribute after extraction
                    '__retain' => true,
                ],
            ],
            // modify attributes by calling functions
            'preprocess' => [
                // takes a callable
                // optional third parameter of array if callable method needs an instance
                // e.g. ['App\\Item', 'foo', true] or 'bar'
                'title' => null,
            ],
            // remap entity attributes to model properties (optional)
            'remap' => [
                'title' => null,
                'body' => null,
            ],
            // scraps containing any of these words will be rejected (optional)
            'bad_words' => [
                'office',
            ],
        ],

        // Google SERP example:
        'google' => [
            'example' => true,
            'serp' => true,
            'model' => 'App\\GoogleResult',
            'source' => 'https://www.google.com',
            'search' => [
                'keywords' => ['dog'],
                'form' => [
                    'selector' => 'form[name="f"]',
                    'keyword_input_name' => 'q',
                ],
            ],
            'pages' => 2,
            'pager' => [
                'selector' => '#foot > table > tr > td.b:last-child a',
            ],
            'markup' => [
                '__result' => 'div.g',
                'title' => 'h3 > a',
                'description' => '.st',
                // the 'link' and 'position' attributes make use of some of Scavengers available properties
                'link' => '__link',
                'position' => '__position',
            ],
        ],

        // Bing SERP example:
        'bing' => [
            'example' => true,
            'serp' => true,
            'model' => 'App\\BingResult',
            'source' => 'https://www.bing.com',
            'search' => [
                'keywords' => ['dog'],
                'form' => [
                    'selector' => 'form#sb_form',
                    'keyword_input_name' => 'q',
                ],
            ],
            'pages' => 3,
            'pager' => [
                'selector' => '.sb_pagN',
            ],
            'markup' => [
                '__result' => '.b_algo',
                'title' => 'h2 a',
                'description' => '.b_caption p',
                'link' => '__link',
                'position' => '__position',
            ],
        ],
    ],
];

```

<a name="target-breakdown"></a>
### Target Breakdown

The `targets` array is to contain a list of scrap-able entities keyed by a unique target identifier. The structure is as follows.

- `example`: Whether target is an example. Examples are skipped. `[true or false]` (optional)
- `serp`: Whether target is for SERP scraping. This should be used in conjunction with the `__result` key in the `markup` definition. `[true or false]` (optional)
- `model`: Laravel Eloquent model to create from target.
- `source`: Source URL to scrape.
- `search`: Search settings. Use if a search is to be performed before target data is shown. (optional)
    - `keywords`: Array of keywords to search for.
    - `keyword_input`: Keyword input text markup.
    - `form_markup`: CSS selector for search form.
    - `submit_button_text`: The text on the form's submit button.
- `pages`: Max. number of pages to scrape (0 is unlimited)
- `pager`: Next button CSS selector. To skip to next page.
- `markup`: Array of attributes to scrape from main list. `[attributeName => CSS selector]`
    - `__inside`: Sub markup for detail page. Markeup for page which shows when article title is clicked/opened. (optional)
        - `__focus`: Focus detail within a specific section of the page. This is useful if all CSS selectors to be scraped are within a single area. (i.e. focus area)
    - `__result`|`__wrapper`|`__item`: Wrapping selector for each item on single page. If `__inside` special key is set this key becomes invalid (i.e. `__inside` takes preference)
- `dissect`: Split compound attributes into smaller attributes via REGEX. (optional)
- `preprocess`: Array of attributes which need to be preprocessed. `[attributeName => callable]` (optional)
- `remap`: Array of attributes which need to be renamed in order to be saved as target objects. `[attributeName => newName]` (optional)
- `bad_words`: Any scraps found containing these words will be discarded. (optional)
