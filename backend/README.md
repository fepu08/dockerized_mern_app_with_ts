# Notes

The `ts-node` package allows you to run your TypeScript code directly without compiling it to JavaScript. It’s like the node executable but for .ts files.

# Issues on Windows using npm >= 7

`nodemon` cannot find `ts-node`: "'ts-node' is not recognized as an internal or external command,
operable program or batch file." when trying to `npm run start:dev`

The description of the issue can be found at: https://github.com/remy/nodemon/issues/1951#issuecomment-1003362605

Possible solution is installing ts-node globally on your machine with `npm install -g ts-node`
