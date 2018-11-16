# Render arrays in Drupal 7

I'm currently supporting a legacy Dupal 7 application, which is a new experience for me. Drupal 7 has a vast and often confusing API, and finding specific information on how various parts of it work can be a challenge.

Recently I had to implement a new page, which involves returning a render array from the page callback. This strange structure isn't actually that different a concept from render trees in, for example, ReactJS. It does take some digging, however, to figure out how to shape it to my needs. Here's what I've learned so far.

## What it looks like

Fundamentally it's just a tree of associative arrays. Each array represents a conceptual **element** (not neccesarily an HTML element - it could be a component or just text). The array entries can either be **properties** or **children**. Properties are denoted by having keys that start with the `#` character, and they affect the way the element is rendered when it's passed to [`drupal_render`][1]. All other entries are children, which are the descendents of an element.

So that's fairly straight forward, right? Well unfortunately things thence get more complex.

## `#theme` and `#type`

The first two properties it's useful to know about are `#theme` and `#type`.

The `#type` property specifies the name of a drupal element to render. These are more granular components like form controls and lists, usually for use when inserting content into another structure. These elements are more generic--they might occur in many places throughout the site--and have a predefined look and feel, so in most cases you don't actually need to add any aditional styling to them.

The `#type` property is for larger, more purpose-built components. It makes use of the theme layer of Drupal, so is slightly more involved. I hope to cover this one more in a future post.

## Markup

Here's an example of using the `markup` type:

```php
// Let's imagine this is the page callback used in a custom menu hook:
function mymodule_custom_page () {
  return array(
    '#type' => 'markdown',
    '#markdown' => "
<h1>Hello world!</h1>
<p><b>Lorem ipsum</b> dolor sit amet.</p>
    "
  );
}
```

The `markdown` type uses a custom `#markdown` property to render a string of HTML. Obviously this is a trivial use case and not really practical in the real world, but it gives you a basic introduction to the concept.

Different element types will have different properties they require. You should seek out documentation for the type you want to use. Unfortunately I can't find very good documentation on what types are available; the best so far I have found is by looking at the source of [`system_element_info`][2].

## More properties

There are other properties apart from `#theme` and `#type` that you might find useful. Here are some of them (taken from the [`drupal_render` docs][1] and [render arrays docs][3]):

 - `#cache` - Enables caching of the generated code; sholud be an array with the following keys:
   - `keys`: An array of one or more keys that identify the element. If 'keys' is set, the cache ID is created automatically from these keys. See drupal_render_cid_create().
   - `granularity`:  DRUPAL_CACHE_PER_USER to cache for each user separately or DRUPAL_CACHE_PER_PAGE | DRUPAL_CACHE_PER_ROLE to cache separately for each page and role. If not specified the element is cached globally for each theme and language.
   - `cid`: Alternative to using keys&granularity. For special caching requirements.
   - `expire`: "Set to one of the cache lifetime constants." - no explanation of what this is
   - `bin`: "Specify a cache bin to cache the element in. Defaults to 'cache'" - presumably this allows you to have multiple different caches and select which one you use?
 - `#sorted`: - `true` improves performance by disabling sorting of elements by `#weight`
 - `#printed`: - Flag set by drupal_render to prevent rendering multiple times
 - `#prefix`: - String to be added to the start of an element
 - `#sufffix`: - String to be added to the end of an element
 - `#pre_render`: - List of functions to call before render
 - `#post_render`: - Functions to call after render.
 - `#weight`: Determines the sort order of the child elements

## Feedback

I hope someone finds this document useful. These are the results of my own investigations so if I have anything incorrect or have missed anything I'd gladly recieve feedback. You can contact me on [Twitter](http://twitter.com/neonpaul) or [Mastodon](http://mastodon.me.uk/@neonpaul).




[1]: https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_render/7.x: "drupal_render documentation"
[2]: https://api.drupal.org/api/drupal/modules%21system%21system.module/function/system_element_info/7.x "Drupal system_element_info source"
[3]: https://www.drupal.org/node/930760 "Render Arrays overview on Drupal.org"
