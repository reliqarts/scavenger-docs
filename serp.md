# SERP

- [Preface](#preface)
- [Prerequisites](#prereq)
- [Scavenger Config](#config)
- [Execution](#execution)

<a name="preface"></a>
## Preface

As of version 2.0, Scavenger can be used to scrape search engine result pages (or SERP). This is a useful practice in the world of Search Engine Optimization (SEO). This section demonstrates how to use Scavenger for SERP.

---

<a name="prereq"></a>
## Prerequisites

Firstly, you must create eloquent model(s) to support your intended targets. In this case we have created the model, `GoogleResult`, with the following migration and model class:

### Migration: 

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateGoogleResultsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('google_results', function (Blueprint $table) {
            $table->increments('id');
            $table->text('link');
            $table->text('description');
            $table->integer('position')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('google_results');
    }
}

```

### Model class:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class GoogleResult extends Model
{
    //
}
```

---

<a name="config"></a>
## Scavenger Configuration

The following configuration would be used to scrape Google SERP. This example can be used straight out-the-box, *as at Feb. 20, 2018*.

```php
<?php

return [
    'debug' => false,
    'log' => true,
    'verbosity' => 1,
    'database' => [
        'scraps_table' => env('SCAVENGER_SCRAPS_TABLE', 'scavenger_scraps'),
    ],
    'daemon' => [ 
        'model' => 'App\\User',
        'id_prop' => 'email',
        'id' => 'daemon@scavenger.reliqarts.com',
        'info' => [
            'name' => 'Scavenger Daemon',
            'password' => 'pass'
        ]
    ],
    'hash_algorithm' => 'sha512',
    'storage' => [
        'dir' => env('SCAVENGER_STORAGE_DIR', 'scavenger'),
    ],

    'targets' => [
        // Google SERP:
        'google' => [
            'example' => false,
            'serp' => true,
            'model' => 'App\\GoogleResult',
            'source' => 'https://www.google.com',
            'search' => [
                'keywords' => ['dog'],
                'form' => [
                    'selector' => 'form[name="f"]',
                    'keyword_input_name' => 'q',
                ]
            ],
            'pages' => 2,
            'pager' => [
                'selector' => '#foot > table > tr > td.b:last-child',
                'text' => 'Next',
            ],
            'markup' => [
                '__result' => 'div.g',
                'title' => 'h3 > a',
                'description' => '.st',
                'link' => '__link',
                'position' => '__position',
            ],
        ],
    ],
];

```

<a name="execution"></a>
## Execution

<iframe width="700" height="395" src="https://www.youtube.com/embed/Kp2v00UGCd4" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
