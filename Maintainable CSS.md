title: Maintainable CSS
output: maintainable-css.html
--

# Maintainable CSS

--

## What makes CSS difficult?

- easy to write...
- ...but there are a million ways to do the same thing
- styles are *cascading* &rArr; what are the side effects for a given style?
- an existing style already affects my new component, what do I do?
- I need to change the font size / colors / borders everywhere, AIIEEEEE

--

## The issues, briefly

1. Lots of ways to write
2. Side effects
3. Existing styles
4. Design changes

--

## Idea #1

1. Lots of ways to write &rArr; **strict coding standard**
2. Side effects &rArr; **architecture and selectors**
3. Existing styles &rArr; **rewrite ;)**
4. Design changes &rArr; **avoid hard-coding**

--

# Oh bummer, we have a lot of CSS
## and no chance to rewrite all of it...

--

## Idea #2 (for existing projects)

1. Lots of ways to write &rArr; **understand pros and cons**
2. Side effects &rArr; **avoid in *new* CSS**
3. Existing styles &rArr; **???**
 - try to cope; local normalize if needed, ...
4. Design changes &rArr; **???**
 - all is lost...

Sorry.

--

# How do you write maintainable CSS anyway?

--

## Architecture

Hopefully I've convinced you that an architecture is necessary. Here's an example.

A complete site design consists of pieces (say, design ideas). They either

1. apply throughout the site (color scheme, typography)
2. apply to a component that is used in many places
3. define a variant of component
4. define something only used once

--

## Architecture &ndash; concepts

- **component**: `.search-form`
 - two or more words
 - (otherwise too generic; a "box" is not a component)
- **element**: `.field`
 - `.search-form > .field`
 - single word if possible
- **variant**: `.-compact`
 - `.search-form > .field.-compact`
 - prefix, think `ls -a -l`

--

## Can we model everything with these concepts?

Possibly, but it might not be practical. Add a couple of things to the toolbox:

- *nested components* (careful!)
   1. what happens with `.content-box .title` vs. `content-box > .title`?
   2. OTOH we want selectors to be **simple**: a rule of thumb is max 3 levels
- global *helpers*
 ```
 ._pull-right { float: right !important; }
 ```
- layouts

--
## Layouts

Briefly: **Define positioning in parents**.

```
.parent-of-three {
    .clearfix;

    > .child {
        width: 33.33%;
        float: left;
    }
}
```

Vs. in `.child`: where should I be? What width? How? Position absolute, whee, I broke the whole layout...

--

## Hard-coding

```
#footer {
    margin: 6px;
    padding: 4px 8px;
    height: 40px;
    box-sizing: border-box;
    overflow: hidden;
    font-size: 85%;
    line-height: 30px;
    background-color: #006DAD;
    border-top: rgb(62, 133,189) 1px solid;
    border-bottom: rgba(48,95,130,1) 1px solid;
    border-left: rgb(20, 100, 157) 1px solid;
    border-right: #6C93BB 1px solid;
    border-radius: 4px;
}
```

What does this mean?

--

```
// _config.less
@body-side-margin: 6px;

@border-radius: 4px;
@border-width: 1px;

@box-color-bg: #006DAD;
@box-color-border: #6C93BB;
@box-padding-vertical: 4px;
@box-padding-horizontal: 8px;

@footer-height: 40px;

// footer.less
#footer {
    margin: @body-side-margin;
    padding: @box-padding-vertical @box-padding-horizontal;
    height: @footer-height;
    box-sizing: border-box;
    overflow: hidden;
    font-size: 85%;
    line-height: @footer-height - 2 * (@border-width + @box-padding-vertical);
    background-color: #006DAD;
    border: @border-width solid;
    border-color: @box-color-border
        darken(@box-color-border, 5%)
        darken(@box-color-border, 10%)
        lighten(@box-color-border, 5%); // TODO: replace with mixin
    border-radius: @border-radius;
}
```

--

## File structure

Consider project hierarchy. A file per component? Folder per page?

```
styles/
    _config.less
    _normalize.less
    _helpers.less
    _grid.less
    header.less
    footer.less
    components/
        login-form.less
        image-carousel.less
    pages/
        front-page/
            _config.less
            front-hero-area.less
        result-page/
            _config.less
            result-list.less
```

--

## Result

- What can you deduce just by looking at an element?
 - `<span>`
 - `<div class="search-form">`
 - `<button class="action">`
 - `<div class="_clear">`
 - `<div class="news-listing -dark -narrow">`
- How can you find all the styles that affect the element `.news-listing`?
- What do the styles in `styles/components/news-listing.less` affect?
- How can you implement site-wide style changes?

--

## Further reading

- http://rscss.io/
- https://en.bem.info/

--

# Bad CSS
## Examples

--

## Selectors

```
.a .b .c .d .e {}       // Why so specific?
div.foo {}              // Does it need to be <div>? Probably not.
div {}                  // Unexpected. Styling h1 and such *once* is ok.
[class^=btn] {}         // Are you sure?
```

--

## !important

Only use this in global helpers, as discussed earlier.

```
// OK
// Note: this style only does one thing.
._pull-right {
    float: right !important;
}

// BAD
.my-button {
    color: red !important;
}
```

If you need `!important`, you've misunderstood cascading (**C**SS).

--

## Properties *often* used in wrong places

- Positioning: `position`, `top`, `bottom`, `left`, `right`
- Floats: `float`, `clear`
- Dimensions: `width`, `height`
- Fonts: `font-size`, `font-family`
- Others I can't think of right now...

The less code, the better. You don't *need* to use these often, so don't.

--

## Repetition

```
.a {
    // 10 lines
}
// elsewhere
.b {
    // same 10 lines
}
```

Instead:
```
.a, .b {
    // 10 lines
}
```

--

If the styles are in different files, use LESS/SASS/Stylus mixins.

Mixins can even have parameters and logic.
```
.container-fixed(@gutter) {
    margin-right: auto;
    margin-left: auto;
    padding-left:  floor((@gutter / 2));
    padding-right: ceil((@gutter / 2));
    &:extend(.clearfix all);
}

.narrow-container {
    .container-fixed(3rem);
}

.wide-container {
    .container-fixed(0.5rem);
}
```
