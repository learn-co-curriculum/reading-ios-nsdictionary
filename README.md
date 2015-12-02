# Key-value Pairing: `NSDictionary`

## Objectives

1. Identify the usefulness of key-value pairs.
2. Create an `NSDictionary` object with the literal syntax.
3. Access values in a dictionary by key.
4. Access dictionary values within loops.
5. Create an `NSMutableDictionary` using `mutableCopy`.
6. Add, edit, and remove key-values pairs in a mutable dictionary.
7. Avoid modifying a mutable dictionary while iterating over it—use the `allKeys` array instead.

## The Value of Keys

We've introduced ordered collections in the form of arrays (`NSArray` & `NSMutableArray`) which are powerful tools with many uses. But what happens when we have a set of unique information types that we need to keep together, such as the different fields attributed to someone in a list of contacts? We *could* use an array to store their information:

```objc
NSArray *jenny = @[ @"Jenny",
                    @"Friend",
                    @"(555) 867-5309",
                    @"jenny@email.com",
                    @"123 Streetname",
                    @"Anywhere, USA",
                    @"00409"           ];
    
NSLog(@"%@", jenny);
```
This will print:

```
(
    Jenny,
    Friend,
    "(555) 867-5309",
    "jenny@email.com",
    "123 Street Name",
    "Anywhere, USA",
    00409
)
```

But there's a couple of inherent problems with storing this kind of information in an array. First, we can't be quite certain just by looking at each piece information what its meant to represent. Sure we can infer what a phone number is, but is Jenny's last name "Friend" or is that the description of our relationship to her? We could solve this by creating another array called "keys" that stores the information type for each index:

```objc
NSArray *keys = @[ @"first_name",
                   @"relationship",
                   @"phone_number",
                   @"email_address",
                   @"physical_address",
                   @"city_state",
                   @"zip_code"         ];
                   
for (NSUInteger i = 0; i < jenny.count; i++) {
    NSLog(@"%@ : %@", keys[i], jenny[i]);
}
```
This will print:

```
first_name : Jenny
relationship : Friend
phone_number : (555) 867-5309
email_address : jenny@email.com
physical_address : 123 Street Name
city_state : Anywhere, USA
zip_code : 00409
```

This brings us to our second problem: what if we want add a new piece of information, like Jenny's last name? We have to keep the order of not only both the `keys` array and the `jenny` array synchronized, but we have to keep the order of every other contact array synchronized as well:

```objc
NSArray *keys = @[ @"first_name",
                   @"last_name",
                   @"relationship",
                   @"phone_number",
                   @"email_address",
                   @"physical_address",
                   @"city_state",
                   @"zip_code"         ];


NSArray *jenny = @[ @"Jenny",
                    @"Curran",
                    @"Friend",
                    @"(555) 867-5309",
                    @"jenny@email.com",
                    @"123 Street Name",
                    @"Anywhere, USA",
                    @"00409"           ];
    

NSArray *unclebob = @[ @"Bob",
                       @"",       // we must insert an empty string as a placeholder
                       @"Uncle",
                       @"(555) 876-1234",
                       @"unclebob@email.com",
                       @"234 Street Name",
                       @"Somewhere, USA",
                       @"00201"           ];
```

This is a lot of work, and if we miss adding a field to just one contact's array we could cause a crash due to a short index; or if we add it in the wrong place, then the information won't line up correctly with their descriptions in the `keys` array. With these small groups of information, it doesn't seem like an unmanageable task, but when dealing with thousands of groups, each with hundreds of information fields, these manual adjustments become untenable.

Enter key-value pairing.

## Dictionaries: Words And Definitions

Dictionaries are a collection type common to most programming languages. They are aptly named for their resemblance to (and usefulness as) language lexicons (also called dictionaries). While most lexical dictionaries are alphabetized, the order of their contents is not fundamental to their nature. What is fundamental, however, is that each word in the lexicon is printed with the correct definition. When looking up the definition of "jentacular", for example, we should expect the lexicon to inform us of the correct meaning: "of or pertaining to a breakfast taken early in the morning". It matters little which specific page the word is printed on—what's important is getting the definition right!

```
WORD (KEY) : DEFINITION (VALUE)
jentacular : of or pertaining to a breakfast taken early in the morning.
```
In programming, dictionaries aren't just used for the definitions of words. They store an unordered group of key-value pairs. A key-value pair is a binding of two objects: the descriptive accessor of a piece of information called the "key" (the word), and the information itself called the "value" (the definition).

## Creating An `NSDictionary`

The base dictionary type in Objective-C is `NSDictionary` and has its own literal syntax that looks like this:

```objc
NSDictionary *keyAndValue   = @{ key : value };

NSDictionary *keysAndValues = @{ key1 : value1, 
                                 key2 : value2 };
```

The dictionary literal employs the curly braces (`{``}`) to wrap its list of key-value pairs which are separated from each other in the list using `,`s ("commas"); the keys and values themselves are separated from each other with a `:` ("colon") with the key on the left and the value on the right.

**Top-tip:** *While there are some limits on which objects can be keys, it's most common to use strings. Values can be of any object type, even other collections.* 

Translating our `jenny` array into a dictionary with this syntax would look like this:

```objc
//                            KEYS               VALUES
NSDictionary *jenny = @{ @"first_name"       : @"Jenny",
                         @"relationship"     : @"Friend",
                         @"phone_number"     : @"(555) 867-5309",
                         @"email_address"    : @"jenny@email.com",
                         @"physical_address" : @"123 Street Name",
                         @"city_state"       : @"Anywhere, USA",
                         @"zip_code"         : @"00409"        };

NSLog(@"%@", jenny);
```
This might print:

```
{
    "city_state" = "Anywhere, USA";
    "email_address" = "jenny@email.com";
    "first_name" = Jenny;
    "phone_number" = "(555) 867-5309";
    "physical_address" = "123 Street Name";
    relationship = Friend;
    "zip_code" = 00409;
}
```
Did you notice how the order changed when it printed? That's because Apple does ***not*** guarantee that a dictionary will maintain the order of its components (you should always expect that it won't). What it *does* guarantee, however, is the key-value binding. 

When looking up a definition in a lexical dictionary, it's unimportant to the word-to-definition binding exactly which place they hold in the dictionary. If we want to find the definition of "bibble", for example, it's unimportant to the binding whether it's the 298th word in the lexicon or the 300th, so long as it correctly defines itself as "to drink often; to eat and/or drink noisily".

In a similar way to looking up the definition of a word in a lexical dictionary, a dictionary in programming is accessed by submitting the key whose value we wish to get.

## Accessing A Dictionary

In a similar syntax to accessing an array by index:

```objc
Class *element = array[index];
```
We can access the information in a dictionary by key:

```objc
Class *element = dictionary[key];
```

To access Jenny's phone number from the above dictionary that we created to store her information, we can write the following:

```objc
NSString *jennysPhoneNumber = jenny[@"phone_number"];

NSLog(@"%@", jennysPhoneNumber);
```
This will print: `(555) 867-5309`.

**//Flat-fact:** *The parenthetical notation for telephone area codes in the United States was created by [Ladislav Sutnar](https://en.wikipedia.org/wiki/Ladislav_Sutnar) who pioneered the field of [information design](https://en.wikipedia.org/wiki/Information_design) in the mid-20th Century (c. 1941-1960).*

#### Recognizing `objectForKey:`

This literal syntax for accessing a dictionary implicitly calls the `objectForKey:` method on `NSDictionary`. You may see this method syntax in examples online which you can implement in your own code using the literal. In general, avoid using `objectForKey:` in your own code.

```objc
NSString *jennysPhoneNumber = [jenny objectForKey:@"phone_number"];

NSLog(@"%@", jennysPhoneNumber);
```
This will also print: `(555) 867-5309`.

## Enumerating Dictionaries

We can access the entire contents of a dictionary sequentially by iterating over it with a `for-in` loop. The example syntax tells the compiler to treat each key as a string:

```objc
for (NSString *key in dictionary) {
    Class *element = dictionary[key];
}
```

So, we can use a `for-in` loop to print out each of the details we have for Jenny:

```objc
NSDictionary *jenny = @{ @"first_name"       : @"Jenny",
                         @"relationship"     : @"Friend",
                         @"phone_number"     : @"(555) 867-5309",
                         @"email_address"    : @"jenny@email.com",
                         @"physical_address" : @"123 Street Name",
                         @"city_state"       : @"Anywhere, USA",
                         @"zip_code"         : @"00409"        };
    
for (NSString *key in jenny) {
    NSLog(@"Jenny's %@ is %@.", key, jenny[key]);
}
```
This will print: 

```
Jenny's email_address is jenny@email.com.
Jenny's zip_code is 00409.
Jenny's physical_address is 123 Street Name.
Jenny's relationship is Friend.
Jenny's phone_number is (555) 867-5309.
Jenny's city_state is Anywhere, USA.
Jenny's first_name is Jenny.
```
Notice again that the order was not preserved.

#### Grabbing `allKeys`

![](https://curriculum-content.s3.amazonaws.com/ios/reading-ios-nsdictionary/grabAllKeys_meme.jpg)

There's a handy method on `NSDictionary` called `allKeys` that returns an array of all of the keys (and only the keys). This is helpful in sorting and searching (which we'll discuss in other readings), but also in defensive programming to make sure that a dictionary will respond to a key before attempting to access it. Since accessing a dictionary with a nonexistent key returns `nil`, we can implement a "key check" to avoid receiving `nil` from attempts to access a dictionary with a nonexistent key:

```objc
NSLog(@"%@", jenny[@"last_name"]);
```
Since we haven't included a "last_name" key-value pair in Jenny's information dictionary, this will print: `(null)`.

Adding a check can look like this:

```objc
if ([ [jenny allKeys] containsObject:@"last_name"]) {
    NSLog(@"%@", jenny[@"last_name"]);
} else {
    NSLog(@"Jenny's last name has not been saved.");
}
```
Instead of `(null)`, we'll now get a helpful message that `Jenny's last name has not been saved.`.

## Using `NSMutableDictionary`

So what if we want to add Jenny's last name to the dictionary at some later point in our code? So far we've been using the static `NSDictionary` class which would require us to rewrite the entire dictionary from scratch. Fortunately, just like `NSArray` there is a mutable class, in this case `NSMutableDictionary`. 

The relationship between `NSDictionary` and `NSMutableDictionary` is similar to that between `NSArray` and `NSMutableArray`: `NSMutableDictionary` adds functionality to `NSDictionary`, but the literal syntax cannot be used to create a mutable one directly.

#### Creating A Dictionary With `mutableCopy`

While dictionaries have initializer methods such as `initWithObjects:forKeys:`, using them with large key-value groups quickly becomes confusing to read and difficult to correctly modify. Using the `mutableCopy` method to create an instance of a mutable dictionary is perhaps the most legible and easily maintained method. 

You can use `mutableCopy` on an existing dictionary like this:

```objc
NSDictionary *jenny = @{ @"first_name"       : @"Jenny",
                         @"relationship"     : @"Friend",
                         @"phone_number"     : @"(555) 867-5309",
                         @"email_address"    : @"jenny@email.com",
                         @"physical_address" : @"123 Street Name",
                         @"city_state"       : @"Anywhere, USA",
                         @"zip_code"         : @"00409"        };


NSMutableDictionary *mJenny = [jenny mutableCopy];
```

Or by embedding the dictionary literal syntax directly into the method call like this:

```objc
NSMutableDictionary *mJenny = [ @{ @"first_name"       : @"Jenny",
                                   @"relationship"     : @"Friend",
                                   @"phone_number"     : @"(555) 867-5309",
                                   @"email_address"    : @"jenny@email.com",
                                   @"physical_address" : @"123 Street Name",
                                   @"city_state"       : @"Anywhere, USA",
                                   @"zip_code"         : @"00409"        }
                               mutableCopy ];
```

#### Modifying A Mutable Dictionary

##### Adding A Key-value Pair

Now that we have a mutable dictionary `mJenny`, we can add Jenny's last name to the mutable dictionary using the literal syntax:

```objc
mJenny[@"last_name"] = @"Curran";

NSLog(@"%@", mJenny);
```
This will print:

```
{
    "city_state" = "Anywhere, USA";
    "email_address" = "jenny@email.com";
    "first_name" = Jenny;
    "last_name" = Curran;
    "phone_number" = "(555) 867-5309";
    "physical_address" = "123 Street Name";
    relationship = Friend;
    "zip_code" = 00409;
}
```

You may also see the implicit method call `setObject:forKey:` in examples online. This is accomplishing the same thing:

```objc
[mJenny setObject:@"Curran" forKey:@"last_name"];
```
However, notice that the value is called "object" and precedes the setting of the key. Because of the ease of confusion in using or reading this method, it's best to use the literal syntax when adding a key-value pair to a mutable dictionary.

##### Editing A Key-value Pair

With mutable dictionaries, we can use this same syntax to change the value of an existing key, such as if we happen to skip Jenny's birthday party and we want to make a note that she isn't pleased with us:

```objc
mJenny[@"relationship"] = @"It's Complicated";
```
##### Removing A Key-value Pair

There is not a literal syntax for deleting a key-value pair from a mutable dictionary. The `removeObjectForKey:` method must be used. If Jenny changes her phone number, for example, but doesn't tell us her new one to update it with, we can delete the old value like this:

```objc
[mJenny removeObjectForKey:@"phone_number"];

NSLog(@"%@", mJenny);
```
This will now print:

```
{
    "city_state" = "Anywhere, USA";
    "email_address" = "jenny@email.com";
    "first_name" = Jenny;
    "last_name" = Curran;
    "physical_address" = "123 Street Name";
    relationship = "It's Complicated";
    "zip_code" = 00409;
}
```
This will keep us from accidentally texting the stranger who gets her old number.

#### Limitation While Enumerating (Looping)

If you are looping through an `NSMutableDictionary`, attempting to modify the mutable dictionary will cause a crash:

```objc
// Bad example:

for (NSString *key in mJenny) {
    if ([key isEqualToString:@"relationship"]) {
        mJenny[@"relationship"] = @"Friend";
    }
}
```
This will produce an error that reads like this:

```
*** Terminating app due to uncaught exception 
'NSGenericException', reason: '*** Collection <__NSDictionaryM:
0x7fdfe2e0ab50> was mutated while being enumerated.'
```

If you wish to alter a mutable dictionary inside of a loop, make sure to iterate over an array of the keys using `allKeys` instead of the dictionary itself:

```objc
for (NSString *key in [mJenny allKeys]) {
    if ([key isEqualToString:@"relationship"]) {
        mJenny[@"relationship"] = @"Friend";
    }
}

NSLog(@"%@", mJenny);
```
This will successfully print:

```
{
    "city_state" = "Anywhere, USA";
    "email_address" = "jenny@email.com";
    "first_name" = Jenny;
    "last_name" = Curran;
    "phone_number" = "(555) 867-5309";
    "physical_address" = "123 Street Name";
    relationship = "Friend";
    "zip_code" = 00409;
}
```
It looks like she didn't stay mad at us forever.

<a href='https://learn.co/lessons/reading-ios-nsdictionary' data-visibility='hidden'>View this lesson on Learn.co</a>
