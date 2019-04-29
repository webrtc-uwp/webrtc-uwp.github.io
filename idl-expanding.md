
## Expanding WebRTC IDL

Post cloning the webrtc-uwp-sdk repo, the WebRTC IDL files can be found here:
`webrtc-uwp-sdk\webrtc\xplatform\webrtc-apis\idl`

WebRTC IDL compiler reads a configuration file to know which IDL files to read and in which order. This file is named `config.json`. The IDL parser is a single pass parser and only knows about types that have been previously defined. The parser has no `#include` command thus it cannot know about types in advance. However, it types can be forward declared without a definition allowing for a placeholder type to be used until the type's definition occurs later.

The syntax of IDL is loosely based on C++. The IDL supports basic type like integers, floats, strings and binary buffers. The IDL has a few built in array types such as sets, lists and map. The IDL has a set of built in exceptions that it understands. The IDL has a promise type for asynchronous callbacks and built in support for callback event handlers.

The IDL compiler has these built in keywords:
````
typedef
enum
struct
class
interface
interaction
namespace
throws
using
````

The IDL parser supports these basic types:

````
void

bool

uchar
char
schar
ushort
short
sshort
uint
int
sint
ulong
long
slong
ulonglong
longlong
slonglong

uint8
int8
sint8
uint16
int16
sint16
uint32
int32
sint32
uint64
int64
sint64

byte
word
dword
qword

float
double
ldouble
float32
float64

pointer

binary
size

string
astring
wstring

````

### Namespaces

All types exist inside a namespace. Basic types are at the global namespace `::type` and build in types are inside the zs namespace `zs::type`.

Complex types can be forward declared line follows:
````
namespace MyNamesapce
{
  struct MyStruct;  
}
````


New types can defined at any time based on existing defined types using a typedef inside a namespace as follows:
````
namespace code
{
  typedef ExistingType NewType;

}
````

Typedefs become new types based on the existing type inside the defined namespace. Types can also be brought in from existing namesapaces using the `using` clause.

````
namespace One
{
  struct MyStructA;
  struct MyStructB;
}

namespace Two
{
  // Select a specific type to bring into the namespace.
  using One::MyStructA;
}

namespace Three
{
  // all types from the One namespace are brought into the Three namespace.
  using namespace One;
}

````

### Basic Types

Basic types can be integers, floats, strings and binary blobs. The IDL compiler has built in types including fixed width types. The built-in integer types can be signed or unsigned and the `long` keyword is supported to convert a shorter integer and float type into a longer one. The typedef is used to create new types based on existing types.

````
typedef unsigned int MyUnsignedInt;
````

Strings are treated as first class types since they are too complex too define in another manner and all languages support strings of some kind. The two supported encodings are URF-8 and unicode but UTF-8 is preferred. The IDL compiler will translate strings into other computer languages as appropriate.


### Enums

Enums are defined like C where the values are incremented by one from the previous value. A custom decimal or hexadecimal value is supported.

````
enum Colors
{
  Red,
  Green,
  Blue = 5
};

````

The recommendation is that enums are all predefined before any other types to ensure they are fully defined. Forward declare enums are not recommended.

### struct / class / interface / interaction


The struct, class, interface and interactions are functionally identical. Unlike C++ there is no private or public definitions. The different keywords are used to help distinguish intentions, such as simple structures of data, interfaces of methods and properties, run time classes, and finally interactions between components.

A typical struct of simple types would be defined similar to:
````
[dictionary]
struct MyStruct
{
  int simpleValue;
  float anotherValue;
  MyEnumType someValue;
  AnotherStruct containedStruct;
};
````

The IDL compiler takes the `dictionary` attribute and understands the type to contain only simple name/value pairs of data. A dictionary type can be optimized by ensuring the type has object copy semantic instead of object reference semantics. 

Interfaces can contain properties and methods.

````
interface MyInterface
{
  void MyMethod();
  void MyMethodWithArgs(int arg1);

  [getter, setter]
  int prop;
};

````

The IDL compiler will treat this as an interface with two methods and a property which is only accessible with a getter/setter method.

### Method Name Conflicts

````
interface MyInterface
{
  [default]
  void myMethod();

  [default, altName(myMethodWithArg)]
  void myMethod(int withArg);

  [altName(myMethodWithAnotherArg)]
  void myMethod(MyStruct anotherArg);
};

````

The IDL compiler allows the same method named more than one but not all supported languages support method name reuse. The solution is to use the `altName` attribute allows for a substitute name where the target language does not support the same method name twice. The `default` attribute is used to define which method is preferred where two methods with the same name and same number of arguments is used.

### Struct Constructors

````
class MyClass
{
  [default, constructor]
  void MyClass();

  [default, altname(MyClassClone)]
  void MyClass(MyClass source);
}

class MyClass2
{
  [default, delete]
  void MyClass2();
}
````

By default the IDL compiler defines constructors for structs/classes. The `constructor` attribute must be used on methods that are to be recognized as constructors. To define custom constructors with arguments, define a method with the same name as the class name with a `void` return type. However, if the object should never be constructed then the `delete` attribute can be used to delete the constructor.

If a constructor is not specified, the IDL compiler will generate a default constructor with no arguments. If any constructor is defined (including deleted ones) then the IDL compiler will not generate any default constructors.


### Struct static

````
class MyClass
{
  [default, delete, constructor]
  void MyClass();

  [static]
  MyClass makeMyClass();

  [static, getter]
  int maximum;
}

[static]
class MyClass2
{
  [static]
  void onlyHasStaticMethods();

  [static]
  void seeWhatIMean(int value);
}
````

By default the IDL compiler defines constructors for structs/classes. To define custom constructors with arguments, define a method with the same name as the class name with a `void` return type. However, if the object should never be constructed then the `delete` attribute can be used to delete the constructor.

THe `static` attribute can be defined at a class or method/property level. Static classes can only contain static methods or static properties. Non static classes can contain static methods and properties. Static methods/properties can return any type, including being used as factories to construct objects instead of allowing the object to be constructed directly.


### Struct optional


````
struct MyStruct
{
  [optional]
  int foo;

  int bar;
}

[static]
class MyClass2
{
  [optional, getter]
  int foo;

  [getter]
  int foo;

}
````

The `optional` keyword can used to define name/value pairs or properties that do not have to contain a value. The IDL compiler will attempt to map optional values into platform specific types. For example, in C#, optionals become boxed values for simple types and nullable values for classes.


### Struct Inheritance

The IDL compiler understand inheritance including multiple inheritance. The IDL compiler will attempt to map as close in concept to the inheritance model of the target destination language where possible, or may use containment if multiple inheritance is used but the target language does not support the concept.

````
interface IA
{
   void fooMethod();  
};

interface IB
{
  void barMethod();
};

class C : IA, IB
{
  // object will contain both IA and IB methods
  [constructor]
  void C();
};

````

### Struct events

````
[dictionary]
struct EventInfo
{
  int value;
};

class MyClass
{
  [event]
  void onEvent(EventInfo evt);
};

````

The `event` attribute can be used on a method to define the method as a special method that emits events rather than expecting the method to be called. The IDL compiler will map the type into a callback subscription in the target language as appropriate.


### Promises

````
interface MyInterface
{
  zs::Promise doSomethingAsync();
};

interface MyInterface2
{
  zs::PromiseWith<float> doSomethingAsync();
  zs::PromiseWith<AnotherStruct> doSomethingAsync2();
};

````

The `zs::Promise` type can be used to return a promise that will be completed later. This type will be translated into a target language specific promise type. For example in C# this method becomes `async`. The `zs::Promisewith<...>` template can be used to define a promise that returns a result type later. The promise type can contain any result type desired and can throw exceptions.

### Promise Rejection Types

````
typedef zs::PromiseRejectionReason<MyObject> PromiseWithRejectionMyObject;

````

Defining this special typedef will cause the IDL compiler to recognize that any promise might reject a promise with the resulting type. The rejection type need not be used but the compiler will expect this type is a possible rejection reason.


### Exceptions

```
interface IA
{
  void method() throws(zs::exceptions::InvalidArgument, zs::exceptions::SyntaxError);
};
```

With IDL, generic exceptions are not assumed. Each and every method that throws an exception must contain a definition of all the exceptions that can be thrown. Exceptions of any `zs::exceptions` can be thrown. Custom exception types may be thrown but the translation of these custom exception types must be done manually since the IDL compiler only understands built in exception types. Failure to declare a throwing type will cause the IDL compiler not to generate try/catch blocks and those will likely crash when a native type is thrown across a language boundary.

These exception types are built-in:
````
Exception
InvalidArgument
NotImplemented
NotSupported
UnexpectedError
SyntaxError
RangeError
InvalidModification
NetworkError
InternalError
````

The IDL compiler will attempt to map these types to the closest target language definition that seems related. Unfortunately not all languages have rich exception types.

### Lists, Sets, and Maps

The IDL compiler has a build in set of list, set and map types defined in an `std` namespace. These are templated types as they would be in C++ but the IDL tool does not support any other templated types. These are the only built in supported types at this time. Any other type must be hand coded as a special case.

The following are example use cases:

````
typedef std::list<int> MyIntList;
typedef std::list<string> MyStringList;
typedef std::map<int, string> MyIntToStringMap;
typedef std::set<EnumValue> MySetOfEnumValues;

struct UseSets
{
  MyIntList someIntList;
  MyStringList someStringList;
  MyIntToStringMap someIntToStringMap;
  MySetOfEnumValues someEnumSet;

  std::map<EnumValue, string> myNonTypedefedEnumToStringMap;
}
````


### special attribute

The `special` attribute is used in two scenarios. The first is to define build in types under the `std` namespace and the `zs` namespace. These types are pre-known to the IDL compiler which will generate specialized language specific definitions for these targets. When not used on built-in targets, the special can be used on structure/interface/class definitions. The IDL compiler will generate a template version of the structure based on the definition but will intentionally "compile-out" the definition and assume an "override" manual definition will be used instead.

This allows for language specific types, like HWNDs, or media element types to be brought across language barriers in a platform specific way. The IDL compiler has no way to generate platform specific language bindings and relies upon the developer to manually code these class types.

A recommendation is to isolate the platform specific types as much as possible in classes. For example, define a `MediaElement` interface that represents the platforms media element. This interface can be used throughout the rest of the IDL but can internally contain the handle to the native type. This allows the IDL type to be used while still allowing the platform specific type to be wrapped inside an IDL representation.

For example:
````
interface IMediaElement
{
  [getter]
  int totalMediaPhases;

  [static]
  IMediaElement create();
};
````

The IDL generator will construct an object in all targets as specified. This can be used as a template example which can be copied and modified to the specific target's requirements.


## Language Target Generation

The IDL compiler currently supports the following targets types:

  * `wrapper` - a C++ wrapper - This layer is entirely generated, this is the C++ API that all other languages will use as their access point into the API.
  * `impl` - the glue bindings - By default the `impl` layer is generated ONCE only. Once generated the layer contains stubbed generic code that must be implemented by the developer to normalize the project code to the IDL's internal definitions. Regenerating this layer is sometimes necessary but should be carefully done since the IDL compiler will generate and replace hand coded wrapper glue files and should only be done to generate new (or redefined) classes nor originally present in the previous IDL generation. A git tool is recommended to discard unwarranted replacement code while retaining new generated stub implementation glue code.
  * `c` - a C wrapper for the C++ wrapper code - This layer is entirely generated and creates C language bindings in a way that other languages that need to call into DLL can do so in a predictable (and ugly) way. For example, C# dot net can load a wrapper glue DLL and call the generated C APIs using C#'s "pinvoke" mechanism. The C DLL is not meant to be used directly and is intended to be auto-wrapped in other languages that call the C DLL APIs.
  * `dotnet` - a .NET wrapper - The IDL generator can generate .NET code that automatically calls the C APIs using "pinvoke". To the consumer of the generated IDL code, the C# code looks like interfaces and classes but under the hood C handles to C++ objects are used.
  * `cppwinrt` - a Windows managed code wrapper - The IDL generator can create a managed code layer for use in UWP. The IDL compiler can auto generate managed C++ code that will allow the code to be compiled into a DLL/winmd library or a managed code nuget package for use in C# UWP or other managed code languages.
  * `cx` - a C++/CX wrapper - This generates legacy C++/CX wrapper code. This is wrapper is not intended to be used.
  * `msidl` - An MSIDL 3.0 generator - The cppwinrt wrapper needs MSIDL 3.0 definitions to be able to create a winmd. This generator creates the MIDL files.
  * `python` - a python generator - This generates python code that can call into the C DLL wrapper. This is experimental.


### Regenerating IDL

Whenever IDL files are changed, the IDL files need to be regenerated. WinUWP does not automatically detect IDL changes so manual regeneration of the IDL targets must be done as follows:

`python run.py -a clean prepare build -p winuwp -x64 -c debug --cleanOptions cleanidls`

#### Regenerating implementation glue files

`python run.py -a clean prepare build -p winuwp -x64 -c debug --cleanOptions cleanidls --idlImpl`

If a new definition of an IDL is created, new glue code might be needed. Use this `--idlImpl` with extreme caution. This will cause the IDL compiler to create C++ implementation stubs and overwrite any manually coded implementations thus far. Back up your glue code before running the glue stub generator.


### How the glue works

A typical IDL folder structure is as follows:
````
idl                                      - the IDL files are typically places in this folder
idl\wrapper                              - the manually coded C++ implementation glue layer
idl\wrapper\generated                    - the generated C++ header files that are used by all other IDL targets (normalizes the API in C++)
idl\wrapper\generated\c                  - the generated C binding definitions
idl\wrapper\generated\cppwinrt           - the generated C++/winrt managed code language bindings
idl\wrapper\generated\dotnet             - the generated .NET managed code language bindings (what will use pinvoke to call generated C API)
idl\wrapper\generated\msidl              - the generated MSIDL 3.0 definitions required to generated managed code winmd files
idl\wrapper\generated\python             - the generated python code that uses the C generated language bindings

idl\wrapper\override                     - contains all the "special" platform specific class definitions the IDL compiler could not auto generated
idl\wrapper\override\c                   - contains manually code C wrapper logic for special classes
idl\wrapper\override\cppwinrt            - contains manually code cppwinrt managed code C++ wrapper logic for special classes
idl\wrapper\override\msidl               - contains manual definitions for MSIDL 3.0 special classes
idl\wrapper\override\python              - contains manual definitions for python special classes

````

The IDL compiler will generate most of the IDL code automatically. However a clue layer coded in C++ is required. While the IDL generator creates stub implementation glue code, the code must be filled in to bing the wrapper implementation to the native C++ implementation. The wrapper glue is needed since native C++ code is way too complex to generate wrappers in other languages so a "normalized" version of the C++ is used as a proxy to the real native implementation. The `wrapper\generated` C++ interface is a generated normalized version of C++ based on the IDL definitions. The rest of the languages can predict exact C++ wrapper definitions based on the IDL files which would not be possible in normal native C++ code.

The following outputs projects need to be compiled:

  * `<glue>.lib` - A static C++ glue library needs to be created containing the `impl` code for C++ bindings to the native C++ implementation
  * `<cppwinrt>.dll` - A managed cppwinrt code must be generated containing the cppwinrt generated wrapper glue code.
  * `<cppwinrt>.winmd` - A managed code winmd binary definition file.
  * `<cdll>.dll` - A "C" DLL containing the C definitions exported in a DLL definition file.
  * `<dotnet>.dll` - A dot net wrapper that uses the C DLL bindings to pinvoke into C which then calls C++ APIs.


The calling is done as follows:

````
C# UWP app <---> winmd <---> cppwinrt dll <---> C++ wrapper generated glue <---> C++ implementation glue
C# .NET app <---> dotnet DLL <---> C dll <---> C++ wrapper generated glue <---> C++ implementation glue
python app <---> python wrapper <---> C dll <---> C++ wrapper generated glue <---> C++ implementation glue
````

### Overrides

To create an override for a platform specific file, copy the template from the `generated\<x>` directory into the `override\<x>` directory and modify the file as appropriate for the target language. Follow existing code as examples in WinUWP to how it is all done as example guidelines.


#### Cppwinrt override

C++/WinRT tooling inside Visual Studio takes winmd files (generated from MSIDL) and generates a wrapper glue layer that needs to be implemented by the developer to glue between C++ and UWP managed code. Fortunately the "zs" IDL compiler (not the MSIDL compiler) tool generates most of that implementation for cppwinrt but it cannot do so for special attribute declared structures. Those must be hand coded.

The first step is to create a MIDL 3.0 file representing the API for cppwinrt. The "zs" IDL compiler will generate an example MSIDL template for all special structures declared. Theses are output here:
`webrtc-uwp-sdk\webrtc\xplatform\webrtc-apis\windows\wrapper\generated\msidl`

And each special file contains a `*.idl.template` definition. This file must be copied to the `override\msidl` folder and renamed to just `.idl`. The contents of the IDL file must be changed to match MSIDL 3.0 specifications to define the desired managed UWP surface API in the winmd file.

More information on MSIDL (MIDL) 3.0 can be found here:
[https://docs.microsoft.com/en-us/uwp/midl-3/intro](https://docs.microsoft.com/en-us/uwp/midl-3/intro)

Once the MSIDL is defined, the file must be added to the cppwinrt UWP project and compiled.

When compiled, the cppwinrt generation tool will create an example source code to be coded here:
`webrtc-uwp-sdk\webrtc\windows\projects\msvc\Org.WebRtc.Universal\Generated Files\sources`

 The template code should be copied out of that folder and included as part of the cppwinrt project. From there the code needs to talk to the IDL wrapper and/or the wrapper implementation.