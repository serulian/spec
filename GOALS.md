# Overall Project Goals

A performant, secure language for easy development of web-based and mobile-web applications

## Performance
- Compilation time must be nearly instantaneous: no more than a few seconds for even the largest projects when in development mode
- Optimization: the code produced with full optimizations should be lean and fast
- Significant effort should be developed to develop future-facing code: Leverage newer ECMAScript and browser features when available, but make it as transparent to the developers as possible

## Safety
- Type safety: Types provide better readability and code safety
- Null safety: Language should be designed to make null pointer exceptions avoidable when possible
- Injection safety: Untrusted input should be treated as such by the type system, preventing injection attacks

## Maintainability
- All library code should be stored in source control and referenced by tag or commit, preventing library code from changing “under” the developer
- Modules and imports should allow for consistent organization
- "Built in"s should be replaceable: allows for customization and prevents lock-in

## Developer speed
- Concise syntax for fast development
- Provide nice-to-have features for better development
- Templates and data binding will make it easier to write data-driven applications
- Easy serialization and deserialization of structures for moving data 
- Easy use of external JavaScript libraries