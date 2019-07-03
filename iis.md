
# IIS

## Install IIS on Win10

1. install IIS on Win10

ref:[Win10 IIS web server](https://blog.csdn.net/gameplayer097/article/details/83016112)

default rootpath is at `C:\inetpub\wwwroot`

## Add MIME type

1. open IIS Manager

1. open `IIS->MIME type`

1. right click to add new MIME type

# Chrome Profiler

## Performance profile json file

- snapshot
  - format: `{"pid":???,"tid":???,"ts":???,"ph":"O","cat":"disabled-by-default-devtools.screenshot","name":"Screenshot","id":"0x1","tts":???,"args":{"snapshot":"???"}}`
  - Image format: base64

- metadata
  - format: `{"pid":???,"tid":???,"ts":???,"ph":"M","cat":"__metadata","name":"???","args":???}`
  - information: CPU number, process information, chrome library information, thread information
  
- profiler
  - format: `{"pid":???,"tid":???,"ts":???,"ph":"P","cat":"disabled-by-default-v8.cpu_profiler","name":"ProfileChunk","id":"???","tts":???,"args":{"data":{"cpuProfile":{"nodes":[{"callFrame":{"functionName":"???","url":"???","scriptId":?,"lineNumber":?,"columnNumber":?},"id":?,"parent":?}, ...],"samples":[...]},"timeDeltas":[...],"lines":[...]}}}`
  - information: ??
