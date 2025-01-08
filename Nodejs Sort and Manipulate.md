In the JavaScript file, you have a program that performs a GET request on the route `https://coderbyte.com/api/challenges/json/wizard-list` and then sort the object keys alphabetically. However, the sorting should be case-insensitive, and the original data structure should be preserved (e.g., arrays should remain arrays, objects should remain objects).

Next, remove any duplicate objects from arrays. Two objects are considered duplicates if they have the same keys and values in the same order. Although JavaScript objects don't inherently maintain key order, key order should be considered for this challenge (using something like a Set). Only the first occurrence should be preserved when an array contains duplicate objects.

Finally, remove any object properties with all values set to an empty string, null, or undefined, and console log an array of the modified objects as a string.

Example Input:
```
[{"name":"John","age":30,"city":"New York","country":"USA","Hobbies":["reading","swimming","hiking","swimming"],"occupation":"programmer","favorite_foods":{"Breakfast":"pancakes","Lunch":"","dinner":"pasta","Snack":""},"gear":{"type":"","material":"","color":null},"affiliations":["","",""],"friends":[{"name":"Jane","age":28,"city":"New York","country":"USA","occupation":null},{"name":"Tom","age":32,"city":"London","country":"UK","occupation":"teacher"},{"name":"Jane","age":28,"city":"New York","country":"USA","occupation":null}]}]
```

Example Output:
```
[{"age":30,"city":"New York","country":"USA","favorite_foods":{"Breakfast":"pancakes","dinner":"pasta"},"friends":[{"age":28,"city":"New York","country":"USA","name":"Jane"},{"age":32,"city":"London","country":"UK","name":"Tom","occupation":"teacher"}],"gear":{},"Hobbies":["reading","swimming","hiking"],"name":"John","occupation":"programmer"}]
```

-----

My Code:

```
const https = require('https');
const _ = require('lodash'); 

function caseInsensitiveSort(a, b) {
  return a.toLowerCase().localeCompare(b.toLowerCase());
}

function process(dataString) {
  let data = JSON.parse(dataString);
  data = data.map(element => processStep(element));
  return JSON.stringify(data);
}

function processStep(value) {
  if(isObject(value)){
    let orderedKeys = _.keys(value).sort(caseInsensitiveSort)
    let outputObject = {}
    _.each(orderedKeys, (orderedKey) => {
      if(_.isArray(value[orderedKey])) {
        value[orderedKey] = _.compact(_.map(value[orderedKey], processStep))
        if(_.isEmpty(value[orderedKey])) return;
        value[orderedKey] = _.uniqWith(value[orderedKey], _.isEqual)
      }
      
      if(isObject(value[orderedKey])){
        value[orderedKey] = processStep(value[orderedKey])
      }

      if(value[orderedKey] == null || value[orderedKey] == "") return
      outputObject[orderedKey] = value[orderedKey];
    });
    if(_.isEmpty(value)) return;
    return outputObject;
  }
  return value;
}

function isObject(value) {
  return _.isObject(value) && !_.isArray(value) && !_.isEmpty(value)
}

https.get('https://coderbyte.com/api/challenges/json/wizard-list', (resp) => {

  let wizardListRequestString = '';

  resp.on('data', (data) => {
    wizardListRequestString += data;
  })

  resp.on('end', () => {
    console.log(process(wizardListRequestString))
  })
});

```
