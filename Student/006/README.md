
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

- [Frida pre-knowledge:iOS/ObjC syntax advanced and its ARM assembly implementation] (#frida pre-knowledge iosobjc syntax advanced and its arm assembly implementation)
- [Underlying Implementation Logic for 1.ObjC Classes and Methods] (#Underlying Implementation Logic for 1objc Classes and Methods)
- [(1) Basic Concepts] (#1 Basic Concepts)
- [(2) Reference Relationship] (#2 Reference Relationship)
- [Structure and Messaging of 2.ObjC Runtime Classes] (#2objc Runtime Class's Structure and Messaging)
- [(1) Runtime class structure:] (#1 Runtime class structure)
- [(2) Messaging in ObjC:] ( Messaging in #2objc)
- [(1)SEL](#1sel)
- [(2)IMP](#2imp)
- [(3) Message Mechanism] (#3 Message Mechanism)
- [Reflection of 3.ObjC runtime ->KVC] (#3objc-runtime's reflection-kvc)
- [(1) Concepts] (#1 Concepts)
- [(2) Use] (#2 Use)
- [(3) Principle Overview] (#3 Principle Overview)
- [4.ObjC dynamically adds properties to objects using AssociatedObject] (#4objc dynamically adds properties to objects using associatedobject)
- [(1) Concept] (#1 Concept-1)
- [(2) Simple Use] (#2 Simple Use)
- [5.ObjC uses Method Swizzling for method binding] (#5objc uses method-swizzling- for method binding)
- [(1) Concept] (#1 Concept-2)
- [(2) Simple Use] (#2 Simple Use -1)
- [6.ARM schema/instruction set/register/number] (#6arm schema instruction set register number)
- [(1) ARM Schema:] (#1arm Schema)
- [(2) Register:] (#2 Register)
- [(1) Universal Register:] (#1 Universal Register)
- [(2) Special Register:] (#2 Special Register)
- [(3) Processor Status:] (#3 Processor Status)
- [(3) Script Number:] (#3 Script Number)
- [7.ARM64 arithmetic/transport/logical/address/shift instruction] (#7arm64 arithmetic transport logical address shift instruction)
- [8. Implementation details of heap and method on ARM64 instruction set] (#8 Implementation details of heap and method on arm64 instruction set)
- [(1) Heap Foundation Introduction] (#1 Heap Foundation Introduction)
- [(2). Heap Related Registers] (#2 Heap Related Registers)
- [(3).Stack Frame Structure] (#3 Stack Frame Structure)
- [9. Function Call/Parameter Pass/Inbound Heap Full Flow] (#9 Function Call Parameter Pass Inbound Heap Full Flow)
- [(1) function call logic and parameter pass] (#1 function call logic and parameter pass)
- [(2) Assembly code for the message mechanism in OC] ( Assembly code for the message mechanism in #2oc)
- [10.ObjC Assembly Static Analysis and CrackMe Dynamic Debugging] (#10objc Assembly Static Analysis and crackme Dynamic Debugging)
- [Summary] (#Summary)
- [Ref.] (#Ref.)

<!— /code_chunk_output —>

# Frida Preknowledge: Advanced iOS/ObjC Syntax and Its Implementation in ARM Assembly

Learning the properties of these languages and assemblages helps us to understand the Frida later in the hook when the specific register represents a specific value. For example, the hook function, why x0 for the object itself, x1 for the selector method name, because this is determined by the calling convention, or why we do not use ObjC.implemt to hook, but Interceptor.attach, because ObjC itself is a runtime of C++, so we can use the same hook address. The pre-language learning is the theoretical basis of learning Frida hook ObjC, I hope you can master.

Objective-C language is a dynamic language, it puts a lot of static language in the period of compiling and linking to the runtime to deal with. The advantage of this dynamic language is that we can write code more flexibly, such as we can forward the message to the object we want, or exchange the implementation of a method at will. This feature means that Objective-C requires not only a compiler, but also a runtime system to execute the compiled code. For Objective-C, this runtime system is like an operating system: it allows all the work to run properly. This runtime system is Objc Runtime. Objc Runtime is actually a Runtime library, it is basically written with C and Sinks, the library makes C language has object-oriented capabilities. This article contains the following knowledge points that you can master when you are finished:

>Frida Prerequisites: Advanced iOS/ObjC Syntax
>- Underlying Implementation Logic for ObjC Classes and Methods
>- Structure and messaging of ObjC runtime classes
>- Reflection of ObjC runtime->KVC get and set class properties
>- ObjC dynamically adds attributes to objects using AssociatedObject
>- ObjC uses Method Swizzling for method binding

> ARM Assembly Hands-on Learning
>- ARM Architecture/Instruction Set/Register/Encoding
>- ARM64 arithmetic/transport/logic/address/shift instructions
>- Implementation Details of Heap and Method on ARM64 Instruction Set
>- Function Call/Parameter Pass/Inbound Heap Heap Full Flow
>- ObjC assembly static analysis and CrackMe dynamic debugging


# The underlying implementation logic of 1.ObjC classes and methods

## (1) Basic Concepts

- Root class: Almost all classes in OC inherit from NSObject, the NSObject class is the root class, and the parent class of the root class is nil
- Meta-class: In our usual development will use the class method and the instance method, but in the underlying implementation there is no such distinction, in fact, all through the instance method to look for, in order to distinguish between the two methods, the underlying introduction of the concept of meta-class, and then the instance method is stored in the class, class method is stored in the meta-class. The class's isa points to a metaclass. (All classes are themselves an object)
- Root metaclass: The class to which isa of the root class NSObject points

## (2)Reference Relationship

![1](image/1.png)

(3) The underlying implementation of the class:

First write the code below and then turn to c.

```ObjectiveC
#import <UIKit/UIKit.h>
@interface MyClass : NSObject

@property NSString *myProperty;

@end
@implementation MyClass
-(void)myMethod{
    NSLog(@"my method");
}
+(void)myClassMethod{
    NSLog(@"my class method");
}

@end
int main(int argc,char *argv[]){
    @autoreleasepool {
    [MyClass myClassMethod];
    return 0;
}
}
```
Convert it to C using the following command

```bash
xcrun -sdk iphoneos clang -rewrite-objc -F UIKit -fobjc-arc -arch arm64 ClassAndMethod.m
```

Open the C file for analysis using VS

Search for the MyClass we wrote to find its declaration and its implementation

![2](image/2.png)

![3](image/3.png)

Discover the NSObject_IMPL attribute in its implementation, search

![4](image/4.png)

You can see that inside is actually a Class pointer, and look at its declaration and see that it's a objc_class structure.

- First property: isa pointer (inherited from root class)
- Second property: parent class pointer

- Third property: Used to cache the most recently used methods.
- Fourth property: storage of instance methods, properties, protocols in class

Here we can see the basic information of the class structure in OC, and the objc_class structure is defined in the NSObject.h header file, it inherits the '_class_t' structure defined in the runtime.h header file, the next we look at the '_class_t' structure.

Then we pull the code to the end and you can see that the defined classes are written in the following section

![7](image/7.png)

And it is initialized in the 'OBJC_CLASS_SETUP_$_MyClass' method, we can see that the initialization is divided into meta-class and class

![8](image/8.png)

Retrieve 'OBJC_CLASS_$_MyClass' View its type Discovery Yes '_class_t' Structure

![9](image/9.png)

The structure is defined as follows

![10](image/10.png)

You can see that there is a '_class_ro_t' structure for attributes in the structure body, and the search finds its storage and definition

![11](image/11.png)

![12](image/12.png)

Then retrieve 'OBJC_METACLASS_$_MyClass' and 'OBJC_METACLASS_$_MyClass' separately to see the following implementations

![14](image/14.png)

Both RO properties here are readonly read-only determined in the editor, continue to retrieve both variables to view the definition

![15](image/15.png)

Here we can already see the difference between the method attribute object method and the class method, which are saved in the meta-class and the class

Continue retrieving 'OBJC_$_INSTANCE_METHODS_MyClass' and 'OBJC_$_CLASS_METHODS_MyClass' look-at methods and object methods

![16](image/16.png)

We can see the object method myMethod and the class method myClassMethod which we write by ourselves. We know that '_class_t' is the class structure, the interior contains the method and the attribute structure body '_class_ro_t' attribute. In the actual realization process, we realize that the 'OBJC_METACLASS_$_MyClass' and 'OBJC_METACLASS_$_MyClass' based on '_class_t' are the class and the meta-class respectively for a class, and both of them have the realization 'OBJC_$_INSTANCE_METHODS_MyClass' and 'OBJC_$_CLASS_METHODS_MyClass' based on '_class_ro_t' structure body.

Inheritance of classes can be summarized as follows:

isa points to:

- The 'isa' of the instance variable points to the corresponding class objc_
- 'isa' of the class points to the corresponding meta-class
- 'isa' of a metaclasses points to the root metaclasses
- 'isa' of the root metaclass points to itself

Inheritance of class:

- Class's 'superclass' points to parent class
- Parent's 'superclass' points to root class
- 'superclass' of the root class points to 'nil'

Inheritance of meta classes:

- The 'superclass' of a meta class points to the meta class of the parent class of the corresponding class
- The 'superclass' of the parent's metaclass points to the root metaclass
- 'superclass' of the root metaclass points to the root class
- 'superclass' of the root class points to 'nil'

Here our analysis is Class —>'_class_t' (class structure body)—>'_class_ro_t' (properties within a class structure method structure)—>'_methood_list_t' (method list of classes)—>'_objc_method' (structure body for a method within a class, including the name method name hash SEL and the method actual address IMP)

# Structure and Messaging of 2.ObjC Runtime Classes

## (1) Runtime class structure:

![48](image/48.png)

We have analyzed the structure of the class in OC, but class_ro_t is a read-only structure. In order to realize the dynamic of OC language, we add an intermediate layer to the class at runtime. We borrow [Demo case] from the book of AloneMonkey's books (https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-4/4.3%20%E7%B1%BB%E4%B8%8E%E6%96%B9%E6%B3%95) to view the structure of the class at runtime

![47](image/47.png)

You can see that there is more in the class structure