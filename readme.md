#Session 13

Final session

Promises

##Type Ahead

See completed sample. Note city AND state name matching.

###fetch()

`fetch()` -  No call back function but returns a promise. We DO NOT use a callback function:

```js
fetch(endpoint, function(data){
  console.log(data)
})
```

Instead: 

```
const cities = [];
const promise = fetch(endpoint)
console.log(promise)
```

 Doesn't return the data but a promise.

```js
const cities = [];
fetch(endpoint).then(blob => console.log(blob))
```

 The browser doesn't know what kind of data blob is. (Image, audio, etc.). Note the prototypes - json

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  // returns another promise
  .then(data => console.log(data))
```

Returns the complete array.

We could try:

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  // returns another promise
  .then(data => cities = data)
```

But this wants to overwrite a constant variable. 

Use .push and we end up with an array inside an array:

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  .then(data => cities.push(data))
```

We want everything to have its own item in the array.

In the console:

`> cities.push(1,2,3,4)`

Let's use a spread operator.

###Spread Operator

Takes every element from an array (or any iterable) and applies it to a containing array.

See: `micro/spread-operators.js`

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  .then(data => cities.push(...data))
```

###Filtering the array

We need to create a function that will filter the array to a subset.

See: `micro/arrays-filter.js`

```js
function findMatches(wordToMatch, cities){
  return cities.filter(place => {
    // does the city or state match wordToMatch?
  })
}
```

The match() method searches a string for a match against a regular expression, and returns the matches, as an Array object.

e.g. we cannot do this:

```js
function findMatches(wordToMatch, cities){
  return cities.filter(place => {
    return place.city.match(/wordToMatch/i)
  });
}
```
Putting a variable into a regex:

```js
function findMatches(wordToMatch, cities){
  return cities.filter(place => {
    const regex = new RegExp(wordToMatch, 'gi'); //global / entire string, case insensitive
    return place.city.match(regex) || place.state.match(regex);
  });
}
```

`> findMatches('new', cities)`

###Displaying the matches

Create our function, variables for dom elements and bind the `change` event to the search input:

```js
function displayMatches(){
  console.log(this.value);
}

const searchInput = document.querySelector('.search');
const suggestions = document.querySelector('.suggestions');

searchInput.addEventListener('change', displayMatches);
```

Only displays when clicked outside, add:

```js
searchInput.addEventListener('keyup', displayMatches);
```

Call findMatches and pass our information:

```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  console.log(matchArray);
}
```

####Use mapping

Takes in an array, does something with it, then returns a new array with (unlike `filter()`) the same length. 

See `micro/arrays-filter.js`

Create an html snippet from the matches:

```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
    return `
      <li>
        <span class="name">${place.city}, ${place.state}</span>
        <span class="population">${place.population)</span>
      </li>
    `;
  })
}
```

Add it to the dom:

```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
    return `
      <li>
        <span class="name">${place.city}, ${place.state}</span>
        <span class="population">${place.population}</span>
      </li>
    `;
  });
  suggestions.innerHTML = html;
}
```

Join the array elements together:

```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
    return `
      <li>
        <span class="name">${place.city}, ${place.state}</span>
        <span class="population">${place.population}</span>
      </li>
    `;
  }).join('');
  suggestions.innerHTML = html;
}
```

##Formatting

The highlighting - use `replace()` string method. (Docs)[http://www.w3schools.com/jsref/jsref_replace.asp]

```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
  //below
    const regex = new RegExp(this.value, 'gi');
    const cityName = place.city.replace(regex, `<span class="hl">${this.value}</span>`);
    return `
      <li>
        <span class="name">${cityName}, ${place.state}</span>
        <span class="population">${place.population}</span>
      </li>
    `;
  }).join('');
  suggestions.innerHTML = html;
}
```

Do the same for state:


```js
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
  //below
    const regex = new RegExp(this.value, 'gi');
    const stateName = place.state.replace(regex, `<span class="hl">${this.value}</span>`);
    const cityName = place.city.replace(regex, `<span class="hl">${this.value}</span>`);
    return `
      <li>
        <span class="name">${cityName}, ${stateName}</span>
        <span class="population">${place.population}</span>
      </li>
    `;
  }).join('');
  suggestions.innerHTML = html;
}
```

Add a comma to the population (stack overflow):

```js
function numberWithCommas(x) {
  return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
}
```

```
<span class="population">${numberWithCommas(place.population)}</span>
```









































