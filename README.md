---
tags: dictionary, beginner
language: objc
---

# NSDictionary

## What is an NSDictionary?

An `NSDictionary` is an Objective-C object that stores a series of "key / value pairs." What is a key / value pair you might ask? Simply put, a key / value pair is two objects, most often, with the "key" object being a string (though it may be any of the property list objects, see below.) An `NSDictionary` is efficiently searched by the key.

A simple example you might consider is a basic contact list. A contact list consists of a series of names, and a series of phone numbers. It is standard to look up a phone number value by its key, the contact's name.

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
@"Jim Sherman":jimShermanDictionary, 
@"Lena Ricciardi":lenaRicciardiDictionary};
```

Now, in order to get Marc's phone number out of `contactDictionary`, we should do the following:

```
NSNumber *marcsPhoneNumber = contactDictionary[@"Marc Bevilaqua"][@"Phone Number"];
```

Here, we have nested dictionaries, and we may use this shorthand literal syntax to access the phone number. Recall in our advanced contact list, the phone number is stored as an `NSNumber`. If you tried to place this value into an `NSString`, your program would crash.

You might also see the following old-school syntax used. (If you decide to use a database service like Parse down the line to store your contact list in the cloud, you'll see this often.)

```
NSNumber *marcsPhoneNumber = [[contactDictionary objectForKey:@"Marc Bevilaqua"] objectForKey:@"Phone Number"];
```

Here we have used the `NSDictionary` method `objectForKey:` in order to obtain first, the value for the key "Marc Bevilaqua" (which is a dictionary itself.) And we have nested this method call inside a second method call that gets us the value for the key "Phone Number."

Feel free to use the syntax most comfortable for you, but you should be familiar with both.


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
NSDictionary *contactDictionary = @{@"Marc Bevilaqua":@"212-555-1212", 
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

## Important tip

If you are looping through an `NSMutableDictionary`, you cannot modify that dictionary inside of the loop. 
