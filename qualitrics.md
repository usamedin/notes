# Qualtrics Lab session

## Count text 
1. Each recording component the html contet should reflect following structure
```    
<div style="text-align: center;">
  <span style="font-size:22px;">
    <strong id="countLabel"></strong>
    </span> 
    Please Look at the Camera
</div>
 ```
 Notice the `<strong id="countLabel"></strong>` is empty html tag with id `countLabel`. The text will be added dynamicly when the camera recording is started
 
 2. In the Javascript code after line #27 inser following code
 ```
#27  PipeSDK.insert('third-recorder', pipeParams, function (recorderInserted) {
#28     countLabel = document.getElementById("countLabel");
        ...
 ```
 3. In the Javascript code after line #183 inser following code
 ```
 #183  recorderInserted.onRecordingStarted = function (id) {
 #184     countLabel.innerHTML = "COUNT"
          ...
 ```
 
 ## Count timeout
 
 1. In each recording component the html content we should append following code
```    
<div style="text-align: center;">
  <span style="font-size:22px;">
    <strong id="countLabel"></strong>
    <strong id="countdownContainer"></strong>
    </span> 
    Please Look at the Camera
</div>
 ```
 `<strong id="countdownContainer"></strong>` is the new part
 
 2. In the Javascript code after line #30 inser following code
 ```
#28  PipeSDK.insert('third-recorder', pipeParams, function (recorderInserted) {
#29     countLabel = document.getElementById("countLabel");
#30     let timer = pipeParams.mrt
#31     countdownContainer = document.getElementById("countdownContainer");
        ...
 ```
 
 3. In the Javascript code after line #184 inser following code
 ```
 #183  recorderInserted.onRecordingStarted = function (id) {
 #184     countLabel.innerHTML = "COUNT"
 #185     const interval = setInterval(() => {
 #185           timer--;
 #187           countdownContainer.innerHTML = timer + 's'
 #188           if(timer<=0){
 #189                clearInterval(interval)
 #190                countdownContainer.innerHTML = ""
 #191            }
 #192        },1000)
          ...
 ```
 
 # Improvement and Refactoring
 
 The code can be further improved by reusing existing components and utilizing functionalities like variables for controling the step, countdown timer and recording name for saving to the embedded data.
Summary: 
 With a little bit of logic we will end up with with 9 components instead of 19 components, and simplyfy the Survey flow to simpler easy to understand flow
Pros:
- Easier to maintain the code, always in one place
- Easier to make a change in the steps

 
