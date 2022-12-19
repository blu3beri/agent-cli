# Design document new architecture

Right now there are a couple of problems with the current design.


## Everything is done manually

Registering a module in the CLI has to be done by hand. Meaning that when we
want to register 10 modules we have to repeat the same exact code for all of
the 10 modules.

This can be mitigated by using a macro where a macro takes the module-specific
input and expands to the registration of a module.

### Example

```rust
// From
match &cli.commands {
  Commands::Automate(options) => parse_automation_args(options, agent).await,
  Commands::Connection(options) => parse_connection_args(options, agent).await,
}

// To
register_cli!(automation)

// pseudo-macro
Commands::$1(options) => parse_$1_args(options, agent).await,
```

## Better difference and overlap between afj and ACA-Py

Right now the design always had aca-py in mind and we later tried to port AFJ
as a first class citizen. However, since there are quite some differences
between the agents there needs to be a better seperation.

## Keep it at a core for now

We recently added some functionality for stuff like `webhooks` and `wallet`
which are not well-defined core behaviour. Both can be useful, but since our
core is still not finished it just pollutes the API with unfinished work. 

We should prioritize the core functionality and make sure that that works very
very well.

Things like: `connection`, `oob`, `proof`, `credentials`, etc. have a way
higher priority.

## API should be more inline with AFJ

ACA-Py's public API, which is what siera is based on, is not the best looking.
It should be more based on the public API of AFJ.

## Look into Aries VCX support

## Code changes

### Idiomatic rust

The codebase is not idiomatic Rust and uses a lot useless `clones` and `String`
where we should just use `&str`. using static lifetimes should be okay, but
avoided.

### Abuse macros

We do so much repetitive work that macros can help with. Registering on the
cli, creating modules, etc.
