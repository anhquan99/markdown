# Abstract Factory
## Intent
- A design pattern produces families of related objects without specifying their concrete classes.
## Problem
- **Enforcing Compatibility:** How do you guarantee that a group of objects designed to work together (or share a theme) are consistently created together, preventing the system from mixing incompatible object variants?
- **Maintaining Extensibility (Open/Closed Principle):** How do you design your system so that you can add entirely new variants or families of products in the future without having to rewrite or modify the existing client code that relies on them?
## Solution
To safely create families of related objects without tightly coupling your code to concrete classes, the pattern proposes a specific structural abstraction:
- **Abstract Products:** Define a base interface (or abstract class) for every distinct type of object in your product family (e.g., Product A, Product B).
- **Concrete Products:** Create specific implementations of those abstract products for each distinct theme or variant (e.g., Variant 1 of Product A, Variant 2 of Product A).
- **The Abstract Factory:** Define a single overarching interface that declares a creation method for _each_ of the abstract products.
- **Concrete Factories:** Implement the Abstract Factory interface for each specific variant. Each concrete factory is exclusively responsible for instantiating the correct, compatible concrete products for its specific theme.
- **Client Decoupling:** Program the core application (the client) to interact solely with the abstract interfaces. The application is handed a concrete factory object at runtime (usually during initialization), but it uses that factory purely through the abstract interface to retrieve abstract products.
## Structure
![](/image/Pasted%20image%2020260909063013.png)
1. **Abstract Products** declare interfaces for a set of distinct but related products which make up a product family.
2. **Concrete Products** are various implementations of abstract products, grouped by variants. Each abstract product (chair/sofa) must be implemented in all given variants (Victorian/Modern).
3. The **Abstract Factory** interface declares a set of methods for creating each of the abstract products.
4. **Concrete Factories** implement creation methods of the abstract factory. Each concrete factory corresponds to a specific variant of products and creates only those product variants.
5. Although concrete factories instantiate concrete products, signatures of their creation methods must return corresponding _abstract_ products. This way the client code that uses a factory doesn’t get coupled to the specific variant of the product it gets from a factory. The **Client** can work with any concrete factory/product variant, as long as it communicates with their objects via abstract interfaces.
## Example
![](/image/Pasted%20image%2020260909063117.png)