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
    "telegraf": "4.4.2"
  },
  "devDependencies": {
    "esbuild": "^0.13.13",
    "lerna": "^3.22.1"
  }
}
```

And I have 1 super simple file `index.js`:

```js
import { Telegraf } from 'telegraf'
console.log(Telegraf)
```

When I try to run `yarn build` with `lerna` in the devDependencies, I get **712.7kb** out.js file

When I remove `lerna` form devDependencies out.js file weight is **173.1kb** (See analyze output in the steps to reproduce section)


## Steps to reproduce

1. Run `yarn` and `yarn build`. Check build details in the terminal:

```shell
$ esbuild index.js --bundle --outfile=out.js --platform=node --target=node14 --analyze

  out.js  712.7kb


  out.js                                                                                 712.7kb  100.0%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/cp936.json            88.6kb   12.4%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/cp950.json            81.8kb   11.5%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/eucjp.json            79.3kb   11.1%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/cp949.json            71.1kb   10.0%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/sbcs-data-generated.js       60.5kb    8.5%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/shiftjis.json         45.8kb    6.4%
   ├ node_modules/node-fetch/lib/index.js                                                 35.9kb    5.0%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/big5-added.json       35.8kb    5.0%
   ├ node_modules/telegraf/lib/context.js                                                 17.3kb    2.4%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/dbcs-codec.js                16.1kb    2.3%
   ├ node_modules/telegraf/lib/telegram.js                                                15.0kb    2.1%
   ├ node_modules/event-target-shim/dist/event-target-shim.js                             13.8kb    1.9%
   ├ node_modules/telegraf/lib/composer.js                                                13.8kb    1.9%
   ├ node_modules/telegraf/lib/core/network/client.js                                      9.0kb    1.3%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/utf32.js                      7.7kb    1.1%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/utf7.js                       7.3kb    1.0%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/sbcs-data.js                  6.8kb    1.0%
   ├ node_modules/telegraf/lib/telegraf.js                                                 6.1kb    0.9%
   ├ node_modules/telegraf/node_modules/debug/src/common.js                                5.0kb    0.7%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/internal.js                   4.9kb    0.7%
   ├ node_modules/encoding/node_modules/iconv-lite/lib/index.js                            4.7kb    0.7%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/utf16.js                      4.5kb    0.6%
   ├ node_modules/telegraf/node_modules/debug/src/browser.js                               4.3kb    0.6%
   ├ node_modules/telegraf/node_modules/debug/src/node.js                                  4.2kb    0.6%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/dbcs-data.js                  3.7kb    0.5%
   ├ node_modules/telegraf/lib/scenes/context.js                                           3.5kb    0.5%
   ├ node_modules/sandwich-stream/dist/sandwich-stream.js                                  3.0kb    0.4%
   ├ node_modules/encoding/node_modules/iconv-lite/lib/streams.js                          3.0kb    0.4%
   ├ node_modules/telegraf/lib/core/network/polling.js                                     2.9kb    0.4%
   ├ node_modules/telegraf/node_modules/ms/index.js                                        2.8kb    0.4%
   ├ node_modules/supports-color/index.js                                                  2.8kb    0.4%
   ├ node_modules/abort-controller/dist/abort-controller.js                                2.8kb    0.4%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/gb18030-ranges.json    2.7kb    0.4%
   ├ node_modules/telegraf/lib/markup.js                                                   2.7kb    0.4%
   ├ node_modules/buffer-fill/index.js                                                     2.7kb    0.4%
   ├ node_modules/safer-buffer/safer.js                                                    2.3kb    0.3%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/tables/gbk-added.json         2.2kb    0.3%
   ├ node_modules/telegraf/lib/session.js                                                  2.1kb    0.3%
   ├ node_modules/telegraf/lib/button.js                                                   2.1kb    0.3%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/sbcs-codec.js                 2.1kb    0.3%
   ├ node_modules/telegraf/lib/index.js                                                    2.0kb    0.3%
   ├ node_modules/p-timeout/index.js                                                       1.9kb    0.3%
   ├ node_modules/telegraf/lib/scenes/stage.js                                             1.8kb    0.3%
   ├ node_modules/telegraf/lib/router.js                                                   1.5kb    0.2%
   ├ node_modules/encoding/lib/encoding.js                                                 1.5kb    0.2%
   ├ node_modules/telegraf/lib/scenes/wizard/index.js                                      1.4kb    0.2%
   ├ node_modules/telegraf/lib/core/network/webhook.js                                     1.3kb    0.2%
   ├ node_modules/telegraf/lib/core/network/multipart-stream.js                            1.2kb    0.2%
   ├ node_modules/encoding/node_modules/iconv-lite/lib/bom-handling.js                     1.2kb    0.2%
   ├ node_modules/telegraf/lib/scenes/base.js                                              1.2kb    0.2%
   ├ node_modules/telegraf/lib/scenes/index.js                                             1.1kb    0.2%
   ├ node_modules/safe-compare/index.js                                                    1.1kb    0.2%
   ├ node_modules/telegraf/lib/scenes/wizard/context.js                                    975b     0.1%
   ├ node_modules/buffer-alloc/index.js                                                    863b     0.1%
   ├ node_modules/telegraf/lib/core/network/error.js                                       750b     0.1%
   ├ node_modules/encoding/node_modules/iconv-lite/encodings/index.js                      669b     0.1%
   ├ node_modules/telegraf/lib/core/helpers/check.js                                       622b     0.1%
   ├ node_modules/telegraf/lib/core/helpers/compact.js                                     617b     0.1%
   ├ node_modules/buffer-alloc-unsafe/index.js                                             536b     0.1%
   ├ node_modules/has-flag/index.js                                                        456b     0.1%
   ├ node_modules/telegraf/lib/telegram-types.js                                           387b     0.1%
   ├ node_modules/telegraf/node_modules/debug/src/index.js                                 339b     0.0%
   ├ node_modules/telegraf/lib/deunionize.js                                               311b     0.0%
   └ index.js                                                                               89b     0.0%
```

2. Remove `lerna` from devDependencies, re-run `yarn` and `yarn build`. Check build details in the terminal again:

```shell
$ esbuild index.js --bundle --outfile=out.js --platform=node --target=node14 --analyze

  out.js  173.1kb
   ├ node_modules/telegraf/lib/core/network/polling.js             2.9kb    1.7%
   ├ node_modules/ms/index.js                                      2.8kb    1.6%
   ├ node_modules/abort-controller/dist/abort-controller.js        2.8kb    1.6%
   ├ node_modules/telegraf/lib/markup.js                           2.7kb    1.6%
   ├ node_modules/buffer-fill/index.js                             2.7kb    1.6%
   ├ node_modules/telegraf/lib/session.js                          2.1kb    1.2%
   ├ node_modules/telegraf/lib/button.js                           2.1kb    1.2%
   ├ node_modules/telegraf/lib/index.js                            2.0kb    1.2%
   ├ node_modules/p-timeout/index.js                               1.9kb    1.1%
   ├ node_modules/telegraf/lib/scenes/stage.js                     1.8kb    1.0%
   ├ node_modules/telegraf/lib/router.js                           1.5kb    0.9%
   ├ node_modules/telegraf/lib/scenes/wizard/index.js              1.4kb    0.8%
   ├ node_modules/telegraf/lib/core/network/webhook.js             1.3kb    0.7%
   ├ node_modules/telegraf/lib/core/network/multipart-stream.js    1.2kb    0.7%
   ├ node_modules/telegraf/lib/scenes/base.js                      1.2kb    0.7%
   ├ node_modules/telegraf/lib/scenes/index.js                     1.1kb    0.7%
   ├ node_modules/safe-compare/index.js                            1.1kb    0.6%
   ├ node_modules/telegraf/lib/scenes/wizard/context.js            975b     0.6%
   ├ node_modules/buffer-alloc/index.js                            863b     0.5%
   ├ node_modules/telegraf/lib/core/network/error.js               750b     0.4%
   ├ node_modules/telegraf/lib/core/helpers/check.js               622b     0.4%
   ├ node_modules/telegraf/lib/core/helpers/compact.js             617b     0.3%
   ├ node_modules/buffer-alloc-unsafe/index.js                     536b     0.3%
   ├ node_modules/telegraf/lib/telegram-types.js                   387b     0.2%
   ├ node_modules/debug/src/index.js                               317b     0.2%
   ├ node_modules/telegraf/lib/deunionize.js                       311b     0.2%
   └ index.js                                                       89b     0.1%
```

