# Handling Highlight Effects

### TL;DR

Use this following paradigm to add highlight effects.

Say you have a bunch of text elements that are encoding some keywords, and now you have `highlighted_keywords` which stores all the keywords that you want to be highlighted:

```javascript
  //
  // somewhere in your js 
  //
  svg.selectAll("text")
    .classed("label-highlight", false)
    .classed("label-not-highlight", true)
    .filter((d) => highlighted_keywords.includes(d)))
    .classed("label-not-highlight", false)
    .classed("label-highlight", true);
  
  // ...
  // then in the css file, define:
  // 
  .label-highlight {
      font-weight: 700;
      ... (and whatever style you want)
  }
  .label-not-highlight {
      opacity: 0.1;
      ...
  }
```

### Why this is a good practice

Let's take a look at the most basic way of adding highlight effects:

<pre class="language-javascript"><code class="lang-javascript"><strong>// The text labels are highlighted when hovered, 
</strong><strong>// and the others are dimmed
</strong>svg.selectAll("text")
    .data(keywords)
    .join("text")
        .attr(...)
        .on("mouseover", function() { 
            // this is not good!
            d3.selectAll("text").attr("opacity", 0.1)
            d3.select(this)
                .attr("opacity", 1)
                .attr("font-weight", "700")
        })
        .on("mouseout", function() {
            // this is not good!
            d3.selectAll("text")
                .attr("font-weight", "500")
                .attr("opacity", 1)    
        })
</code></pre>

#### Problem #1: What's the default value?&#x20;

The font weight is set to `700` as the highlight effect, sure. Now, what's the **default** value of font weight? You can't reset it back in `mouseout` if you don't know that.  If the effect involves many attributes, it would be a waste of time to find out all of their default values.

#### Problem #2: Which state to return to?

This problem still is with the `mouseout`handler, and has two layers:&#x20;

First, what are the attributes that should be reset? You have to always make sure that you did not miss any attributes, **by maunally checking.** In this toy example, the `mouseover` and `mouseout` handlers are short and close to each other, so it's easy. But sometimes, the handlers might be long and far apart. It is cognitively demanding to make sure of the consistency.

Second, what is you have **multiple highlight effects**? This can easily happen if you have some filter functionality, plus the hover effects. For example:

<figure><img src="../../.gitbook/assets/Group 31 (2).jpg" alt=""><figcaption><p>The scatter plot has two highlight effects. First, users highlight all the orange circles by making other circles transparent. Then, users can hover over any highlighted circle, which would be enlarged as the second highlight effect.</p></figcaption></figure>

The above is a common filter+hover highlight effect. Now, consider the implementation of `mouseout` on the circle: which **state** should it be reset to? To account for the correct state to reset, the `mouseout` function would become more complicated and harder to maintain, leading you to a **debugging hell.**&#x20;

#### Summary

Now back to our initial question: _why is it a good practice to use classes to add highlight effects?_

And the answer is _We let CSS handle the default value, and the state to return to._&#x20;



#### Author

Sam, ytlee@ucdavis.edu
