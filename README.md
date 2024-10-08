# purescript-open-foreign-generic

[![Build Status](https://github.com/paf31/purescript-foreign-generic/actions/workflows/ci.yml/badge.svg)](https://github.com/paf31/purescript-foreign-generic/actions/workflows/ci.yml)

[![Build Status](https://travis-ci.org/paf31/purescript-foreign-generic.svg?branch=master)](https://travis-ci.org/paf31/purescript-foreign-generic)

Generic deriving for `purescript-foreign`.

- [Module Documentation](generated-docs/Foreign/Generic.md)
- [Example](test/Main.purs)
- [Further examples in this repo](https://github.com/justinwoo/purescript-howto-foreign-generic)

## Example Usage

First, define some data type and derive `Generic`:

```purescript
import Prelude
import Data.Generic.Rep (class Generic)
import Data.Generic.Rep.Show (genericShow)

newtype MyRecord = MyRecord { a :: Int }
derive instance genericMyRecord :: Generic MyRecord _
instance showMyRecord :: Show MyRecord where show = genericShow
```

To encode JSON, use `genericEncodeJSON`:

```purescript
import Foreign.Generic (defaultOptions, genericEncodeJSON)

opts = defaultOptions { unwrapSingleConstructors = true }

genericEncodeJSON opts (MyRecord { a: 1 })
-- "{\"a\":1}"
```

And to decode JSON, use `genericDecodeJSON`:

```purescript
import Control.Monad.Except (runExcept)
import Foreign.Generic (genericDecodeJSON)

runExcept (genericDecodeJSON opts "{\"a\":1}" :: _ MyRecord)
-- (Right (MyRecord { a: 1 }))
```

Badly formed JSON will result in a useful error, which can be inspected or pretty-printed:

```purescript
import Data.Bifunctor (lmap)
import Foreign (renderForeignError)

lmap (map renderForeignError) $ runExcept (genericDecodeJSON opts "{\"a\":\"abc\"}" :: _ MyRecord)
-- (Left
--   (NonEmptyList
--     (NonEmpty
--       "Error at array index 0: (ErrorAtProperty \"a\" (TypeMismatch \"Int\" \"String\"))"
--       Nil)))
```
