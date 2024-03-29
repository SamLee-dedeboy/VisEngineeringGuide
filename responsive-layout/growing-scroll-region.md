## Growing Scroll Region
Here is a common problem I ran into in responsive layouts:
I have a **responsive** parent div whose height is decided by its parent (`grandparent`). Then I have two children in the div, the first is of certain height (`child 1`), and the second is a `scroll region` that I want to fill up the remaining space in parent div. Like so:

```html
<div class="grandparent-with-whatever-height">
    <div class="parent" style="height: 100%; display: flex; flex-direction: column">
        <div class="child"> child 1 </div>
        <div class="child"> scroll region </div>
    </div>
</div>
```
The intuition is simply to use `flex` and `overflow-y`, but surprisingly this will not work.

<img src="./imgs/example-1.png" alt="drawing" align="right" width="40%" height="auto" />

For example, in the figure, the upper part is a utility panel with some buttons and controllers, then at the bottom, I have a `document list` that should take up the remaining space.
We could use this simple HTML to demonstrate the structure:


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

This CSS will **NOT** work because `overflow-y: scroll` requires the element to have a `max-height` specified. However, we have a *dynamic* max height here: the max height of the scroll panel is dependent on how much space is left in the container.
You may want to add `max-height: 100%` to `scroll-panel`, I assure you this will not work.


## Solution
The trick is to implicitly set `max-height` with `position: absolute`.
Here's how:

First, we need to identify the element that *could* overflow, which will be wrapped inside `scroll-panel`
```html
<div class="container">
    <div class="control-panel"/>
    <div class="scroll-panel">
        <div> a div with very large height </div> 
    </div>
</div>
```
Then we use `position: absolute` combined with `top: 0` and `bottom: 0` to make `scroll-panel` scrollable.
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
## Author
Sam, ytlee@ucdavis.edu