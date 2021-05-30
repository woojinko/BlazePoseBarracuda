# BlazePoseBarracuda
![demo_tabletop](/screenshot/demo_tabletop.gif)
![demo_fitness](/screenshot/demo_fitness.gif)
![demo_dance](/screenshot/demo_dance.gif)

**BlazePoseBarracuda** is a human pose estimation neural network that works with a monocular color camera.

BlazePoseBarracuda is Unity Package that runs the [Mediapipe Pose(BlazePose)](https://google.github.io/mediapipe/solutions/pose) pipeline on the [Unity](https://unity.com/).

BlazePoseBarracuda has 2 estimation modes(`Upper Body Only` and `Full Body`) and, can be switched on the realtime.

BlazePoseBarracuda implementation is inspired by [HandPoseBarracuda](https://github.com/keijiro/HandPoseBarracuda) and I referenced [his](https://github.com/keijiro) source code.(Thanks, [keijiro](https://github.com/keijiro)!).

### Dependencies
BlazePoseBarracuda uses the following sub packages:
- [PoseDetectionBarracuda](https://github.com/creativeIKEP/PoseDetectionBarracuda)
- [PoseLandmarkBarracuda](https://github.com/creativeIKEP/PoseLandmarkBarracuda)

### Install
BlazePoseBarracuda can be installed by adding below URLs from the Unity Package Manager's window
```
https://github.com/creativeIKEP/PoseDetectionBarracuda.git?path=Packages/PoseDetectionBarracuda#v1.0.0
```
```
https://github.com/creativeIKEP/PoseLandmarkBarracuda.git?path=Packages/PoseLandmarkBarracuda#v1.0.1
```
```
https://github.com/creativeIKEP/BlazePoseBarracuda.git?path=Packages/BlazePoseBarracuda#v1.0.0
```
or, appending lines to your manifest file(`Packages/manifest.json`) `dependencies` block.
Example is below.
```
{
  "dependencies": {
    "jp.ikep.mediapipe.posedetection": "https://github.com/creativeIKEP/PoseDetectionBarracuda.git?path=Packages/PoseDetectionBarracuda#v1.0.0",
    "jp.ikep.mediapipe.poselandmark": "https://github.com/creativeIKEP/PoseLandmarkBarracuda.git?path=Packages/PoseLandmarkBarracuda#v1.0.1",
    "jp.ikep.mediapipe.blazepose": "https://github.com/creativeIKEP/BlazePoseBarracuda.git?path=Packages/BlazePoseBarracuda#v1.0.0",
    ...
  }
}
```

### Usage Demo
Below code is the demo that estimate human pose from a image and get pose landmark.
Check ["/Assets/Script/PoseVisuallizer.cs"](/Assets/Script/PoseVisuallizer.cs) and ["/Assets/Scenes/SampleScene.unity"](/Assets/Scenes/SampleScene.unity) for BlazePoseBarracuda usage demo details.
```cs
using UnityEngine;
using Mediapipe.BlazePose;

public class <YourClassName>: MonoBehaviour
{
  // Set "Packages/BlazePoseBarracuda/ResourceSet/BlazePose.asset" on the Unity Editor.
  [SerializeField] BlazePoseResource blazePoseResource;
  [SerializeField] bool isUpperBodyOnly;

  BlazePoseDetecter detecter;

  void Start(){
      detecter = new BlazePoseDetecter(blazePoseResource, isUpperBodyOnly);
  }

  void Update(){
      Texture input = ...; // Your input image texture

      // Predict pose by neural network model.
      // Switchable anytime between upper body and full body with 2nd argment.
      detecter.ProcessImage(input, isUpperBodyOnly);

      /*
      `detecter.outputBuffer` is pose landmark result and ComputeBuffer of float4 array type.
      0~(24 or 32) index datas are pose landmark.
          Check below Mediapipe document about relation between index and landmark position.
          https://google.github.io/mediapipe/solutions/pose#pose_landmarks
          Each data factors are
          x: x cordinate value of pose landmark ([0, 1]).
          y: y cordinate value of pose landmark ([0, 1]).
          z: Landmark depth with the depth at the midpoint of hips being the origin.
             The smaller the value the closer the landmark is to the camera. ([0, 1]).
             This value is full body mode only.
             **The use of this value is not recommended beacuse in development.**
          w: The score of whether the landmark position is visible ([0, 1]).

      (25 or 33) index data is the score whether human pose is visible ([0, 1]).
      This data is (score, 0, 0, 0).
      */
      ComputeBuffer result = detecter.outputBuffer;

      // `detecter.vertexCount` is count of pose landmark vertices.
      // `detecter.vertexCount` returns 25 or 33 by upper or full body mode.
      int count = detecter.vertexCount;

      // Your custom processing from here, i.e. rendering.
      // For example, below is CPU log debug.
      var data = new Vector4[count];
      result.GetData(data);
      Debug.Log("---");
      foreach(var d in data){
        Debug.Log(d);
      }
  }
}
```

### Demo Image
Videos for demo scene(["/Assets/Scenes/SampleScene.unity"](/Assets/Scenes/SampleScene.unity)) was downloaded from [pixabay](https://pixabay.com).
- ["/Assets/Images/Doctor.mp4"](/Assets/Images/Doctor.mp4) was downloaded from [here](https://pixabay.com/videos/id-49811).
- ["/Assets/Images/Fitness.mp4"](/Assets/Images/Fitness.mp4) was downloaded from [here](https://pixabay.com/videos/id-72464).
- ["/Assets/Images/Dance.mp4"](/Assets/Images/Dance.mp4) was downloaded from [here](https://pixabay.com/videos/id-21827).

### Author
[IKEP](https://ikep.jp)

### LICENSE
Copyright (c) 2021 IKEP

[Apache-2.0](/LICENSE.md)
