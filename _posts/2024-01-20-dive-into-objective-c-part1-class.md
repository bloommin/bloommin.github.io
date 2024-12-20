---
layout: post
title: "Dive Into Objective-C Part1 Class"
date: 2024-01-20 14:16:09 +0800
published: false
categories: [Programming Language, Objective-C]
tags: [source analysis, objective-c, runtime]
# The categories of each post are designed to contain up to two elements, and the number of elements in tags can be zero to infinity. 
# TAG names should always be lowercase
author: min
toc: true
comments: false
math: false
mermaid: false
# Mermaid is a great diagram generation tool
media_subpath: '/assets/img/2024-01-20'
image:
  path: cover.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAABxSURBVH3BsQ6CUAxA0Utp3yAfINE4ufr/n0McIS7qgsXWZxhMXPSc5nA81eey8I+Vgpop+13PSqRl5e6YGRGBass0XdB5fjAMZ2ga+n5Lt+m43u64O+6OiCAiaK0vIpPVOE7UWslMPjKTUgzlS0TwyxsqDDDQ1HPWvwAAAABJRU5ErkJggg==
  alt: Set Up Your Blog On Github.
---

## Why Still Learning Objective-C

It is reasonable to feel unbelievable to see an article about Objective-C in 2024 with the increase popularity of swift. However, as an iOS developer, I always believe it is important to be familiar with it, because we may still use `UIKit` or something else written with it.

## Prepare for Researching Objective-C

In my opinion, there are three most important parts in Objective-C: **Class**, **Message** and **Memory**. Today I will disscuss about the first part: **Class**.

Before starting our adventure, we can clone Objective-C sources from [Apple's repository](https://github.com/apple-oss-distributions/objc4.git){:target="_blank"} in order to take a deep look at them carefully. For convenience, I will focus on the latest version of Objective-C, and by now, the latest version is `objc4-906.2`.

My system environment is:

```shell
$ clang -v
Ubuntu clang version 14.0.0-1ubuntu1.1
Target: x86_64-pc-linux-gnu
```

It would be better if your system is MacOS, because you can get executable `Objective-C` **runtime** from [here](https://github.com/internetWei/ObjCRuntimes.git){:target="_blank"}, so that you can play around with it.

## What Is a Class Essentially

First, let's figure out what is a class in Objective-C. You can create a file *animal.m*, and write some codes in it as below:

```objectivec
@interface Animal
@property char *type;
@property int num;
- (void)shout;
@end

@implementation Animal
- (void)shout {}
@end
```

Then execute the command `clang -rewrite-objc animal.m` in shell, and you will get a file named *animal.cpp*. Now you can open and explore it.
Let's search for the class name `Animal` we defined above, and we will see a line of code as below:

```c++
typedef struct objc_object Animal;
```

It shows that the Objective-C declears our class as a `objc_object` *struc*, and by looking for it in **objc4** sources which were downloaded beforehand, we can find they define the `objc_object` as below:

```c++
struct objc_object {
private:
    char isa_storage[sizeof(isa_t)];
    isa_t &isa() { return *reinterpret_cast<isa_t *>(isa_storage); }
    const isa_t &isa() const { return *reinterpret_cast<const isa_t *>(isa_storage); }

public:

    // ISA() assumes this is NOT a tagged pointer object
    Class ISA(bool authenticated = false) const;
// ...
}

union isa_t {
    isa_t() { }
    isa_t(uintptr_t value) : bits(value) { }
    uintptr_t bits;
private:
    // Accessing the class requires custom ptrauth operations, so
    // force clients to go through setClass/getClass by making this
    // private.
    Class cls;
// ...
}
```

The code above shows the `objc_object` stores a `Class` type `cls`, and if we continue to research we can find the `Class` is defined as below:

```c++
/// An opaque type that represents an Objective-C class.
typedef struct objc_class *Class;

struct objc_class : objc_object
{
    Class superclass;
    cache_t cache;             // formerly cache pointer and vtable
    class_data_bits_t bits;    // class_rw_t * plus custom rr/alloc flags
}
```

As we can see, `objc_class` inherits from `objc_object`, so besides `isa`, it also has a `superclass` property. We can infer that the `superclass` represents its super class, but what is `isa` used for?

By searching for `isa` in **objc4**, we can find a function named `objc_getMetaClass` and its relative functions are implemented as below:

```c++
/***********************************************************************
* objc_getMetaClass.  Return the id of the meta class the named class.
* Warning: doesn't work if aClassName is the name of a posed-for class's isa!
**********************************************************************/
Class objc_getMetaClass(const char *aClassName)
{
    Class cls;
    if (!aClassName) return Nil;
    cls = objc_getClass (aClassName);
    if (!cls){
        _objc_inform ("class `%s' not linked into application", aClassName);
        return Nil;
    }
    return cls->ISA();
}
```

Now we find a **meta class**, it seems that the `isa` represents a class's **meta class**. But, what is a **meta class**? What's the relationship among **meta class**, **class** and **instance**? If you've been aware of object-oriented already, you will know that an **instance** is generated by a **class**, and the *super* **class** is inherited by a *sub* **class**. 

To figure out the questions above, we can recall what we've done when we create a class using Objective-C **runtime**.

```c++
Class cls = objc_allocateClassPair(ASuperClass, "SomeClass", 0);
objc_registerClassPair(cls);
```

Then you can use `SomeClass` to do some work. Let's take a close look at `objc_allocateClassPair`.

```c++
/* Adding Classes */

/** 
 * Creates a new class and metaclass.
 * 
 * @param superclass The class to use as the new class's superclass, or Nil to create a new root class.
 * @param name The string to use as the new class's name. The string will be copied.
 * @param extraBytes The number of bytes to allocate for indexed ivars at the end of 
 *  the class and metaclass objects. This should usually be 0.
 * 
 * @note To create a new class, start by calling objc_allocateClassPair. 
 *  Then set the class's attributes with functions like class_addMethod and class_addIvar.
 *  When you are done building the class, call objc_registerClassPair. The new class is now ready for use.
 * @note Instance methods and instance variables should be added to the class itself. 
 *  Class methods should be added to the metaclass.
 */
Class objc_allocateClassPair(Class superclass, const char *name, size_t extraBytes)
{
    Class cls, meta;

    // Omit something not important

    // Allocate new classes.
    cls  = alloc_class_for_subclass(superclass, extraBytes);
    meta = alloc_class_for_subclass(superclass, extraBytes);

    objc_initializeClassPair_internal(superclass, name, cls, meta);

    return cls;
}

static void objc_initializeClassPair_internal(Class superclass, const char *name, Class cls, Class meta)
{
    // Omit unimportant code

    // Connect to superclasses and metaclasses
    cls->initClassIsa(meta);

    if (superclass) {
        meta->initClassIsa(superclass->ISA()->ISA());
        cls->setSuperclass(superclass);
        meta->setSuperclass(superclass->ISA());
        addSubclass(superclass, cls);
        addSubclass(superclass->ISA(), meta);
    } else {
        meta->initClassIsa(meta);
        cls->setSuperclass(Nil);
        meta->setSuperclass(cls);
        addRootClass(cls);
        addSubclass(cls, meta);
    }

    addClassTableEntry(cls);
}
```

According to the code above, we can know that a **class** and a **meta class** are always created at the same time, the **meta class** will be added to the **class** by setting its `isa` value. And if there is a **super class**, the value of the **class** and **meta class**'s `superclass` will be changed to the argument `superclass` and `superclass->isa` separately. In order to demonstrate more figuratively, we can look at the picture below:

![realtionship among them](relationship.png){: lqip="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAAklEQVR4AewaftIAAAERSURBVBXBQZKbMBBA0d8tAQYU5ILyyjeY3P8wmV1m56qJxxoCNu1CSvyeLMtSzIxhGFBV1nWh63pens8nVVXx4lWVEAKqipmx75nH44GZkXOmqiratsU75yilkHPmfr/zdfui8hWHw4F5nhFRpmlEr9crvz8+2LYNEaGpG4bhB8u6UNc1MQ68+GmaGMcR5xwiQs4ZVcc0TqSUMDPqusbzn4hQSmFZ/vL5+Yfj8Yj3HhEQEZxz6OVy4df7O2YbXddzOp3o+551XVFV+r5HRNDz+czPtzfatiPnTCmFfd8JIWD25Ha7ISL4lBKbbcQhYmbM8zcxHnkJISAiiAheRKCAiBBCoGkaVJWUEjFGnHM45/gHK2Z1Jc0XjdYAAAAASUVORK5CYII=" }

This picture tells us:
- The `isa` of an **instance** is its **class**.
- The `isa` of a **class** is its **meta class**.
- Both **class** and **meta class** have their **super class**.

## What's in a Class

If you read the code above carefully, you will find it mentions that:

> Instance methods and instance variables should be added to the class itself. Class methods should be added to the metaclass.

So, storing **class method** is what **meta class** used for. Let's continue our journey to explore how **class** stores **instance variables** and **instance methods** and how **meta class** stores **class methods**.

### Instance Variable & Property

Let's take a close look at `class_addIvar` to figure out how Objective-C stores the instance variables.

```c++
// Adds an ivar to a class.
BOOL class_addIvar(Class cls, const char *name, size_t size,
              uint8_t alignment, const char *type)
{
    // Omit something unimportant

    class_ro_t *ro_w = make_ro_writeable(cls->data());

    ivar_list_t *oldlist, *newlist;
    if ((oldlist = (ivar_list_t *)cls->data()->ro()->ivars)) {
        size_t oldsize = oldlist->byteSize();
        newlist = (ivar_list_t *)calloc(oldsize + oldlist->entsize(), 1);
        memcpy(newlist, oldlist, oldsize);
        free(oldlist);
    } else {
        newlist = (ivar_list_t *)calloc(ivar_list_t::byteSize(sizeof(ivar_t), 1), 1);
        newlist->entsizeAndFlags = (uint32_t)sizeof(ivar_t);
    }

    uint32_t offset = cls->unalignedInstanceSize();
    uint32_t alignMask = (1<<alignment)-1;
    offset = (offset + alignMask) & ~alignMask;

    ivar_t& ivar = newlist->get(newlist->count++);
#if __x86_64__
    // Deliberately over-allocate the ivar offset variable.
    // Use calloc() to clear all 64 bits. See the note in struct ivar_t.
    ivar.offset = (int32_t *)(int64_t *)calloc(sizeof(int64_t), 1);
#else
    ivar.offset = (int32_t *)malloc(sizeof(int32_t));
#endif
    *ivar.offset = offset;
    ivar.name = name ? strdupIfMutable(name) : nil;
    ivar.type = strdupIfMutable(type);
    ivar.alignment_raw = alignment;
    ivar.size = (uint32_t)size;

    ro_w->ivars = newlist;
    cls->setInstanceSize((uint32_t)(offset + size));

    // Ivar layout updated in registerClass.

    return YES;
}

struct ivar_t {
#if __x86_64__
    // *offset was originally 64-bit on some x86_64 platforms.
    // We read and write only 32 bits of it.
    // Some metadata provides all 64 bits. This is harmless for unsigned 
    // little-endian values.
    // Some code uses all 64 bits. class_addIvar() over-allocates the 
    // offset for their benefit.
#endif
    int32_t *offset;
    const char *name;
    const char *type;
    // alignment is sometimes -1; use alignment() instead
    uint32_t alignment_raw;
    uint32_t size;

    uint32_t alignment() const {
        if (alignment_raw == ~(uint32_t)0) return 1U << WORD_SHIFT;
        return 1 << alignment_raw;
    }
};
```

We can know from the code that:
- The **instance variables** are stored in a **class**'s `class_data_bits_t` structure whose name is `bits` in `objc_class`.
- When we read or write **instance variables**, we do not use `.` or `->`, because they are not stored like a `struct`, instead, we use `ivar_t`'s `offset` to read or write.
- `offset = (offset + alignMask) & ~alignMask;` This line is for memory alignment. Assume `offset` is 0x12, and now if we want to add a 4 bytes data such as `int`, the `alignMask` is 0x3, so the value of `(offset + alignMask)` is 0x15, and new value of `offset` is 0x14 which meets the requirement. This is a smart way to get an alignment offset.

You might want to say, we never use `offset` to `get` or `set` a `instance`'s variables. Instead, we use its property to manipulate data stored in an **instance**. Let's take a look at the code about `property`.

```c++
// Adds a property to a class.
static bool _class_addProperty(Class cls, const char *name,
                    const objc_property_attribute_t *attrs,
                    unsigned int count, bool replace)
{
    // Omit somrthing unimportant

    auto rwe = cls->data()->extAllocIfNeeded();
    ASSERT(cls->isRealized());

    property_list_t *proplist = (property_list_t *)
        malloc(property_list_t::byteSize(sizeof(property_t), 1));
    proplist->count = 1;
    proplist->entsizeAndFlags = sizeof(property_t);
    proplist->begin()->name = strdupIfMutable(name);
    proplist->begin()->attributes = copyPropertyAttributeString(attrs, count);

    rwe->properties.attachLists(&proplist, 1, /*preoptimized*/false,
                            PrintPreopt ? "properties" : nullptr);

    return YES;
}
```

So, the property is also stored in `bits`.

Let's call a `getter` to help explain the property.

```objectivec
@interface Animal
@property char *type;
@property (getter=getNum) int num;
- (void)shout;
@end

@implementation Animal
- (void)shout { self.num; }
@end
```

When we call an **instance**'s property, like `someInstance.someProperty`, using `self.num` as an example, which is in `animal.m`'s `shout` method, the complier will change it to `((int (*)(id, SEL))(void *)objc_msgSend)((id)self, sel_registerName("getNum"))` written in `animal.cpp`.

So, the meaning of property is to tell the **runtime** to add an **instance variable** to the **class**, and generate a `getter` and a `setter` for us, when we call the property with **dot** symbol, we are actually sending a message to the instance, and the property saves the information about what message to send exactly.

Besides the role above, due to the property's manifold **attributes** (you can get more information about its attributes [here](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW1){:target="_blank"}), it also help us with memory management and multiple thread atomic.

### Class Variable

Since we use `static` to indicate a variable to be a class's `class variable` directly, there is no need to store `class variable` in a class.

### Instance Method

```c++
BOOL class_addMethod(Class cls, SEL name, IMP imp, const char *types)
{
    if (!cls) return NO;

    mutex_locker_t lock(runtimeLock);
    return ! addMethod(cls, name, imp, types ?: "", NO);
}

static IMP addMethod(Class cls, SEL name, IMP imp, const char *types, bool replace)
{
    IMP result = nil;

    // Omit something unimportant

    // fixme optimize
    method_list_t *newlist = method_list_t::allocateMethodList(1, fixed_up_method_list);

#if TARGET_OS_EXCLAVEKIT
    auto &first = newlist->begin()->bigStripped();
#else
    auto &first = newlist->begin()->bigSigned();
#endif
    first.name = name;
    first.types = strdupIfMutable(types);
    first.imp = imp;

    addMethods_finish(cls, newlist);
    result = nil;

    return result;
}

static void addMethods_finish(Class cls, method_list_t *newlist)
{
    auto rwe = cls->data()->extAllocIfNeeded();

    if (newlist->count > 1)
        newlist->sortBySELAddress();

    prepareMethodLists(cls, &newlist, 1, NO, NO, __func__);
    rwe->methods.attachLists(&newlist, 1, /*preoptimized*/false, PrintPreopt ? "methods" : nullptr);

    // If the class being modified has a constant cache,
    // then all children classes are flattened constant caches
    // and need to be flushed as well.
    flushCaches(cls, __func__, [](Class c){
        // constant caches have been dealt with in prepareMethodLists
        // if the class still is constant here, it's fine to keep
        return !c->cache.isConstantOptimizedCache();
    });
}
```

### Class Method


### Category

### Protocol

### Extension

- Meta class stores class methods.
- Class stores instance methods and some information about how instance variables are stored in instance. But class variables are just `static` variables, they are not stored in here.
- Intstance is the object of a class, it stores instance variables.
