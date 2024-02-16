## RGBA to RGB with same color
In weird cases I use this function to transform an RGBA value to an RGB value with the same color:
```javascript
// hex is like #8c1edc, alpha is between 0 and 1
function rgba_to_rgb(hex, alpha) {
    return `${hex}${Math.floor(alpha * 255).toString(16).padStart(2, '0')}`
}
```

## Author
Sam, ytlee@ucdavis.edu