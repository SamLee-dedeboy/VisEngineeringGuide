## Growing Scroll Region
Here is a common problem I ran into in responsive layouts:
```html
<div class="grand-parent-with-whatever-height">
    <div class="parent" style="height: 100%; display: flex; flex-direction: column">
        <div class="child"> child 1 </div>
        <div class="child"> scroll region </div>
    </div>
</div>
```
I have a **responsive** parent div whose height is decided by its own parent. Then I have two children in the div, the first is of certain height, and the second is a scroll region that I want to fill up the remaining space in parent div. The intuition is simply to use `flex` and `overflow-y`, but surprisingly this work not work.

<div style="display: flex; column-gap: 5px;">
<div style="display: flex; flex-direction: column; width: 100%">
For example, in the figure, the upper part is a utility panel with some buttons and controllers, then at the bottom I have a `document list` that should take up the remaining space.
We could use this simple html to demonstrate the  structure:

```html
<div class="container">
    <div class="control-panel"/>
    <div class="scroll-panel"/> 
</div>
```
And our previous intuition can be expressed as:
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

This css will **NOT** work because `overflow-y: scroll` requires the element to have a `max-height` specified. However, we have a *dynamic* max height here: the max-height of the scroll panel is dependent on how much space is left in the container.

</div>

<div style="">
    <img src="./imgs/example-1.png" alt="drawing" />
</div>
</div>

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