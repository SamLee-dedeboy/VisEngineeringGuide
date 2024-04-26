## RGB to RGBA
In weird cases I use this function to set opacity on an RGB value that makes it RGBA.
```javascript
// hex is a string (e.g., #8c1edc), alpha is between 0 and 1
// returns RGBA hex string
function setOpacity(hex, alpha) {
    return `${hex}${Math.floor(alpha * 255).toString(16).padStart(2, '0')}`
}
```

## Author
Sam, ytlee@ucdavis.edu