## Terminology

Before we continue, let's establish some quick terminology:

- __Struct:__ like an "object" in JS. It can both be a data-only type. But can
    also work like a "class" with methods (both inherent and static).
- __Vec:__ like a JS "array".
- __Slice:__ like a ["view" into a `TypedArray` in
    JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays).
    You can't grow them, but you can create a "view of a view"
    (slice of a slice).
- __Traits:__ essentially the answer to the question: "what if a class could
    inherit from multiple other classes". Traits are also referred to as
    "mixins" in other languages. They don't allocate any data, but only provide
    methods and type definitions related to those methods.

