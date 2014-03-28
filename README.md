# grunt-stripmq
> Mobile-first CSS Fallback

Fork of [jtangelder](https://github.com/jtangelder/grunt-stripmq), adding a switch to ignore base CSS.

**grunt-stripmq** is a Grunt task to generate a fallback version of your fancy mobile first stylesheet. Since IE8 and lower dont support media queries, you can use a javascript library like respond.js to enable this, or generate a fallback version on build-time with this task.

Here's the workflow:

1. Write Mobile-first CSS
2. Generate Desktop Fallback for IE < 9 with `grunt-stripmq`
3. Old IE Users see the desktop version of your mobile-first website
4. Profit!

## Getting Started
This plugin requires Grunt `~0.4.0`, and is available on [npmjs.org](https://npmjs.org/package/grunt-stripmq)

```shell
npm install grunt-stripmq --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```shell
grunt.loadNpmTasks('grunt-stripmq');
```

## The "stripmq" task

### Overview
In your project's Gruntfile, add a section named `stripmq` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({

    stripmq: {
        //Viewport options
        options: {
            width: 1000,
            type: 'screen'
        },
        all: {
            files: {
                //follows the pattern 'destination': ['source']
                'css/app-old-ie.css': ['css/app.css']
            }
        }
    }
});

```

### Demo

#### app.css:
Some mobile-first CSS that manipulates the background based on the viewport's width.
```css
body {
    background: url('mobile-background.png');
}

/* Change the background for tablets */
@media screen and (min-width: 640px) {
    body {
        background: url('tablet-background.png');
    }
}

/* Change the background again for desktop and increase the font-size */
@media (min-width: 900px) {
    body {
        background: url('desktop-background.png');
        font-size: 120%;
    }
}

/* If it's a monochrome screen, show a black background */
@media (monochrome) {
    body {
        background: black;
    }
}
```

#### app-old-ie.css (generated by grunt-stripmq)
Note how the media queries were removed. The `monochrome` media query did not match the default `options` that were passed in, and were therefore discarded.

```css
body {
  background: url('mobile-background.png');
}

body {
  background: url('tablet-background.png');
}

/* This will be shown to old-ie users. */
body {
  background: url('desktop-background.png');
  font-size: 120%;
}
```

#### index.html
In your `index.html`, add in conditional comments to serve `app-old-ie.css` to old IE browsers, and your mobile-first styles to modern browsers that support media queries.

````html
<!--[if lt IE 9]><link rel="stylesheet" href="app-old-ie.css"><![endif]-->
<!--[if gt IE 8]><!--><link rel="stylesheet" href="app.css"><!--<![endif]-->
````


### How it works
Here's what the `stripmq` task does under the hood:

* It parses your `source.css` files and copies over all default CSS rules to the `destination.css` file
* When it encounters a media query, it parses the media query using [css-mediaquery](https://github.com/ericf/css-mediaquery)
* It compares the parsed media query to the JavaScript object that you pass into `options` (more on this below)
  * If the comparison passes, it unwraps all the rules from the media query and adds them to `destination.css` file in the same place
  * If the comparison fails, it ignores all the rules within the media query
* It outputs the `destionation.css` file.



### Options

The `options` object is used to specify a "viewport" that you want your old IE users to see. The media queries in your mobile-first stylesheet will be compared against the properties of the `options` object.

The `options` follows the W3C Recommendations for [CSS3 Media Queries](http://www.w3.org/TR/css3-mediaqueries/) and [CSS3 Values and Units](http://www.w3.org/TR/css3-values/). It supports all of the [Media Features](http://www.w3.org/TR/css3-mediaqueries/#media1) and will properly convert values to a common unit before comparing them.

#### options.type
Type: `String`
Default value: `"screen"`

#### options.width
Type: `Integer`
Default value: `1024`

If you pass in a number, the unit is assumed to be `px`. The following units are also supported: `em`, `rem`, `cm`, `mm`, `in`, `pt`, and `pc`.

#### options['device-width']
Type: `Integer`
Default value: Defaults to `options.width` if provided, `1024` otherwise

#### options.height
Type: `Integer`
Default value: `768`

#### options['device-height']
Type: `Integer`
Default value: Defaults to `options.height` if provided, `768` otherwise

#### options.resolution
Type: `String`
Default value: `"1dppx"`

This property also supports [other resolution units](http://www.w3.org/TR/css3-mediaqueries/#resolution0).

#### options.orientation
Type: `String`
Default value: `"landscape"`

#### options['aspect-ratio']
Type: `String`
Default value: Defaults to `options.width/options.height` if both are provided, `1024/768` otherwise.

#### options['color']
Type: `Integer`
Default value: `3`

#### options.ignoreBase
Type: `Boolean`
Default value: `false`

#### options.prefixCSS
Type: `String`
Default value: `""`

#### options.prefixWithSpace
Type: `Boolean`
Default value: `true

## Contributing

In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using Grunt.
