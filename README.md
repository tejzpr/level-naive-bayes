# leveldb-naive-bayes

Naive Bayes text classifier that runs on top of leveldb. Based on the [bayes](https://github.com/ttezel/bayes) module.
It returns log-probabilities.
[Log_probaility](https://en.wikipedia.org/wiki/Log_probability)

```
npm install leveldb-naive-bayes
```
[![NPM version][npm-image]][npm-url] [![Known Vulnerabilities](https://snyk.io/test/github/tejzpr/level-naive-bayes/badge.svg)](https://snyk.io/test/github/tejzpr/level-naive-bayes)

## Usage

``` js
var bayes = require('syzer-level-naive-bayes')

var nb = bayes(db) // where db is a levelup instance

nb.train('positive', 'amazing, awesome movie!! Yeah!! Oh boy.', function() {
  nb.train('positive', 'this is incredibly, amazing, perfect, great!', function() {
    nb.train('negative', 'terrible, shitty thing. Damn. Sucks!!', function() {
      nb.classify('awesome, cool, amazing!! Yay.', function(err, out) {
        console.log('category is '+out.category)
        console.log('probability is '+out.match)
      })
    })
  })
})
```

## API

#### `nb = bayes(db, [options])`

Creates a new instance. `db` should be a [levelup](https://github.com/rvagg/node-levelup).
Options include:

``` js
{
  tokenize: function(str) {
    return str.split(' ') // pass in custom tokenizer
  }
}
```

#### `nb.train(category, text, cb)`

Train the classifier with the given text for a category.
If the text is already tokenized pass in an array of tokens instead of text

#### `nb.classify(text, cb)`

Classify the given text into a category.
If the text is already tokenized pass in an array of tokens instead of text


#### `nb.trainAsync(category, text)`
Returns a promise of finished training, usage:

``` js
nb.trainAsync('positive', 'amazing, awesome movie!! Yeah!! Oh boy.').then(function () {
  return nb.classify('awesome, cool, amazing!! Yay.', function (err, out) {
    console.log('positive', out.category);
  })
})
```

#### `nb.classifyAsync(text)`
Returns a promise of finished classification
```js
var thingsToDo = [
  nb.trainAsync('positive', 'Sweet, this is incredibly, amazing, perfect, great!!'),
  nb.trainAsync('positive', 'amazing, awesome movie!! Yeah!! Oh boy.'),
  nb.trainAsync('negative', 'terrible, shitty thing. Damn. Sucks!!')
];

q.all(thingsToDo)
  .then(function () {
    return nb.classifyAsync('awesome, cool, amazing!! Yay.')
  })
  .then(function (category) {
    console.log(category, 'should be positive')
  })
```

#### `nb.classifyLabelsAsync(text)`
Returns a promise of finished classification, usage:

```js
var thingsToDo = [
  nb.trainAsync('positive', 'Sweet, this is incredibly, amazing, perfect, great!!'),
  nb.trainAsync('neutral', 'amazing, awesome movie!! Yeah!! Oh boy.'),
  nb.trainAsync('negative', 'terrible, shitty thing. Damn. Sucks!!')
];

q.all(thingsToDo)
  .then(() => (nb.classifyLabelsAsync('awesome, cool, amazing!! Yay.')))
  .then((labels) => {
    console.log(labels[0].label, 'should be neutral') 
    console.log(labels[0].logProb, 'should be logProbability')
    console.log(labels[1].label, 'should be second guess')
    console.log(labels[1].logProb, 'should be logProbability')
  })
```


### Tests
`npm test`

## License

MIT

[npm-image]: https://img.shields.io/npm/v/leveldb-naive-bayes.svg?style=flat-square
[npm-url]: https://npmjs.org/package/leveldb-naive-bayes
