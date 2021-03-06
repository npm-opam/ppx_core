## 113.24.00

- Kill the nonrec rewrite done by typerep. It is no longer needed since
  4.02.2, we kept it only for compatibility with the camlp4 code.

- Merlin uses `@merlin.* ...` attributes in different places. Which ppx\_driver
  reports as unused.

  Introduce the concept of reserved namespaces.
  When one declares the namespace "foo" as reserved then:
    - `foo.*` will never get reported as unused
    - it is impossible to `Attribute.declare "foo.*"`

  Mark the "merlin" namespace as reserved by default.

- Don't print:

    Extension `foo' was not translated.
    Hint: Did you mean foo?

- OCaml makes no distinctions between "foo" and
  `{whatever|foo|whatever}`. The delimiter choice is simply left to the
  user.

  Do the same in our ppx rewriters: i.e. wherever we accept "foo", also
  accept {whatever|foo|whatever}.

- Avoid stupid hints like this one:

    Attribute `default' was not used.
    Hint: `default' is available for label declarations but is used here
    in the context of a label declaration. Did you put it at the wrong
    level?

- Update the API for the common case of extension point expanders.

  Make it simpler to define ppx rewriters that locally expand extension
  points, which is the majority of our non-type-conv rewriters.

  Such expanders are run inside the same `Ast_traverse.map` in a
  top-down manner which:

  - probably improve speed
  - help with rewriters that capture a pretty-print of their payload
  - help with rewriter that interpret some extension points in a special
    way inside their payload

- Fix the order in which errors are reported by ppx rewriters.
  Make them be reported in the same order as they appear.

- Mark attributes as handled inside explicitly dropped pieces of code.

  So that a `@@deriving` inside a let%test dropped by
  `ppx_inline_test_drop` doesn't cause a failure.
