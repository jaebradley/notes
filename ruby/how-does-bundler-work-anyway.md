# [How Does Bundler Work Anyway](https://andre.arko.net/2015/04/28/how-does-bundler-work-anyway/)

## How does `require` work

* `require` loads code from files and directories
* A naive implementation looks like

```ruby
def require(filename)
  eval File.read(filename)
end
```

* Downsides are that you could load the same file multiple times and this is dangerous
* Get around this by keeping track of all files loaded with global variable like this

```ruby
$LOADED_FEATURES = []

def require(filename)
  if !$LOADED_FEATURES.include?(filename)
    eval File.read(filename)
    $LOADED_FEATURES << filename
  end
end
```

* Now, `filename` has to be absolute path - but is there a way to consider a set of different paths to load from?

```ruby
$LOAD_PATHS = ["first/path", "second/path"]
def require(filename)
  full_path = $LOAD_PATHS.find do |path|
    File.exist?(File.join(path, filename))
  end
  eval File.read(full_path)
end
```

* Can combine these two concepts, previously loaded files and different load paths

```ruby
$LOADED_FEATURES = []
$LOAD_PATHS = ["first/path", "second/path"]

def require(filename)
  full_path = $LOAD_PATHS.find do |path|
    File.exist?(File.join(path, filename))
  end
  if !$LOADED_FEATURES.include?(full_path)
    eval File.read(full_path)
    $LOADED_FEATURES << full_path
  end
end
```

## RubyGems

* If no version of a gem has been activated, RubyGems will automatically activate the newest version that is already installed
* Can load a specific version of a gem by doing `gem "rack", "1.0"; require "rack"`
* It's hard to coordinate dependencies
  * If one developer installs a gem, everybody (and every machine) has to install the gem
  * This also could mean that a different version is installed for each person
* Gem activation errors
  * RubyGems activates a gem and then is asked to activate a different version of the same gem
  * This raises an exception since you can't have two versions of the same gem
  * RubyGems loads the newest installed gem by default so when another dependency declared only works for an older version, things explode
  * RubyGems does runtime dependency resolution which means that is loads the gems you ask for, when you ask for them, and doesn't know if you're going to need different versions later
  * To prevent this, dependency resolution has to happen before runtime
* Bundler records the exact versions of every gem into another file called `Gemfile.lock` - this lockfile is the reason for consistent dependency versions
* `bundle install`
  * Read the Gemfile and Gemfile.lock
  * Ask RubyGems for all versions of all the dependencies
  * If necessary, find gem versions allowed by the Gemfile that work together
  * If found, write does those versions in the lockfile for future installs
  * Install gems until every gem in the lockfile is installed
* `bundle exec`
  * Read the Gemfile and Gemfile.lock
  * If needed, find gem versions allowed by the Gemfile that work together
  * If found, write down these versions in the lockfile for future installs
  * Remove any existing gems from the `$LOAD_PATH` array
  * Add each gem listed in the lockfile to the `$LOAD_PATH` array
  * Doesn't ask RubyGems for a list of gem versions - it only sets up Ruby by loading gems that are installed
  * It doesn't install gems that are missing
