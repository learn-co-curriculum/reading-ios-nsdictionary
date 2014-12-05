---
tags: dictionary, beginner
language: objc
---

# NSDictionary

## The problem

We've just finished learning a great way to store collections of data, `NSArray`. Let's take a contrived example now. We have friends named "1","2","3"..."100". And each of them has a phone number. With an array, we could simply store these in order and assuming all of our friends are uniquely named different numbers, we would have an easy way of pulling up the friend's phone number we are seeking.

```objc
NSString *phoneNumberForFriendNamed57 = friendPhoneArray[56];
```

 But unfortunately, none of my friends are named "1","45","62",or "99". And arrays can only be accessed by index. So what are we to do?


## What is an NSDictionary?

An `NSDictionary` is an Objective-C object that stores a series of "key / value pairs." What is a key / value pair you might ask? Simply put, a key / value pair is two objects, most often, with the "key" object being a string (though it may be any of the property list objects, see below.) An `NSDictionary` is efficiently searched by the key.

A simple example you might consider is the basic contact list we have already described. Wouldn't it be nice if we could do something along these lines?


```objc
NSString *phoneNumberForFriendNamedJoe = friendPhonesDictionary["Joe"];
```

A contact list consists of a series of names, and a series of phone numbers. It is standard to look up a phone number value by its "key", the contact's name.

Simply put, the property list objects are: `NSString`, `NSNumber`, `NSArray`, `NSDictionary`, `NSDate`, and `NSData`. 

## Creating an NSDictionary

Let's move ahead with the example of a contact list with names and phone numbers. A dictionary creates pairs for these names and numbers such that we can search for a phone number, given a contact's name. Here is a sample of what an `NSDictionary` would look like. (We'll store our phone numbers as strings initially as well.)

```
NSDictionary *contactDictionary = @{@"Marc Bevilaqua":@"212-555-1212", 
@"Jim Sherman":@"858-111-9999",
@"Lena Ricciardi":@"973-666-1111"};
```

Alternatively, if we stored the phone numbers as `NSNumbers`, our `contactDictionary` would look like this:

```
NSDictionary *contactDictionary = @{@"Marc Bevilaqua":@2125551212, 
@"Jim Sherman":@8581119999,
	@"Lena Ricciardi":@9736661111};
```

Now let's attempt a more advanced `NSDictionary` using the same example. In a real contact list, each of our contacts would have a phone number, an email address, and a favorite color. In this case, each contact would have its own `NSDictionary` of attributes!

For example:

```
NSDictionary *marcBevilaquaDictionary = {@"Phone Number":@2125551212, 
@"Email":@"me@marcbevilaqua.com",
@"Color":@"Orange"};

NSDictionary *jimShermanDictionary = {@"Phone Number":@8581119999, 
@"Email":@"me@jimsherman.com",
@"Color":@"Blue"};

NSDictionary *lenaRicciardiDictionary = {@"Phone Number":@9736661111, 
@"Email":@"me@lenaricciardi.com",
@"Color":@"Seafoam"};

NSDictionary *contactDictionary = @{"Marc Bevilaqua":marcBevilaquaDictionary, 
@"Jim Sherman":jimShermanDictionary, 
@"Lena Ricciardi":lenaRicciardiDictionary};
```

As you can see from the above, we have defined a complete dictionary for each contact and then made our "values" in `contactDictionary`, be dictionaries themselves! Dictionaries may be nested like this.

## Accessing values in an NSDictionary

So now you want to get a specific friend's phone number. Let's go back to our simple contact list:

```
NSDictionary *contactDictionary = @{@"Marc Bevilaqua":@"212-555-1212", 
@"Jim Sherman":@"858-111-9999",
@"Lena Ricciardi":@"973-666-1111"};
```

How can we ask our dictionary for Marc Bevilaqua's phone number? Easy.

```
NSString *marcsPhoneNumber = contactDictionary[@"Marc Bevilaqua"];
```

By specifying the key we are seeking, we will get back its value. In this case, that value is a string. But it could be any type of object. Remember, the computer is only so smart. If you try to assign the value of a key in a dictionary to an object of a type that it is not (i.e. trying to put an NSArray into an NSString object), you can expect your program to crash. Let's take our advanced contact list example to see what I mean.

```
NSDictionary *marcBevilaquaDictionary = {@"Phone Number":@2125551212, 
@"Email":@"me@marcbevilaqua.com",
@"Color":@"Orange"};

NSDictionary *jimShermanDictionary = {@"Phone Number":@8581119999, 
@"Email":@"me@jimsherman.com",
@"Color":@"Blue"};

NSDictionary *lenaRicciardiDictionary = {@"Phone Number":@9736661111, 
@"Email":@"me@lenaricciardi.com",
@"Color":@"Seafoam"};

NSDictionary *contactDictionary = @{"Marc Bevilaqua":marcBevilaquaDictionary, 
									   "Jim Sherman":jimShermanDictionary, 
								   @"Lena Ricciardi":lenaRicciardiDictionary};
```

Now, in order to get Marc's phone number out of `contactDictionary`, we should do the following:

```
NSNumber *marcsPhoneNumber = contactDictionary[@"Marc Bevilaqua"][@"Phone Number"];
```

Here, we have nested dictionaries, and we may use this shorthand literal syntax to access the phone number. Recall in our advanced contact list, the phone number is stored as an `NSNumber`. If you tried to place this value into an `NSString`, your program would crash.

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

## Enumerating Dictionaries

You have already learned how to use a for..in loop. Combined with `NSDictionary` objects, we can run through all of the phone numbers in our contact list like so:

```
for (id key in contactDictionary)
{
  NSLog(@"%@'s phone number is: %@",contactDictionary[key],key)
}
```

# NSMutableDictionary

Instead of `NSDictionary`, we might want to use `NSMutableDictionary` in order to add objects to our dictionary after it has initially been created.

##Updating an object for a key

Let's say the phone number for Marc Bevilaqua changes. Let's go back to our simple NSDictionary to make this more straightforward.

```
NSDictionary *contactDictionary = @{
 @"Marc Bevilaqua":@"212-555-1212", 
	@"Jim Sherman":@"858-111-9999",
 @"Lena Ricciardi":@"973-666-1111"};
```

A couple of ways to do this. First:

```
contactDictionary[@"Marc Bevilaqua"] = @"201-973-0101";
```

Alternatively, you may see the following syntax, using the NSDictionary method `setObject:ForKey:`.

```
[contactDictionary setObject:@"201-973-0101" forKey:@"Marc Bevilaqua"];
```

Again, use what you feel most comfortable with.

To remove an object for a key, you would use:

```
[contactDictionary removeObjectForKey:@"Marc Bevilaqua"];
```

## `[[NSMutableDictionary alloc] init]`

If you are mutating a dictionary, you may want to initialize it separately from assigning key / value pairs. You can do this using the more traditional syntax using `alloc` and `init`. Take the following examples where we have some names and phone numbers in arrays and want to create a useful dictionary out of them.

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

If you are looping through an `NSMutableDictionary`, you cannot modify that dictionary inside of the loop. 
