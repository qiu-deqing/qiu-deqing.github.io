---
title: 大文本文件读写
---



```
var fs = require('fs')
const readline = require('readline')
var stream = require('stream')

var srcFile = './2017_01_03_car_peijian.sql'
var targetFile = './car_peijian_sample_normalized.sql'

var instream = fs.createReadStream(srcFile)
var outstream = fs.createWriteStream(targetFile, { flags: 'w' })
outstream.readable = true
outstream.writable = true

var rl = readline.createInterface(instream, outstream)

/**
INSERT INTO `car_peijian` (car_model_id, peijian_id) VALUES
     ('12593', '1352564'),
     ('12593', '1352564')
    ;
 */
var isFirst = true

outstream.write('INSERT INTO `car_peijian` (car_model_id, peijian_id) VALUES')

rl.on('line', function (line) {
  if (line && line.startsWith('INSERT INTO `2017_01_03_car_peijian` VALUES')) {
    var value = line.slice(line.indexOf('(') + 1, line.indexOf(')'))
    var items = value.split(/\s*,\s*/)
    items = items.map(d => {
      return d == 'null' ? '' : d.replace(/^'(.*)'$/, '$1')
    })

    var carModelId = items[10]

    var start = 16
    var end = 198
    for (var i = start; i <= end; ++i) {
      var peijianId = items[i]
      var prefix = isFirst ? '' : ','
      if (peijianId) {
        outstream.write(`\n${prefix}('${carModelId}', '${peijianId}')`)
        isFirst = false
      }
    }
  }
})

rl.on('close', function () {
  outstream.write(';')
})
```