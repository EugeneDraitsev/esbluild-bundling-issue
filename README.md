# Esbuild bundling issue

In this repository I have a reproducible example of esbuild bundling issue

## Description

For some reason, esbuild includes `encoding` package in bundle when I don't have it in dependencies.
I have super simple app with only 1 dependency and 2 devDependencies and 1 build script:

```json
{
  "scripts": {
    "build": "esbuild index.js --bundle --outfile=out.js --platform=node --target=node14 --analyze"
  },
  "dependencies": {
    "node-fetch": "^2.6.6"
  },
  "devDependencies": {
    "encoding": "^0.1.13",
    "esbuild": "^0.13.13"
  }
}
```

And I have 1 super simple file `index.js`:

```js
import fetch from 'node-fetch'
console.log(fetch)
```

When I try to run `yarn build` with `encoding` in the devDependencies, I get **917.4kb** out.js file

When I remove `encoding` form devDependencies out.js file weight is **382.2kb** (See analyze output in the steps to reproduce section)


## Steps to reproduce

1. Run `yarn` and `yarn build`. Check build details in the terminal:

```shell
yarn run v1.22.15
$ esbuild index.js --bundle --outfile=out.js --platform=node --target=node14 --analyze

  out.js  917.4kb

  out.js                                                           917.4kb  100.0%
   ├ node_modules/tr46/lib/mappingTable.json                       286.4kb   31.2%
   ├ node_modules/iconv-lite/encodings/tables/cp936.json            88.6kb    9.7%
   ├ node_modules/iconv-lite/encodings/tables/cp950.json            81.8kb    8.9%
   ├ node_modules/iconv-lite/encodings/tables/eucjp.json            79.3kb    8.6%
   ├ node_modules/iconv-lite/encodings/tables/cp949.json            71.0kb    7.7%
   ├ node_modules/iconv-lite/encodings/sbcs-data-generated.js       60.5kb    6.6%
   ├ node_modules/iconv-lite/encodings/tables/shiftjis.json         45.8kb    5.0%
   ├ node_modules/node-fetch/lib/index.js                           36.2kb    3.9%
   ├ node_modules/iconv-lite/encodings/tables/big5-added.json       35.8kb    3.9%
   ├ node_modules/whatwg-url/lib/url-state-machine.js               34.5kb    3.8%
   ├ node_modules/iconv-lite/encodings/dbcs-codec.js                16.1kb    1.8%
   ├ node_modules/tr46/index.js                                      7.9kb    0.9%
   ├ node_modules/iconv-lite/encodings/utf32.js                      7.7kb    0.8%
   ├ node_modules/iconv-lite/encodings/utf7.js                       7.3kb    0.8%
   ├ node_modules/iconv-lite/encodings/sbcs-data.js                  6.8kb    0.7%
   ├ node_modules/webidl-conversions/lib/index.js                    4.9kb    0.5%
   ├ node_modules/iconv-lite/encodings/internal.js                   4.9kb    0.5%
   ├ node_modules/whatwg-url/lib/URL.js                              4.8kb    0.5%
   ├ node_modules/iconv-lite/lib/index.js                            4.6kb    0.5%
   ├ node_modules/iconv-lite/encodings/utf16.js                      4.5kb    0.5%
   ├ node_modules/whatwg-url/lib/URL-impl.js                         4.3kb    0.5%
   ├ node_modules/iconv-lite/encodings/dbcs-data.js                  3.7kb    0.4%
   ├ node_modules/iconv-lite/lib/streams.js                          3.0kb    0.3%
   ├ node_modules/iconv-lite/encodings/tables/gb18030-ranges.json    2.7kb    0.3%
   ├ node_modules/safer-buffer/safer.js                              2.3kb    0.2%
   ├ node_modules/iconv-lite/encodings/tables/gbk-added.json         2.2kb    0.2%
   ├ node_modules/iconv-lite/encodings/sbcs-codec.js                 2.0kb    0.2%
   ├ node_modules/encoding/lib/encoding.js                           1.5kb    0.2%
   ├ node_modules/iconv-lite/lib/bom-handling.js                     1.2kb    0.1%
   ├ node_modules/whatwg-url/lib/public-api.js                       750b     0.1%
   ├ node_modules/whatwg-url/lib/utils.js                            725b     0.1%
   ├ node_modules/iconv-lite/encodings/index.js                      647b     0.1%
   └ index.js                                                        180b     0.0%
```

2. Remove `encoding` from devDependencies, re-run `yarn` and `yarn build`. Check build details in the terminal again:

```shell
$ esbuild index.js --bundle --outfile=out.js --platform=node --target=node14 --analyze

  out.js  382.2kb

  out.js                                               382.2kb  100.0%
   ├ node_modules/tr46/lib/mappingTable.json           286.4kb   74.9%
   ├ node_modules/node-fetch/lib/index.js               36.2kb    9.5%
   ├ node_modules/whatwg-url/lib/url-state-machine.js   34.5kb    9.0%
   ├ node_modules/tr46/index.js                          7.9kb    2.1%
   ├ node_modules/webidl-conversions/lib/index.js        4.9kb    1.3%
   ├ node_modules/whatwg-url/lib/URL.js                  4.8kb    1.3%
   ├ node_modules/whatwg-url/lib/URL-impl.js             4.3kb    1.1%
   ├ node_modules/whatwg-url/lib/public-api.js           750b     0.2%
   ├ node_modules/whatwg-url/lib/utils.js                725b     0.2%
   └ index.js                                            180b     0.0%
```

