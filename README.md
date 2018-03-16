### Core Data

Object-oriented database, usually backed by SQL (but also can do XML or just in memory).   

To use Core Data we have to create `Data Model` and add some code in `AppDelegate`.  
In `AppDelegate` we need `lazy var persistentContainer: NSPersistentContainer`, probably also `saveContext()`
 and change `applicationWillTerminate` to call `self.saveContext()`.  
     
The persistence stack is a set of objects that work together to fetch and save your data, consists of three objects:  
• A **managed object model** (`NSManagedObjectModel`) describing the structure of the data  
• A **managed object context** (`NSManagedObjectContext`) for communicating with the data  
• A **persistent store coordinator** (`NSPersistentStoreCoordinator`) for dealing with actual storage of the data as a file       
     
Starting in iOS 10, this entire stack is created for us by an NSPersistentContainer object.  
The template code provides a lazy initializer for this object:

```
    lazy var persistentContainer: NSPersistentContainer = {
        let container = NSPersistentContainer(name: "ReadingPractice")
        container.loadPersistentStores { storeDescription, error in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        }
        return container
    }()
```
       
This will be added automatically if we check `Use Core Data` when creating a project.  


#### The managed object model

A Core Data database stores things in a way that looks very object-oriented to our code:  
**Entities** (which are like a class)  
**Attributes** (which are like a var)  
**Relationships** (like a var that points to other Entities)  

To describe the structure and relationships of the objects constituting your data model (the managed object model), you design an object graph in a data model document.  
My model contains two entities `Lesson` and `Word`  
`Lesson` has two attributes: `topic`, `createDate` and one relationship `words` (To Many - `Lesson` can have multiple `Words`)   
`Word` has three attributes: `text`, `successed`, `failured` and relationship `lesson` (To One)   

All Core Data model objects are instances of `NSManagedObject`, which has no `topic` property and so on.   
Instead, Core Data model objects make themselves dynamically KVC compliant for attribute names.   
For example, Core Data knows, thanks to our object graph, that a `Lesson` entity is to have a `topic` attribute, so if an `NSManagedObject` represents a `Lesson` entity, you can set its `topic` attribute by calling `setValue(_:forKey:)` and retrieve its by calling `value(forKey:)`, using a key "topic".


#### The managed object context
You get context out of an `NSPersistentContainer`. We can add some convenience static vars in AppDelegate.

```
    static var persistentContainer: NSPersistentContainer {
        return (UIApplication.shared.delegate as! AppDelegate).persistentContainer
    }

    static var viewContext: NSManagedObjectContext {
        return persistentContainer.viewContext
    }
```

The persistentContainer `viewContext` var is `NSManagedObjectContext` suitable only for use on the **main queue**.
