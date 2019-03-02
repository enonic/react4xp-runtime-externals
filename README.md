# react4xp-runtime-externals

Externals is webpack's way to keep certain dependencies out of compilation and produced chunks, and instead tell compiled code that these libraries will be globally available in runtime from other ímported libraries (say from a CDN).

This webpack file wraps the building those external libraries from existing packages. They are built into one separate chunk (TODO for later: multiple chunks?), along with some features tailored for React4xp: it gives the produced chunk a contenthash in the filename, and outputs a JSON file with the hashed name, for runtime reference. Content-hashed for caching and cache-busting.

## Install

```bash
npm add --save-dev react4xp-runtime-externals
```

## Usage

Which dependencies are inserted into the external library, depends on an `env.EXTERNALS` parameter. `EXTERNALS` can also be supplied ìndirectly through a JSON config file referenced with an `env.REACT4XP_CONFIG_FILE` parameter - see for example [react4xp-buildconstants](https://www.npmjs.com/package/react4xp-buildconstants), although you can roll your own. 

This `EXTERNALS` parameter must be an object on the webpack externals format `{ "libraryname": "ReferenceInCode", ... }`, e.g. `{ "react-dom": "ReactDOM" }`. `EXTERNALS` can also be a valid JSON-format string. 

These libraries of course have to be available from the calling context (as such, they can be thought of as peer dependencies, but are obviously impossible to declare). 

In the same way, other parameters are expected either directly through `env` or in the JSON file referenced through `env.REACT4XP_CONFIG_FILE`:
  - `BUILD_R4X`: mandatory string, full path to the folder where the output files will be built
  - `RELATIVE_BUILD_R4X`: mandatory string, path to same output folder, but relative to the project root folder
