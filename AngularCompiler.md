# Angular Compiler History
## 1.Template Compiler (Angular 2+) released in 2016 
 	compiles html template into generated JS code to both render and Change detection at run time.
	ngFactory file generated from the html template

	Cons:
	    Generated template code is too verbose.
	    Added too many bytes / too much overhead to the bundle.
 
 ## 2.View Engine (Angular 4+)
	Template converted into list of rendering operations in the form of data structure instead of instructions.
	There is a runtime component which traverses this data structure to create / delete /
		update the Dom nodes or execute the CD.
    Pros:
      bundle size is optimised.
    
    Cons:
    	Tree shaking is difficult in dynamic component initiation
    	ComponentFactoryResolver is a map of (component , componentFactory) 
      
## 3.Ivy (Angular 8+)

	In addition to transpiling ts to js , it also parses the decorators and templates and generate some code
	  that runtime can understand.

Angular decorators  like @component, @directive get compiled into static fields of their respective classes.
Separate ngFactory file (View Engine) moved into the component and directive classes themselves.
ngComponentDef added as a static field in the component Class itself.

Runtime broken into set of individual instruction sets -> kind of assembly language for angular  templates 
Templates get compiled into 2 sequence of instruction set.
  
  ### Instruction set are for 
   DOM Creation
   Data binding 
   Internationalization 
   Change detection 
   Styling
   Content projection
   Dependency injection 
   Pipes 
   SVG 
   Containers 
   Queries 
### @Injectable => ngIntjectableDef 
	It has a factory function which is responsible for creating the instance of the associated service 
	For every dependency, it needs to be injected.
	The factory function will call Ivy inject (ng.inject()) 
	instruction by giving a token and get back the dependency instance.

### Optimizabiity:
    Ivy leverages the JS ecosystem tools to provide optimisability

    Tree Shaking
    Code which not to be used in runtime , that will be removed.
### Incrementally:
    Speed up compilation process dramatically 
    Previous engines create global compilation.
    They would compile the components and directives from external libraries which are being imported.
    Ivy implemented a rule called 'locality'. It won't compile the external lib's components and directives.
                
## Design Goals:
1.Simplification of angular???s internal working.
    
2. Remove complexities in how we compile the applications
how we package , bundle and ship the libraries on npm
how these parts work together and depend on each other at run time. 

Angular compilation is an incremental process which provides flexible technical foundation for angular apps.

### Phases of Typescript compiler:
Program creation => Type checking => Emit 

## Phases of Angular Compiler:
Program Creation => Analysis => Resolve => Type checking => Emit 

### 1.Program creation
   Typescript???s process of discovering all the source files and it starts from tsconfig.json file 
   Ngc compiler adds little more on this process by creating some ddtional files such ngFactory files.
### 2. Analysis
   Looking into all the classes with decorators 
   Component =>  template gets parsed
   Analysing them in isolation
### 3. Resolve
   It does the above steps now in the context of larger picture
   Component =>  module mapping 
   Global decisions are made and optimizations are done based on that.
### 4. Type checking
   Typescript check on errors and also report error on angular templates
      
   Angular specific task during this step:
      i)Tree shaking in angular source code  
      achieved via Compilation scope (ngModule declarations)
      and Export Scope of all imported components ( imports ).
      ii)Surface the errors in the structure of the application
### 5. Emit
    Low level JS creation done after series of transformation - JS is 
ready to run in the browser 
Along with this, Angular adds some imperative code generated out of angular classes.
Translates the declarative template code into imperative instructions which are to be in browser.
    
JIT compiler (View Engine)
    Typescript code => tsc => js code => declarative code related to angular would still be available in
    run time =>  compilation happens at run time in the browser.
AOT (ivy compiler)
   Compiler takes declarative template + decorators => imperative code.
   So no more application angular code shipped to browser.
   Component meta data already added to ngComponentDef static field. 
   Ivy allows us to ship the AOT compiled code to  npm 

Ivy offers JIT / AOT interop

## Compilation Model:
d.ts file (type declaration file) describes shape of the types declared in the original file.

When we use NPM libraries, TS needs to have these .d.ts files 
to get to know what are the types are being imported 

Ivy changes the d.ts file and includes info abt the component that future compilations will need 
generic type of component definition => selector, what inputs are etc  are added.
Allows the future compiler to make use of it .
These pieces are part of components??? public API.

## Features of Compiler:
### 1. NgModules Scope 
  No explicit imports of the templates / components module  Compilation scope 
   eg: app component declared in app module,
   all other components declared in the same module are accessible
   in app component.
### 2. Export scopes
    All the components / parts of the modules which are imported.
    Due to the above, tree shaking is acheivable.
### 3. Partial Evaluation
    Angular compiler has typescript interpretor 
    Compiler runs almost the ts code which we write , in order to evaluate the following:
       Property chains (foo.bar.baz)
       Literals (objects, arrays)
       Constans/ Variables
       Binary / ternary / logic expressions 
       String templates 
       Function calls
       Imports / Exports 
                  
  DynamicValue() used for variables / properties whose values cant be evaluated during compilation.
  It helps the compiler throw a well formed error message.

### 4.Template type-checking 
 Angular template expressions are transformed into TS code called ???Type check blocks???
 along with some comments and numbers.
	
 These numbers are offsets in the templates from where we got these expressions.
   - this was never emitted as JS .. never shown to the user / browser .
    this will be fed to typescript compiler (tsc)  and it will return errors if anything observed. 
    those errors will be shown to the developer mapped with the context of the template.

  Refined error message provided for template type checking
       
  Template type checking for *ngFor:
  type checking feature implemented for *ngFor loop variable

                
     - Reference 
     [Deep Dive into the Angular Compiler | Alex Rickabaugh | #AngularConnect](https://www.youtube.com/watch?v=anphffaCZrQ&list=PLAw7NFdKKYpE-f-yMhP2WVmvTH2kBs00s&index=2)
        
