chbspassgen
===========

#####*generate strong passwords you can easily remember, with lots of entropy and stuff*

 Copyright (C) 2014 Gael Abadin<br/>
 License: [MIT Expat][1]<br />
 [![Code Climate](https://codeclimate.com/github/elcodedocle/chbspassgen.png)](https://codeclimate.com/github/elcodedocle/chbspassgen)
 
![chbspassgen password generator test site snapshot with default settings](http://i.imgur.com/ahbMwZo.png "This is how chbspassgen's test web app looks like. Check it out on https://synapp.info/password-generator ;-) )")

 
### Motivation

I wanted to build a class able to pick random words from a dictionary in a safe way, 
so they could be used as passwords ([Correct, horse. That's a battery staple][2]). 

This my first attempt at properly working with namespaces, interfaces and abstract classes in PHP, 
plus something close to "meta programming" (In my own words; I suck at it and between that and the 
abandoned intention of TDD implementing unit tests before the actual code (yeah, I know TDD saves 
time and increases quality, but I'm too lazy for that unless I am being paid by the hour, which is 
an interesting contradiction...).

Anyway, somehow I ended up turning a perfectly fine 200 lines script I had crafted with care into 
this monstruosity with phpdoc blocks that will make you want to poke your eyes out (I'll fix those 
blocks, I promise...)

### How to use

Basic usage:

```php
require_once 'cryptosecureprng/CryptoSecurePRNG.php';
require_once 'dictionary/DictionaryInterface.php';
require_once 'dictionary/Dictionary.php';
require_once 'PasswordGeneratorAbstract.php';
require_once 'PasswordGenerator.php';

$passwordGenerator = new synapp\info\tools\passwordgenerator\PasswordGenerator();  // This assumes a dictionary generated from a source on a file named 'top10000.txt'

$password = $passwordGenerator->generatePassword(); // Generates a password with default settings

$entropy = $passwordGenerator->getEntropy(); // entropy of the last generated password (won't change unless you change settings)

```

That's it. Pretty easy, huh? There are many parameters you can tinker with, though:

```php

// Here is a quick debrief on the class constructor parameters (See the phpdoc blocks for more info):

$passwordGenerator = new synapp\info\tools\passwordgenerator\PasswordGenerator(
  // the dictionary 
  // (null defaults to new Dictionary($this->defaultDictionaryFilename,$minReadWordsWordSize))
  // with $this->defaultDictionaryFilename set to 'top10000.txt'
  $dictionary = null, 
  // set the level of entropy used when none is explicitly specified on the generatePassword() call
  // (null defaults to $this->defaultLevel, set to 2)
  $level = 2, 
  // a string of unique chars from where to randomly choose the password separator 
  // (null defaults to $this->defaultSeparator, set to ' ')
  $separators = ' ', 
  // an ascending ordered array of ints containing the minimum entropies for each level
  // (null defaults to $this->defaultMinEntropies, set to array(64,80,112,128))
  $minEntropies = array(64,80,112,128), 
  // boolean, whether to use selected random variations on the password words to increase entropy 
  // defaults to true
  $useVariations = false, 
  // (array of booleans which activate random variations on the words, increasing entropy. 
  // Valid keys: 'allcaps', 'capitalize', 'punctuate', 'addslashes'). Use null for defaults.
  $variations = null, 
  // Minimum length of the words used to create the password
  // (null defaults to $this->defaultMinWordSize, set to 4)
  $minWordSize = 4, 
  // Minimum length of the words read from the dictionary source
  // (null defaults to $this->defaultMinReadWordsWordSize, set to 4)
  $minReadWordsWordSize = 4, //(minimum length of the words read from the Dictionary source)
  // the pseudoaleatory random generator (new CryptoSecurePRNG() by default)
  $prng = new synapp\info\tools\passwordgenerator\cryptosecureprng\CryptoSecurePRNG() 
);

// generatePassword method takes almost the same parameters as the contructor:

$password = $passwordGenerator->generatePassword(
  $dictionary = null,     // use null to skip parameters (set to the current setting)
  $minEntropy = null,     // and here too, and anywhere else when you want to
  $level = 1,             // specify further parameters like this one
  $separators = '_ -',    // and this one
  $useVariations = true,  // and this one
  $variations = array(    // and this one too 
    'allcaps'=>true,      // (BTW, this system also works in the constructor, where you can
    'capitalize'=>true    // specify some params and leave others to their defaults using null)
  ) 
);


// getEntropy can return a pretty accurate estimate of the entropy of the last generated 
// password, but can also be given a password and a set of parameters to extract its entropy
// (the interface I came up with is crazy weird, so I won't even bother to explain it. I'll 
// just rebuild it in a more intuitive and practical way as soon as I can...)

$entropy = $passwordGenerator->getEntropy(
 $password, 
 $dictionary = null, 
 $variationsCount = null, 
 $lastOrSeparator = true, 
 $separatorsCount = null
);


```

Check the code (or generate the docs using phpdocumentor) if you want more info on tweaks and available parameters.

### Web app

There is also available a little test web app ([passgenController.php][3], [passgenClientController.js][4] and [password_generator.html][5]) 
you can load by uploading all the files to a public folder on your web server and pointing your browser to password_generator.html

Here is a demo: https://synapp.info/password-generator

### Web app pitfalls 

Well, the web app is just a proof of concept and it was designed in the simplest possible way. That means efficiency or performance were not taken on account at all. A quick benchmark shows an Amazon EC2 micro instance is only able to handle a maximum of about 10.2 requests per second, mainly on account of the dictionary class being instantiated on each and every request and making a system call to read the words file on each and every instantiation (one would think that's the kind of thing to be automagically optimized on the fly in 2014, but whatever... Not my first deception about living in the 21st century, after all). Anyhow, if you want to build a production ready web app using chbspassgen, check out this for a few suggestions and more info on how to work around this issue: http://stackoverflow.com/questions/23846611/oo-php-service-performance

### Acks

Caffeine. (Seriously, I did this project on a single night of insomnia. Stupid coffee and tea are to blame.)

[Peter Norvig](http://norvig.com/) for being such an awesome proffesor (If you haven't checked out his [Stanford University course on AI](https://www.udacity.com/course/cs271) you ABSOLUTELY MUST) and publishing the [compilation of the 1/3 million most frequent English words](http://norvig.com/ngrams/count_1w.txt) on the [natural language corpus data ](http://norvig.com/ngrams/) from where the [word list](https://github.com/elcodedocle/chbspassgen/blob/master/top10000.txt) used by the default dictionary source for this project has been derived (and thanks to [Josh Kaufman](https://github.com/worldlywisdom) too for the tip).

[Randall Munroe](http://xkcd.com). He is funny, smart, and inspiring. Thanks, Mr. Munroe.

And that's all for now, folks. If you like this project, feel free to buy me a beer ;-)

bitcoin: 1A7rSMddjwPbxFW71ZD724YaQLa8HCAJTT 

dogecoin: DAQBLYtCjBnZ8eGdcaR7kE517Ew5tptUeW 

paypal: http://goo.gl/RQVD5u


Have fun.-

[1]: https://raw.githubusercontent.com/elcodedocle/chbspassgen/master/LICENSE
[2]: http://xkcd.com/936/
[3]: https://github.com/elcodedocle/chbspassgen/blob/master/webapp/passgenController.php
[4]: https://github.com/elcodedocle/chbspassgen/blob/master/webapp/passgenClientController.js
[5]: https://github.com/elcodedocle/chbspassgen/blob/master/webapp/password_generator.html
