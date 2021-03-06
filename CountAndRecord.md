# Simplifying the logic for count and record step

The implementation of working example can be found [https://bino.ca1.qualtrics.com/survey-builder/SV_bpiiqwA8HhUVVVc/edit](https://bino.ca1.qualtrics.com/survey-builder/SV_bpiiqwA8HhUVVVc/edit)

## Insert the script in the header section in the Geenral settings
```
<script type="text/javascript" src="//cdn.addpipe.com/2.0/pipe.js">
</script>
```

## Count and Record Questions configuration
![image](https://user-images.githubusercontent.com/5623935/139067653-2066d595-8bb4-4a52-a6f5-bcecc9974537.png)

## Recording step HTML
```
<div style="text-align: center;">
    <span style="font-size:22px;">
        <strong id="countLabel"></strong>
        <strong id="countdownContainer"></strong>
    </span>
    <span id="lookAtTheCameraText"> Please Look at the Camera</span>
</div>

<div id="recorder"></div>
```


## Recording step Javascript
```
// Account hash from addPype configuration
var accountHash = "edcf44381da8d8890a7b2028c111f725"
// Environment ID from addPype configuration
var eid = "fCGHCD"

Qualtrics.SurveyEngine.addOnload(function () { });
Qualtrics.SurveyEngine.addOnReady(function () {
    var that = this;
    var countTime = parseInt(Qualtrics.SurveyEngine.getEmbeddedData('countTime') || 0)
    console.log('Count and Record ', countTime)

    var pipeParams = {
        size: { width: 640, height: 360 },
        qualityurl: "avq/720p.xml",
        payload: "${e://Field/ResponseID}",
        mrt: countTime, accountHash, eid,
        showMenu: 0, sis: 1, asv: 1, mv: 1, st: 1, ssb: 0, dup: 0, srec: 0
    };

    PipeSDK.insert('recorder', pipeParams, function (recorderInserted) {
        var countDownInterval
        var nextStepTimeout
        var countLabel = document.getElementById("countLabel");
        var lookAtTheCameraText = document.getElementById("lookAtTheCameraText");
        // var countdownContainer = document.getElementById("countdownContainer");
        // let timer = pipeParams.mrt

        recorderInserted.onRecordingStarted = (id) => {
            console.log('Recording started', `hbRecordingStarted${countTime}`, `hbRecordingStarted` + countTime)

            Qualtrics.SurveyEngine.setEmbeddedData(`hbRecordingStarted` + countTime, new Date().toISOString())
            clearInterval(countDownInterval)
            clearTimeout(nextStepTimeout)

            countLabel.innerHTML = "COUNT"
            lookAtTheCameraText.innerHTML = 'Please Look at the Camera'

            nextStepTimeout = setTimeout(() => {
                console.log('Recording ended')
                // countdownContainer.innerHTML = ""
                lookAtTheCameraText.innerHTML = ""
                countLabel.innerHTML = "STOP"
                recorderInserted.stopVideo();
                Qualtrics.SurveyEngine.setEmbeddedData(`hbRecordingEnded` + countTime, new Date().toISOString())
            }, countTime * 1000)

            // Countdown text
            // countdownContainer.innerHTML = timer + 's'
            // countDownInterval = setInterval(() => {
            //     timer--;
            //     // countdownContainer.innerHTML = timer + 's'
            //     if (timer <= 0) {
            //         clearInterval(countDownInterval)
            //     }
            // }, 1000)
        }

        recorderInserted.onReadyToRecord = function (id, type) {
            recorderInserted.record();
        }

        recorderInserted.onSaveOk = function (recorderId, streamName, streamDuration, cameraName, micName, audioCodec, videoCodec, filetype, videoId, audioOnly, location) {
            console.log('onSaveOk')
            recorderInserted.stopVideo();
            recorderInserted.remove();
            that.clickNextButton()
        }
    });
});

Qualtrics.SurveyEngine.addOnUnload(function () { });
```

## Script for hearthbeats input field
```
Qualtrics.SurveyEngine.addOnload(function()
{
		jQuery('input:text').val('')
});
```

## Survey flow 
![image](https://user-images.githubusercontent.com/5623935/139033993-1cc9f95f-e925-4a81-8273-d8793aeb9f84.png)
- As first step we need to declare all variables that we will set trough the script
  - Each Count and Record step needs 2 variables hbRecordingStarted<seconds> and hbRecordingEnded<seconds>
- Each Count and Record step has group of steps defined as in the image above
  - Set countTime variable
  - Count and Record
  - Save counted heartbeats to embeded data
	
## Sigle step for Count and Record
![image](https://user-images.githubusercontent.com/5623935/139068313-fd927405-1b08-4bf2-adde-24db78973f38.png)

![image](https://user-images.githubusercontent.com/5623935/139068137-b3186dc7-a0cd-4adc-b0ea-fa26f61ee1f2.png)
