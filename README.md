# What is rql

rql is a bunch of functions for working with a collection of records.
It contains SQL like functions for selecting / removing records from
a collection of records.

# Install

If you're using leiningen just add [rql "1.1.0"] to your dependencies.

# Usage

Given the following definitions

``` clojure
(defrecord Person [first-name last-name age])
	
(def maarten (Person. "Maarten" "Hus" 21))
(def danny   (Person. "Danny" "Kieanu" 21))
(def cornel  (Person. "Cornel" "Berberus" 22))
(def ronald  (Person. "Ronald" "Chocolate" 19))

(def persons [maarten danny cornel ronald])
```

## Where: filtering records based on predicates.	
	
``` clojure
(where persons :age 21)  
```

Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 21} #Person{:first-name "Danny", :last-name "Kieanu", :age 21})
```

***

``` clojure
(where persons :first-name "Maarten")
```

Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 21})
```

## Delete: removing records based on predicates.

``` clojure
(delete persons :age 21)
```

Results in:

``` clojure
(#Person{:first-name "Cornel", :last-name "Berberus", :age 22} #Person{:first-name "Ronald", :last-name "Chocolate", :age 19})
```

***

``` clojure
(delete persons :last-name "Kieanu")
```

Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 21} #Person{:first-name "Cornel", :last-name "Berberus", :age 22} 
 #Person{:first-name "Ronald",:last-name "Chocolate", :age 19})
```	
	
## Insert: adding to a collection of records

``` clojure
(insert persons (Person. "Tom" "Sawyer" 35) (Person. "Adam" "Sawyer" 35))
```

Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 21} #Person{:first-name "Danny", :last-name "Kieanu", :age 21} 
#Person{:first-name "Cornel", :last-name "Berberus", :age 22} #Person{:first-name "Ronald", :last-name "Chocolate", :age 19} 
#Person{:first-name "Tom", :last-name "Sawyer", :age 35} #Person{:first-name "Adam", :last-name "Sawyer", :age 35})
```

## Update: updating records with a hash-map based on predicates.

``` clojure
(update persons {:age 10})
```

Results in:
	
``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 10} #Person{:first-name "Danny", :last-name "Kieanu", :age 10} 
#Person{:first-name "Cornel", :last-name "Berberus", :age 10} #Person{:first-name "Ronald", :last-name "Chocolate", :age 10})
```

***

``` clojure
(update persons {:last-name "Goose"} :last-name "Hus" :first-name "Maarten")
```

Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Goose", :age 21} #Person{:first-name "Danny", :last-name "Kieanu", :age 21} 
 #Person{:first-name "Cornel", :last-name "Berberus", :age 22} #Person{:first-name "Ronald", :last-name "Chocolate", :age 19})
```

## Sort-on: sorting the records based on a key.

``` clojure
(sort persons :first-name)
```

Results in:

``` clojure
(#Person{:first-name "Cornel", :last-name "Berberus", :age 22} #Person{:first-name "Danny", :last-name "Kieanu", :age 21} 
#Person{:first-name "Maarten", :last-name "Hus", :age 21} #Person{:first-name "Ronald", :last-name "Chocolate", :age 19})
```

***

You can also add you own operator:

``` clojure
(sort persons > :age)
```

Results in:

``` clojure
(#Person{:first-name "Cornel", :last-name "Berberus", :age 22} #Person{:first-name "Maarten", :last-name "Hus", :age 21} 
#Person{:first-name "Danny", :last-name "Kieanu", :age 21} #Person{:first-name "Ronald", :last-name "Chocolate", :age 19})
```


## Group: partition the collection based on a key.

``` clojure
(group persons :age)
```	
	
Results in:

``` clojure
( (#Person{:first-name "Ronald", :last-name "Chocolate", :age 19}) 
(#Person{:first-name "Cornel", :last-name "Berberus", :age 22}) 
(#Person{:first-name "Maarten", :last-name "Hus", :age 21} #Person{:first-name "Danny", :last-name "Kieanu", :age 21}) )
```

## Persistence

Update, insert, where, delete and sort-on all have persistent variants ie. update!, insert!, where! delete! and sort-on!.
In other words they actually change the collections and doesn't just create new ones. These functions don't take collections but ref's to collections.

For example:
	
``` clojure
(def persons (ref [maarten danny cornel ronald]))
(update! persons {:age 10})
@persons
```
	
Results in:

``` clojure
(#Person{:first-name "Maarten", :last-name "Hus", :age 10} #Person{:first-name "Danny", :last-name "Kieanu", :age 10} 
#Person{:first-name "Cornel", :last-name "Berberus", :age 10} #Person{:first-name "Ronald", :last-name "Chocolate", :age 10})
```

Note that group doesn't have a persistent version because it changes the depth of the collection. Which I think is wrong to persist.
