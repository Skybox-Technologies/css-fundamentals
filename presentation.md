# Let's talk about styling!

To do that, and to understand how we got to where we are, first we need to establish the basics. So...


## WTF is HTML?

HTML is an ancient technology dating back from the very beginning of the internet. Back then, the big idea was to share content primarily of textual nature, like articles, newsletters, dissertations, etc. As with any other text document, they wanted to apply some basic formatting with things like headers, paragraphs, lists and tables. Maybe, even an image!

The way HTML did this was to wrap content in semantically descriptive tags, which browsers then interpreted when rendering the document. Various browsers had their own take on what they thought a heading or a paragraph should look like, which resulted in the document/website looking different from browser to browser. Cute! (...or so you'd think!)

The main takeaway here is that HTML was meant for _text_ and that browsers come with some built-in rule sets for how they interpret all the various HTML tags we feed them. If you pass pure HTML to a browser, without any styling, it will still render out stuff based on that foundational ruleset.

### A note about block vs inline

One of the most important concepts to grasp in HTML is that not all tags are equal neither semantically nor in how they behave. Behaviourally, they break down into two categories: **Block-level** and **Inline-level** elements.

**Block-level** elements stretch out to fill the entire width of whatever space they are given. If unrestricted by a sized container, they will fill the entire browser window. Block-level elements break the flow of the document and force other elements to render above or below them (dependent on order in the HTML - top to bottom). Think of them as creating new lines in a written document. How tall they become is driven entirely by what content is inside them. Block-level elements can accept both other block-level and inline-level children.

Examples of block-level elements: `div`, `h1`, `h2`, `h3`, `h4`, `h5`, `h6`, `ul` and `p`.

**Inline-level** elements, on the other hand, are meant to live _inside_ of other elements, either block or inline. Opposite to block-level elements, they don't create new lines, but rather slot neatly into the content without breaking its flow. To do that, they inherit styling from their parent element and conform to it. Inline-level elements are only supposed to accept other inline-level children. You can force them to take block-level elements - and nothing happens if you do in modern browsers - but it's semantically wrong and should be avoided. In the old days, it was a big deal that would result in all sorts of shitty visual bugs; you learned the hard way to respect the nesting rules.

Examples of inline-level elements: `span`, `a`, `button`, `strong`, `em`, `i`, `u` and `b`. (`img`)

Resources:
- https://www.youtube.com/watch?v=x_i2gga-sYg

## CSS has entered the chat

As the internet developed, people got creative and craved more options to control their content. CSS was born and shipped in 1996 (a few years after HTML), allowing you to manipulate your elements in ways that were simply impossible without it.

CSS stands for Cascading Style Sheets and it works by introducing an incredibly flexible, hierarchical system of rule definitions to act on your HTML tags. This enormous flexibility is simultaneously CSS's greatest strength and biggest pitfall. The rules are fairly simple, but the results in practice can often become extremely convoluted due to the amount of rules that (un)intentionally act upon any element in the document.

The main rules of CSS are as follows:
- Rules applied to a parent element (most of them...) cascade down to affect all child elements below it, and
- In case of conflicting rules, the rule with the greatest specificity wins.

Specificity is a measure of how specific a rule is. For instance, a very generic rule like
```
p {
    font-size: 12px;
}
```
targets all elements in the document of type `p`. That's a very generic rule with the lowest possible specificity, which makes it very easy to override.

If I want to hit a specific `p` tag, either with an id, a class name or a tiered selector, the specificity increases, as the selector hugely narrows the scope of what it hits. This selector, for instance
```
div.my-important-section ul > li p.something {
    font-size: 13px;
}
```
has a very high specificity, in that it targets a very specific subset of nodes within the document and will therefore override the more generic rule above where and when it applies.

If the `<p class="something">...</p>` tag happens to contain other elements, like `span` or `a` links or other, those elements will _also_ have a font-size of 13px.

Remember the base rules browsers use for presenting tags, which I mentioned earlier? Those still apply. CSS layers on top of those, extending and overriding those rules. So, if you wonder one day why your element behaves in a certain way without you having defined it manually yourself, the answer will typically be that it's base behaviour implemented by the browser.

Speaking of behaviours, block-level elements are easy to work with in CSS, because they allow for direct manipulation of their properties.

Inline-level elements, on the other hand, are inherently resistant to these overrides and don't take well to sizing inputs, for instance, because it is in their nature to conform to their parent! Because of this, `display: inline-block` was born, which kept the "same-line" behaviour of `inline` elements while unlocking all the direct control afforded by `block`.

Nowadays, we have some very advanced options for layouting, such as `grid` and `flex`, which negate the properties of `inline` elements by overruling their behaviour entirely. In other words, it doesn't really matter, from a styling perspective, whether your element is `inline` or `block` once it is positioned inside a `flex` or `grid` context. _Outside_ of those, however, the old rules still apply - so, be mindful of that!


## Kinds of positioning

So, CSS gave us a bunch of tools to position our elements in respect to the page and to each other - and we have mentioned some of them here already. These tools have been revised and expanded upon multiple times, but what options do we have now?

### Content inside of an element

To control the positioning of content within a simple element, such as text in a button or a list item, etc, you often just need to reach for `text-align`, `padding` and `line-height`. Together with `width` and `height` on `inline-block` or `block`-level elements, these are usually enough to do what you want.


### Elements in relation to surrounding elements

To control positioning of an element within the world that it lives in, we have the `position` property. `position` allows us to position a single element relative to the parent node or, in some cases, the browser window. Options for `position` are: `relative`, `static`, `absolute`, `fixed` and `sticky`.

- `static` is the default positioning every node comes with, which is a sort of reset. No special treatment here.

All other options unlock 5 additional controls that allow your to offset your element (and all children within) along 3 dimensions: `top`, `left`, `bottom`, `right` and `zIndex`. `zIndex` takes a whole number (integer) either in the positive or the negative reflecting stacked layers. The other controls can be set in any regular unit, `px`, `em`, `rem`, `%`, `vw`/`vh`, `ch`, etc.

- `relative` positioning sets a new reference point for all of its child nodes of position `absolute`. Elements with position `relative` _stay_ in the document flow and take up space like they normally would.

- `absolute` _removes_ an element from the document flow, meaning it no longer takes up space in the document, and gives you full control over where that element should be positioned by use of its offset controls. `absolute`ly positioned elements are positioned _in relation to_ the **closest** `relative` parent! If no parent is found in the hierarchy with `relative` positioning, the browser window becomes the de-facto top parent with `relative` positioning.

- `fixed` works like `absolute`, except it anchors to the viewport. Things like headers and footers that stay in place, while the rest of the page scrolls, are typically positioned `fixed`. `fixed` elements are _also_ taken out of the normal document flow and can be controlled with the usual offset controls.

- `sticky` was introduced more recently (2012) and combines aspects of both `relative` and `fixed` positioning. An element with `position: sticky` is treated as `relative` until it crosses a specified threshold (defined by `top`, `left`, `bottom`, or `right`), at which point it becomes `fixed` within its containing block. This is useful for elements like headers that should remain visible while scrolling within a section but not fixed to the viewport.

In addition to the `position` controls, we can also leverage the `margin` property, which goes in all 4 cardinal directions and can receive both positive and negative values (though having negative values in opposing carinal directions _can_ become a bit funky depending on context and other sizing rules applied).

In recent years, the `transform` property was also introduced (with CSS3), which allows us to `translate` an element in 3 dimentions.

Both `margin` and `transform` _do not_ remove the element from the document flow, meaning elements will still take up space despite not being physically in the described location - though negative margin _can_ reduce the footprint of the element and affect how following elements stack against it. Be mindful of how this may affect the rest of your elements!

### Elements within other elements (layouting)

For more advanced layouting needs (and graceful responsiveness), there are `display: flex` and `display: grid`. They are both applied to a parent element whose children we wish to control.

`flex` and `grid` are really powerful tools that make it trivial to achieve a lot of goodness that was extremely hairy to get done back in the day. They do this through a large array of associated properties, which are used in conjunction with each other to achieve the desired effect. There is a big overlap between these properties across `flex` and `grid` and they behave much the same across both.

So, when should you reach for `flex` and when should you reach for `grid`? What's the difference?

The unsatisfactory answer to that is: it depends. You can achieve largely similar effects with both, which makes it a personal and situational choice. You can get by with either without needing to combine them, but you will only achieve ultimate frontend nirvana through mastering both! =)

My rule of thumb is this:
- If I want to easily position a few elements in relation to each other (typically next to one another, in a row), I use `flex`. `flex` is also _excellent_ at positioning child elements with relation to parent elements _without_ needing to resort to `absolute` positioning (which takes the child element out of the normal document flow and requires you to consider its positioning along the Z-axis).
- If I need to position my elements along a 2D or tabular system but don't want to resort to a table, I reach for `grid`.

That's how I use them. Your mileage may vary. As previously stated, neither is inherently right(er) or wrong(er). Play with them on your own terms and figure out by yourself, what you like most.

### Flex

Important concepts to understand about `flex` are that it works in relation to a coordinate system and can either go forward or in reverse.

What does that mean?

It means whenever you create a space with `display: flex`, you should consider that space a 2D coordinate system with a horizontal x-axis and a vertical y-axis. `flex` maps those to a primary (reference) axis and a secondary axis. By default (`flex-direction: row`), the x-axis is the main axis and the y-axis is the secondary axis. You can flip those around with `flex-direction: column`. All additional properties you apply are in relation to either the main or secondary axis and will flip from horizontal to vertical and vice versa based on the `flex-direction`.

Furthermore, you can reverse the order the elements are rendered in by appending a `-reverse` to the `flex-direction` - for instance `flex-direction: row-reverse` or `flex-direction: column-reverse`.

Another aspect of `flex` that's important to understand is `flex-wrap`. By default, a `flex` context will **not** wrap, meaning it will always attempt to squeeze all elements into the same row regardless of what properties those elements have on them. `width` and `height` **are not respected** so long as the `flex` parent is not wrapping. Once told to `wrap`, however, this changes and styling on the child element is suddenly respected.

The main, useful properties you will find yourself using again and again with `flex` are `justify-content` and `align-items`.

`justify-content` aligns the items along the main axis (so, x by default). This property allows you to control whether your child elements come at the start of your row, sit in the middle, bunch up at the far end or are to be distributed across the width of the parent container with space between or around them.

`align-items` does basically the same thing as `justify-content`, but in the _secondary_ axis (so, it works in the verytical by default).

To center your content within a `flex` environment, you typically use `justify-content: center` in conjunction with `align-items: middle`.

The child elements themselves also get a say, however, and can position and resize themselves based on rules that apply on them specifically. For instance, `align-self` and `justify-self` will override the settings inherited from the parent, if, for some reason, you should need or wish to do so.

For dynamic resizing based on available space, the child elements get `flex-grow`, `flex-shrink` and `flex-basis`.

Resources:
- https://css-tricks.com/snippets/css/a-guide-to-flexbox/
- https://www.youtube.com/watch?v=phWxA89Dy94

### Grid

A grid can be thought of as a hyper-dynamic table, which you get to size exactly as you like (irregularly is an option). You can also define and assign zones in the form of area maps to your elements to fill out. It's a much more fluid and powerful option for managing a larger area than `flex`, in my opinion. Inverse to `flex`, `grid`s cannot flip axial orientation.

Much like with `flex`, a `grid` is defined on the parent container element. Defining the property on its own doesn't do much, until you tell it how to make your grid. You define this with `grid-template-columns` and `grid-template-rows`.

Both of these can define the grid in a few ways. One option is to repeat the width (cols) or height (rows) of each individual entry. These can be defined in all the regular units that govern such things, such as `px`, `%`, `em`, `rem`, etc, and they can be as equal or irregular as you like. You can even mix and match the units you use within the same definition and `grid` will attempt to respect them as closely as possible!
```
grid-template-columns: 1fr 10% 320px 1fr 6rem;
```

`grid` also introduces the `fr` unit, which stands for "fraction". `fr` is extremely dynamic and gets calculated at the end, once you have defined all of your cols or rows, based on how many fractions you have declared. It can be a bit messy to wrap your head around, so best to try it out and learn by doing.

If you intend for all of your entries to share the same size, you can short-hand it with `repeat(n, size)`, where _size_ can be expressed in any fixed unit.

Once you have your template defined, your individual elements can be made to fill more than one cell with `col-span` and `row-span`, if desired.

Alternatively, area maps can be defined to automatically resize child elements based on where they belong in the area map. For instance:

```
grid-template-areas: "header header header header"
                     "main main . sidebar"
                     "footer footer footer footer"
```
This allows you to assign `grid-area` to child elements and have them snap to the boundaries you have defined - such as `grid-area: header`. Note that `.` means a dead zone, where nothing is supposed to be assigned. Also note that the above grid will not take effect without _also_ defining the template for the rows and cols as previously specified - as the `grid` wouldn't know inherently from the area map how to size each area on its own.

In common with `flex`, `grid` shares `justify-content` and `align-items`. Both of these properties work conceptually the same, except `justify-content` applies within the boundaries of the cells and not on a row level.

Resources:
- https://css-tricks.com/snippets/css/complete-guide-grid/
- https://www.youtube.com/watch?v=EiNiSFIPIQE