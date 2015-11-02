
# FTE Technical Standards

## Browser support

- Latest two versions of all modern browsers
- Internet Explorer 10 and up

TBD:
- Is this the right list?

## HTML

- Use HTML5 elements wherever possible
- Element attributes are omitted where redundant with HTML5 defaults

*Good*
```html
<meta charset="utf-8">
```
*Bad*
```html
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
```

More [examples](http://davidbcalhoun.com/2010/top-ten-things-html5-makes-simpler/).

- (i.e., `http-equiv=Content-Type content="text/html; charset=utf-8"` can just be `charset="utf-8"`)
- Character encoding should be utf-8 and declared in both the `<head>` and the HTTP header
- All attributes should be quoted
- Page `<title>` should be unique across pages for easy tabbing
- The unique portion of the `<title>` should come first (i.e., "Bernie Sanders’s Big Chance To Woo Non-White Voters - FiveThirtyEight")
- Some sort of templating engine (Handlebars, Twig) should be used
- localStorage and sessionStorage are preferred for web storage, with cookies as a fallback for Opera Mini

TBD: 
- Which templating engine should we use?

## Styling

- Nesting should only be used for pseudoelements and pseudoselectors
- Complexity in the markup (many classes) is preferred over complexity in the styles (levels of nesting, @extends)
- Styles should target classes, not ids or data attributes
- Hard-coded magic numbers are avoided and, if necessary, defined in the _variables.scss file. An example would be something like `$leftRailMinWidth: 30rem`
- Attempt to keep items within the document flow if possible (i.e., `margin-left: auto;` is preferred over `position: absolute; right: 0;`)
- There are many ways to position an image. In order of preference: block, flex, relative, sticky, absolute, fixed.
- Inline styling should not be used
- Avoid utility classes such as `.hidden` or `.clearfix`, which are easily forgotten and inconsistently applied. Use includes or mixins instead.
- attribute values of 0 do not require units
- Flexbox in particular is a strong and robust addition to the CSS specifications. Given our browser support requirements, we will use it whenever possible for layout and alignment.
- Text styling (color, font-size, line-height) is applied to the text elements (`<a>`, `<p>`, `<span>`, etc.), not parent elements
- Media queries are built mobile first
- Spacing units are as much as possible defined as rem or em units so they scale appropriately with text size. Pixels can be used for detail work and should not exceed 5px (for example: 3px borders).
- Floats should be avoided in favor of flexbox and absolute positioning
- The box-model should be consistent across the piece
- Favor `transition` over `animation`
- Favor seconds over ms for `transition` units

## Technology

- Use SASS, LESS or postcss
- No vendor styles aside from an opinionated reset, such as Normalize CSS
- Vendor prefixes will be added and maintained in post-processing
- Mixins go in a mixins file. Could include:
    + RGBA converter for color variables (tints such as $colorRed800 will be used for styling, but the mixin should exist for rare cases where transparency is necessary, such as subtle `box-shadow`)
- Favor native methods over lodash over underscore for functional programming needs
- Use Browserify or Webpack for dependecy management and build processing
- Use gulp or Webpack for postprocessing
- In the event that we need feature detection, use Modernizr. Don't browser detect and don't include Modernizr unless necessary.

## Style Guide
- Living style guide will document all changes to components. Part of QA will be ensuring styles have been updated in both places.
- Should include section for deprecated styles to help teach people what's wrong as well as right
- Global variables are defined in a variables file. Shold include:
    + Colors 
    + Fonts 
    + Spacing 
    + Max-width for body copy
    + Breakpoints
    + Z-index
    + Transition duration, easing

## JS

- Site will use ES6 with compilation via Babel
- A jslint config file will be used to enforce syntax and style (indenting, spaces, capitalization, braces). This file will be drafted as a group. Linting will happen in the build process.
- Functions should aim toward statelessness, where they require no outside data, produce no side effects and return a new object instead of modifying an existing one
- Prefer the rest parameter (`...nametk`) over `arguments`
- Avoid nested function calls, use composition instead
- Favor `const` over `let` and `let` over `var`
- Favor immediately invoking function expressions over `if`, `else if`, `else` and `switch`
- Favor Maps over Objects if it makes sense
- Clear, readable, expansive code is preferred to comments. Still, for code like regular expressions or anything else that might not be easy for a beginning or intermediate developer to read, comment appropriately.
- Use `===` unless converting checking against `null`, which will check against `undefined` as well
- If using a loop, cache the array length
- Identify Boolean variables with prefixes such as `is`, `can` and `has`
- To hide images at certain breakpoints, target the parent of the `<img>` or `background-image` with `display: none;`, not the element itself
- Use second parameter for radix with parseInt()

## Build processes

- Testing
- Webpack?

## Version control

- Site will use GitHub for version control
- Merges will follow established pattern (FLESH OUT)

## Testing

- Flesh out
- All pull requests will go throug

## Workflow
- PRs for all changes other than hotfixes
- 

## File structure

- Assets should be separated into a separate directory, organized by asset type (images, fonts)

## Syntax and editor config

- There is no enforced character count for line breaks. For comments, break where it feels natural.

## Security

- Use HTTPS
- Flesh out with Paul

## Performance

- Site will use a single stylesheet for common styles across the site and then page-specific stylesheets so that the user can cache the shared css
- Ads, social sharing and any other third-party widgets should be loaded at the end of the body, asynchronously
- Assets are gzipped, except for woff and woff2 font files
-  In content headers, set `Expires` to a date up to one year in the future (one year is good unless we think the asset will change), set `Last-Modified` to the date the asset was last modified
- @font-face stack will use .woff2 and then .woff, in that order
- Ideally we should load four or less font files per page. Max is six.
- Site will implement responsive images solution, ideally using srcset and srcset polyfill
- Site will take advantage of prefetching in Chrome
- HTTP/2 will be adopted as soon as our server supports it
- Build process will use UnCSS or similar tech to prune unused styles
- Assets will be gzipped, concatenated and minified (not in that order), with appropriate expiration dates on the content headers
- Responsive images solution should be in place for serving smaller or retina images when appropriate. srcset is preferred, given it's increasing adoption.
- Performance budget should be agreed upon and then built into the build process. For example:
    + Total page load time - 2s - WebPageTest, median from 5 runs on 3G - all breakpoints - All pages
    + Total page load time - 2s - GA, median across geographies - all breakpoints - All page
    + Total page weight - 800kb - WebPageTest - desktop - All pages
    + Total page weight - 300kb - WebPageTest - mobile - All pages
    + Speed Index - 1000 - WebPageTest using Dulles location on Chrome - all breakpoints - All pages
- Implement monthly performance report showing median and 95th percentile load time for the month (available in GA), a time series to contextualize those numbers, and specific pages and changes that celebrate performance wins

## Accessibility

- Users should be able to tab to navigate between links.
- Users should be able to activate a link when pressing ‘Enter’ on their keyboard.
- Users should be able to identify links without relying on color alone.
- Users should be able to activate hover and and focus states with both a mouse and a keyboard.
- ARIA roles should be used throughout the site
- All form elements should have an associated label
- Do not use divs for form elements
- Icons elements should have the descriptive text within and hidden via CSS
- Include a skip navigation link with a `tab-index='1'` for users of assistive technologies
- Add a title attribute to iframes and test them for keyboard accessibility
- Header cells in tables should have scope='col' or scope='row'.
- Color contrast should always be greater than or equal to 4.5:1 (you can use a contrast analyzer such as: https://www.paciellogroup.com/resources/contrastanalyser/)
- Alternate entrypoints for images must always be provided. Examples include a caption, alt tag, title tag, or aria label. This requirement should be built into the CMS.
- All buttons use button element (unless `<input>` or `<a>` is more appropriate)
- Multimedia should have closed captioning or audio description
- We should use an accessibility auditor/linter in our build process
- Site should be zoomable up to 200% without losing functionality
- Use http://wave.webaim.org/ for benchmarking

