# bxb

bidirectional transformation library


## Motivation

We are interested in bidirectional transformations formalizm and implementation for
hierarchiecal structures (json-like). Transformation without loosing information is 
important aspect of semantic integrations between systems, as well flexibility in 
representation is useful in same systzm for different usecases.

Here is a wish-list of properties and features:

* declarative and expressive as possible
* non-turing complete, which allow to analyze transformations and prove it
* practical and non-reversable extensions
* debug frendly
* inference transformation by samples
* efficient implementation for production usage
* string extension to parse/transform strings



## Formalizm

Object of transformation is hierarchiecal data structures, which
are a composition of:

* primitives (like string, number etc)
* map (set of key value pairs)
* set (unordered uniq set of values)
* collection (ordered; non uniq collection of values)

### Canonical form

There is bidirectional operation to transofm such data 
structures into a set of pairs - path & value:


```yaml
name:
  - given: ['s', 's']
    family: 's'
birthDate: '1980'

# canonical representation

name.0.given.0  's'
name.0.given.1  's'
birthDate       '1980'
```

### Transformation flow

```
data structure 
  => canonical form 
    => transformation 
       => canonical form 
         => result data structure
```

Transformation is a collection of transform operations over cannonical form.
This operations are applied in order to source to get target result.
To do backward transformation operations are applied to result data structure
in reverse order.

Transform operation is a function from set of path/value pairs 
into path/value pairs:

T(pvs; prog)=> pvs


### Basic transformation

In special case transformation consist of two independent pure transformations

* source path => target path
* source value => target value

For example;

path: name.given => first_name
value: prefix/unfix  pre, s => "${pre}_{s}" and back "${pre}_{s}" => s

Each transformation operation (path and value) are two way function (i.e. two functions)

target <- op params -> source

### Parametrized transformation

generalizing basic transformation transormation can map
set of path/value pairs into set of path/value pairs

One of special case is parametrized path's. For example

```yaml
name.#.given => name.#.first_name
```
Where `#` is a place holder, which in concrete transformation will get
a specific value, becomming a parameter passed into resulting path.

Example:

```yaml
name.#.given => (# = ?) => name.#.first_name

name.0.given => (# = 0) => name.0.first_name
name.1.given => (# = 1) => name.1.first_name
```

### Dependent transformation

In more general case, path transformation can depend on value and value transformation can depend on path.
In simple words data can move to structure and backward, consider this:

```yaml
- system: phone
  value: <phone-1>
- system: phone
  value: <phone-2>
- system: mail
  value: <mail>

#=>

phone: [<phone-1>, <phone-2>]
mail: [<mail>]

```


