#Session 13

Final session


##Type Ahead

###fetch()

`fetch()` -  No call back function but a promise.

```
const promise = fetch(endpoint)
console.log(promise)
```

 Doesn't return the data but a promise.

```js
fetch(endpoint).then(blob => console.log(blob))
```

 We don't know what kind of data blob is. (Image, audio, etc.). Note the prototypes.

```js
fetch(endpoint).then(blob => JSON.parse(blob))
```

 Doesn't work because the data is not yet json.

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  // returns another promise
  .then(data => console.log(data))
```

Returns the complete array.

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
  // returns another promise
  .then(data => cities.push(data))
```

In the console:

> cities.push[1,2,3,4]

Let's use a spread operator.

###Spread Operator

Takes every element from an array (or any iterable) and applies it to a containing array.

See: `micro/spread-operators.js`

```js
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  // returns another promise
  .then(data => cities.push(...data))
```

###Filtering the array

See: `micro/arrays-filter.js`

```js
function findMatches(wordToMatch, cities){
  return cities.filter()
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

> findMatches('west', cities)

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

Takes in an array, does something with it, then returns a new array with (unlike filter()) the same length. 

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


