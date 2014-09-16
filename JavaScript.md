# Performance

Writing performant code is absolutely critical. Poorly written JavaScript can significantly slow down and even crash the browser. Performance at the browser level is a major part of user experience which is part of our mission statement.

##Only Load Libraries You Need

JavaScript libraries should only be loaded on the page when needed. jquery-1.11.1.min.js is 96 KB. This isn’t a huge deal on desktop but can add up quickly on mobile when we start adding a bunch of libraries.

## Use jQuery Wisely

jQuery is a JavaScript framework that allows us easily accomplish complex tasks such as AJAX and animations. jQuery is great for certain tasks but overkill for others. For example, let’s say we want to hide an element:

```javascript
document.getElementById( ‘element’ ).style.display = ‘none’;
```

vs.

```javascript
jQuery( ‘#element’ ).hide();
```

The jQuery method is [~98% slower](http://jsperf.com/selecting-and-hiding-element-with-without-jquery) than non-jQuery.

Much of the time we do need jQuery (or something like it). It’s important that we not depend on it to accomplish simple tasks like the one above. Even if we are using jQuery for something else like sending an AJAX call, we can still use non-jQuery methods in other areas of the same script.

## Cache DOM Selections

It’s a common JavaScript request to reselect something unnecessarily. For example, every time a menu button is clicked, we do not need to reselect the menu. Rather we select the menu once and cache it’s selector. This applies whether you are using jQuery or not. For example:

non-jQuery Uncached:

```javascript
var hideButton = document.getElementByClassName( ‘hide-button’ )[0];
hideButton.onclick = function() {
    var menu = document.getElementById( ‘menu’ );
    menu.style.display = ‘none’;
}
```

non-jQuery Cached:

```javascript
var menu = document.getElementById( ‘menu’ );
var hideButton = document.getElementByClassName( ‘hide-button’ )[0];
hideButton.onclick = function() {
    menu.style.display = ‘none’;
}
```

jQuery Uncached:

```javascript
var $hideButton = jQuery( ‘.hide-button’ );
$hideButton.on( ‘click’, function() {
    var $menu = jQuery( ‘#menu’ );
    $menu.hide();
});
```

jQuery Cached:

```javascript
var $menu = jQuery( ‘#menu’ );
var $hideButton = jQuery( ‘.hide-button’ );
$hideButton.on( ‘click’, function() {
	$menu.hide();
});
```
Notice how in cached version we are pulling the menu selection out of the event handler so it only happens once.

## Event Delegation:

Event delegation is the act of adding one event listener to a parent node to listen for events bubbling up from children. This is much more performant than adding one event listener for each child element. Here is an example:

Without jQuery:

```javascript
document.getElementById( ‘menu’ ).addEventListener( ‘click’, function( event ) {
    if( event.target && event.target.nodeName === ‘LI’ ) {
        // Do stuff!
    }
});
```

With jQuery:

```javascript
jQuery( ‘#menu’ ).on( ‘click’, ‘li’, function() {
    // Do stuff!
});
```

# Code Style

We conform to [WordPress JavaScript coding standards](http://make.wordpress.org/core/handbook/coding-standards/javascript/).

# Unit and Integration Testing

At 10up, we generally employ unit and/or integration tests only when building applications that are meant to be distributed. Building tests for client themes does usually not offer a huge amount of value (there are of course exceptions to this). When we do write tests, we use [QUnit](http://qunitjs.com) which is a WordPress standard.

# Libraries

There are many JavaScript libraries available today. Many of them directly compete with each other. We try to stay consistent with what WordPress uses. The following is a list of libraries whitelisted for use at 10up. If you need to use a library outside of this list, it’s worth getting a second opinion.

## DOM Manipulation

[jQuery](http://jquery.com/) - Our and WordPress’s library of choice for DOM manipulation. You should not need any other DOM manipulation library such as Prototype or MooTools.

## Utility:

[Underscores](http://underscorejs.org) - Provides a number of useful utility functions such as ```clone()```, ```each()```, and ```extend()```. WordPress core uses this library quite a bit.

## Frameworks:

[Backbone](http://backbonejs.org) - Provides a framework for building complex JavaScript applications. Backbone is based on the usage of models, views, and collections. WordPress core relies heavily on Backbone especially in the media library. Backbone requires underscores.js and a DOM manipulation library (jQuery)
