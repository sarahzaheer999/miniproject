# miniproject
chloekim, sarah zaheer
// Copyright (c) 2019 ml5
//
// This software is released under the MIT License.
// https://opensource.org/licenses/MIT

```
/* ===
ml5 Example
PoseNet example using p5.js
=== */

var words = [" Draw a House", "Draw a Monkey", "Draw a Smiley Face "];
var x

let video;
let poseNet;
let poses = [];
var segments = [];
var prevX = 320
var prevY = 240


let timer = 500

function setup() {
  createCanvas(640, 480);
  video = createCapture(VIDEO);
  video.size(width, height);
  var randomword = random(words); //pick a random word!
  textSize(50);
  text(randomword, 320, 240);



  // Create a new poseNet method with a single detection
  poseNet = ml5.poseNet(video, modelReady);
  // This sets up an event that fills the global variable "poses"
  // with an array every time new poses are detected
  poseNet.on('pose', function(results) {
    poses = results;
  });
  // Hide the video element, and just show the canvas
  video.hide();
}

function modelReady() {
  select('#status').html('Model Loaded');
}

var replay = [];

function draw() {
  background('#ffe650')
  // var randomword = random(words); //pick a random word!
  textSize(50);
  text(random(words), 130, 240);

  image(video, 0, 0, width, height);

  // We can call both functions to draw all keypoints and the skeletons
  drawKeypoints();
  // drawSkeleton();
  drawanyThing()

}

function drawanyThing() {

  if (timer > 0) {

    stroke(0);
    strokeWeight(4)
    for (var x = 0; x < segments.length; x++) {
      var segment = segments[x];

      line(segment.x1, segment.y1, segment.x2, segment.y2);

      var lastPoint = segments[segments.length - 1];
      ellipse(lastPoint.x2, lastPoint.y2, 10);
    }

  } else {

    video.remove()
    textSize(25);
    fill('#ffe650')
    text("LOOK AT THIS MASTERPIECE WOW", 140, 50);
    stroke(0)
    strokeWeight(4)
    if (segments.length > 0) {
      //segments.shift();

      for (var x = 0; x < segments.length; x++) {
        var segment = segments[x];

        line(segment.x1, segment.y1, segment.x2, segment.y2);
      }


    }


  }
}

// A function to draw ellipses over the detected keypoints

function drawKeypoints() {
  // Loop through all the poses detected
  for (let i = 0; i < poses.length; i++) {
    // For each pose detected, loop through all the keypoints
    let pose = poses[i].pose;

    for (let j = 0; j < pose.keypoints.length; j++) {
      // A keypoint is an object describing a body part (like rightArm or leftShoulder)
      let keypoint = pose.keypoints[j];

      if (keypoint.score > 0.2) {

        if (keypoint.part == 'nose') {
          segments.push({
            x1: prevX,
            x2: keypoint.position.x,
            y1: prevY,
            y2: keypoint.position.y
          });
          stroke(0) //color for the line

          prevX = keypoint.position.x
          prevY = keypoint.position.y
        }
      }
      // Only draw an ellipse is the pose probability is bigger than 0.2
      if (keypoint.score > 0.2) {
        fill('#b73434');
        noStroke();

      }
      text(timer, 20, 50);
      if (frameCount % 10 == 0 && timer > 0) {
        timer--;


      }
    }

  }
}



// A function to draw the skeletons
function drawSkeleton() {
  // Loop through all the skeletons detected
  for (let i = 0; i < poses.length; i++) {
    let skeleton = poses[i].skeleton;
    // For every skeleton, loop through all body connections
    for (let j = 0; j < skeleton.length; j++) {
      let partA = skeleton[j][0];
      let partB = skeleton[j][1];
      stroke(255, 0, 0);
      line(partA.position.x, partA.position.y, partB.position.x, partB.position.y);
    }
  }

}
```
