## Modules and Packages

Modules and Packages will provide the basic organization building block in Serulian.

### Module Definition

A module is defined as a single Serulian file (ending in `.seru`) or a directory containing `.seru`
files that **exports** some set of types or functions to be accessible to other modules.

### Package Definition

A package is defined as a directory of Serulian modules imported under a single name.

### Using modules and packages

Modules and Packages can be imported using the `from` or `import` keywords, which support import
from two different sources: local file system and VCS.

Syntax Examples:

```seru
// Imports the file "myfavoritemodule.seru" directly, adding its exported members into the local context
import myfavoritemodule

// Imports the file "someothermodule.seru", adding its exported members under the "FooBar" namespace
import someothermodule as FooBar

// Only imports "SomeClass" and "SomeStaticFunction" from the file "somelocalmodule.seru"
from somelocalmodule import SomeClass, SomeStaticFunction as Something

// Performs a VCS-import and places its members into the "FooBar2" namespace
import "github.com/foo/bar" as FooBar2

// Performs a VCS-import and imports the name "FooBar"
from "github.com/foo/bar:some_branch" import FooBar

// Performs a VCS-import at the given commit and places its members into the "BarBaz2" namespace
import "github.com/bar/baz@38fa35" as BarBaz2

// Imports the file "someidl.webidl" via WebIDL under the "SomeIDL" namespace
import webidl`someidl` as SomeIDL

// Only imports "Foo" from the file "someidl.webidl"
from webidl`someidl` import Foo

// Performs a VCS-import and imports the name Bar
from webidl`github.com/foo/bar` import Bar
```

#### Local File Import

Imports of local file-based modules or packages are accomplished by checking the following paths in order:

- The specified module name with `.seru` appended, starting at the current working directory
- The specified package name as a directory, starting as the current working directory

### VCS-based Import

VCS-based modules will be stored under the `.pkg` directory under the current working directory by replicating the URL structure:

Examples: 

For a HEAD commit: `.pkg/github.com/bar/bar/HEAD`
For a tag: `.pkg/github.com/bar/bar/tag/{tag}`
For a branch: `.pkg/github.com/bar/bar/branch/{branch}`
For a commit: `.pkg/github.com/bar/bar/branch/{commit SHA}`


Imports of VCS-based modules are accomplished as follows:

- If the VCS path is found under a directory specified via the `--vcs-dev-dir`, then the local found there is used and a warning is emitted.
- If a VCS tag or explicit commit ID is specified (with the `@` sign), checking for that specific commit in the `pkg` directory under the current working directory. If found, the local library copy is imported.
- If a VCS tag or explicit commit ID is *not* specified, the VCS repository is **checked out at runtime** into the `pkg` directory, after which it is imported. If the directory **already exists**, `pull` is run to update the directory, as `HEAD` of some branch was specified. A warning will be emitted noting this pull occurred.
- If not found in the above locations, fail.


### Declaring exports

Members of a module are exported by making them Capitalized. All lowercase members are unexported.

### Module members

#### Variables

Modules can have variables declared, with optional default values for nullable instances:

```seru
var<int> someModuleVar = 42
var<bool?> someNullableVar
```

#### Functions

Modules can declare functions that can be invoked by any member of the module or imported (if Capitalized).

```seru
function<void> doSomething() {
	...
}

function<int> SomeExportedFunction() {
	return 42
}
```

##### Asynchronous Functions

If a function defined at the **module-level** has a name that ends with the `Async` suffix, it is considered an asynchrounous function and is executed automatically via a web-worker or other async system. *All parameters and return values of async functions must be [Structural](structs.md)*.

```seru
function<int> DoSomethingAsync(a int, b int) {
	return a + b
}

function<int> CallTheAsyncThing() {
	return <- DoSomethingAsync(1, 2)
}
```


#### Types

[Classes](classes.md), [Interfaces](interfaces.md), [Nominal Types](nominals.md), and [Structural](structs.md) types can be declared under a module.


