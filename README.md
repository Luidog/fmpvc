# FMPVC

`FMPVC` is a tool to help FileMaker developers by creating a set of text files which represent design objects in their databases (e.g. scripts, custom functions, layouts, etc.).  `fmpvc` has no access to database content.  The command, `fmpvc`, parses a Database Design Report (DDR) produced by FileMaker Pro Advanced and creates text files for each of the primary FileMaker objects described in the DDR.  With those files the developer may:

1. use a version control system to track changes to databases
1. diff current objects with objects from previous versions (e.g. compare different versions of a script)
2. perform full text searches of a set of FileMaker datases (e.g. find all uses of a custom function in a solution)
3. obtain text representations of FileMaker objects (e.g. create a list of fields in a table)

DDR parsing is a one-way process, and there is currently no way to re-create a FileMaker file from DDR,  Therefore, there is no direct way to restore, for instance, an old version of a FileMaker Script to your current working FileMaker file.  The best we can do is retrieve the previous text version and examine it to recreate the FileMaker script manually.  It is recommended that developers save clones of the FileMaker databases with each version control commit so that older versions of some of the items may be copied from the clone and pasted into latest versions (or, of course, entire databases may be restored).


## Installation

`FMPVC` is a ruby gem and may be installed as follows:

    $ gem install fmpvc

`FMPVC` requires both Nokogiri and ActiveSupport gems.

`FMPVC` requires ruby 2.0 or later.  FMPVC has only been tested on Mac OS X, and in it's current state, it is unlikely to work properly in a Windows ruby environment (due to line endings, file paths, etc.).  `fmpvc` should run fine on a Linux machine, but of course, the DDR generation requires FileMaker Pro Advanced, which is only available on Mac OS X and Windows.

## Usage

By default the `fmpvc` command looks for a `Summary.xml` file in a directory called `fmp_ddr` in the current working directory.  It reads the contents of that file and then processes each of the referenced report files (there is one for each FileMaker file included in the DDR).  It produces a set of text files and directories representing each database inside of the directory, `fmp_text`.  Example output looks like this:

		├── fmp_clone/
		│   └── FMServer_Sample Clone.fmp12
		├── fmp_ddr/
		│   ├── FMServer_Sample_fmp12.xml
		│   └── Summary.xml
		├── fmp_text/
		│   └── FMServer_Sample_fmp12.xml/
		│       ├── Accounts.txt
		│       ├── CustomFunctions/
		│       │   └── GetWorkDays (id 1).txt
		...etc.

Usage in brief:

- change directory to the location where you'd like to save the DDR and clones and produce the text files
- create a directory, `fmp_ddr`, to hold DDR
- from FileMaker Pro Advanced, choose "Database Design Report..." from the Tools menu.  
	- choose project database files
	- include all tables for each file
	- include all DDR sections
	- choose XML output
	- save in the folder created above with the default name, `Summary`
- optionally, save clones of the same databases in a folder named, `fmp_clone` (this is not required)
- run the command `fmpvc`

Command-line options:

    -h                               Show help message
    -b, --base-dir <directory>       Path to base directory (contains fmp_ddr/).
    -d, --ddr-dir <directory>        Look for DDR files in directory named <directory>.
    -D, --text-dir <directory>       Write text files in directory named <directory>.
    -q, --quiet                      Suppress progress messages.
    -s, --summary-file <filename>    Look for Summary file named <filename>.
    -t, --tree-file <filename>       Set tree file name.
    -T, --no-tree                    Don't create a tree file.
    -Y, --no-yaml                    Suppress YAML in text files.

### YAML

By default, `fmpvc` appends a [YAML](http://www.yaml.org/spec/1.2/spec.html) representation of the FileMaker element to each text file.  `fmpvc` doesn't capture all of the details of every FileMaker object, and even when it does, there are cases where there isn't an easy way to represent the object that is more concise or clear than the YAML description.  In cases where `fmpvc` doesn't describe an aspect of a FileMaker element, by including a full YAML representation, changes will not be missed in a diff.  The YAML is typically more human-readable and easier to diff than the original XML from the DDR.

YAML generation can be suppressed with the `-Y` command-line option.

### tree command

The tree command creates a textual representation of a directory and its filesystem objects.  By default, `fmpvc` searches for `tree` in the shell's path and if it finds, one, uses it to create the file, `./fmp_text/tree.txt`.

`tree` is not available by default on Mac OS X, but can be easily installed with a package manager such as [homebrew](http://brew.sh/).  Most Linux installations include the `tree` command.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release` to create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

1. Fork it ( https://github.com/MartinBoswell/fmpvc/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
