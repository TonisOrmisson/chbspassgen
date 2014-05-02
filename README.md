chbspassgen
===========

#####*generate strong passwords you can remember, with lots of entropy and stuff*

 Copyright (C) 2014 Gael Abadin<br/>
 License: [MIT Expat][1]
 
### Motivation

I wanted to build a class able to pick random words from a dictionary in a safe way, 
so they could be used as passwords ([Correct, horse. That's a battery staple][2]). 

This my first attempt at properly working with namespaces, interfaces and abstract classes in PHP, 
plus something close to "meta programming" (In my own words; I suck at it and between that and the 
abandoned intention of TDD implementing unit tests before the actual code (yeah, I know TDD saves 
time and increases quality, but I'm too lazy for that unless I am being paid by the hour, which is 
an interesting contradiction...).

Anyway, somehow I ended up turning a perfectly fine 200 linesscript I had crafted with care into 
this monstruosity with phpdoc blocks that will make you want to poke your eyes out (I'll fix those 
blocks, I promise...)

### How to use

Basic usage:

```php
require_once 'CryptoSecurePRNG.php';
require_once 'DictionaryInterface.php';
require_once 'Dictionary.php';
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
  $dictionary = null, // the dictionary (null defaults to new Dictionary($this->defaultDictionaryFilename,$minReadWordsWordSize))
  $level = 2, // set the level of entropy used when none is explicitly specified on the generatePassword() call
  $separators = ' ', // a string of unique chars from where to randomly choose the password separator (defaults to $this->defaultSeparator, set to ' ')
  $minEntropies = array(64,80,112,128), // an ascending ordered array of ints containing the minimum entropies for each level
  $useVariations = false, // boolean, whether to use selected random variations on the password words to increase entropy
  $variations = null, //(array of booleans which activate random variations on the words, increasing entropy. Valid keys: 'allcaps', 'capitalize', 'punctuate', 'addslashes'). Use null for defaults.
  $minWordSize = 4, //(minimum length of the words used to create the password)
  $minReadWordsWordSize = 4, //(minimum length of the words read from the Dictionary source)
  $prng = new synapp\info\tools\CryptoSecurePRNG() // the pseudoaleatory random generator (CryptoSecurePRNG by default)
);

// generatePassword method takes almost the same parameters as the contructor:

$password = $passwordGenerator->generatePassword(
  $dictionary = null,     // use null to skip parameters (set to the current setting)
  $minEntropy = null,     // and here too, and anywhere else when you want to
  $level = 1,             // specify further parameters like this one
  $separators = '_ -',    // and this one
  $useVariations = true,  // and this one
  $variations = array(    // and this one too (also works in the constructor)
    'allcaps'=>true,
    'capitalize'=>true
  ) 
);


// getEntropy can return a pretty accurate estimate of the entropy of the last generated password, but can also be given a password and a set of parameters to extract its entropy
// (the interface I came up with is crazy weird, I'll rebuild it with a more intuitive and practical parameter list as soon as I can...)

$entropy = $passwordGenerator->getEntropy($password, $dictionary = null, $variationsCount = null, $lastOrSeparator = true, $separatorsCount = null);


```

Check the code (or generate the docs using phpdocumentor) if you want more info on tweaks and available parameters.

### Webapp

There is also available a little test web app ([passgenController.php][3], [passgenClientController.js][4] and [password_generator.html][5]) 
you can load by uploading all the files to a public folder on your web server and poiting your browser to password_generator.html

Here is a demo: https://synapp.info/password-generator

### Acks

Caffeine. (Seriously, I did this project on a single night of insomnia. Stupid coffee and tea are to blame.)

If you like this project, feel free to buy me a beer ;-)

bitcoin: 1A7rSMddjwPbxFW71ZD724YaQLa8HCAJTT 

dogecoin: DAQBLYtCjBnZ8eGdcaR7kE517Ew5tptUeW 

paypal: http://goo.gl/RQVD5u


Have fun.-

[1]: https://raw.githubusercontent.com/elcodedocle/chbspassgen/master/LICENSE
[2]: http://xkcd.com/936/
[3]: https://github.com/elcodedocle/chbspassgen/blob/master/passgenController.php
[4]: https://github.com/elcodedocle/chbspassgen/blob/master/passgenClientController.js
[5]: https://github.com/elcodedocle/chbspassgen/blob/master/password_generator.html
