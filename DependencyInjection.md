
# Dependency Injection
    Based on Dependency inversion principle which is the last one of SOLID principles.
    An architectural design pattern which solves the following primary problems.
      1. Testability (Not mockable)
      2. Dependency Resolving
      3. Manual instantiation of dependencies
      4. Unique instances 
      5. Embedded instantion code
      6. Hidden dependcies
    DI solves dependency resolving through the help of DI container.
    Dependency instances are being injected into the consumers.
    It would be easier to mock those instances during unit test cases 
    It allows the calling party / caller / consumer to decide what kind of dependencies
    this class should use.
   ## Pros
   ### Configurability
       Switching diff type of DB  service implementation to be injected  
   ### Testability
	Services can easily be mocked to run unit test cases
    Loosely coupled rather than concrete implementation using interfaces
    Maintainability and flexibility
  ### Singleton service
    A single instance (singleton) for the required services.
    
 # Angular DI History
  ## Angular JS
    Used string as keys for storing the DI Token.
    Single global container 
    
    String tokens are nothing but the service/factory names 
    which are converted into their string value by calling toString()
    on them and then the respective dependencies list are extracted using regExp.
    Required min safing with annotations 

