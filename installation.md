# Installation

1. Install via composer; in console: 
   ```
   composer require reliqarts/laravel-scavenger
   ``` 
   or require in *composer.json*:
   ```json
   {
       "require": {
           "reliqarts/laravel-scavenger": "^3.1"
       }
   }
   ```
   then run `composer update` in your terminal to pull it in.
   
 2. (Optional) Publish package resources and configuration:
   
       ```bash
       php artisan vendor:publish --provider="ReliqArts\Scavenger\ServiceProvider"
       ``` 
       
       You may opt to publish only configuration by using the `scavenger-config` tag:
       
       ```bash
       php artisan vendor:publish --provider="ReliqArts\Scavenger\ServiceProvider" --tag="scavenger-config"
       ``` 
       
       or only the migrations via the `scavenger-migrations` tag:
          
       ```bash
       php artisan vendor:publish --provider="ReliqArts\Scavenger\ServiceProvider" --tag="scavenger-migrations"
       ``` 


