# resampler
js audio resampler

### example
```javascript

navigator.mediaDevices.getUserMedia({audio: true})
    .then((stream) => { 
      let context    = new AudioContext(),
          bufSize    = 4096,
          microphone = context.createMediaStreamSource(stream),
          processor  = context.createScriptProcessor(bufSize, 1, 1), ;
          res        = new Resampler(context.sampleRate, 16000, 1, bufSize),
          bufferArray= [];
      
      processor.onaudioprocess = (event) => {
        // const right = event.inputBuffer.getChannelData(1);
        const outBuf = res.resample(event.inputBuffer.getChannelData(0));
        bufferArray.push.apply(bufferArray, outBuf);
      }
      
      this.start   = () => {
        if (processor && microphone) {
          bufferArray = [];
          microphone.connect(processor);
          processor.connect(context.destination);
        }
      };
      
       this.stop    = () => {
        if (processor && microphone) {
          microphone.disconnect();
          processor.disconnect();
        }
      };
      
      this.getPcm = () => {
        return new Float32Array(bufferArray);
      };
    })
```
