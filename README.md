This is a fork of https://github.com/gruntjs/grunt-contrib-copy. This was necessary as the project seemingly is dead.<br>
The only change is that the idea of the pull request https://github.com/gruntjs/grunt-contrib-copy/pull/306 is applied (thank you to @greggman) but modified.<br>
Here fs.copyFileSync is the default instead of the Grunt variant as this is much faster.

# grunt-contrib-copy-faster v1.1.0

> Copy files and folders



## Getting Started

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-contrib-copy-faster --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-contrib-copy-faster');
```

*This plugin was designed to work with Grunt 0.4.x. If you're still using grunt v0.3.x it's strongly recommended that [you upgrade](http://gruntjs.com/upgrading-from-0.3-to-0.4), but in case you can't please use [v0.3.2](https://github.com/gruntjs/grunt-contrib-copy/tree/grunt-0.3-stable).*



## Copy task
_Run this task with the `grunt copy` command._

Task targets, files and options may be specified according to the grunt [Configuring tasks](http://gruntjs.com/configuring-tasks) guide.
### Options

#### useFsCopy
Type: `Boolean`
Default: `true`

This determines if fs.copyFileSync should be used instead of the Grunt copy variant. It **highly** reduces the execution time for copying files.<br>
Beware that if active the options in regards to `encoding` and the multiple ones for `process` then take no effect.
If they are needed you have to deactivate `useFsCopy`.

#### process
Type: `Function(content, srcpath)`

This option is passed to `grunt.file.copy` as an advanced way to control the file contents that are copied.

*`processContent` has been renamed to `process` and the option name will be removed in the future.*

#### noProcess
Type: `String`

This option is passed to `grunt.file.copy` as an advanced way to control which file contents are processed.

*`processContentExclude` has been renamed to `noProcess` and the option name will be removed in the future.*

#### encoding
Type: `String`  
Default: `grunt.file.defaultEncoding`

The file encoding to copy files with.

#### mode
Type: `Boolean` or `String`  
Default: `false`

Whether to copy or set the destination file and directory permissions.
Set to `true` to copy the existing file and directories permissions.
Or set to the mode, i.e.: `0644`, that copied files will be set to.

#### timestamp
Type: `Boolean`  
Default: `false`

Whether to preserve the timestamp attributes(`atime` and `mtime`) when copying files. Set to `true` to preserve files timestamp. But timestamp will *not* be preserved when the file contents or name are changed during copying.

### Usage Examples

```js
copy: {
  main: {
    files: [
      // includes files within path
      {expand: true, src: ['path/*'], dest: 'dest/', filter: 'isFile'},

      // includes files within path and its sub-directories
      {expand: true, src: ['path/**'], dest: 'dest/'},

      // makes all src relative to cwd
      {expand: true, cwd: 'path/', src: ['**'], dest: 'dest/'},

      // flattens results to a single level
      {expand: true, flatten: true, src: ['path/**'], dest: 'dest/', filter: 'isFile'},
    ],
  },
},
```

This task supports all the file mapping format Grunt supports. Please read [Globbing patterns](http://gruntjs.com/configuring-tasks#globbing-patterns) and [Building the files object dynamically](http://gruntjs.com/configuring-tasks#building-the-files-object-dynamically) for additional details.

Here are some additional examples, given the following file tree:
```shell
$ tree -I node_modules
.
├── Gruntfile.js
└── src
    ├── a
    └── subdir
        └── b

2 directories, 3 files
```

**Copy a single file tree:**
```js
copy: {
  main: {
    expand: true,
    src: 'src/*',
    dest: 'dest/',
  },
},
```

```shell
$ grunt copy
Running "copy:main" (copy) task
Created 1 directories, copied 1 files

Done, without errors.
$ tree -I node_modules
.
├── Gruntfile.js
├── dest
│   └── src
│       ├── a
│       └── subdir
└── src
    ├── a
    └── subdir
        └── b

5 directories, 4 files
```

**Copying without full path:**
```js
copy: {
  main: {
    expand: true,
    cwd: 'src',
    src: '**',
    dest: 'dest/',
  },
},
```

```shell
$ grunt copy
Running "copy:main" (copy) task
Created 2 directories, copied 2 files

Done, without errors.
$ tree -I node_modules
.
├── Gruntfile.js
├── dest
│   ├── a
│   └── subdir
│       └── b
└── src
    ├── a
    └── subdir
        └── b

5 directories, 5 files
```

**Flattening the filepath output:**

```js
copy: {
  main: {
    expand: true,
    cwd: 'src/',
    src: '**',
    dest: 'dest/',
    flatten: true,
    filter: 'isFile',
  },
},
```

```shell
$ grunt copy
Running "copy:main" (copy) task
Copied 2 files

Done, without errors.
$ tree -I node_modules
.
├── Gruntfile.js
├── dest
│   ├── a
│   └── b
└── src
    ├── a
    └── subdir
        └── b

3 directories, 5 files
```


**Copy and modify a file:**

To change the contents of a file as it is copied, set an `options.process` function as follows:

```js
copy: {
  main: {
    src: 'src/a',
    dest: 'src/a.bak',
    options: {
      process: function (content, srcpath) {
        return content.replace(/[sad ]/g, '_');
      },
    },
  },
},
```

Here all occurrences of the letters "s", "a" and "d", as well as all spaces, will be changed to underlines in "a.bak". Of course, you are not limited to just using regex replacements.

To process all files in a directory, the `process` function is used in exactly the same way.

NOTE: If `process` is not working, be aware it was called `processContent` in v0.4.1 and earlier.


##### Troubleshooting

By default, if a file or directory is not found it is quietly ignored. If the file should exist, and non-existence generate an error, then add `nonull:true`. For instance, this Gruntfile.js entry:

```js
copy: {
  main: {
    nonull: true,
    src: 'not-there',
    dest: 'create-me',
  },
},
```

gives this output:

```shell
$ grunt copy
Running "copy:main" (copy) task
Warning: Unable to read "not-there" file (Error code: ENOENT). Use --force to continue.

Aborted due to warnings.
```



## Release History

 * 2023-07-19   v1.1.1   Corrections/improvements in regards to using fs.copyFileSync. This is now the default.
 * 2023-07-18   v1.1.0   Use fs.copyFileSync if possible to highly improve copy speed.
 * 2016-03-04   v1.0.0   Bump devDependencies. Add example of using relative path. Point main to task and remove peerDeps.
 * 2015-10-19   v0.8.2   Fix expand-less copies with multiple files.
 * 2015-08-20   v0.8.1   Update `chalk` dependency.
 * 2015-02-20   v0.8.0   Performance improvements. The `mode` option now also applies to directories. Fix path issue on Windows.
 * 2014-10-15   v0.7.0   Add timestamp option to disable preserving timestamp when copying.
 * 2014-09-17   v0.6.0   Update chalk dependency and other devDependencies. Preserve file timestamp when copying.
 * 2013-12-23   v0.5.0   If an encoding is specified, overwrite `grunt.file.defaultEncoding`. Rename `processContent`/`processContentExclude` to `process`/`noProcess` to match Grunt API. `mode` option to copy existing or set file permissions.
 * 2013-03-26   v0.4.1   Output summary by default ("Copied N files, created M folders"). Individual transaction output available via `--verbose`.
 * 2013-02-15   v0.4.0   First official release for Grunt 0.4.0.
 * 2013-01-23   v0.4.0rc7   Updating grunt/gruntplugin dependencies to rc7. Changing in-development grunt/gruntplugin dependency versions from tilde version ranges to specific versions.
 * 2013-01-14   v0.4.0rc5   Updating to work with grunt v0.4.0rc5. Conversion to grunt v0.4 conventions. Replace `basePath` with `cwd`. Empty directory support.
 * 2012-10-18   v0.3.2   Pass `copyOptions` on single file copy.
 * 2012-10-12   v0.3.1   Rename grunt-contrib-lib dep to grunt-lib-contrib.
 * 2012-09-24   v0.3.0   General cleanup and consolidation. Global options depreciated.
 * 2012-09-18   v0.2.4   No valid source check.
 * 2012-09-17   v0.2.3   `path.sep` fallback for Node.js <= 0.7.9.
 * 2012-09-17   v0.2.2   Single file copy support. Test refactoring.
 * 2012-09-07   v0.2.0   Refactored from grunt-contrib into individual repo.

---

Task submitted by [Chris Talkington](http://christalkington.com/)
