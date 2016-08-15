# bunto-import

[![Build Status](https://travis-ci.org/bunto/bunto-import.svg?branch=master)](https://travis-ci.org/bunto/bunto-import)
[![Security](https://hakiri.io/github/bunto/bunto-import/master.svg)](https://hakiri.io/github/bunto/bunto-import/master)

The new __Bunto__ command for importing from various blogs to Bunto format.

**Note: _migrators_ are now called _importers_ and are only available if one installs the `bunto-import` _gem_.**

## How `bunto-import` works:

### Bunto v2.x and higher

1. Install the _rubygem_ with `gem install bunto-import`.
2. Run `bunto import IMPORTER [options]`

### Bunto v1.x

Launch IRB:

```ruby
# 1. Require bunto-import
irb> require 'bunto-import'
# 2. Choose the importer you'd like to use.
irb> importer_class = "Behance" # an example, there are many others!
# 3. Run it!
irb> BuntoImport::Importers.const_get(importer_class).run(options_hash)
```

## Documentation

bunto-import has its own documentation site, found at https://bunto-import.tk/.
Dedicated [documentation for each migrator](https://bunto-import.tk/docs/home/) is available there.
