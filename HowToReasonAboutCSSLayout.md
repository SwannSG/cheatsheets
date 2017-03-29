## How to reason about CSS layout

Understand how elements will be positioned from first principles.

### What are we positioning ?

A rectangular box or block.
- height >=0
- width >=0
- some kind of an html *element*

### Positioning Axis

- x-axis
- y-axis
- z-axis

### Positioning Schemes

- normal flow
- floats
- absolute positioning

### Normal flow positioning scheme

Formatting contexts
- block-level
- inline-level
- relative
    - relative to where the block would have been under normal flow 

The parent (containing bloack) establishes the *formatting context* for its children based on whether the child boxes are considered to be *inline-level* or *block-level*.

In normal flow, elements inside a parent are either in *block formatting context* or *inline formatting context*, but not both at the same time.

### Floats positioning scheme

- floats combine with *normal flow*, so we need to first understand normal flow

### Absolute positioning scheme

- absolutes combine with *normal flow*, so we need to fist understand normal flow.

### What determines layout

- how the box of an element is aligned and sized
- how elements within a particular parent element are positioned relative to each other

#### CSS properties that determine how the box of an element is aligned and sized

size of element
- *width*
- *height*
- *margin*
- *display*
    - a *block-level* element takes the entire width of container

### How to decide on formatting context for elements in a parent container ?

Is the formatting context *block* or *inline* ?

The parent (container) establishes the formatting context for its children based on whether the child boxes are considered to be inline-level or block-level.

#### How to decide if child block is inline-level or block-level ?

*display* determines which layout algorithim to use. Each element has a default value for *display* which can be explicitly overwritten to change the layout algorithim.
- *block, list-item, table*  
- *inline, inline-table, inline-block* 

See [Block-level elements](https://developer.mozilla.org/en/docs/Web/HTML/Block-level_elements) and  [Inline-level elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements)

#### What if the parent contains both inline-level and block-level boxes?

The normal flow algorithim generates anonymous box(es). Depending on the context, either
- inline-level anonymous block

or

- block-level anonymous block

```html
<div>
    Some text       <!--becomes anonymous block-level box-->
    <p>
        More text
    </p>
</div>
```

or 

*Anonymous inline boxes* are generated when a block container element contains text that is not enclosed within an inline-level element.

```html
<p>Some <em>emphasized</em> text</p>

<!--'some' becomes anonymous inline-level box-->
<!--'text' becomes anonymous inline-level box-->
```


#### CSS properties that also influence layout

*position* associated with positioning
- static ..............default

*float* 
- none ................default

### Normal flow block-level formatting context

- Child boxes are stacked vertically, starting at the top of the containing block.
- Child boxes always try to align as left as possible inside the parent container, even in the presence of floats.
- Vertical abutting margins collapse i.e. only largest margin is used.
- Child boxes expand to 100% of containing element     

[Block-Level and Float Ex 1](https://jsfiddle.net/SwannSG/324750w7/)

[Block-Level and Float Ex 2](https://jsfiddle.net/SwannSG/jafq7904/)
 - notice how a float expands horizontally to just enclose content
     - do not necessarily expand to 100% of containing element  

### Normal flow inline-level formatting contex

Inline-level formatting is much more complex than block-level formatting.

- Boxes are laid out horizontally one after the other.
- Starting at top, left (actually depends on text direction) of the container block.
- Horizontal margins, borders and paddings are respected between boxes.
- Boxes may align vertically in different ways.
- Where the boxes form a line, the line is called a *line box*.
- The width of a *line box* is determined by the containing block and the presence of floats.
- The height of a *line box* is determined by line height calculations.
- *Line boxes* are generated as needed.
- *Line box* width is generally the width of the containing block (minus floats).
- *Line box* height is always sufficient for all the boxes it contains.

[Best description of inline formatting model](http://meyerweb.com/eric/css/inline-format.html)


**What happens if *inline-box* is too wide for the *line-box* ?**

- if it can be split, it will be split across serveral lines.
- if it cannot be split, then the *inline-box* horizontally overflows the *line box*.

**What controls the horizontal alignment of inline-boxes ?**

- The *line box* needs some unused space.
- We cannot **directly** control how inline-level content is placed on line boxes.
- Use *text-align* CSS property
- Normally, whitespace (spaces, tabs etc.) can be affected by justification. Unless *white-space* property is set to **pre(serve)** or **pre(serve)-wrap**.

[inline-block horizontal alignment of contents](https://jsfiddle.net/SwannSG/4jhvov1c/)


**What controls the vertical alignment of inline-boxes ?**

There are two properties that control this, *vertical-align* and *line-height*.

**vertical-align** controls the vertical position of *inline-boxes* inside a *line box*. It does not control the vertical position of each *line box*.

The *line box* height is determined by two factors:
- the height of the inline boxes contained within it
- the alignment of the inline boxes contained within it
- the *height* property does not apply
- line height --> line-height = value X height of line box content area
The height of the line box content are dependent on:
- the font
- vertical padding,border and margin start outside the content area

Each font must define a 
- baseline,
- a text-top edge
- a text-bottom edge.
- height of font = top - bottom  


line box height is a function of:
- font ..................font-type, font-size
- line-height ...........factor to expand *inline box* height
- vertical-alignement ...how *inline boxes* are spatialy related to each other

*vertical-align* has a range of possible values.

To recap, *inline boxes* have:
- a font size, which determines the size of the text glyphs
- a line height, which determines the height of the inline box (in absolute terms or relative to the font size)
- a baseline, which is a position defined by the font, and on which the bottom edges of most glyphs / characters are aligned (excluding characters such as q and g, which have descenders/ascenders - parts that extend below/above the baseline alignment)

The *line box* height is calculated after calculating the heights of every inline box in it, and then applying all vertical-align alignments other than vertical-align: top and vertical-align: bottom.

Each *line box* has:
- a font size, which is inherited from the parent
- a height defined by the heights and alignments of *inline boxes* in the line box
- a baseline, which is defined by a "strut" (except in rare cases involving vertical-align: top / bottom): an invisible, zero-width inline box with the element's font and line height properties

[Normal Flow Inline Level Formatting Context And Vertical Align](https://jsfiddle.net/SwannSG/6h9nq28r/) 

Note that many of the vertical-align values are relative to the parent element's baseline - which is determined by the font metrics and font size of the parent element. For example, the chain of vertical-align: super elements gradually shifts the baseline upwards.

### Text and Inline Blocks Normal Flow

A sequence of text, conceptually forms a single *inline block*. For display purposes the text may be spread across multiple line boxes, but the text is rendered using its' own internal mechanics.  

```javascript
<p>The cat sat on the mat and ate some fish !</p>
```

```javascript
<p>The cat <em>sat</em> on the mat and ate some fish !</p>
```
Above forms *three inline boxes*. This is quite different to a single sentence, which is one *inline block*.
- "The cat "
- "sat"
- " on the mat and ate some fish !"

#### Sequence of text in single inline box

We can influence text layout inside an inline box to some extent. Excluding the obvious font type stuff, we can change the space between letters by using **letter-spacing** property.

But if we want to change the vertical rhythm of a letter (or bunch of letters), we first need to create a separate inline box for the letter or group of letters, and then use **vertical-align**.

**vertical-align** controls the vertical rhythm of inline blocks, not the vertical rhythm of text in a single inline box.

#### Algorithim for vertical-align

Assume we have the following scenario

```javascript
parent container
    inline block 1
    inline block 2
    inline block 3
end parent container
```

- firstly scan through each inline block (1-3) and determine highest inline block
    - that effectively sets the *line block* height
- the parent font is the reference NOT the highest inline block
- then read the specifictation of each [vertical-align setting](https://developer.mozilla.org/en/docs/Web/CSS/vertical-align) carefully and don't put your own spin on how you think it may work
- notice how *middle* references the midpoint of the "x-height" of the parent font
- The baseline of some replaced elements, like *textarea*, is not specified by the HTML specification, meaning that their behavior with this keyword may change from one browser to the other.

#### Inline block centering technique

[Normal Flow Inline Level Formatting Context, Center A Block](https://jsfiddle.net/SwannSG/6h9nq28r/7/)

#### Other inline block issues

[Normal Flow Inline Formatting Context, Two Divs](https://jsfiddle.net/SwannSG/16bct65t/2/) 

Each "div half" takes up 50% width of the parent. But because they are diplayed as "inline-block", the inline formatting context puts whitespace between the divs. Hence the second div is pushed to the next line. We need to eliminate the whitespace between the divs.

One way of achieving this is by settig the font-size in the parent to 0. Effectively, then the whitespace has zero width. 

But a better way, is to have no newline character between inline blocks. The newline, line break character that is turned into whitespace

```javascript
<div class="blue">
  <div class="half green">width: 50%</div><div class="half orange">width: 50%</div>
</div>
```
Above is NOT equivalent to below.

```javascript
<div class="blue">
  <div class="half green">width: 50%</div>
  <div class="half orange">width: 50%</div>
</div>
```

#### Center Something Inside A Container Using Inline Block Formatting 

[Normal Flow Inline Formatting Context, Vertical Align Middle 1](https://jsfiddle.net/SwannSG/16bct65t/8/)


#### Vertical Align Example

[Normal Flow Inline Formatting Context, Vertical Align Middle 2](https://jsfiddle.net/SwannSG/16bct65t/9/)

vertical-align: middle means,
- take the inline box and get the vertical midpoint (vmp_inline_box)
- place said box above the baseline of the parent element at half the hight of an x character in the parent font


- align that point (vmp_inline_box) with vertical midpoint of the line box (vmp_line_box)  


The short version of this is that vertical-align: middle really means "place the child element above the baseline of the parent element at half the height of an x character in the parent font" - not "middle" in almost any circumstance. This is a very font-centric view of the world: it's not the middle of the parent element and not even the middle of the line box that the child element resides on. While vertical-align: middle often looks fine, pedantically speaking it's not the middle of anything other than an x-glyph in the parent font.


### Inline formatting model basics

#### EM-box or EM-square

The dimensions of the area around a capital letter M are called an *em-box*. The dimensions depend on:  
- typeface (font-type)
- scale (font-size)

The *em-box* is defined for a given typeface and scale. 

#### Content Area

When we string non-replaced elements (characters with specific typeface and scale) together, we create a contiguous block, and this is called the *content area*.

In replaced elements, the *content area* includes the intrinsic height of the element plus any margins, borders or padding.

Replaced elements are:
```html
<img> <object> <video> <textarea> <input>
```

#### Leading

*Leading* is an old term that relates to lead placed between horizontal text. This provides vertical space between lines. 

*leading* = *line-height* - *font-size*

Half leading above and below the *content area* for each element.

See [Line Height](https://www.slideshare.net/maxdesign/line-height) presentation.  

#### Inline box ...with the text

*inline box* height = content area + half leading(top and bottom)

*inline box* height = *line-height*

For replaced elements, the height of the inline box of an element will be exactly equal to the intrinsic height of the element plus any margins, border or padding. 

#### Line box

The box which bounds the highest and lowest points of the *inline boxes* spread across the line.

#### Summary picture

```javascript

--------------------------------------------top line box, top inline box
                    half-leading
--------------------------------------------top content box

                    content box

--------------------------------------------bottom content box
                    half-leading
--------------------------------------------bottom line box, bottom inline box
 
line-height = inline box height NOT line box height
```

### Consequences of inline formatting model

- the *content area* is analogous to the *content box* of a block-level element
- the backgound of an inline element is applied to the content area plus any padding
    - any border will also surround said area
- vertical margins on *non-replaced* elements (the majority) have no vertical effects on inline boxes
    - they do have a horizontal impact
- margins and borders on *replaced* elements affect the height of the inline box for that element, and by implication the height of the line box for that line.

Inline boxes are vertically aligned according to the value of *vertical-align*. 

### Normal Flow and Relative Positioning

We use the CSS *position* property.

The default value for this property is **static**.

Here we use the value **relative**.

The element is positioned according to normal flow, and then offset using *top, bottom, left, right* properties.

Notice all the other elements are ositioned according to normal flow. The relative offset has NO effect on their layout.

[Position relative example](https://jsfiddle.net/SwannSG/9yxot2jp/)

### Float Positioning Scheme

It was originally intended for text to wrap around images. The purpose of float is to allow other elements to float around the element, in this case the image.

[Excellent Float Examples Website](http://css.maxdesign.com.au/floatutorial/)

We use the *float* property.

Default value is **none**.

A *float* must always be set to a *width* (except for some replaced elements).

A float is a box that is shifted to the left or right on the current line. The most interesting characteristic of a float (or "floated" or "floating" box) is that content may flow along its side (or be prohibited from doing so by the 'clear' property). Content flows down the right side of a left-floated box and down the left side of a right-floated box.

Floats exhibit several special behaviors:
- it is useful to think of a float as a special kind of inline box that can be directed left or right
- removed from normal flow during layout
    - other block-level elements do not "see" the floated element.
    - consequently the block may move into space already occupied by the float
- aligned to either the left or right outer edge of their container.
- stacked starting from either the left or right edge, and are stacked in the order they appear in markup.
    - for right-floated boxes, the first right-floated box is positioned on the right edge of the box that contains it and the second right-floated box is positioned immediately left of the first box.
- may affect current and subsequent elements' inline-level content's line boxes.
    - may be shortened to make space for the float.
    - because the float behaves like an inline box, the actual content in the subsequent element, can be squeezed out of the element's content box. To stop that we can set *overflow: hidden*.
- because floats are not in the normal flow, they do not normally affect parent height.
    - hence "clearfix" technique.
- block elements abutting a float can be cleared using the *clear* property.
    - the *clear* property is NOT applied to float element 


[Floats example 1](https://jsfiddle.net/SwannSG/9yxot2jp/1/)

Floats that cannot fit horizontally are shifted downwards.

[Floats example 2](https://jsfiddle.net/SwannSG/9yxot2jp/2/)

#### Establishing a new block formatting context (Overflow: not visible)

The border box of an element in the normal flow that establishes a new block formatting context (such as an element with 'overflow' other than 'visible') must not overlap the margin box of any floats in the same block formatting context as the element itself.

If necessary, implementations should clear the said element by placing it below any preceding floats, but may place it adjacent to such floats if there is sufficient space.

[Floats example 3 - orange overlap](https://jsfiddle.net/SwannSG/9yxot2jp/3/)

[Floats example 4 - orange no overlap](https://jsfiddle.net/SwannSG/9yxot2jp/4/)

This technique of setting *overflow: anything other than visible* on the block(s) that abutt the float, and prevent left or right margin overlap with the float, is very useful.

#### Float Clearing

*clear* property is not placed on the floated element itself

*clear:left* for this block, do not left abutt a floated element, move down to the next "line".

*clear:right* for this block, do not right abutt a floated element, move down to the next "line".

*clear:both* for this block, do not right or left abutt a floated element, move down to the next line.

[Layout with no floats](https://jsfiddle.net/SwannSG/9yxot2jp/5/)


### Absolute/ Fixed Positioning

Boxes are positioned in terms of an absolute offset with respect to the containing block.

In the absolute positioning model, a box is explicitly offset with respect to its containing block. It is removed from the normal flow entirely (it has no impact on later siblings). An absolutely positioned box establishes a new containing block for normal flow children and absolutely (but not fixed) positioned descendants. However, the contents of an absolutely positioned element do not flow around any other boxes. They may obscure the contents of another box (or be obscured themselves), depending on the stack levels of the overlapping boxes.

*position: absolute* and *position: fixed*

Fixed positioning is relative to the viewport.

Absolute positioning is relative to the containing block.

The exact positioning of such boxes is based on the *width, height, top, left, bottom and right* properties.

## Box Sizing In CSS

![Standard CSS Box Model](http://www.cssterm.com/uploads/images/box_model.gif)

At the centre of the box is the *content area*.

We can specify:
- width
    - only for *block-level* and *float* block
    - not for *inline-level* block 
- height
    - only for *block-level* and *float* block
    - not for *inline-level* block
- padding
    - part of the "inside" box
- border
- margin
    - "outside spacer" box
    - only for *inline-level* block side, left and right margins

#### Margin

Property *margin:* possible values:
- default value is 0
- percentage of containing **block width**
    - even to describe top and bottom margins
- length
- **auto**
    - to centre content horizontally

- Controls the size of the margin.
- Top and bottom margins have special behavior when interacting with other margins, known as margin collapsing. 
- Negative values are allowed and affect collapsing behavior.
- *margin: auto* can be used for centering boxes.
- Top and bottom margins have no effect on (non-replaced) inline elements.
- Percentages refer to width of the containing block, even for margin-top and margin-bottom.

#### Padding

Property *padding* possible values:
- default value is varied
- percentage of containing **block width**
    - even to describe top and bottom padding
- length

#### Border

Border actually consists of 3 properties.
 - border-width, thickness of line
 - border-style, style of line
 - border-color, colour of line


### How box model applies to various box types

| Box type | Height | Width | Margin<br>(Left/Right) | Margin<br>(Top/Bottom) |
| -------- | ------ | ----- | ---------------------- | ---------------------- |
| Inline   | N/A	| N/A	| default -> 0	             | N/A                    |
| Block	   | auto -> content-<br>based | auto -> constraint-<br>based | auto -> center | auto -> 0 |
| Float	| auto -> content-<br>based	| auto -> shrink-<br>to-fit | auto -> 0 | auto -> 0 |
| Inline-block | auto -> content-<br>based | auto -> shrink-<br>to-fit | auto -> 0 | auto -> 0 |
| Absolute | special | special | special | special |

The following box types have distinct box models:
- Inline box
- Block box
- Inline-block box
- Float box
- Absolute box   

The exact box model is dependent on the box type.
- inline box
    - no height or width properties that can be set
    - no top or bottom margin
    - left, right margins default is set to 0
- block box
    - height is automatically set by the content
    - width can be set
    - margin-left = margin-right = auto is a special case for horizontal centering
- float box
    - height is automatically set by the content
    - width can be set ???? shrink to fit
- inline-block
    - height is automatically set by the content
    - width can be set ???? shrink to fit
- absolute
    - special parameters

#### Box model as applied to Inline Box

Specifically we are refering to inline, non-replaced, elements.

Inline-level elements are positioned by placing them on line boxes.

Line boxes are sized based on font-size and line-height and relative alignment (vertical-align) between inline boxes.

margin-top and margin-bottom are ignored for inline-level elements.

margin-left and margin-right do work for inline blocks.
 - only applies to the start and end of the inline block, not each line box.
 - default value=auto, set margin-left=margin-right=0

#### "Content-based" height for blocks, floats and inline-blocks

When *height=auto* blocks, floats and inline-blocks compute the height based on:
 - width constraint
 - content
 - interpreted as content-based height 

The spec actually has two "content-based" height calculations:
 - floats, inline-block elements and block-level elements in normal flow AND **overflow<>visible**
    - includes floating descendants 
 - block-level elements in normal flow AND **overflow=visible** 
    - ignores floating descendants
    - this is the reason why, by default, a block-level box with only floating descendants has a height of 0 since visible is the default value for overflow.

#### Width calculations with margin and/or width set to auto

Width calculations need to consider:
 - padding inside a block
 - width of a block
 - how *box-sizing* is to be calculated
    - width + padding + border (the default)

*box-sizing* is a crucial property that determines how box-sizing is measured. For now we will just use the default. By default, the on-screen width of a CSS box is computed by adding padding and border to the relevant width or height value. 

Margin left and right can control horizontal placement.

Padding and border has to be specificaly set and there is no **auto** option.

Both *margin* and *width* can be set to **auto**.

#### Width calculation, Block-level box

Block-level block, width: auto, margin: auto
 - [block-level, width: auto, margin: auto](https://jsfiddle.net/SwannSG/umwzrhxg/)
 - width = containing element
 - margin = 0 (defaults to zero all round)

Block-level block, width constrained, margin: auto
 - [block-level, width: 100px, margin: auto](https://jsfiddle.net/SwannSG/umwzrhxg/1/)
 - width = width
 - margin-left = margin-right
 - block is centered
 - text wraps inside *content area*
 - can be multiple line boxes

Block-level block, width constrained, marginileft: auto, margin-right: specified
- [block-level, width: 100px, margin-left: auto, margin-right: 5px](https://jsfiddle.net/SwannSG/umwzrhxg/2/)
- width = width
- margin-left = enough space to maintain margin-right and width constraints

#### Width calculations: floating blocks and inline-block elements (shrink-to-fit)

*margin:auto* always sets margins to 0.

[inline-block, unconstrained](https://jsfiddle.net/SwannSG/umwzrhxg/4/)
- inline-block takes up as much horizontal space as neccessary to place text

[inline-block, width constrained](https://jsfiddle.net/SwannSG/umwzrhxg/5/)
- inline-block text now has a line break for contents to fit on multiple lines within constraints
- vertical height of inline-block is adjusted accordingly

[inline-block, width constrained, no suitable line break](https://jsfiddle.net/SwannSG/umwzrhxg/6/)
- inline-block overflows its' containing element

## Additional properties that influence positioning

 - Margin collapsing affects adjacent margins so that only the larger of two margins is applied.
 - Negative margins. Negative values (e.g. -10px) are allowed for margins, and these can be used to position content.
 - Overflow. When the content inside a container box is larger than its parent or positioned at an offset that is beyond the parent's box, it overflows. The overflow property controls how this is handled.
 - max-width, max-height, min-width, min-height, Width and height can be further constrained by using the max-width, max-height, min-width and min-height properties.
 - Pseudo-elements allow additional elements to be generated within the selected element from CSS. They are often used to generate additional content for layout or to provide a particular visual appearance.
 - Stacking contexts and rendering order determine the z-axis rendering of boxes.

### Margin Collapsing

When two vertical margins (margin-top, margin-bottom) abutt, margin collapsing occurs.

The smaller of the abutting margins is ignored, and only the largest margin is used.

The margins do NOT add up cummulatively.

There are several rules which restrict which margins may collapse. The four high-level rules are:
 - Only vertical margins can collapse
    - horizontal margins never collapse.
 - only block-level box vertical margins collapse
    - floats, absolutely positioned elements, and inline-block elements margins never collapse.
    - Inline-level boxes don't have vertical margins, so they don't collapse either.
 - only margins from boxes that participate in the same block formatting context can collapse.
    - block formatting contexts are not established by block boxes with overflow: visible (the default value
    - -n this sense, margin collapsing behaves similar to floats, which only affect line boxes of block-level boxes in the same formatting context.
 - only margins that are considered to be adjoining can collapse.


### What establishes a new *block formatting context* and impact on layout

A block formatting context is a box that satisfies at least one of the following conditions:
 - is a float
    - *float:none* is not a block formatting context
 - the value of *position* is not **static** or **relative**
 - the value of display is *table-cell, table-caption, inline-block, flex*, or *inline-flex*
 - the value of *overflow* is not **visible**
    - example *overflow:hidden*, *overflow:scroll*

### *Block format context* impact on layout:

When a parent container, has Block Format Context, the child elements including their margins have to stay inside the container. Margins from child elements may not extend outside a parent container with BFC.   

[block-level, no BFC, margin collapse](https://jsfiddle.net/SwannSG/0dy7e2m5/)
- each div is not a BFC, the p(ara) top and bottom margins push out beyond the div.

[block-level, BFC, margin no collapse](https://jsfiddle.net/SwannSG/ecvxk5w5/)
- when we make each div a BFC, the p(ara) top and bottom margins are contained inside the BFC div.

[float block, BFC (default), margin no collapse](https://jsfiddle.net/SwannSG/bzpe0bbz/)
- a float block is always BFC

[BFC siblings, margin collapse](https://jsfiddle.net/SwannSG/rcsecnhm/)
- margins between sibling block that are BFC still collapse
- anonymous inline block has no margins  

[No BFC, but borders, no margin collapse](https://jsfiddle.net/SwannSG/qqj83uep/)
- only abutting vertical margins can collapse
    - if we have a top and/or bottom border the margins cannot be abutting, hence no margin collapse 

### Negative Margins

Negative margins shift the position where rendering happens, which can be used to reposition content.

The problem is that negative margins do not properly interact with the content they overlap, leading to hard-to-debug issues where content unexpectedly overlaps each other.

[Negative margin example](https://jsfiddle.net/SwannSG/q63nruru/)

### Overflow

Overflow occurs when a child element is either positioned outside its parent element, or the child element does not fit inside the dimensions of its parent. The *overflow* property controls how the portion of the child that overflows is rendered:

 - not applicable to *inline-block* elements
 - default value is **visible**
    - any other value causes a Block Format Context
 - other values are **hidden, scroll, auto**

### max-width, max-height, min-width and min-height

Width and height can be set explicitly (not for inline-blocks).

Constraints can be added.

Explicit units like px or as a percentage of parent width or height.

max-width, min-width
 - explicit length
 - percentage of containing block's width
     - if negative width, value is zero
     - if containing block's width depends on this element's width, resulting layout is undefined   
 - max-width = none, no limit on box width    

max-height, min-height
 - explicit length
 - percentage of containing block's height
     - if containing block's height not specified explicitly && element not absolutely positioned then
         - min-height=0, max-height=none
 - max-height = none, no limit on box height


[height](https://jsfiddle.net/SwannSG/o72vLav1/)

[min-height](https://jsfiddle.net/SwannSG/fdj8294u/)






