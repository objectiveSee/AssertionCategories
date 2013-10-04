### The problem: ####

```objective-c

NSNumber *userid = [JSONResponse objectForKey:@"userid"];
NSInteger userIdInt = [userid integerValue];  // <--- What happens here if userid is REALLY a NSString

```

Sometimes objects in your JSON response (or any objects inside an Array, Dictionary, or Set) may not be the Class type you expected!  A strict approach to protecting against crashes (or even worse unexpected values) is to be very strict about the class of objects returned as 'id'. 

#### The solution: ####

```objective-c

NSNumber *userid = [JSONResponse objectForKey:@"userid" ofClass:@"NSNumber"];  // <--- Breakpoint + Exception in DEBUG build

NSInteger userIdInt = [userid integerValue];

```

The new method objectForKey:ofClass: replaces objectForKey: on NSDictionary.  In DEBUG builds the method will check the class of the object being returned and cause an NSAssertion if the class is incorrect.  Making this a common practice when dealing with API/JSON data will save you debugging time and instantly raise attention to any bugs regarding class types.  Many bugs would have resulted in immediate crashes anyways (and thus a breakpoint) however the minimal extra time that it takes to type a class name is IMO worth it. In my latest app I have used "ofClass" 530 times! 

## Interface ####

```objective-c

@interface NSDictionary (AssertionCategories)

- (id)objectForKey:(id)aKey ofClass:(NSString *)aClassName;
- (id)objectForKey:(id)aKey ofClass:(NSString *)aClassName mustExist:(BOOL)mustExist;

@end

@interface NSArray (AssertionCategories)

- (id)objectAtIndex:(NSUInteger)index ofClass:(NSString *)aClassName;

@end


```


### Notes ###

My implementation will also cause an assertion when the key value is nil. This may be counter to the pattern in objective-C where returning nil is not typically a cause for alarm. The objectForKey:ofClass:mustExist method can be used to specify that nil objects do not cause an assertion.
