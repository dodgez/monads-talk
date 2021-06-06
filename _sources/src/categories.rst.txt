What is a Category?
===================

Definition
----------
A category is a collection of objects and arrows (called morphisms) such that any object has an identity arrow (works the way you may think) and for any two arrows :math:`f:X->Y` and :math:`g:Y->Z`, there exists its composition arrow :math:`g\circ f:X->Z`.
This means you can define another arrow that is just the result of applying :math:`f` and then :math:`g`.
*Note: if this composition arrow doesn't exist in your collection of arrows already, just add it.  We will see what this means later.*
Notice that a category is determined by its arrows, meaning any changes to arrows will result in a different category (if you modify objects, you get an isomorphic, or equivalent, category).

Examples
---------
- A collection of objects with arrows a ranking between them (e.g. people ranked >= height)
- :math:`Set`, the category of sets with arrows the functions between them.
- Any collection of objects with only the identity arrows (can be *any* collection!)
- :math:`Hask`, the category where each object is a type in Haskell with its collection of all possible instances (like integers, strings, etc) and arrows all possible functions between types
