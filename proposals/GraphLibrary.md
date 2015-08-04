## Graph Library

The Graph Library in the Serulian compiler will represent and store the full Serulian program and
all associated metadata.

### Goals

- Built on a true graph database: https://github.com/google/cayley
- Provide a nice API layer for use by not only the compiler but **other tools as well**
- Performant to update portions of the graph (partial updates, etc)
- Easy to visualize the full program's state

### Subgraphs/Graph layers

#### Source Representation Graph

The Source Representation Graph (SRG) will represent the Abstract Syntax Tree parsed from source files
plus additional metadata (such as the locations of the parsed node in the original files)

##### Structure

TBD: Most likely a rough 1-to-1 correspondence with the AST produced by the parser

 
#### Type Information Graph

The Type Information Graph (TIG) will represent an abstracted view of the type system
(Classes, Interfaces, Enums, Structs, etc), their relationships and their members.

##### Structure

TBD


#### Scope Information Graph

The Scope Information Graph (SIG) will represent the full scope information of the SRG. The SIG will
contain references to the members called, the types declared, etc.

##### Structure

TBD: Most likely links to the TIG


#### Optimization Metadata Graphs

Optimization Metadata Graph (OMGs) will be subgraphs/layers produced by information collecting runs
for the purpose of optimization. Name usage analysis, Data flow analysis, etc would be examples.

##### Structure

TBD


### Working with subgraphs/layers

FILL IN: How we can easily create, map and invalidate these graphs with Cayley


### Querying subgraphs/layers

FILL IN: How we can easily query information out of the subgraphs with Cayley

