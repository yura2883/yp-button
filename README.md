# yp-button

[![npm](https://img.shields.io/npm/v/@yup80/yp-button.svg)](https://www.npmjs.com/package/@yup80/yp-button)
[![npm](https://img.shields.io/npm/dt/@yup80/yp-button.svg)](https://www.npmjs.com/package/@yup80/yp-button)

A Vue.js component for buttons with beautiful ripple effects and theme support

## Requires
<ul>
    <li>less</li>
    <li>less-loader</li>
</ul>

## Installation

npm install @Yup80/yp-button

## Using in Vue.js component
```` vue
<template>
    <div class="root">
        <ypBtn>Default (Blue) button</ypBtn>
        <ypBtn class="ahtung">Alternative (Red) button</ypBtn>
        <ypBtn class="link">Like a link (no bgcolor) button</ypBtn>
    </div>
<template>


<script>
import ypBtn from '@Yup80/yp-button'
// ...
export default {
    components: {ypBtn}
}
</script>

````

## Properties
<table>
    <th>Prop</th>
    <th>Type</th>
    <th>Description</th>
    <tbody>
        <tr>
            <td>disabled</td>
            <td>Boolean</td>
            <td>Disabled behavior, styling with "disabled" class</td>
        </tr>
        <tr>
            <td>inverted</td>
            <td>Boolean</td>
            <td>For night themes (lihter ripple)</td>
        </tr>            
    </tbody>
</table>

## Theme composing

```` less
/* Add myTheme class to container */

.myTheme .yp-button_button {
    // default button style
    background-color: #bbb;
    color: #000;
}

.myTheme .yp-button.ahtung>.yp-button_button {
    // alternative button style 
    background-color: #FF3636;
}

.myTheme .yp-button.disabled>.yp-button_button {
    // disabled button style  
    color: green;
}

````

## Demo
For more examples browse and clone github project to see more examples: 

https://github.com/yura2883/yp-button

