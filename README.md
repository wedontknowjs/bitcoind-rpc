bitcoind-rpc
======

[![NPM Package](https://img.shields.io/npm/v/bitcoind-rpc.svg?style=flat-square)](https://www.npmjs.org/package/bitcoind-rpc)
[![Build Status](https://img.shields.io/travis/owstack/bitcoind-rpc.svg?branch=master&style=flat-square)](https://travis-ci.org/owstack/bitcoind-rpc)
[![Coverage Status](https://img.shields.io/coveralls/owstack/bitcoind-rpc.svg?style=flat-square)](https://coveralls.io/r/owstack/bitcoind-rpc?branch=master)

A client library to connect to Bitcoin Core RPC in JavaScript.

## Attribution

This repository was created by copy forking [bitcoind-rpc commit da5d5ec](https://github.com/bitpay/bitcoind-rpc/commit/da5d5ec2723bf86583218f5f63158286a38ac2a2).

## Get Started

bitcoind-rpc.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install bitcoind-rpc
```

## Examples

```javascript
var run = function() {
  var btccore = require('btccore');
  var RpcClient = require('bitcoind-rpc');

  var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: '18332',
  };

  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new btccore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## License

**Code released under [the MIT license](https://github.com/owstack/bitcoind-rpc/blob/master/LICENSE).**

Copyright 2017 Open Wallet Stack
