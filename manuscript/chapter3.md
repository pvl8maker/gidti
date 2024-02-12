# 3. Type theory

Some type theories can serve as an alternative foundation of mathematics, as opposed to standard set theory. One such well-known type theory is Martin-L&#246;f's intuitionistic theory of types, which is an extension of Alonzo Church's simply-typed {$$}\lambda{/$$}-calculus. Before we begin working with Idris, we will get familiar with these theories, upon which Idris is built as a language.

I> ### Definition 1
I>
I> Type theory is defined as a class of formal systems. In these theories, every object is joined with a type, and operations upon these objects are constrained by the joined types. In order to say that {$$}x{/$$} is of type {$$}\text{X}{/$$}, we denote {$$}x : \text{X}{/$$}. Functions are a primitive concept in type theory[^ch3n1].

For example, with $$1 : \text{Nat}, 2 : \text{Nat}$$ we can say that 1 and 2 are of type {$$}\text{Nat}{/$$}, that is natural numbers. An operation (function) {$$}+ : \text{Nat} \to \text{Nat} \to \text{Nat}{/$$} is interpreted as a function which takes two objects of type {$$}\text{Nat}{/$$} and returns an object of type {$$}\text{Nat}{/$$}.

I> ### Definition 2
I>
I> In type theory, a type constructor is a function that builds new types from old ones. This function accepts types as parameters and as a result, it returns a new type.

Idris supports algebraic data types. These data types are a kind of complex types, that is, types constructed by combining other types. Two classes of algebraic types are **product types** and **sum types**.

I> ### Definition 3
I>
I> Algebraic data types are types where we can additionally specify the form for each of the elements. They are called "algebraic" in the sense that the types are constructed using algebraic operations. The algebra here is sum and product:
I>
I> 1. Sum (union) is an alternation. It is denoted as {$$}\text{A | B}{/$$} and it means that a constructed value is either of type A or B
I> 1. Product is a combination. It is denoted as {$$}\text{A B}{/$$} and it means that a constructed value is a pair where the first element is of type A, and the second element is of type B
I>
I> To understand the algebra they capture, we denote with {$$}|\text{A}|{/$$} the number of possible values of type {$$}\text{A}{/$$}. When we create an algebraic sum we have {$$}|\text{A | B}| = |\text{A}| + |\text{B}|{/$$}. Similarly, for algebraic product we have {$$}|\text{A B}| = |\text{A}| * |\text{B}|{/$$}.

As an example, we can assume that we have two types: {$$}\text{Nat}{/$$} for natural numbers, and {$$}\text{Real}{/$$} for real numbers. Using sum (union) we can construct a new type {$$}\text{Nat | Real}{/$$}. Valid values of this type are {$$}1 : \text{Nat | Real}{/$$}, {$$}3.14 : \text{Nat | Real}{/$$}, etc. Using product we can construct a new type {$$}\text{Nat Real}{/$$}. Valid values of this type are {$$}1 \ 1.5 : \text{Nat Real}{/$$}, {$$}2 \ 3.14 : \text{Nat Real}{/$$}, etc. With this, sums and products can be combined and thus more complex data structures can be defined.

The type {$$}\text{Bool}{/$$} has two possible values: {$$}True{/$$} and {$$}False{/$$}. Thus, {$$}|\text{Bool}| = 2{/$$}. The type {$$}\text{Unit}{/$$} (equivalent to {$$}(){/$$}) has one possible value: {$$}Unit{/$$}. We can now form a sum type {$$}\text{Bool | Unit}{/$$} which has length 3 with values {$$}True, False, Unit{/$$}. Additionally, the product type {$$}\text{Bool Unit}{/$$} has length 2 with values {$$}True \ Unit, False \ Unit{/$$}.

Besides the sum and product, there is another important operation called **exponentiation**. This corresponds to functions, so a type {$$}a \to b{/$$} has {$$}|b|^{|a|}{/$$} possible values. In section 3.3 we will see how this algebra can be generalized.

Finally, Idris supports dependent types[^ch3n2]. These kinds of types are so powerful, they can encode most properties of programs and with their help, Idris can prove invariants at compile-time. As we will see in section 4.2 types also allow us to encode mathematical proofs, which brings computer programs closer to mathematical proofs. As a consequence, this allows us to prove properties (e.g. specifications) about our software[^ch3n3].

Q> Why are types useful?
Q>
Q> Russell's paradox (per the mathematician Bertrand Russell) states the following: In a village in which there is only one barber, there is a rule according to which the barber shaves everyone who don't shave themselves, and no-one else. Now, who shaves the barber? Suppose the barber shaves himself. Then, he's one of those who shave themselves, but the barber shaves only those who do not shave themselves, which is a contradiction. Alternatively, if we assume that the barber does not shave himself, then he is in the group of people whom which the barber shaves, which again is a contradiction. Apparently then the barber does not shave himself, but he also doesn't _not_ shave himself - a paradox.
Q>
Q> Some set theories are affected by Russell's paradox. As a response to this, between 1902 and 1908, Bertrand Russell himself proposed different type theories as an attempt to resolve the issue. By joining types to values, we avoid the paradox because in this theory every set is defined as having elements from a distinct type, for example, {$$}\text{Type 1}{/$$}. Elements from {$$}\text{Type 1}{/$$} can be included in a different set, say, elements of {$$}\text{Type 2}{/$$}, and so forth. Thus, the paradox is no longer an issue since the set of elements of {$$}\text{Type 1}{/$$} cannot be contained in their own set, since the types do not match. In a way, we're adding hierarchy to sets in order to resolve the issue of "self-referential" sets. This is also the case with Idris, where we have that {$$}\text{Type : Type 1 : Type 2}{/$$}, etc.
Q>
Q> Thus, for Russell's paradox specifically, if we set the type of a person to be {$$}\text{P}{/$$}, then the list of people would be of type {$$}\text{List P}{/$$}. However, there is no way to express {$$}\{ \text{P} \}{/$$} such that {$$}\text{P} \in \text{P}{/$$}, since {$$}\text{List P}{/$$} only contains elements of type {$$}\text{P}{/$$}, and not {$$}\text{List P}{/$$}.

## 3.1. Lambda calculus

Lambda calculus is a formal system for expressing computation[^ch3n4]. The grammar rules are divided in two parts: function abstraction and function application. Function abstraction defines what a function does, and function application "computes" a function. For example {$$}f(x) = x + 1{/$$} is a function abstraction and {$$}f(3){/$$} is a function application. The equality sign {$$}={/$$} is replaced with a dot, and instead of writing {$$}f(x){/$$} we write {$$}\lambda x{/$$}. To represent {$$}f(x) = x + 1{/$$} we write {$$}\lambda x . x + 1{/$$}. Parentheses allow us to specify the order of evaluation.

I> ### Definition 4
I>
I> The set of symbols for the lambda calculus is defined as:
I>
I> 1. There are variables {$$}v_1, v_2, \ldots{/$$}
I> 1. There are only two abstract symbols: {$$}.{/$$} and {$$}\lambda{/$$}
I> 1. There are parentheses: {$$}({/$$} and {$$}){/$$}
I>
I> The set of grammar rules {$$}\Lambda{/$$} for well-formed expressions is defined as:
I>
I> 1. If {$$}x{/$$} is a variable, then {$$}x \in \Lambda{/$$}
I> 1. If {$$}x{/$$} is a variable and {$$}M \in \Lambda{/$$}, then {$$}(\lambda x.M) \in \Lambda{/$$} (rule of abstraction)
I> 1. If {$$}M, N \in \Lambda{/$$}, then {$$}(M \ N) \in \Lambda{/$$} (rule of application)

Some examples of well-formed expressions are {$$}\lambda f \ x . f \ x{/$$} and {$$}\lambda f \ x . f \ (f \ x){/$$}. In fact, we can encode numbers this way. The first expression can be thought of as the number one, and the second as the number two. In other words, the number 1 is defined roughly as {$$}f(x){/$$}, and 2 as {$$}f(f(x)){/$$}. Note that {$$}f{/$$} and {$$}x{/$$} do not have special definitions, they are abstract objects. This encoding is known as the Church encoding. Operations on numbers (plus, minus, etc) and other data such as Booleans and lists can also be encoded similarly.

Note that {$$}\lambda f \ x . f \ x{/$$} is shorthand for {$$}\lambda f . \lambda x . f \ x{/$$}.

X> ### Exercise 1
X>
X> Convince yourself that the expression {$$}\lambda f \ x . f \ x{/$$} is a well-formed expression by writing down each one of the grammar rules used.

### 3.1.1. Term reduction

Every variable in a lambda expression can be characterized as either _free_ or _bound_ in that expression.

I> ### Definition 5
I>
I> A variable in a lambda expression is called _free_ if it does not appear inside at least one lambda body where it is found in the abstraction. Alternatively, if it does appear inside at least one lambda body, then the variable is _bound_ at the innermost such lambda abstraction.

This definition of "bound" corresponds roughly to the concept of _scope_ in many programming languages. Lambda expressions introduce a new scope in which their argument variables are bound.

For example, in the expression {$$}\lambda y.x \ y{/$$} we have that {$$}y{/$$} is a bound variable, and {$$}x{/$$} is a free one. Variable binding in lambda calculus is subtle but important, so let's see some trickier examples.

1. In {$$}x(\lambda x.x){/$$}, the leftmost {$$}x{/$$} is free, while the rightmost {$$}x{/$$} is bound by the lambda
1. In {$$}(\lambda x.x)(\lambda x.x){/$$}, both occurrences of {$$}x{/$$} are bound; the first at the left lambda, and the second at the right lambda
1. In {$$}\lambda x.(\lambda x.x){/$$} the sole occurrence of {$$}x{/$$} is certainly bound. Now there are two potential "binding sites" - the inner lambda and the outer lambda. Given a choice like this, we always say the variable is bound at the innermost lambda

The distinction between free and bound variables becomes important when we ask whether two different lambda expressions are "equal". For instance, consider the two expressions {$$}\lambda x.x{/$$} and {$$}\lambda y.y{/$$}. Syntactically these are not the same; they use different characters for the variable. But semantically they are identical because in lambda calculus variables bound by a lambda are "dummy" variables whose exact names are not important. When two lambda expressions differ only by a consistent renaming of the bound variables like this we say they are _alpha equivalent_.

There are two other useful notions of semantic equivalence for lambda expressions: beta and eta equivalence.

I> ### Definition 6
I>
I> The rules of terms reduction (inference rules) allow us to compute (simplify) lambda expressions. There are three types of reduction:
I>
I> 1. {$$}\alpha{/$$} - (alpha) reduction: Renaming bound variables
I> 1. {$$}\beta{/$$} - (beta) reduction: Applying arguments to functions
I> 1. {$$}\eta{/$$} - (eta) reduction: Two functions are "equal" iff they return the same result for all arguments

For example, for the expression {$$}(\lambda x . f \ x) \ y{/$$}, we can use alpha reduction to get to {$$}(\lambda z . f \ z) \ y{/$$}, by changing {$$}x{/$$} to {$$}z{/$$}. Using beta reduction, the expression can further be reduced to just {$$}f \ y{/$$}, since we "consumed" the {$$}z{/$$} by removing it from the abstraction and wherever it occurred in the body we just replaced it with what was applied to it, that is {$$}y{/$$}. Finally, with eta reduction, we can rewrite {$$}(\lambda x . f \ x){/$$} to just {$$}f{/$$}, since they are equivalent.

Given these rules, we can define the successor function as {$$}\text{SUCC} = \lambda n\ f\ x\ . f\ (n\ f\ x){/$$}. Now we can try to apply 1 to {$$}\text{SUCC}{/$$}:

1. Evaluating {$$}\text{SUCC} \ 1 ={/$$}
1. Substitute the very own definitions of {$$}\text{SUCC}{/$$} and 1: {$$}(\lambda n \ f \ x . f \ (n \ f \ x)) \ (\lambda f \ x . f \ x) ={/$$}
1. Apply 1 to {$$}\text{SUCC}{/$$} i.e. "consume" {$$}n{/$$} by beta reduction: {$$}\lambda f \ x . f \ ((\lambda f \ x . f \ x) \ f \ x) ={/$$}
1. Finally, apply {$$}f{/$$} and {$$}x{/$$} to a function that accepts {$$}f{/$$} and {$$}x{/$$} (which is just the body of the abstraction): {$$}\lambda f \ x . f \ (f \ x) = 2{/$$}

I> ### Definition 7
I>
I> A fixed-point combinator is any function that satisfies the equation {$$}\text{fix} \ f = f \ (\text{fix} \ f){/$$}.

One example of such a combinator is {$$}\text{Y} = \lambda f . (\lambda x . f\ (x\ x))\ (\lambda x . f\ (x\ x)){/$$}. This definition satisfies {$$}\text{Y} \ f = f \ (\text{Y} \ f){/$$}. This combinator allows for recursion in lambda calculus. Since it is impossible to refer to the function within its body, recursion can only be achieved by applying parameters to a function which is what this combinator does.

X> ### Exercise 2
X>
X> Evaluate {$$}\text{SUCC} \ 2{/$$} to find out the definition of number 3.

X> ### Exercise 3
X>
X> Come up with your own functions that operate on the Church numerals. It can be as simple as returning the same number, or a constant one.

## 3.2. Lambda calculus with types

So far we've been discussing the _untyped_ lambda calculus, but it is possible to augment the rules of lambda calculus so that variables are _typed_. This makes it possible to add an additional statically checked layer of semantics to a lambda expression so we can ensure that values are used in a consistent and meaningful way. There are several ways to add types to lambda calculus, and our goal is to approach the full _dependent_ type system of Idris. As a stepping stone, we first consider _simple_ types.

I> ### Definition 8
I>
I> Simply typed lambda calculus is a type theory that adds types to lambda calculus. It joins the system with a unique type constructor {$$}\to{/$$} which constructs types for functions. The formal definition and the set of lambda expressions are similar to that of lambda calculus, with the addition of types.
I>
I> The set of symbols for this system is defined as:
I>
I> 1. There are variables {$$}v_1, v_2, \ldots{/$$}
I> 1. There are only two abstract symbols: {$$}.{/$$} and {$$}\lambda{/$$}
I> 1. There are parentheses: {$$}({/$$} and {$$}){/$$}
I>
I> The set of grammar rules {$$}\Lambda{/$$} for well-formed expressions is defined as:
I>
I> 1. If {$$}x{/$$} is a variable, then {$$}x \in \Lambda{/$$}
I> 1. If {$$}x{/$$} is a variable and {$$}M \in \Lambda{/$$}, then {$$}(\lambda x.M) \in \Lambda{/$$} (rule of abstraction)
I> 1. If {$$}M, N \in \Lambda{/$$}, then {$$}(M \ N) \in \Lambda{/$$} (rule of application)
I> 1. If {$$}x{/$$} is a variable, {$$}\text{T}{/$$} is a type, and {$$}M \in \Lambda{/$$}, then {$$}(\lambda x: \text{T} .M) \in \Lambda{/$$}
I> 1. If {$$}x{/$$} is a variable and {$$}\text{T}{/$$} is a type, then {$$}x: \text{T} \in \Lambda{/$$}
I>
I> There is a single type constructor:
I>
I> 1. For some type {$$}A{/$$}, the type constructor {$$}T{/$$} is defined as {$$}\text{A | T} \to \text{T}{/$$}

That is, an expression in this system can additionally be an abstraction with {$$}x{/$$} having joined a type (rule 4) or an expression of a variable having joined a type {$$}\text{T}{/$$} (rule 5), where our type constructor is a sum type and it says that we either have primitive types or a way to form new types. In an attempt to re-define Church numerals and the successor function we have to be careful as the types of these definitions have to match. Let's recall the Church numerals:

1. Number 1, i.e. {$$}\lambda f \ x . f \ x{/$$}
1. Number 2, i.e. {$$}\lambda f \ x . f \ (f \ x){/$$}

Given the definition of 1, its type must have the form {$$}(\text{a} \to \text{b}) \to \text{a} \to \text{b}{/$$} for some values {$$}\text{a}{/$$} and {$$}\text{b}{/$$}. We are expecting to be able to apply {$$}f{/$$} to {$$}x{/$$}, and so if {$$}x : \text{a}{/$$} then {$$}f : \text{a} \to \text{b}{/$$} in order for our types to match correctly. With similar reasoning, we have the same type for 2. At this point, we have the type of {$$}(\text{a} \to \text{b}) \to \text{a} \to \text{b}{/$$}. Finally, with the given definition of 2, we can note that expressions of type {$$}\text{b}{/$$} need to be able to be passed to functions of type {$$}\text{a} \to \text{b}{/$$}, since the result of applying {$$}f{/$$} to {$$}x{/$$} serves as the argument of {$$}f{/$$}. The most general way for that to be true is if {$$}a = b{/$$}. As a result we have the type {$$}(\text{a} \to \text{a}) \to \text{a} \to \text{a}{/$$}. We can denote this type definition to be {$$}\text{Nat}{/$$}. We have the following for our numbers:

1. Number 1 becomes {$$}\lambda [f:(\text{a} \to \text{a})] \ [x : \text{a}] . f \ x : \text{Nat}{/$$}
1. Number 2 becomes {$$}\lambda [f:(\text{a} \to \text{a})] \ [x : \text{a}] . f \ (f \ x) : \text{Nat}{/$$}

The (typed) successor function is: {$$}\text{SUCC} = \lambda [n:\text{Nat}]\ [f:(\text{a} \to \text{a})] \ [x : \text{a}] . f\ (n\ f\ x) : \text{Nat} \to \text{Nat}{/$$}.

Simply typed lambda calculus sits in a sweet spot on the spectrum of type systems. It is powerful enough to do useful work, but also simple enough to have strong properties. Simple types have limitations when compared to full dependent types, discussed in the next section, but their great trade-off is the existence of a full _inference algorithm_. The strategy we used above to determine the type of a lambda expression from the bottom up is the core of the widely used Hindley-Damas-Milner algorithm for type inference, which can automatically _infer_ the simple type of a lambda expression without requiring any explicit type annotations from the programmer.

Fixed-point combinators do not exist in the simply-typed lambda calculus[^ch3n5]. To see why, consider the function {$$}\text{fix} = \lambda [f:\text{a} \to \text{a}] : \text{a}{/$$}. We can apply {$$}\text{fix}{/$$} to some element {$$}x : \text{a} \to \text{a}{/$$}. Thus, {$$}\text{fix} \ x : \text{a}{/$$}, but {$$}x = \text{fix} \ x{/$$} is a type error because the infinite type {$$}\text{a} \to \text{a} = \text{a}{/$$} cannot be matched.

X> ### Exercise 4
X>
X> Come up with a definition of the typed number 3.

X> ### Exercise 5
X>
X> Apply the typed 1 to {$$}\text{SUCC}{/$$} and confirm that the result is 2. Make sure you confirm that the types also match in the process of evaluation.

X> ### Exercise 6
X>
X> In Exercise 3 you were asked to come up with a function. Try to figure out the type of this function or, if not applicable, come up with a new function and then figure out its type using the reasoning above.

## 3.3. Dependent types

In the simply-typed lambda calculus, _values_ and _types_ are fundamentally different kinds of things that are related only by the "has type" predicate, {$$}:{/$$}. Values are allowed to depend on values - these are lambda abstractions. And types are allowed to depend on types - these are arrow types. But types are not allowed to depend on values. A _dependent typing_ system lifts this restriction.

I> ### Definition 9
I>
I> Dependent types are types that depend on values.

A list of numbers is a type ({$$}\text{List}\ \text{Nat}{/$$}, for example). However, a list of numbers whose length is bounded by some constant, or whose entries are increasing, is a dependent type.

I> ### Definition 10
I>
I> A dependent product type is a collection of types {$$}B : \text{A} \to U{/$$} where for each element {$$}a : \text{A}{/$$}, there's an assigned type {$$}B(a) : U{/$$}, where {$$}U{/$$} is a universe of types[^ch3n6]. We say that {$$}B(a){/$$} varies with {$$}a{/$$}. It is denoted as {$$}\Pi(x : \text{A}), B(x){/$$} or {$$}\prod\limits_{x \ : \ \text{A} } B(x){/$$}.

This definition might seem a bit scary and tricky to grasp, but it really is simple, and it is best to see it in action through the following example:

1. Our universe of types contains all possible types. For example, {$$}\text{Type}{/$$}, {$$}\text{Nat}{/$$}, etc, so {$$}U = \{ \text{Type}, \text{Nat}, \text{List n}, \ldots \}{/$$}
1. Our collection of types of interest is {$$}\text{List n}{/$$}, which represents a list of {$$}\text{n}{/$$} elements. That is, {$$}A = \{ \text{List n} \}{/$$}

The definition states that in the universe {$$}U{/$$}, there exists a function {$$}B(\text{n}) = \text{List n}{/$$}. {$$}B{/$$} is the collection of functions which given a number {$$}n{/$$}, will return a list of {$$}n{/$$} numbers. For example, we have the following lists:

1. List of 1 element: {$$}[1] : B(1){/$$}, that is {$$}[1] : \text{List 1}{/$$}
1. List of 2 elements: {$$}[1, 2] : \text{List 2}{/$$}
1. List of n elements: {$$}[1, 2, \ldots, n] : \text{List n}{/$$}

In general, we have a function that takes an {$$}\text{n}{/$$} and produces a {$$}\text{List n}{/$$}, that is, {$$}f : \Pi(x : \text{Nat}), \text{n} \to \text{List n}{/$$} or simply {$$}f : \text{n} \to \text{List n}{/$$}, where the possible types for it are {$$}f : 1 \to \text{List 1}{/$$} and {$$}f : 2 \to \text{List 2}{/$$}, etc. We've just constructed our first dependent type!

I> ### Definition 11
I>
I> A dependent sum type can be used to represent indexed pairs, where the type of the second element depends on the type of the first element. That is, if we have {$$}a : \text{A}{/$$} and {$$}b : B(\text{a}){/$$}, then this makes a sum type. We denote it as {$$}\Sigma(x : \text{A}), B(x){/$$} or {$$}\sum\limits_{x \ : \ \text{A} } B(x){/$$}.

For example, if we set {$$}A = \text{Nat}{/$$}, and {$$}B(\text{a}) = \text{List a}{/$$}, then we form the dependent sum type {$$}\Sigma(x : \text{Nat}), \text{List x}{/$$}. Possible types for it are {$$}(1, \text{List 1}){/$$} or {$$}(2, \text{List 2}){/$$}, etc. For example, we can construct the following pairs: {$$}(1, [1]), (2, [1, 2]), (3, [1, 2, 3]){/$$}, etc.

Dependent types generalize product and exponentiation. Namely, {$$}\Sigma{/$$} (multiplication) is a generalization of the product type where the type of the second element depends on the first element, and {$$}\Pi{/$$} (exponentiation) is a generalization of the exponentiation type where the resulting type of a function depends on its input.

X> ### Exercise 7
X>
X> Think of a way to construct a different dependent product type and express it by using the reasoning above.

X> ### Exercise 8
X>
X> Think of a way to construct a different dependent sum type and express it using the reasoning above.

## 3.4. Intuitionistic theory of types

The core "construct" in Idris are types. As we've seen, foundations are based on type theory. As we've also seen, in classical mathematical logic we have sets and propositions, according to set theory.

The intuitionistic theory of types (or constructive type theory) offers an alternative foundation to mathematics. This theory was introduced by Martin-L&#246;f, a Swedish mathematician in 1972. It is based on the isomorphism (or "equality") that propositions are types.

Proving a theorem in this system consists of constructing[^ch3n7] (or providing evidence for) a particular object. If we want to prove something about a type {$$}\text{A}{/$$} and we know that {$$}a : \text{A}{/$$}, then {$$}a{/$$} is one proof for {$$}\text{A}{/$$}. Note how we say one proof, because there can be many other elements of type {$$}\text{A}{/$$}.

Propositions can also be defined through types. For example in order to prove that {$$}4 = 4{/$$}, we need to find an object {$$}x{/$$} of type {$$}\text{4 = 4}{/$$}, that is {$$}x : \text{4 = 4}{/$$}. One such object is {$$}refl{/$$} (which can be thought of as an axiom), which stands for reflexivity, which states that {$$}x = x{/$$} for all {$$}x{/$$}.

One thing worth noting is that in Idris there are "two" types of truths: {$$}\text{Bool}{/$$} and {$$}\text{Type}{/$$}. Even though there is some similarity (in terms of proofs), in Idris they are fundamentally different. The type {$$}\text{Bool}{/$$} can have a value of {$$}True{/$$} or {$$}False{/$$}, while the type {$$}\text{Type}{/$$} is either provable or not provable[^ch3n8].

This system is useful since with the use of computer algorithms we can find a constructive proof for some object (assuming it exists). As a consequence, this is why it can be considered as a way to make a programming language act like a proof-assistant.

I> ### Definition 12
I>
I> The set of grammar rules {$$}\Lambda{/$$} for well-formed expressions is defined as:
I>
I> 1. {$$}s : \text{Type} \in \Lambda{/$$} means that {$$}s{/$$} is a well-formed type
I> 1. {$$}t : \text{s} \in \Lambda{/$$} means that {$$}t{/$$} is a well-formed expression of type {$$}\text{s}{/$$}
I> 1. {$$}\text{s} = \text{t} \in \Lambda{/$$} means that {$$}\text{s}{/$$} and {$$}\text{t}{/$$} are the same type
I> 1. {$$}t = u : \text{s} \in \Lambda{/$$} means that {$$}t{/$$} and {$$}u{/$$} are equal expressions of type {$$}\text{s}{/$$}
I>
I> The type constructors are:
I>
I> 1. {$$}\Pi{/$$} types and {$$}\Sigma{/$$} types, as we've discussed them earlier
I> 1. Finite types, for example the nullary (empty) type 0 or {$$}\bot{/$$}, the unary type 1 or {$$}\top{/$$}, and the boolean type 2
I> 1. The equality type, where for given {$$}a, b : \text{A}{/$$}, the expression {$$}a = b{/$$} represents proof of equality. There is a canonical element {$$}a = a{/$$}, that is, an "axiom" for the reflexivity proof: {$$}refl : \Pi (a : \text{A}) \ a = a{/$$}
I> 1. Inductive (or recursive) types. This way we can implement a special form of recursion - one that always terminates, and we will see the importance of this with total functions. Additionally we can implement product and sum types, which encode conjunction and disjunction respectively
I>
I> The inference rules are:
I>
I> 1. The rule of type equality which states that if an object is of a type {$$}\text{A}{/$$}, and there is another type {$$}\text{B}{/$$} equal to {$$}\text{A}{/$$}, then that object is of type {$$}\text{B}{/$$}: {$$}(a : \text{A}, \text{A} = \text{B}) \to (a : \text{B}){/$$}

There are other inference rules, for example introduction and elimination. We will show an example of using these rules in section 4.2.

As an example, for well-formed expressions rule 1 says that we can form an expression such that an object inhabits the type {$$}\text{Type}{/$$}, so an example of a well-formed expression is {$$}1 : \text{Nat}{/$$}, per rule 2, and {$$}\text{Nat} : \text{Type}{/$$} per rule 1.

A valid type as per the fourth rule of type constructors is the definition of natural numbers {$$}\text{Nat = Z | S Nat}{/$$}. Some valid values are {$$}\text{Z : Nat, S Z : Nat}{/$$}, etc.

X> ### Exercise 9
X>
X> We used rule 1 and rule 2 in the example earlier. Try to come up with different ways to use some of the rules.

X> ### Exercise 10
X>
X> Combine the use of rules along with the connectives described earlier and try to come up with a recursive type and then construct some new objects from it.

### 3.4.1. Intuitionistic logic

I> ### Definition 13
I>
I> A constructive proof proves the existence of a mathematical object by creating or constructing the object itself. This is contrary to non-constructive proofs which prove the existence of objects without giving a concrete example.

I> ### Definition 14
I>
I> Intuitionistic logic, also known as constructive logic, is a type of logic that is different than classical logic in that it "works" with the notion of constructive proof.

I> ### Definition 15
I>
I> The BHK (Brouwer-Heyting-Kolmogorov) interpretation is a mapping of intuitionistic logic to classical mathematical logic, namely:
I>
I> 1. A proof of {$$}P \land Q{/$$} is a product type {$$}\text{A B}{/$$}, where {$$}a{/$$} is a proof of (or, object of type) {$$}\text{P}{/$$} and {$$}b{/$$} is a proof of {$$}\text{Q}{/$$}
I> 1. A proof of {$$}P \lor Q{/$$} is a product type {$$}\text{A B}{/$$}, where {$$}a{/$$} is 0 and {$$}b{/$$} is a proof of {$$}\text{P}{/$$}, or {$$}a{/$$} is 1 and {$$}b{/$$} is a proof of {$$}\text{Q}{/$$}
I> 1. A proof of {$$}P \to Q{/$$} is a function {$$}f{/$$} that converts a proof of {$$}\text{P}{/$$} to a proof of {$$}\text{Q}{/$$}
I> 1. A proof of {$$}\exists x \in S : f(x){/$$} is a pair {$$}\text{A B}{/$$} where {$$}a{/$$} is an element of {$$}\text{S}{/$$}, and {$$}b{/$$} is a proof of {$$}f(a){/$$} (dependent sum types)
I> 1. A proof of {$$}\forall x \in S : f(x){/$$} is a function {$$}f{/$$} that converts an element {$$}a{/$$} from {$$}\text{S}{/$$} to a proof of {$$}f(a){/$$} (dependent product types)
I> 1. A proof of {$$}\lnot P{/$$} is defined as {$$}P \to \bot{/$$}, that is, the proof is a function {$$}f{/$$} that converts a proof of {$$}\text{P}{/$$} to a proof of {$$}\bot{/$$}
I> 1. There is no proof of {$$}\bot{/$$}

For example, to prove distributivity of {$$}\land{/$$} with respect to {$$}\lor{/$$}, that is, {$$}P \land (Q \lor R) = (P \land Q) \lor (P \land R){/$$}, we need to construct a proof for the function of type {$$}f : \text{P (Q | R)} \to \text{P Q | P R}{/$$}. That is, a function that takes a product type of {$$}\text{P}{/$$} and sum type of {$$}\text{Q}{/$$} and {$$}\text{R}{/$$}, and returns a sum type of product {$$}\text{P}{/$$} and {$$}\text{Q}{/$$}, and product {$$}\text{P}{/$$} and {$$}\text{R}{/$$}. Here's the function that accomplishes that:

```
f (x, left y)  = left (x, y)
f (x, right y') = right (x, y')
```

This notation (which is pretty similar to how we would write it in Idris) uses `(x, y)` to denote product type, that is, extract values from a product-type pair, and `left` and `right` to denote value constructors for sum type in order to extract values from a sum-type pair. In the next chapter we will introduce Idris and its syntax.

X> ### Exercise 11
X>
X> Try to use some of the proofs in the earlier chapters as motivation and work them out by using intuitionistic logic.

[^ch3n1]: Unlike in set theory, where they are defined in terms of relations.

[^ch3n2]: Dependent types allow proofs of statements involving first-order predicates, compared to simple types that correspond to propositional logic. While useful (since we can check whether an expression fulfills a given condition at compile-time), dependent types add complexity to a type system. In order to calculate type "equality" of dependent types, computations are necessary. If we allow any values for dependent types, then solving equality of a type may involve deciding whether two programs produce the same result. Thus, the check may become undecidable.

[^ch3n3]: This is what makes Idris a so-called proof assistant. In general, Idris combines a lot of functionalities from mainstream languages (Java, C, C++) and some functionalities from proof assistants, which further blurs the line between these two kinds of software.

[^ch3n4]: A _Turing machine_ is a very simple abstract machine designed to capture our intuitive understanding of _computation_ in the most general sense. Any formal system that can simulate a Turing machine, and thus also perform arbitrary computations, is called _Turing complete_.

[^ch3n5]: For this reason, the typed lambda calculus is not Turing complete, while the untyped lambda calculus is. Fixed-point combinators provide flexibility, but that has its drawbacks. They can be non-terminating - loop indefinitely without producing an answer. While non-termination has its uses for software (e.g. a program keeps running until we choose to close it), termination is important for mathematical proofs as we will see in section 4.2.

[^ch3n6]: Collections, in general, are considered to be subcollections of some large universal collection, also called the universe. Depending on the context, the definition of this universe will vary.

[^ch3n7]: As a consequence that we need to construct an object as evidence in order to prove something, the law of excluded middle {$$}P \lor \lnot P{/$$} is not valid in this logic, whereas in classical mathematical logic this is taken as an axiom. For some propositions, for example, {$$}P{/$$} is an odd number or not, there are proofs that we can provide. However, for some propositions this is impossible, for example, {$$}P{/$$} is a program that halts or not. Unlike classical mathematical logic, in this logic, the law of excluded middle does not exist due to the undecidability problem.

[^ch3n8]: It is provable in case we can construct an object of such type, and not provable otherwise.
