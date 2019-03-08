# react4xp-runtime-externals

**React4xp helper: using webpack externals as input, generates a runnable externals chunk for shared external libraries**

Externals is webpack's way to keep certain dependencies out of compilation and produced chunks, and instead tell compiled code that these libraries will be globally available in runtime from other ímported libraries (say from a CDN).

This webpack file wraps the building of those external libraries from existing packages. They are built into one separate chunk (TODO for later: multiple chunks?), along with some features tailored for React4xp: it gives the produced chunk a contenthash in the filename (content-hashed for HTTP client caching and cache-busting), and outputs a JSON file with the hashed name, for runtime reference. 

## Install

```bash
npm add --save-dev react4xp-runtime-externals
```

## Usage

Which dependencies are inserted into the external library, depends on an `env.EXTERNALS` parameter. `EXTERNALS` can also be supplied ìndirectly through a JSON config file referenced with an `env.REACT4XP_CONFIG_FILE` parameter - see for example [react4xp-buildconstants](https://www.npmjs.com/package/react4xp-buildconstants), although you can roll your own. 

This `EXTERNALS` parameter must be an object on the webpack externals format `{ "libraryname": "ReferenceInCode", ... }`. `EXTERNALS` can also be an object as a valid JSON-format string. 

These libraries of course have to be available from the calling context (as such, they can be thought of as peer dependencies, but are obviously impossible to declare here). 

In addition, a few more parameters are mandatory. Just like for `EXTERNALS`, they're expected either directly through `env` (webpack's [environment variable](https://webpack.js.org/guides/environment-variables/)) or in the JSON file referenced through `env.REACT4XP_CONFIG_FILE` (which is both preferable and easier):
  - `BUILD_R4X`: string, full path to the React4xp build folder (where all react4xp-specific output files will be built)
  - `CHUNK_CONTENTHASH`: integer, length of hash in chunk filenames
  - `EXTERNALS_CHUNKS_FILENAME`: string,name of an intermediary JSON file that stores the dynamic, hashed name of the output chunk.
  
## Example

After installing `react4xp-runtime-externals`, `react` and `react-dom`, and running this from the project folder `/me/myproject/`: put the following into `/me/myfolder/src/react4xpConstants.json` (or let [react4xp-buildconstants](https://www.npmjs.com/package/react4xp-buildconstants) create it for you)... 

```json
{
  "BUILD_R4X": "/me/myproject/build/r4x",
  "EXTERNALS": {
    "react": "React",
    "react-dom": "ReactDOM"
  },
  "CHUNK_CONTENTHASH": 8,
  "EXTERNALS_CHUNKS_FILENAME": "chunks.externals.json"
}

```

Then run:

```bash
webpack --config node_modules/react4xp-runtime-externals/webpack.config.js --env.REACT4XP_CONFIG_FILE=/me/myfolder/src/react4xpConstants.json
```

This will transpile React and ReactDom into the chunk `/me/myfolder/build/r4x/externals.<HASH>.js`, and that chunk filename is available in the file `/me/myfolder/build/r4x/chunks.externals.json`.


