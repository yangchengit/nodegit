NodeGit
-------

> Node bindings to the [libgit2](http://libgit2.github.com/) project.

[![Build
Status](https://travis-ci.org/tbranyen/nodegit.png)](https://travis-ci.org/nodegit/nodegit)

**Stable: 0.1.1**

Maintained by Tim Branyen [@tbranyen](http://twitter.com/tbranyen), Michael
Robinson [@codeofinterest](http://twitter.com/codeofinterest), and Nick Kallen
[@nk](http://twitter.com/nk), with help from [awesome
contributors](https://github.com/tbranyen/nodegit/contributors)!

## API Documentation. ##

http://www.nodegit.org/nodegit/

## Building and Installing. ##

Dependencies:

* [Python 2](https://www.python.org/)
* [CMake >= 2.8](http://www.cmake.org/)

``` bash
npm install nodegit
```

### Building manually: ###

If you wish to help contribute to nodegit it is useful to build locally.

``` bash
# Fetch this project.
git clone git://github.com/tbranyen/nodegit.git

# Enter the repository.
cd nodegit

# Install the template engine, run the code generation script, and install.
npm install ejs && npm run codegen && npm install
```

If you encounter errors, you most likely have not configured the dependencies
correctly.

### Installing dependencies: ###

Using Brew on OS X:

``` bash
brew install cmake libzip
```

Using APT on Ubuntu:

``` bash
sudo apt-get install cmake libzip-dev build-essential
```

## API examples. ##

### Cloning a repository and reading a file: ###

``` javascript
var clone = require("nodegit").Repo.clone;

// Clone a given repository into a specific folder.
clone("https://github.com/nodegit/nodegit", "tmp", null, function(err, repo) {
  if (err) {
    throw err;
  }

  // Use a known commit sha from this repository.
  var sha = "59b20b8d5c6ff8d09518454d4dd8b7b30f095ab5";

  // Look up this known commit.
  repo.getCommit(sha, function(err, commit) {
    if (err) {
      throw error;
    }

    // Look up a specific file within that commit.
    commit.getEntry("README.md", function(err, entry) {
      if (err) {
        throw error;
      }

      // Get the blob contents from the file.
      entry.getBlob(function(err, blob) {
        if (err) {
          throw err;
        }

        // Show the name, sha, and filesize in byes.
        console.log(entry.name() + entry.sha() + blob.size() + "b");

        // Show a spacer.
        console.log(Array(72).join("=") + "\n\n");

        // Show the entire file.
        console.log(String(blob));
      });
    });
  });
});
```

### Emulating git log: ###

``` javascript
var open = require("nodegit").Repo.open;

// Open the repository directory.
open("tmp", function(err, repo) {
  if (err) {
    throw err;
  }

  // Open the master branch.
  repo.getMaster(function(err, branch) {
    if (err) {
      throw err;
    }

    // Create a new history event emitter.
    var history = branch.history();

    // Create a counter to only show up to 9 entries.
    var count = 0;

    // Listen for commit events from the history.
    history.on("commit", function(commit) {
      // Disregard commits past 9.
      if (++count >= 9) {
        return;
      }

      // Show the commit sha.
      console.log("commit " + commit.sha());

      // Store the author object.
      var author = commit.author();

      // Display author information.
      console.log("Author:\t" + author.name() + " <", author.email() + ">");

      // Show the commit date.
      console.log("Date:\t" + commit.date());

      // Give some space and show the message.
      console.log("\n    " + commit.message());
    });

    // Start emitting events.
    history.start();
  });
});
```

## Unit tests. ##

You will need to build locally before running the tests.  See above.

``` bash
npm test
```
