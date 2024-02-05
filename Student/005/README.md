[toc]

# ObjC Syntax, iOS Application Development, and Getting Started with Objection Automation hook

# Intro

This article is the second lesson of the Challenge Reverse iOS APP without macOS series, iOS Reverse Basics, which is intended to learn some common knowledge about the reverse process of iOS APP.

- ObjC underlying syntax and messaging
- iOS Development Simple Understanding
- iOS Signature Related Understanding
- Why do business signatures sell?
- Can I sign with my client? Without paying for that?
- Can IPAs downloaded online be used painlessly for a long time?
- Is third-party software store available? How you feeling?
- AltStore 3rd party software store experience
- Objection Automation Converse and hook
- Objection Break Simple CrackMe

This series is a good series of works for the students. The accessories, apk, code and so on, are in my project. You can choose from:

[https://github.com/r0ysue/AndroidSecurityStudy](https://github.com/r0ysue/AndroidSecurityStudy)

# 1.Objective-C Base Syntax and Messaging

We've got the simplest Hello World!source code to learn the basic grammar of ObjC.

## (1) class declaration and implementation

![1](./image/1.png)

```ObjectiveC
Declaration: All classes inherit from the NSObject class
@interface test : NSObject {

}
@end

Implementation:
#import "test.h"
@implementation test

@end
```

## (2) Declaration and Implementation of Class and Instance Methods

![2](./image/2.png)

### (1) Class method

```ObjectiveC
Declaration:
+(void)class_method;

Implementation:
+(void)class_method;
{
NSLog(@"This is class_method");
}
```

### (2) Instance method

```ObjectiveC
Declaration:
-(void)instance_method;

Implementation:
-(void)instance_method;
{
NSLog(@"This is instance_method");
test *test1 = [test new];
}
```

## (3) Variables and Attributes

Variables in the Objective- C class default to private permissions, and objects are not directly accessible, or an error is reported, and attributes are declared using @property, with the option to automatically generate the getter() and setter() methods

```ObjectiveC
Declaration:
{
int num1;
@public
int num2;
}
@property(assign)int num1;//(assign)	Property attributes are written in parentheses
@property(assign)int num2;

Implementation:
@synthesize num1;
@synthesize num2;
```

## (4) Messaging

The relation between class and method in C++ is very clear. A method must belong to a class, and it is bound tightly at compile time. It is impossible to call a method that does not exist in class. However, in Objective-C, the relationship between the class and the message is loose, the calling method is considered to send the message to the object, and all methods are considered to respond to the message. All message processing is not dynamically determined until run time, and is left to the category to decide how to handle incoming messages.

```ObjectiveC
[test class_method];
test *test1 = [test new];
[test1 instance_method];
```

Full Class Code

```ObjectiveC
Class declaration:
@interface test : NSObject{
    int num1;
    @public
    int num2;
}
@property(assign)int num1;
@property(assign)int num2;
+(void)class_method;
-(void)instance_method;

@end

Class implementation:
    #import "test.h"
    @implementation test
    @synthesize num1;
    @synthesize num2;
    +(void)class_method;
    {
        NSLog(@"This is class_method");
    }
        -(void)instance_method;
    {
        NSLog(@"This is instance_method");
        test *test1 = [test new];
    
}
@end

Class instantiation and method invocation:
    #import <Foundation/Foundation.h>
    #import "test.h"
    int main(int argc, const char * argv[]){
        @autoreleasepool {
            // insert code here...
            NSLog(@"Hello, World!");
            [test class_method];
                
            test *test1 = [test new];
            [test1 instance_method];
                
        }
        return 0;
}
```

# A Brief Description of 2.iOS Development

## (1)ios architecture

![3](./image/3.png)

- Application Layer,
- Cocoa Touch Layer,
- Media Layer,
- Core Services Layer,
- Core OS Layer,
- The Kernel and Device Drivers layer (kernel and driver layer)

## (2) System architecture of each layer function module view:

![4](./image/4.png)

##(3) Use Xcode to establish a simple MVC program

- MVC Brief in Xcode

- The model is the application's data.
- The view is the user-visible interface containing the various UI components built in Interface Builder.
- Controllers are logical units that connect model and view elements together, handling user input and UI interaction.

- Interface Builder Brief:

Interface Builder is a control toolbox, developers only need to drag the controls from the toolbox to the window or menu to complete the interface design. Then, we connect the "action" and the object of the control with the "method" and the "interface" of the object in the application code.

### (1) Open xcode New Project, where xcode version 14

![5](./image/5.png)

### (2) Select iOS APP Template

![6](./image/6.png)

### (3) Fill in the project-related information

![7](./image/7.png)

### (4) Build Project After Save You Can See Automatically Generated Project Template Files

![8](./image/8.png)

- AppDelegate: The AppDelegate file is the delegation of applications to respond to system and application events, handle the application lifecycle, and configure and operate at the application level.
- SceneDelegate: The SceneDelegate file is a new file from iOS13 and above that manages scenes in the application, each representing a window and corresponding view hierarchy in the application.
- ViewController: The Controller part of the MVC pattern is used to manage the interface of the application program and deal with the interaction of the user. The corresponding ViewController class inherited from the UIViewController class is part of the UIKit framework, is the core component and class of building iOS application program.
- storyboard: Storyboard File Where LaunchScreen is the application launch screen, we don't mind him, Main is our main storyboard, containing all the UI elements and the view controller.
- Assets.xcassets: Resource folder.
- Info.plist: The application configuration file.

### (5) Design Interface

Opening the Main.storyboard storyboard, the arrow points to the first two objects in the main view file, the user input area (text box) and the output (label), while the third object (button) triggers the action in the code to set the content of the label to that of the text box

![9](./image/9.png)

### (6) Use controls

Using the Interface Builder editor (shortcut key Shift+command+L), we pull the control directly to the page. Here we pull a text box (UITextField), a label (UILabel), and a button (UIButton). There are three component objects, the first two objects are the user input area (text box) and the output (label), and the third object (button) triggers the operation in the code, so that the content of the label is set to the content of the text box. Set the view corresponding class to ViewController when the component is set

![10](./image/10.png)

### (7) Create and connect outlets and operations

Introduction to IBAction and IBOutlet:

- IBAction: From a return value perspective, acts like void, and only methods whose return value is declared IBAction can be connected to controls in storyboard
- IBOutlet: Only properties declared as IBOutlet can be connected to controls in storyboard

In the ViewController class declare two IBOutlet properties corresponding to UILabel and UITextField two input and output objects, declare a method with return value of IBAction type corresponding to the button click event, and at the same time in the ViewController file implementation, you can see, when the object and method declared three circles waiting to be bound with storyboard

![11](./image/11.png)

```ObjectiveC
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController
@property(strong, nonatomic)IBOutlet UILabel *userOutput;
@property(strong, nonatomic)IBOutlet UITextField *userInput;
-(IBAction)setOutput:(id)sender;
@end

#import "ViewController.h"
@implementation ViewController
@synthesize userOutput;
@synthesize userInput;
-(void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view.
self.userOutput.text=self.userInput.text;
}

@end
```

### (8) Binding

Drag directly to bind space to properties and methods

![11](./image/11.png)

### (9) Set Start-up View

![12](./image/12.png)

Boot Project Discovery ran successfully

![13](./image/13.png)

### (10) Simple analysis of project initiation process:

main.m -> Locate the AppDelegate file

![14](./image/14.png)

AppD