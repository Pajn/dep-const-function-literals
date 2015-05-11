# Constant function literals

## Contact information

**Name**: Rasmus Eneman  

**Email**: rasmus@eneman.eu  

**GitHub Username**: Pajn  

[Proposal Location](https://github.com/Pajn/dep-const-function-literals)
[dartbug](https://code.google.com/p/dart/issues/detail?id=4596)

## Summary

Allow compile-time constant function literals

## Motivation

Problems to solve:

 - Passing functions to annotations or as default values for optional parameters requires external
  declaration.
 - Avoid creating multiple closures when a function literal is declared in a loop
  (can be avoided with external declaration today) 

## Examples

### As default value

```dart
abstract class Map<K,V> {
  V get(K key, [V onAbsent() = const () => null]);
}
```

### In annotation

```dart
class Model {
  @Ensure(const (value) => value is String && value.length > 0)
  String name;
}
```

### In loop

```dart
for (var row in matrix) {
  row.sort(const (a, b) => a.name.compareTo(b.name));
}
```
## Proposal

It should be possible to declare function literals as compile-time constants. A constant function literal is prefixed with `const` just as constant Lists and Maps.

### Semantics

The scope of a constant function literal is the scope of the enclosing library, the same as a top-level function declared in the same library.

### Current Syntax Equivalent

```dart
/// With this Proposal
abstract class Map<K,V> {
  V get(K key, [V onAbsent() = const () => null]);
}

/// Without this Proposal
_onAbsent() => null;
abstract class Map<K,V> {
  V get(K key, [V onAbsent() = _onAbsent);
}
```

```dart
/// With this Proposal
class Model {
  @Ensure(const (value) => value is String && value.length > 0)
  String name;
}

/// Without this Proposal
_notEmptyString(value) => value is String && value.length > 0;
class Model {
  @Ensure(_notEmptyString)
  String name;
}
```

```dart
/// With this Proposal
for (var row in matrix) {
  row.sort(const (a, b) => a.name.compareTo(b.name));
}

/// Without this Proposal
_onName(a, b) => a.name.compareTo(b.name);
for (var row in matrix) {
  row.sort(_onName);
}
```

## Alternatives

### Infer constness of function literals.
Constness could be inferred in two different ways. 

The first is that function literals where an compile-time constant is required is automatically constant. This does not solve the loop use case as it doesn't require a compile time constant.
  
The second is that function literals is automatically constant if it only access the scope available to a constant function literal. This is a potentially breaking change.

Both cases of constness inferation may make it hard to see if a function literal is const or not and also breaks previous ***

## Deliverables

### Language specification changes

**TODO**

### A working implementation

**TODO**

### Tests

**TODO**

## Patents rights

TC52, the Ecma technical committee working on evolving the open [Dart standard][], operates under a royalty-free patent policy, [RFPP][] (PDF). This means if the proposal graduates to being sent to TC52, you will have to sign the Ecma TC52 [external contributor form][] and submit it to Ecma.

[tex]: http://www.latex-project.org/
[language spec]: https://www.dartlang.org/docs/spec/
[dart standard]: http://www.ecma-international.org/publications/standards/Ecma-408.htm
[rfpp]: http://www.ecma-international.org/memento/TC52%20policy/Ecma%20Experimental%20TC52%20Royalty-Free%20Patent%20Policy.pdf
[external contributor form]: http://www.ecma-international.org/memento/TC52%20policy/Contribution%20form%20to%20TC52%20Royalty%20Free%20Task%20Group%20as%20a%20non-member.pdf
