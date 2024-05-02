---
description: Here is a simple way to make ChatGPT-like text appearing effect
---

# ChatGPT-like text effect

### A pure javascript version:

```javascript
function make_delay(text) {
    let delayed_text = "";
    const steps = generate_steps(text);
    let messageBody = document.querySelector(".message-body");
    steps.forEach((step, i) => {
      setTimeout(function () {
        delayed_text += step;
        messageBody.innerText = delayed_text;
        if (messageBody)
          messageBody.scrollTop =
            messageBody.scrollHeight - messageBody.clientHeight;
      }, i * 30); // 30ms intervals
    });
    function generate_steps(text) {
      let steps = [];
      let i = 0;
      while (i < text.length) {
        const start = i;
        let end = i + 1;
        if (text[i] === "<") {
          while (text[end] !== ">") {
            end++;
          }
          end++;
        }
        steps.push(text.substring(start, end));
        i = end;
      }
      return steps;
    }
  }
```

### [See the effect in action in a CodePen](https://codepen.io/Sam-YuTe-Lee/pen/RwOmmjR)

### A svelte version&#x20;

(or similarly any other framework where you can bind a variable to HTML):

<pre class="language-javascript"><code class="lang-javascript"><strong>// Inside of a svelte file:
</strong><strong>&#x3C;script>
</strong><strong>let delayed_text; // a reactive variable
</strong>function make_delay(text) {
    delayed_text = "";
    const steps = generate_steps(text);
    let messageBody = document.querySelector(".bot-response");
    steps.forEach((step, i) => {
      setTimeout(function () {
        delayed_text += step;
        if (messageBody)
          messageBody.scrollTop =
            messageBody.scrollHeight - messageBody.clientHeight;
      }, i * 30);
    });
  function generate_steps(text) {
      let steps = [];
      let i = 0;
      while (i &#x3C; text.length) {
        const start = i;
        let end = i + 1;
        if (text[i] === "&#x3C;") {
          while (text[end] !== ">") {
            end++;
          }
          end++;
        }
        steps.push(text.substring(start, end));
        i = end;
      }
      return steps;
    }
  }
&#x3C;/script>

&#x3C;div class="message-body">{delayed_text}&#x3C;/div>
</code></pre>
