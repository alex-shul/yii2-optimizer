# yii2-optimizer
> **Help is welcome!** 
The project is under development, so you can commit to the master branch. You can also write about errors found in "issues".

Tool for automatic assets optimization. Work with CSS &amp; JS files. This tool created as Yii2 extension. Refactoring in progress...

## Install

#### Using composer:
1) Edit file composer.json in the root directory of your yii2 app ("basic" or "advanced" folder), adding following lines:
```
    ...
    "require": {
        ...
        "alex-shul/yii2-optimizer": "*"        
    }
    ...
    "repositories": [        
        ...
        {
            "type": "git",
            "url": "https://github.com/alex-shul/yii2-optimizer.git"
        }       
    ]
    ...
```
2) Run composer command `update` in app root folder.

## Configure

1) Open config file `basic/config/web.php` or `advanced/frontend/config/main.php`.
2) Add parameters for extension to the `components` section of config. Example:
```
    'bootstrap' => [
        ...
        'optimizer'
     ],
    ...
    'components' => [
        ...
        'optimizer' => [
            'class' => 'alexshul\optimizer\Module',
            'assetsClearStyles' => false,
            'assetsClearScripts' => false,
	        'assetsAddLoader' => true,
	         'assetsMinifyLoader' => true,
	         'assetsToWatch' => [
	                'My styles bundle' => [
	                        'condition' => false,
                             'type' => 'link',
                              'files' => [
                                     'site' => [
                                         'pathDirectory' => 'assets/data/css/site/',
                                         'fileName' => 'site.css',
                                         'version' => 'v1'
                                     ],
                              ],
                              'dest' => 'assets/styles.min.css',
                              'autoload' => false
                     ],
                    'Promise fallback' => [ 
                                'condition' => 'typeof Promise !== \'function\'',          
                                'dest' => 'assets/fallbacks/promise.min.js'                                 
                    ],
                    'fetch fallback' => [ 
                                'condition' => 'typeof fetch !== \'function\'',          
                                'dest' => 'assets/fallbacks/fetch.umd.js'                                        
                    ],
                    'My scripts bundle' => [
                                'condition' => false,
                                 'type' => 'script',
                                 'files' => [
                                     'main' => [
                                         'pathDirectory' => 'assets/data/js/main/',
                                         'fileName' => 'main.js',
                                         'version' => 'v2'
                                     ],
                                     'hello' => [
                                         'pathDirectory' => 'assets/data/js/hello/',
                                         'fileName' => 'hello.js',
                                         'version' => 'v1'
                                     ],
                                 ],
                                 'dest' => 'assets/scripts.min.js'
                    ],
             ],
        ],
    ],
```

## Parameters

Extension has options:
- `assetsClearStyles` ***{bool}*** *(default: false)*

	(Not released yet) If true - clear on page all link tags with rel=stylesheet. It can be useful when all styles, that we need, added to autoloader.
- `assetsClearScripts` ***{bool}*** *(default: false)*

	(Not released yet) If true - clear on page all script tags with src attribute. It can be useful when all scripts, that we need, added to autoloader.
- `assetsAddLoader` ***{bool}*** *(default: false)*

	If true - generates and adds loader javascript to the page before closing body tag. Loader script creates queue with js & css files from option `assetsToWatch` ordered like it is in this option. When page will loaded - script will attach links to ther first css or js file from queue to the page head. When first asset will loaded or error will generated - script attach next asset from queue and so on...
	>**Note**! Extension caches loader script in `@app/runtime` folder of app. So, you must delete cached script in `@app/runtime/alex-shul/yii2-optimizer/loader.js` when you make changes in option `assetsToWatch`, that affects assets loading queue in loader script - then extension will generate new loader script.
	
- `assetsMinifyLoader` ***{bool}*** *(default: false)*

	If true - minifies loader javascript before adding it to the page.
- `assetsToWatch` ***{array}*** *(default: empty array)*

    Example directory structure:
```
     data          
     ├─── css
     │    └─── site
     │         ├─── v1
     │         │    └── site.css
     │         └─── v2
     │              └── site.css
     └─── js
          ├─── main
          │    ├─── v1
          │    │    └── main.js
          │    └─── v2
          │         └── main.js
          └─── hello
               ├─── v1
               │    └── hello.js
               └─── v2
                    └── hello.js  
```
   Array with keys = "random semantic name for your asset", and values = "array with options". Example:
```
 
         'My styles bundle' => [                // Name of your asset
                'condition' => false,               // Condition, which allows loader attach link with asset to the page head.
                 'type' => 'link',                      // Name of tag element with link to asset, which loader script will attach to the page head. 
                  'files' => [                              // Array of files for to watch and combine+minify
                        'site' => [                         // File name asset
                                'pathDirectory' => 'assets/data/css/site/',     //  Source path directory
                                'fileName' => 'site.css',           // File name asset
                                'version' => 'v1'                      // Version name
                        ],
                  ],
                  'dest' => 'assets/styles.min.css',    // (Required) Destination file for your asset.    
                  'autoload' => false                          // (Optional) If set to false - this asset will be not included to loader script.
         ],               
            
```
