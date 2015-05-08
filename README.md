---
tags: dictionary, beginner
language: objc
---

# NSDictionary

## The problem

We've just finished learning a great way to store collections of data, `NSArray`. Let's take a contrived example now. We have friends named "1", "2", "3",... "100", and each of them has a phone number. With an array, we could simply store these in order. Assuming all of our friends are uniquely named sequential integers, we would have an easy way of pulling up the friend's phone number we are seeking:

```objc
NSString *phoneNumberForFriendNamed57 = friendPhoneArray[56];
```

 But unfortunately, none of my friends are named "1", "45", "62", or "99". And arrays can only be accessed by index. So what are we to do?


## What is an NSDictionary?

An `NSDictionary` is an Objective-C object that stores a series of "key-value pairs." What is a key-value pair you might ask? Simply put, a key-value pair is two objects, most often, with the "key" object being a string (though it may be any objects satisfying a few constraints -- see below.) An `NSDictionary` can be efficiently indexed by the key. A side-effect of that efficiency is that a dictionary can only have one value associated with each key, and that the order of elements in a dictionary is not guaranteed.

A simple example you might consider is the basic contact list we have already described. Wouldn't it be nice if we could do something along these lines?


```objc
NSString *phoneNumberForFriendNamedJoe = friendPhonesDictionary["Joe"];
```

A contact list consists of a series of names, and a series of phone numbers. It is standard to look up a phone number value by its "key", the contact's name.


## Creating an `NSDictionary`

Let's move ahead with the example of a contact list with names and phone numbers. A dictionary creates pairs for these names and numbers such that we can search for a phone number, given a contact's name. Here is a sample of what creating that `NSDictionary` would look like, using the [literal syntax](http://cocoaheads.tumblr.com/post/17757846453/objective-c-literals-for-nsdictionary-nsarray) for dictionaries. (We'll store our phone numbers as strings initially as well.)

```
NSDictionary *contactDictionary = @{ @"Marc Bevilaqua": @"212-555-1212", 
									    @"Jim Sherman": @"858-111-9999",
									 @"Lena Ricciardi": @"973-666-1111" };
```

Alternatively, if we stored the phone numbers as `NSNumbers`, our `contactDictionary` would look like this:

```
NSDictionary *contactDictionary = @{ @"Marc Bevilaqua": @2125551212, 
									    @"Jim Sherman": @8581119999,
									 @"Lena Ricciardi": @9736661111 };
```

Now let's attempt a more advanced `NSDictionary` using the same example. In a real contact list, each of our contacts would have a phone number, an email address, and a favorite color. In this case, each contact would have its own `NSDictionary` of attributes!

For example:

```
NSDictionary *marcBevilaquaDictionary = { @"Phone Number": @2125551212, 
									     		 @"Email": @"me@marcbevilaqua.com",
											     @"Color": @"Orange" };

NSDictionary *jimShermanDictionary = { @"Phone Number": @8581119999, 
											  @"Email": @"me@jimsherman.com",
											  @"Color":@"Blue" };

NSDictionary *lenaRicciardiDictionary = { @"Phone Number": @9736661111, 
												 @"Email": @"me@lenaricciardi.com",
												 @"Color": @"Seafoam" };

NSDictionary *contactDictionary = @{ "Marc Bevilaqua": marcBevilaquaDictionary, 
									   @"Jim Sherman": jimShermanDictionary, 
								    @"Lena Ricciardi": lenaRicciardiDictionary };
```

As you can see from the above, we have defined a complete dictionary for each contact and then made our "values" in `contactDictionary`, be dictionaries themselves! Dictionaries may be nested like this.

## Accessing values in an NSDictionary

So now you want to get a specific friend's phone number. Let's go back to our simple contact list:

```
NSDictionary *contactDictionary = @{ @"Marc Bevilaqua": @"212-555-1212", 
									    @"Jim Sherman": @"858-111-9999",
									 @"Lena Ricciardi": @"973-666-1111" };
```

How can we ask our dictionary for Marc Bevilaqua's phone number? Easy.

```
NSString *marcsPhoneNumber = contactDictionary[@"Marc Bevilaqua"];
```

By specifying the key we are seeking, we will get back its value. In this case, that value is a string. But it could be any type of object. Remember, the computer is only so smart. If you try to assign the value of a key in a dictionary to an object of a type that it is not (i.e. trying to put an NSArray into an NSString object), you can expect your program to crash. Let's take our advanced contact list example to see what I mean.

In order to get Marc's phone number out of `contactDictionary`, we should do the following:

```
NSNumber *marcsPhoneNumber = contactDictionary[@"Marc Bevilaqua"][@"Phone Number"];
```

Here, we have nested dictionaries, and we may use this shorthand subscripting syntax to access the phone number. Recall in our advanced contact list, the phone number is stored as an `NSNumber`. If you tried to place this value into an `NSString`, your program will (eventually) crash. The actual assignment won't cause a crash, but any attempt to call an `NSString` method on the value would blow up.

## `objectForKey`

You might also see the following old-school syntax used. We do not suggest you use this syntax, but you should know how to read it.


```
NSNumber *marcsPhoneNumber = [[contactDictionary objectForKey:@"Marc Bevilaqua"] 
												 objectForKey:@"Phone Number"];
```

Here we have used the `NSDictionary` method `objectForKey:` in order to obtain first, the value for the key "Marc Bevilaqua" (which is a dictionary itself.) And we have nested this method call inside a second method call that gets us the value for the key "Phone Number."

Again, because this syntax is more verbose, we don't suggest you default to it, but you may come across it, and therefore you must be familiar with it.

## `alloc` and `init`

As with all objects in Objective-C, you may also use the more traditional `alloc` and `init` to initialize a dictionary, like so:

```objc
NSDictionary *marcDictionary = [[NSDictionary alloc] initWithObjectsAndKeys:@2125551212, @"Phone Number", nil]`
```

Like `-objectForKey`, this syntax is old, verbose, error-prone (you *must* add `nil` at the end), and somewhat confusing (values come before their keys?!). There is also a method `-initWithObjects:forKeys:` that matches up corresponding elements of two arrays. It is equally verbose but arguably less error-prone.

Use the literal syntax, but be aware that you may see these methods in older code.


## Enumerating Dictionaries

You have already learned how to use a `for..in` loop. `for..in` loops are defined to loop over the keys of `NSDictionary` objects, so we can run through all of the phone numbers in our contact list like so:

```
for (NSString *contactName in contactDictionary)
{
  NSLog(@"%@'s phone number is: %@", contactDictionary[contactName], contactName);
}
```

Keep in mind that **order is not guaranteed**, so don't be surprised if this prints out the elements in different orders across different executions of your application. Do not rely on or assume the order of keys in a dictionary!

Another way to iterate over a dictionary is to manually grab its keys and loop over those. You can get an array of all of the keys in a dictionary using the `-allKeys` method:

```objc
NSArray *contactNames = [contactDictionary allKeys];

for(NSString *contactName in contactNames)
{
  NSLog(@"%@'s phone number is: %@", contactDictionary[contactName], contactName);
}
```

This is functionally equivalent to the `for..in` loop (though perhaps slightly slower). But it can be useful if you need to sort the keys beforehand, or need direct access to the keys for some other reason.


# `NSMutableDictionary`

All of the examples we've seen thus far have not changed the dictionary -- we define it as a literal and never modify it. Just as there are mutable and immutable arrays, there are mutable dictionaries. Use `NSMutableDictionary` in order to add or remove objects to our dictionary after it has initially been created. `NSMutableDictionary` is a subclass of `NSDictionary`, so all the methods we've covered thus far will continue to work on your mutable instances.

##Creating `NSMutableDictionary` instances

Let's say the phone number for Marc Bevilaqua changes. Let's go back to our simple dictionary to make this more straightforward.

```
NSDictionary *contactDictionary = @{ @"Marc Bevilaqua": @"212-555-1212", 
									    @"Jim Sherman": @"858-111-9999",
 									 @"Lena Ricciardi": @"973-666-1111" };
```

But, as we just discussed, we have no means to mutate a straight `NSDictionary`. So, how do we make a mutable dictionary with some default values? The trouble is that the dictionary literal syntax (`@{ ... }`) returns an `NSDictionary`. We have two options to convert that to an `NSMutableDictionary`:

1. Use `NSMutableDictionary`'s `-initWithDictionary:`. This creates a new mutable dictionary using the values in the dictionary argument. So, we could do this:

	```objc
	NSDictionary *immutableContactDictionary = @{ @"Marc Bevilaqua": @"212-555-1212", 
												     @"Jim Sherman": @"858-111-9999",
 									              @"Lena Ricciardi": @"973-666-1111" };

 	NSMutableDictionary *contactDictionary = [[NSMutableDictionary alloc] initWithDictionary:immutableContactDictionary];
	```

2. Use `-mutableCopy` on the literal `NSDictionary`. This method does exactly what it sounds like -- it makes a copy of the receiver that is mutable. So, this option is a little shorter than the first:

	```objc
	NSDictionary *contactDictionary = [@{ @"Marc Bevilaqua": @"212-555-1212", 
										  @"Jim Sherman": @"858-111-9999",
									      @"Lena Ricciardi": @"973-666-1111" } mutableCopy];
	```


##Modifying a dictionary

Now that we have a mutable dictionary, there are a couple of ways to change things in it. First:

```
contactDictionary[@"Marc Bevilaqua"] = @"201-973-0101";
```

Alternatively, you may see the following syntax, using the NSDictionary method `setObject:ForKey:`.

```
[contactDictionary setObject:@"201-973-0101" forKey:@"Marc Bevilaqua"];
```

Again, use what you feel most comfortable with. Both of these will change the value for a given key, or add it if the key is not currently in the dictionary.

To remove an object for a key, you would use:

```
[contactDictionary removeObjectForKey:@"Marc Bevilaqua"];
```

## `[[NSMutableDictionary alloc] init]`

If you are building a dictionary from scratch, you may want to initialize it separately from assigning key-value pairs. You can do this using the more traditional syntax using `alloc` and `init`, which will give you an empty dictionary. Take the following examples where we have some names and phone numbers in arrays and want to create a useful dictionary out of them:

```objc
- (NSDictionary *)createDictionaryFromArrays {

	NSArray *names = @[@"James", @"Marc", @"Rich"];
	NSArray *phoneNumbers = @[@"858-111-9999", @"212-555-1212", @"973-666-1111"];

	NSMutableDictionary *dictionaryIWillAddStuffTo = [[NSMutableDictionary alloc] init];

	for (NSInteger i = 0; i < [phoneNumbers count]; i++) {
		dictionaryIWillAddStuffTo[names[i]] = phoneNumbers[i];
	}
}
```

## Important tip

If you are looping through an `NSMutableDictionary`, you cannot modify that dictionary inside of the loop. If you really must do this, make a copy of the dictionary (with `-copy`) before the loop and modify that instead.


## What can be the keys of a dictionary?

There are restrictions on what can be the keys in an `NSDictionary`, due to some technicalities in how it's implemented. There are three conditions a class must meet to be suitable as a dictionary key:

1. It must conform to the `NSCopying` protocol. Dictionary keys are copied when they're put into the dictionary, and `NSCopying` is the protocol that represents that capability. There's a hint that this is the case in the definition of `NSMutableDictionary`'s method for setting the value for a key: `- (void)setObject:(id)anObject forKey:(id <NSCopying>)aKey;`.

2. It must override `-isEqual:` to provide a meaningful definition of equality of instances based on the properties of those instances.

3. It must override `-hash` to provide a [hash](http://en.wikipedia.org/wiki/Hash_function) based on the properties of an instance. Hashes and how to best create them is a huge topic in computer science. Generally it's best to defer to the built-in implementation of `-hash` from a property on the instance, if possible. 

Here's a good [blog post](http://bynomial.com/blog/?p=73) that goes into more detail about why these conditions exist and how to implement them. If you're interested in the ins-and-outs of equality in Objective-C (and more on `-hash`), check out this [NSHipster article](http://nshipster.com/equality/).

Feeling a little intimidated? Here's the good news: a lot of Apple's built-in classes satisfy these three criteria. `NSString`, `NSNumber`, `NSDate`, `NSData`, and `NSValue` are all perfectly fine to use as keys. Technically, so are `NSArray` and `NSDictionary` (but not the mutable versions!), though that would be really weird. (This set of types is sometimes referred to as the "property list types" -- they are basic value types that can be direclty written to disk by built-in methods. As a side-effect of that they're perfect for dictionary keys.)

But, don't assume all built-in classes are acceptable. For instance, `UIView` is not: it is not copyable. 

Note that **there are no such restrictions on values**: any object is fine. (Primitives are not, though. Hence `NSNumber` and other such wrapper classes.)
