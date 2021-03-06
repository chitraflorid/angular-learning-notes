# Ivy Angular Runtime

This part of Angular code ships with application code to the browser and runs there in browser.
Component code with decorator and template => compiler => Component Definition => Runtime => DOM.
Component definitions are set of instructions which run time could execute.

ComponentName.ngComponentDef = defineComponent({
Selectors: [],
Template: {
		// create part and update part 
 
}, 
Factory: {},
},
Directives: []
});

## 1. Application bootstrap
 Module set up => View Creation => Change Detection
 While bootstrapping the application, the framework is looking out for the bootstrap prop in @ngModule decorator. 
   ### Root setup
	Locate root element => it takes the selector from the component def
	    and then query for the selector using document.querySelector 
	Instantiate the root component using component definition’s factory 
	Render the root component using component definition’s template function
 ## 2. View Creation
  DOM creation and directives’ instantiation 
  Invoke component in create mode 
  App.ngComponentDef.template(create, app);
  InfoCard.ngComponentDef.template(create, info);
  template() :
	Html template got transformed into template function  runtime need to call this with the relevant flag (create / update mode).
	It's a function which has set of instructions which are implemented by angular framework.
	Creation instructions and Update instructions
  
   ### LView
  	During creation of DOM, we keep track of them using an internal array data structure called LView.
  	Reason for LView:
	   Later during change detection these elements might need to be updated. 
        Referring the elements via DOM APIs may slow down the application performance. 
        LVIew (Logical View): unique to each view / comp template instance 
    
         LView => [DOM elements, Binding Values, Directive instances]
         After each DOM node creation, the LView  gets updated with the relevant node info.
        LV will be created 1 per component instance.
  ### Directive Matching
  
  During element creation, we are also matching the directive against the element which are created.
  Directive matching process is expensive.this will happen only once per directive when first time 
   its found in the component.
   For further reference , this directive instance will get stored in a shared data structure
     called TView (Template View)/

   ### TView
   
 	For storing the shared data between all instances of the view. ( 1 per component type )
 	TView will be stored in componentDef

   Directive matching => once found => initiate using it’s factory() method and directiveInject(<directiveRef>).
   If not there , fall back to the Module Injector 
   it would take the token and first check the Directive Injector tree.
   It's a place to keep track of all added providers in the directive definition
    
## 3. Change Detection
It's lot more predictable as it runs CD from top to bottom.
update instructions would be executed from top to down.
Advance instruction would be used to keep negotiate the elements which have bindings
advance(1) => index @ LView 

need to keep track of 2 things
 current node => current LView  
 current binding => old bound value 

Binding orders are not guaranteed within the nodes

micro optimisation: 
Property chaining 

### Pre Ivy Change detection
One full pass for directive input 
And another pass for elements and text binding

Component tree checked from top-to-bottom.
Nodes checked from top-to-bottom for directive inputs.
node checked from top to bottom for property or text bindings.

##. Lifecycle Hooks
As part of change detection, these hooks are being executed.
ngOnInit
ngOnChanges 
ngDoCheck 
=> these 3 run after @Inputs checked
   node by node 

ngAfterViewInit 
ngAfterViewChecked 
=> after view checked
    view by view 

ngAfterContentInit
ngAfterContentChecked
=> After content checked
    view by view

After template function invocation:
	=> refresh embedded views 
	=> flush all content hooks 
	=> set host bindings 
	=> check child component 
	=> Flush all view hooks - bottom - up

- Reference
How Angular works | Kara Erickson | #AngularConnect
(https://www.youtube.com/watch?v=S0o-4yc2n-8&list=PLAw7NFdKKYpE-f-yMhP2WVmvTH2kBs00s&index=26)
