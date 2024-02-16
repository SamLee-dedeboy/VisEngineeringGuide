## Set opacity on RGB values
In weird cases I use this function to set opacity on an RGB value without using RGBA.
```javascript
// hex is a string (e.g., #8c1edc), alpha is between 0 and 1
// returns another RGB hex string with opacity applied
function setOpacity(hex, alpha) {
    return `${hex}${Math.floor(alpha * 255).toString(16).padStart(2, '0')}`
}
```

## Author
Sam, ytlee@ucdavis.edu