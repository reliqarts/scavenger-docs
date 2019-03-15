# Installation

1. Download Package ZIP and extract to your "premium packages" folder or folder of your choice.

2. Update your `composer.json` to load package.

    - Add local `path` repository. See composer [doc](https://getcomposer.org/doc/05-repositories.md#loading-a-package-from-a-vcs-repository) for more info.

        eg.
        ```json
        "repositories": [
            {
                "type": "path",
                "url": "./packages/laravel-scavenger/"
            }
        ],
        ```

    - Require the package.

        ```json
        "require": {
            "reliqarts/scavenger": "^2.2"
        },
        ```

