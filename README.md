
# FTE Technical Standards

The writing below supplements the best practices already in place at our partner organizations. The following documents should be read first, as we have made efforts to eliminate any redundancy between them and this document. 
- [10up Engineering Best Practices](https://10up.github.io/Engineering-Best-Practices)
- [WordPress VIP Best Practices](https://vip.wordpress.com/documentation/best-practices/)
- [WordPress Core Coding Standards](https://make.wordpress.org/core/handbook/best-practices/coding-standards/php/)

Some recommendations here are broad, others are very specific. Some are goals for the redesign, others should go into effect right away. There's a lot missing and a number of sections need to be fleshed out due to my lack of knowledge.

## Browser support

We support any broswer with greater than 2% usage in the United States according to [caniuse.com](http://caniuse.com/). As of November 2015, that includes:

Browser | Versions
------- | --------
Internet Explorer | 11
Chrome | 46, 45
Firefox | 41
iOS Safari | 9.1
Opera Mini | 5.0-8.0
Android Browser | 4.4
Chrome for Android | 46

This means we do not support, among other browsers:

Browser | Versions
------- | --------
Internet Explorer | 8, 9, 10
Internet Edge | 12, 13
Safari | 8, 9
Android Browser | 4.3-4.4, 4.2-4.3

We should design with progressive enhancement in mind and support for many of these browsers (IE10, Edge, Safari) will come for free given their implementation of modern web standards. But in terms of what we test and what prevents us from shipping, the first list serves as our QA checklist.

## Performance

- Site should use a single stylesheet for styles common across the site and then page-specific stylesheets so that the user can cache the shared css
- Ads, social sharing and any other third-party widgets should be loaded at the end of the body, asynchronously
- @font-face stack will use .woff2 and then .woff, in that order
- Ideally we should load four or less font files per page. Max is six.
- FOUT is preferable to blocking fonts, though design should aim to ensure that the fallback fonts are as similiar in size to the replacement in order to prevent page jumps on @font-face load
- Site will implement responsive images solution, ideally using srcset and srcset polyfill
- Site will take advantage of prefetching in Chrome
- HTTP/2 will be adopted as soon as our server supports it
- Build process will use UnCSS or similar tech to prune unused styles
- Assets will be gzipped, concatenated and minified (not in that order)
- woff and woff2 font files should not be gzipped
-  In content headers, set `Expires` to a date up to one year in the future (one year is good unless we think the asset will change), set `Last-Modified` to the date the asset was last modified
- Implement monthly performance report showing median and 95th percentile load time for the month (available in GA), a time series to contextualize those numbers, and specific pages and changes that celebrate performance wins
- To hide images at certain breakpoints, target the parent of the `<img>` or `background-image` with `display: none;`, not the element itself
- Performance budget should be agreed upon and then built into the build process. Before the full redesign, the following should be achieved:

Metric      | Value  | Method           | Details                       | Breakpoints | Pages
----------- | ------ | ---------------- | ----------------------------- | ----------- | -----
Page load   | 3 sec. | WebPageTest      | Median from five runs on 3G   | All         | All
Page load   | 3 sec. | Google Analytics | Median across geographies     | All         | All 
Page weight | 1.5MB  | WebPageTest      |                               | Desktop     | All
Page weight | 1MB    | WebPageTest      |                               | Mobile      | All
Speed Index | 3000   | WebPageTest      | Use Dulles location on Chrome | All         | All

- After redesign, the following should be achieved:

Metric      | Value  | Method           | Details                       | Breakpoints | Pages
----------- | ------ | ---------------- | ----------------------------- | ----------- | -----
Page load   | 2 sec. | WebPageTest      | Median from five runs on 3G   | All         | All
Page load   | 2 sec. | Google Analytics | Median across geographies     | All         | All 
Page weight | 800kb  | WebPageTest      |                               | Desktop     | All
Page weight | 500kb  | WebPageTest      |                               | Mobile      | All
Speed Index | 2000   | WebPageTest      | Use Dulles location on Chrome | All         | All

## Accessibility

- Users should be able to tab to navigate between links.
- Users should be able to activate a link by pressing ‘Enter’ on their keyboard.
- Users should be able to identify links without relying on color or hover alone.
- Users should be able to activate hover and and focus states with both a mouse and a keyboard.
- ARIA roles should be used throughout the site
- All form elements should have an associated label
- Do not use divs for form elements
- Icons elements should have descriptive text within that is hidden via CSS
- We should enable keyboard users to skip global nav with a well placed `tab-index='1'`
- Add a title attribute to iframes and test them for keyboard accessibility
- Header cells in tables should have scope='col' or scope='row'.
- Color contrast should be greater than or equal to 4.5:1 (you can use a contrast analyzer such as: https://www.paciellogroup.com/resources/contrastanalyser/)
- Alternate entrypoints for images must always be provided. Examples include a caption, alt tag, title tag, or aria label. This requirement should be built into the CMS.
- All buttons use button element (unless `<input>` or `<a>` is more appropriate)
- Multimedia should have closed captioning or audio description
- We should use an accessibility auditor/linter in our build process
- Site should be zoomable up to 200% without losing functionality
- Use http://wave.webaim.org/ for benchmarking

## HTML

- Element attributes such as `content="text/html"` are omitted where redundant with HTML5 defaults. More [examples](http://davidbcalhoun.com/2010/top-ten-things-html5-makes-simpler/).
- Declare character encoding as `utf-8` in both the `<head>` and the HTTP header
- All attributes should be wrapped in quotation marks (`data-attribute="32"`) and paired with a value (`selected="selected"`).
- Page `<title>` should be unique across pages for easy tabbing
- The unique portion of the `<title>` should come first (i.e., "Bernie Sanders’s Big Chance To Woo Non-White Voters - FiveThirtyEight")
- localStorage and sessionStorage are preferred for web storage, with cookies as a fallback for Opera Mini

TBD: 
- Could we use a templating engine, like Twig?

## Styling

- A naming convention for styles should be agreed upon and followed. I have enjoyed using [BEM](https://en.bem.info/method/definitions/), but I know others prefer [RSCSS](https://github.com/rstacruz/rscss) and [SMACSS](https://smacss.com/).
- Before the redesign we should annotate comps with the skeleton of styles so that we are all using the same descriptors for lists, list items, wrapping divs, etc.
- Nesting should _only_ be used for pseudoelements and pseudoselectors. Nobody wants to play the "how many levels deep do I need to nest this to override another style" game.
- Complexity in the markup (many classes) is preferred over complexity in the styles (levels of nesting, @extends)
- Styles should target classes, not ids or data attributes
- Inline styling should not be used
- Attempt to keep items within the document flow if possible (i.e., `margin-left: auto;` is preferred over `position: absolute; right: 0;`)
- There are many ways to position an image. In order of preference: 
	1. block
    2. flex
    3. relative
    4. sticky
    5. absolute
    6. fixed
- Flexbox is a strong and robust addition to the CSS specifications. Given our browser support requirements, we will use it whenever possible for layout and alignment.
- Avoid utility classes such as `.hidden` or `.clearfix`, which are easily forgotten and inconsistently applied. Use includes or mixins instead.
- Text styling (color, font-size, line-height) should be applied to the text elements (`<a>`, `<p>`, `<span>`, etc.), not parent elements
- Hard-coded magic numbers are avoided and, if necessary, defined in the _variables.scss file. An example would be something like `$leftRailMinWidth: 30rem`
- Spacing units are defined as rem or em units so they scale appropriately with text size. Pixels can be used for detail work and should not exceed 5px (for example: 3px borders).
- Floats should be avoided in favor of flexbox and absolute positioning
- Favor `transition` over `animation`
- Favor seconds over ms for `transition` units

## Style Guide
- Living style guide will document all changes to components. Part of QA will be ensuring styles have been updated in both places.
- We should include a section for deprecated styles to help teach people what's wrong as well as right
- Global variables are defined in a variables file. Should include:
    + Colors 
    + Fonts 
    + Spacing 
    + max-width for body copy
    + Breakpoints
    + z-index
    + Transition duration, easing

## JS

- Identify Boolean variables with prefixes such as `is`, `can` and `has`
- Site should use ES6 with compilation via Babel
- A .jslint config file will be used to enforce syntax and style (indenting, spaces, capitalization, braces). Linting will happen in the build process.
- Functions should aim toward statelessness, where they require no outside data, produce no side effects and return a new object instead of modifying an existing one
- Clear, readable, expansive code is preferred to comments. Still, for code like regular expressions or anything else that might not be easy for a beginning or intermediate developer to read, comment appropriately.
- Use `===` unless checking against `null`, where a `==` will check against `undefined` as well
- If using a loop, cache the array length
- Use second parameter for radix with parseInt()

Ideas that I've read are good practices but I don't care too much about
- Prefer the rest parameter (`...nametk`) over `arguments`
- Avoid nested function calls, use composition instead
- Favor `const` over `let` and `let` over `var`
- Favor immediately invoking function expressions over `if`, `else if`, `else` and `switch`
- Favor Maps over Objects when it makes sense

## Security

- We should use HTTPS, and therefore all ads should be served via HTTPS

## Testing

- Implement some sort of continuous integration to automate testing and deployment
- HTML should be run once a month through the W3C html validator. If there are areas where we're OK not passing, those will be documented and kept up-to-date.

TBD
- Flesh out in terms of when we write tests, what framework we use and which tests are mandatory for build deployment

## Technology

- We should avoid vendor styles aside from an opinionated reset, such as Normalize CSS or Sanitize CSS
- Vendor prefixes should be added and maintained in post-processing
- Mixins go in a mixins file. This mixin file should be very well documented and include example usage in the comments
- Favor native methods over lodash over underscore for functional programming needs
- Some sort of live reload feature should be attached to our watch process

TBD: 
- We have a lot of grunt tasks. Is there a desire to switch to gulp or Webpack for dependecy management and build processing?
- We currently use SASS for style abstraction. Is there a desire to switch to postcss?

## Version control

TBD
- Is there a world in which we use GitHub for version control instead of Beanstalk? Just to lower the bar for new hires who are working on the project.
- Flesh out merge process with standards about non fast-forward, when to use rebasing, namespace for different kinds of branches

## Workflow

TBD
- I'd like to implement code review not only for error checking but also to encourage knowledge sharing between developers. Are the downsides in added overhead too costly?

## Syntax and editor config

- There is no enforced character count for line breaks. For comments, break where it feels natural.
- A shared [.editorconfig](http://editorconfig.org/) file could help prevent whitespace changes in diffs, but I know people might have strong personal preferences
