# How to write Swift

The intention of this document is to guide how we should write Swift in projects. The document should be considered as work-in-progress and PR's with corrections etc. are much appreciated!

## Table of Contents

- [Spacing](#spacing)
- [Code Organization](#code-organization)
- [Comments](#comments)
- [Outcommented Code](#outcommented-code)
- [let VS. var](#let-vs-var)
- [Parentheses](#parentheses)
- [Generics](#generics)
- [Use of Self](#use-of-self)
- [Mindset](#mindset)
- [Naming](#naming)
- [Long Lines of Code](#long-lines-of-code)
- [Type Inference](#type-inference)
- [Private and Final](#private-and-final)
- [Appearance and Constraints](#appearance-and-constraints)
- [Extensions](#extensions)
- [Golden Path](#golden-path)

## Spacing

- Indent using 2 spaces.
  - `Xcode pref. -> Text Editing -> Indentation` and then make sure it is set to `"Spaces"` and `width = 2`.
- Long lines should be wrapped at **100** characters.
  - `Xcode pref -> Text Editing -> Display: Page guide at column: 100.`
- Avoid trailing whitespaces at the ends of lines.
  - `Xcode pref. -> Text Editing -> Editing` and make sure "Automatically trim trailing whitespace" and "Including whitespace-only lines" are checked.
  
## Code Organization

Use marks `// MARK: -` to keep things separated and well-organized.

## Comments

As little as possible as much as necessary.
When they are needed, use comments to explain why a particular piece of code does something. Comments must be kept up-to-date or deleted.

## Outcommented Code

Don't leave out commented code. Delete commented out code. We use git, so you can always get old code from there.

## let vs var

- Use `let` and not `var` wherever possible.
- No force-unwrapping of optionals. Instead use a `if let`, `guard let` or similar. Alternatively use optional chaining `myVar?.someFuncToCallIfMyVarIsNotNil()`.
  
## Parentheses

Parentheses around conditionals are not required and should be omitted.

**Preferred**

```swift
if user.role == .developer {
  // ...
}
```

**NOT Preferred**

```swift
if (user.role == .developer) {
  // ...
}
```

## Generics

Generic type parameters should be descriptive, upper camel case names. When a type name doesn't have a meaningful relationship or role, use a traditional single uppercase letter such as `T`, `U`, or `V`.

## Use of Self

For conciseness, avoid using `self` since Swift does not require it to access an object's properties or invoke its methods.

Use self only when required by the compiler (in `@escaping` closures, or in initializers to disambiguate properties from arguments). In other words, if it compiles without `self` then omit it.

## Mindset
Seperation of concerns. When writing a function you should always strive to have it do *one* job. For example:

**Preferred**

```swift
final class User {
  var name: String
  var age: Int
  
  func isValid() -> Bool {
    return validateName() && validateAge()
  }
  
  private func validateName() -> Bool {
    return !name.isEmpty
  }
  
  private func validateAge() -> Bool {
    return age >= 18
  }
}
```

**NOT Preferred**

```swift
final class User {
  var name: String
  var age: Int
  
  func isValid() -> Bool {
    return !name.isEmpty && age >= 18
  }
}
```

## Naming

Descriptive variable naming. Don't use variable names such as `number`, `a`, `b`, `x`, `button`, `label`, etc. 

**Preferred**

```swift
let nameLabel = UILabel()
```

**NOT Preferred**

```swift
let label = UILabel()
```

## Long Lines of Code
When writing `if let` or `guard let` or just `functions` etc.:

```swift
// From
guard let firstName = user.firstName, let lastName = user.lastName, let age = user.age, let gender = user.gender else {
  // ...
}

// To
guard
  let firstName = user.firstName,
  let lastName = user.lastName,
  let age = user.age,
  let gender = user.gender
else {
  // ...
}
```

Similarly a function like:

```swift
// From
func users(for category: Category, of kind: Kind, completionHandler: @escaping () -> Void) throws -> [User] {
  // ...
}

// To
func users(
  for category: Category,
  of kind: Kind,
  completionHandler: @escaping () -> Void
) throws -> [User] {
  // ...
}
```

## Type Inference

Prefer compact code and let the compiler infer the type for constants or variables of single instances wherever possible.
```swift
let age = 23
let nameList = ["Tai", "Matt", "Kari", "Sora"]
```

## Private and Final

It is preferred to define a class `final` and functions `private` by default. That said, remove `final` or `private` only if you do need to subclass or access a function publicly.

**Preferred**

```swift
final class MyCoolButton: UIButton {
  private func doSomeMagic() {
    // ...
  }
}
```

 **Not Preferred (unless needed)**
 
```swift
class MyCoolButton: UIButton {
  func doSomeMagic() {
    // ...
  }
}
```

## Appearance and Constraints

When setting up your `ViewController` or any `View` the following style is preferred:

**Preferred**

```swift
final class UserListVC: UIViewController {
  private let userListTableView = UITableView()
  
  override func viewDidLoad() {
    super.viewDidLoad()
    setupView()
  }
  
  // MARK: - Setup View
  
  private func setupView() {
    setupNavigation()
    setupUserListTableView()
  }
  
  private func setupNavigation() {
    // setup title, navigationItems, etc.
  }
  
  private func setupUserListTableView() {
    // setup constraints, colors, etc.
  }
}
```

**NOT Preferred**

```swift
final class UserListVC: UIViewController {
  private var userListTableView: UITableView {
    let tableView = UITableView()
    // setup code
    return tableView
  }
  
  override func viewDidLoad() {
    super.viewDidLoad()
    setupView()
  }
  
  private func setupView() {
    // some additional tableView related setup like constraints etc.
  }
}
```

## Extensions

When conforming to a protocol, add a separate extension for the protocol implementation and use a `//MARK: -` comment.

```swift
final class UserListVC: UIViewController {
  // ...
}

// MARK: - UITableViewDelegate

extension UserListVC: UITableViewDelegate {
  // implementation ...
}
```

## Golden Path

When coding with conditionals, the left-hand margin of the code should be the "golden" or "happy" path. That is, don't nest `if` statements. Multiple return statements are OK. The `guard` statement is built for this.

**Preferred:**

```swift
func makeFreelancer(using developer: Developer?, projectList: [Project]?) throws -> Freelancer? {
  
  guard let developer = developer else {
    throw FreelancerError.noDeveloper
  }
  
  guard let projectList = projectList, !projectList.isEmpty {
    return nil
  }
  
  // logic to create freelancer

  return freelancer
}
```

**Not Preferred:**

```swift
func makeFreelancer(using developer: Developer?, projectList: [Project]?) throws -> Freelancer? {

  if let developer = developer {
    if let projectList = projectList, !projectList.isEmpty {
      // use developer and projectList to create freelancer
      return freelancer
    } else {
      return nil
    }
  } else {
    throw FreelancerError.noDeveloper
  }
}
```

