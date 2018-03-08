# webshell-scanner-client

[![中文帮助](https://img.shields.io/badge/README-切换语言-blue.svg)](readme-zh_CN.md)

## Introduction

A Golang API client of [https://scanner.baidu.com](https://scanner.baidu.com). 

### List of supported file formats

Maximum allowed file size is `50MB`

| Language   | File extensions                             |
| ---------- | ------------------------------------------- |
| PHP        | .php .phtml .php3 .php4 .php5 .inc          |
| ASP        | .asp .asa .cer .cdx .asa                    |
| ASP.NET    | .aspx .asmx .ashx                           |
| Java       | .jsp .jspx .war                             |
| Archive    | .rar .zip .tar .xz .tbz .tgz .tbz2 .bz2 .gz |

## API Usage

The webshell scanner API runs asynchronously. The verdict needs be retrieved later via the `result` API.

### Enqueue API

Submit a sample to our scanning queue

```
curl https://scanner.baidu.com/enqueue -F archive=@web.zip
```

API response - on success

```
{
  // API return status and description
  "status": 0,
  "descr":  "Task enqueued",

  // sample hash
  "md5":    "de7128e140ac0add05d1728ab95d9d5d",

  // where to fetch scanning results
  "url":    "https://scanner.baidu.com/result/2744a8f7b85e799b79caf0ff014bfb3a",

  "data":   []
}
```

API response - on error

```
{
   // non-zero API status
   "status": 1

   // error description
   "descr":  "Extension 'xxx' is currently unsupported, list of supported file formats are: rar zip tar xz tbz tgz tbz2 bz2 gz php phtml inc php3 php4 php5 war jsp jspx asp aspx cer cdx asa ashx asmx cfm",

   "data" : [],   
}
```

## Result API

Retrieve the detection results, e.g

```
curl https://scanner.baidu.com/result/2744a8f7b85e799b79caf0ff014bfb3a
```

API response

```
[
  {
    // sample hash
    "md5": "de7128e140ac0add05d1728ab95d9d5d",

    // total files scanned
    "total": 1,

    // number of files scanned
    "scanned": 1,

    // number of detections
    "detected": 1,

    // status: pending/scanning/done
    "status": "done",

    // results
    "data": [
       {
          // relative path (only useful for archives)
          "path": "2744a8f7b85e799b79caf0ff014bfb3a.php",

          // produced verdict
          "descr": "BDS.WebShell.Chopper.1"
       }
    ]
  }
]
```

