---
description: This page documents how to create a button in svg.
---

# SVG button

### Implementation:

The below code creates a button in svg with some animated effects.

Check out the effects in this [CodePen](https://codepen.io/Sam-YuTe-Lee/pen/KKYOdzx):

```javascript
function add_utility_button({
    parent, // a d3 selection of parent element to append to
    class_name, // utility button class name
    activated_color,
    deactivated_color,
    activated_text_color, 
    deactivated_text_color,
    text, // label
    x, y,// top let corner of the button
    width,
    height,
    onClick, // handler
    stateless = false, // if stateless is true, the button will 
                       // return to default color once clicked;
                       // otherwise it will stay at activated_color
  }) {
    const utility_button = parent.append("g").attr("class", class_name);
    const animation_scale_factor = 1.1;
    // button rect and handlers
    utility_button
      .append("rect")
      .attr("class", "utility-button-rect")
      .attr("x", x)
      .attr("y", y)
      .attr("width", width)
      .attr("height", height)
      .attr("fill", deactivated_color)
      .attr("rx", "0.2%")
      .attr("stroke", "gray")
      .attr("cursor", "pointer")
      .on("mouseover", function () {
        d3.select(this).attr("stroke-width", 2)
        if(stateless) return;
        const activated = d3.select(this).attr("fill") === activated_color;
        d3.select(this).attr("fill", activated? activated_color: "lightgray");
      })
      .on("mouseout", function () {
        d3.select(this)
          .attr("stroke-width", 1)
          .attr("fill", () => {
            if(stateless) return deactivated_color;
            const activated = d3.select(this).attr("fill") === activated_color;
            return activated ? activated_color : deactivated_color
        });
      })
      .on("click", function () {
        onClick();
        const button = d3.select(this);
        const activated = button.attr("fill") === activated_color;
        button.attr("fill", activated ? deactivated_color : activated_color);
        button
          .transition()
          .duration(200)
          .attr("x", function () {
            return x - (width * (animation_scale_factor - 1)) / 2;
          })
          .attr("y", function () {
            return y - (height * (animation_scale_factor - 1)) / 2;
          })
          .attr("width", function () {
            return width * animation_scale_factor;
          })
          .attr("height", function () {
            return height * animation_scale_factor;
          })
          .transition()
          .duration(100)
          .attr("x", x)
          .attr("y", y)
          .attr("width", width)
          .attr("height", height)
          .on("end", () => {
            if (stateless) button.attr("fill", deactivated_color);
          });
        if (!stateless) {
          d3.select(this.parentNode)
            .select("text")
            .attr("fill",activated ? deactivated_text_color : activated_text_color);
        }
      });
      
    // label
    utility_button
      .append("text")
      .attr("x", x + width / 2)
      .attr("y", y + height / 2)
      .attr("pointer-events", "none")
      .text(text)
      .attr("fill", stateless? activated_text_color: deactivated_text_color)
      .attr("dominant-baseline", "middle")
      .attr("text-anchor", "middle");
  }

```

### Usage:

```javascript
const utility_container = d3.select("...")
const utility_button_attrs = {
    parent: utility_container,
    class_name: "utility-button-demo",
    activated_color: "rgb(187 247 208)",
    deactivated_color: "white",
    activated_text_color: "black",
    deactivated_text_color: "#aaaaaa",
    text: 'demo-button',
    x: 0,
    y: 0,
    width: 100,
    height: 20,
    onClick: () => {
        // ...
    },
    stateless: false
}
add_utility_button(utility_button_attrs)
```



### Author

Sam Yu-Te Lee, ytlee@ucdavis.edu
