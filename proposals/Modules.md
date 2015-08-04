## Modules

Modules will provide the basic organization building block in Serulian.

### Basic Definition

A module is defined as a single Serulian file (ending in `.seru`) that **exports** some set of types
or functions to be accessible to other modules.

### Using modules

Modules can be **imported** using the `from` or `import` keywords, which support import
from two different sources: local file system and Git.

Syntax Examples:

```
// Imports the file "myfavoritemodule.seru" directly, adding its exported members into the local context
import myfavoritemodule

// Imports the file "someothermodule.seru", adding its exported members under the "FooBar" namespace
import someothermodule as FooBar

// Imports the file "some/local/module.seru", adding its exported members under the "BarBaz" namespace
import "some/local/module" as BarBaz

// Only imports "SomeClass" and "SomeStaticFunction" from the file "somelocalmodule.seru"
from somelocalmodule import SomeClass, SomeStaticFunction as Something

// Performs a Git-import and places its members into the "FooBar2" namespace
import "github.com/foo/bar" as FooBar2

// Performs a Git-import and places its members into the "FooBar3" namespace
import "github.com/foo/bar:some_branch" as FooBar3

// Performs a Git-import at the given commit and places its members into the "BarBaz2" namespace
import "github.com/bar/baz@38fa35" as BarBaz2
```

#### Local File Import

Imports of local file-based modules are accomplished by checking the following paths in order:

- The specified module name with `.seru` appended, starting at the current working directory
- The specified module name with `.seru` appended, looking under a `lib` directory under the current working directory
- (If the environment variable `SERULIAN_LIB` is specified): The specified module name with `.seru` appended, under the `SERULIAN_LIB` directory
- If not found in the above locations, fail

### Git-based Import

Git-based modules will be stored under the `pkg` directory under the current working directory by replicating the URL structure:

Example: `pkg/github.com/bar/bar/38fa35`

Imports of Git-based modules are accomplished as follows:

- If a Git tag or commit ID is specified (with the `@` sign), checking for that specific commit in the `pkg` directory under the current working directory. If found, the local library copy is imported.
- If a Git tag or commit ID is *not* specified, the Git repository is **checked out at runtime** into the `pkg` directory, after which it is imported. If the directory **already exists**, `git pull` is run to update the directory, as `HEAD` of some branch was specified.
- If not found in the above locations, fail.


### Declaring exports

Members of a module are exported by decorating them with the `public` keyword. Any `public` type, top-level function or type member will be accessible
outside of the module.