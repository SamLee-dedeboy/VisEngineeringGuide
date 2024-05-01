---
description: Programmatically scroll an element into viewport
---

# Scroll Into View

```javascript
// input - element_id: string
// Scroll an element into view by its id
const element = document.getElementById(element_id);
element.scrollIntoView({ behavior: "smooth" });
// show a highlight effect
setTimeout(() => {
  console.log("The browser has (theoretically) finished scrolling");
  document.getElementById(element_id).classList.add("highlighted");
  setTimeout(() => {
    document.getElementById(element_id).classList.remove("highlighted");
  }, 1000);
}, 500);

// you can add any highlight effect you want with CSS
// for example: 
.highlighted {
  background-color: rgba(45, 45, 45, 0.2); /* a gray background */
  transition: background-color 0.5s ease;
}
```

What it would look like (for some reason the GIF is in slow-mo):&#x20;

<figure><img src="../../.gitbook/assets/Screen Recording 2024-04-30 at 22.52.18.gif" alt=""><figcaption></figcaption></figure>
