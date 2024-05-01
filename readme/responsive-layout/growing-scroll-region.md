# Responsive Scroll Region

### Growing Scroll Region

Here is a common problem I ran into in responsive layouts: I have a **responsive** parent div whose height is decided by its parent (`grandparent`). Then I have two children in the div, the first is of certain height (`child 1`), and the second is a `scroll region` that I want to fill up the remaining space in parent div. Like so:

```html
<div class="grandparent-with-whatever-height">
    <div class="parent" style="height: 100%; display: flex; flex-direction: column">
        <div class="child"> child 1 </div>
        <div class="child"> scroll region </div>
    </div>
</div>
```

The intuition is simply to use `flex` and `overflow-y`, but surprisingly this will not work.

![drawing](../../responsive-layout/imgs/example-1.png)

For example, in the figure, the upper part is a utility panel with some buttons and controllers, then at the bottom, I have a `document list` that should take up the remaining space. We could use this simple HTML to demonstrate the structure:

```html
<div class="container">
    <div class="control-panel"/>
    <div class="scroll-panel"/> 
</div>
```

Our previous intuition can be expressed as:

```css
.container {
    display: flex;
    flex-direction: column;
}
.control-panel {
    /* do nothing */
}
.scroll-panel {
    overflow-y: scroll;
}
```

This CSS will **NOT** work because `overflow-y: scroll` requires the element to have a `max-height` specified. However, we have a _dynamic_ max height here: the max height of the scroll panel is dependent on how much space is left in the container. You may want to add `max-height: 100%` to `scroll-panel`, I assure you this will not work.

### Solution(s)

There are two solutions to this problem, one is intuitive but with more lines of code, and the other is weird but short (and possibly a hack in CSS?)



### The intuitive solution

The trick is to implicitly set `max-height` with `position: absolute`. Here's how:

```css
.container {
    display: flex;
    flex-direction: column;
}
.control-panel {
    /* do nothing */
}
.scroll-panel {
    position: absolute;
    overflow-y: scroll;
    top: 0;
    bottom: 0;
    /* left and right is not necessary in vertical scroll */
    /* but it will help set the width */
    left: 0; 
    right: 0;
}
```

Basically, we use `position: absolute` combined with `top: 0` and `bottom: 0` to tell the browser what the max height of the scroll panel is. I find this solution to be logical and I always prefer this solution, even though it introduces more lines of code.

### The weird (but short) solution

The weird one is surprisingly simple:  make the scroll panel grow like usual and add a **default height** to the scroll panel like this:

```css
.container {
    display: flex;
    flex-direction: column;
}
.control-panel {
    /* do nothing */
}
.scroll-panel {
    flex-grow: 1;
    overflow-y: scroll;
    height: 1px; /* It doesn't matter how much the height is */
}
```

This solution does not make any sense at all: the `height: 1px` does not actually set the scroll panel to be 1px high, but it works. It is likely that this solution exploits some CSS bugs, therefore note that **it may not work in the future.**

### Author

Sam, ytlee@ucdavis.edu
