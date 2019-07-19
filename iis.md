
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

## Process

- Process: Browser
  - AudioThread
  - Chrome_HistoryThread
  - ThreadPoolServiceThread
  - BrowserWatchdog
  - Chrome_DevToolsHandlerThread
  - CrBrowserMain
    - TracingStartedInBrowser
    - Screenshot
    - InputLatency::TouchStart/TouchMove/TouchEnd...
  - Chrome_IOThread

- Process: GPU Process
  - CrGpuMain
    - GPUTask
  - VizCompositorThread
  - Watchdog
  - Chrome_ChildIOThread
  - ThreadPoolServiceThread

- Process: Renderer
  - Chrome_ChildIOThread
  - ThreadPoolServiceThread
  - Media
  - Compositor/5323(5323 is the tid)
    - ActivateLayerTree
    - BeginFrame
    - RequestMainThreadFrame
    - DrawFrame
  - CrRenderMain
    - // normal
    - RequestAnimationFrame
    - FunctionCall
    - UpdateCounters
    - FireAnimationFrame
    - SetLayerTreeId
    - UpdateLayerTree
    - UpdateLayer
    - CompositeLayers
    - BeginMainThreadFrame
    - Profile
    - V8.InvokeApiInterruptCallbacks
    - V8.HandleInterrupts
    - V8.StackGuard
    - // request
    - XHRReadyStateChange
    - PlatformResourceSendRequest
    - ResourceSendRequest
    - requestStart
    - ResourceReceiveResponse
    - ResourceReceivedData
    - XHRLoad
    - ResourceFinish

## Timestamp
- start time = first `RunTask.ts`

## Performance profile json file

### basic format
- `pid`: The process ID for the process that output this event.
- `tid`: The thread ID for the thread that output this event.
- `ts`: The **tracing clock timestamp** of the event. The timestamps are provided at microsecond granularity. 
- `ph`: The event type. This is a single character which changes depending on the type of event being output. The valid values are listed in the table below. We will discuss each phase type below.
- `cat`: The event categories. This is a comma separated list of categories for the event. The categories can be used to hide events in the Trace Viewer UI.
- `name`: The name of the event, as displayed in Trace Viewer
- `args`: Any arguments provided for the event. Some of the event types have required argument fields, otherwise, you can put any information you wish in here. The arguments are displayed in Trace Viewer when you view an event in the analysis section.
- `dur`: duration | Total Time，There is an extra parameter dur to specify the **tracing clock** duration of complete events in microseconds.
- `tdur`: An optional parameter tdur specifies the **thread clock** duration of complete events in microseconds.
- `tts`: Optional. The **thread clock timestamp** of the event. The timestamps are provided at microsecond granularity.

<!-- 
- send request
  - format: 
    - begin/PlatformResourceSendRequest: `{"pid":5301,"tid":5318,"ts":354567407000,"ph":"B","cat":"devtools.timeline","name":"PlatformResourceSendRequest","tts":531237291,"args":{"data":{"id":"48"}}}`
      - including: tts, dataid
    - instant/ResourceSendRequest: `{"pid":5301,"tid":5318,"ts":354567407175,"ph":"I","cat":"devtools.timeline","name":"ResourceSendRequest","s":"t","tts":531237465,"args":{"data":{"requestId":"1000005301.48","frame":"B9E6A9A5C349398CFCB2203893207F09","url":"http://192.168.1.43/starter2d/sound/destroy.wav","requestMethod":"GET","priority":"High","stackTrace":[{"functionName":"__proto.load","scriptId":"20","url":"http://192.168.1.43/starter2d/libs/laya.core.js","lineNumber":24128,"columnNumber":11}]}}}`
      - including: tts, request, stack, tts
    - end/PlatformResourceRequest: `{"pid":5301,"tid":5318,"ts":354567407449,"ph":"E","cat":"devtools.timeline","name":"PlatformResourceSendRequest","tts":531237717,"args":{}}`
  - summary:
    Send Request
    Resource
    192.168.1.43/starter2d/sound/destroy.wav
    Request Method
    GET
    Priority
    High
    Call Stacks
    Stack Trace
    __proto.load	@	192.168.1.43/starter2d/libs/laya.core.js:24128
    Pending for
    0.2 ms
    Initiator
    Reveal
  - Reveal summary:
    PlatformResourceSendRequest
    Total Time
    0.45 ms
    Self Time
    0.45 ms
  - instantevent.pending_for=instantevent.ts - begin.ts
  - instantevent.reveal.total_time=end.ts-begin.ts

- receive response
  - format: `{"pid":5301,"tid":5318,"ts":354567414657,"ph":"I","cat":"devtools.timeline","name":"ResourceReceiveResponse","s":"t","tts":531241108,"args":{"data":{"requestId":"1000005301.48","frame":"B9E6A9A5C349398CFCB2203893207F09","statusCode":200,"mimeType":"audio/wav","encodedDataLength":0,"fromCache":true,"fromServiceWorker":false,"timing":{"requestTime":354567.409185,"proxyStart":-1,"proxyEnd":-1,"dnsStart":-1,"dnsEnd":-1,"connectStart":-1,"connectEnd":-1,"sslStart":-1,"sslEnd":-1,"workerStart":-1,"workerReady":-1,"sendStart":0.192,"sendEnd":0.192,"receiveHeadersEnd":1.189,"pushStart":0,"pushEnd":0}}}}` 
  - summary: 
    Receive Response
    Resource
    192.168.1.43/starter2d/sound/destroy.wav
    Status Code
    200
    MIME Type
    audio/wav
    Pending for
    7.5 ms
    Initiator
    Reveal
    First Invalidated
    __proto.load	@	192.168.1.43/starter2d/libs/laya.core.js:24128

- receive data
  - format: `{"pid":5301,"tid":5318,"ts":354567415335,"ph":"I","cat":"devtools.timeline","name":"ResourceReceivedData","s":"t","tts":531241785,"args":{"data":{"requestId":"1000005301.48","frame":"B9E6A9A5C349398CFCB2203893207F09","encodedDataLength":14582}}}`
  - summary:
    Receive Data
    Resource
    192.168.1.43/starter2d/sound/destroy.wav
    Encoded Data
    14582 Bytes
    Pending for
    8.2 ms
    Initiator
    Reveal
    First Invalidated
    __proto.load	@	192.168.1.43/starter2d/libs/laya.core.js:24128

- finish loading
  - format: `{"pid":5301,"tid":5318,"ts":354567416674,"ph":"I","cat":"devtools.timeline","name":"ResourceFinish","s":"t","tts":531242991,"args":{"data":{"requestId":"1000005301.48","didFail":false,"encodedDataLength":0,"decodedBodyLength":14582,"finishTime":354567.414536}}}`
  - summary:
    Finish Loading
    Resource
    192.168.1.43/starter2d/sound/destroy.wav
    Decoded Body
    14582 Bytes
    Pending for
    9.5 ms
    Initiator
    Reveal
    First Invalidated
    __proto.load	@	192.168.1.43/starter2d/libs/laya.core.js:24128

  - pending for = finishloading.ts-ResourceSendRequest.ts -->

### Snapshot
- Screenshot
  - thread:
  - task name: `Screenshot`
  - ph: `O`
  - image:
    - `args -> snapshot` 
    - Imageformat: base 64

### Metadata
- Process Metadata
  - ph: `M`
  - cat: `__metadata`
  - info:
    - CPU number (task name:`num_cpus`) 
    - process information (task name:`process_sort_index`/`process_name`/`process_uptime_seconds`/`process_labels` )
    - library information (task name:`chrome_library_address`/`chrome_library_module`)
- Thread Metadata
  - thread: the corresponding thread
  - task name: `thread_name`
  - ph: `M`
  - cat: `__metadata`
  - info:
    - thread name

### Profiler Timeline Tracks

- Interactions->Input
  - Mouse Up/Down/Leave/Move, Touch Start/End, Key Up/Down
    - thread: `Browser->BrowserMainThread / Renderer->CompositorThread / GPU Process->VizCompositorThread`
    - task name: `InputLatency::MouseUp/Down/...`
    - ph: `F/S`
    - duration: task(ph=F).ts-task(ph=F).data.INPUT_EVENT_LATENCY_ORIGINAL_COMPONENT.time
    - (note: Mouse Up/Down/Leave in thread BrowserMainThread/CompositorThread; Mouse Move in VizCompositorThread)

- Interactions->Animation
  - Animation
    - thread: `Renderer->CrRendererMain`
    - task name: `Animation`
    - ph: `b/e`
    - duration: task(ph=b).ts-task(ph=e).ts

- Timings
  - note: `Renderer(main)` is a `Renderer` process with a `CompositorTileWorkerBackground` thread 
  - First Paint(FP)
    - thread: `Renderer(main) -> CrRendererMain`
    - task name: `firstPaint`
    - ph: `R`
  - First Contentful Paint(FCP)
    - thread: `Renderer(main) -> CrRendererMain`
    - task name: `firstContentfulPaint`
    - ph: `R`
  - First Meaningful Paint(FMP)
    - thread: `Renderer(main) -> CrRendererMain`
    - task name: `firstMeaningfulPaint`
    - ph: `R`
  - DOM Content Loaded(DCL)
    - thread: `Renderer(main) -> CrRendererMain`
    - task name: `MarkDOMContent`
    - ph: `I`
    - (note: only show the first one after `FP`) 
  - Onload Event(L)
    - thread: `Renderer(main) -> CrRendererMain`
    - task name: `MarkLoad`
    - ph: `I`
    - (note: only show the last one) 

- Main
  - TODO:

- Raster
  - (note: multi sub-track for each `Rasterizer Thread 1/2/3`)
  - Rasterize Paint
    - thread: `Renderer -> CompositorTileWorker` / `Renderer ->  CompositorTileWorkerBackground`
    - task name: `RasterTask`
    - ph: `B/E`
    - duration: task(ph=E).ts-task(ph=B).ts
  - Image Decode
    - task name: `ImageDecodeTask`
    - ph: `B/E`
    - duration: task(ph=E).ts-task(ph=B).ts

- GPU
  - GPU
    - thread: `GPU Process -> CrGpuMain`
    - task name: `GPUTask`
    - ph: `X`
    - duration: task.dur

- Chrome_ChildIOThread
  - Task
    - thread: `Renderer -> Chrome_ChildIOThread`
    - task name: `RunTask`
    - ph: `X`
    - duration: task.dur

- Compositor/[pid]
  - Task
    - thread: `Renderer -> Compositor` with corresponding [pid])
    - task name: `RunTask` 
    - ph: `X`
    - duration: task.dur

- GPUMemoryTask
  - Task
    - thread: `Renderer -> GPUMemoryThread`
    - task name: `RunTask`
    - ph: `X`
    - duration: task.dur

- ThreadPoolServiceThread
  - Task
    - thread: `Renderer -> ThreadPoolServiceThread`
    - task name: `RunTask` 
    - ph: `X`
    - duration: task.dur

- JS heap, Documents, Nodes, Listeners
  - thread: `Renderer(main) -> CrRendererMain`
  - task name: `UpdateCounters`
  - timestamp: task(ph=I).ts
  - data: args.data

- Network
  - Network request
    - thread: `Renderer -> CrRendererMain`
    - task name: `ResourceSendRequest` / `ResourceReceiveResponse` / `ResourceFinish`
    - Request
      ```
      |----░░░░░▒▒▒▒▒▒----|
      1    2    3    4    5
      ```
      - timestamp 1: `ResourceSendRequest.ts`
      - timestamp 2: `ResourceReceiveResponse.timing.requestTime` + `ResourceReceiveResponse.timing.sendStart`
      - timestamp 3: `ResourceReceiveResponse.timing.requestTime` + `ResourceReceiveResponse.timing.receiveHeadersEnd`
      - timestamp 4: `ResourceFinish.timing.finishTime`
      - timestamp 5: `ResourceFinish.ts`
  - 