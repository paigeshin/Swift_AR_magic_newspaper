# Swift_AR_magic_newspaper

### AR Tips

- 앵간하면 video file은 mp4로 쓰자

### Project  개요

- image recognition 시킨 후
- Sprite Kit을 이용해서 video play

### Configuration - image

```swift
override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        // Create a session configuration
        let configuration = ARImageTrackingConfiguration()

        if let trackedImages = ARReferenceImage.referenceImages(inGroupNamed: "NewsPaperImages", bundle: Bundle.main) {
            configuration.trackingImages = trackedImages
            configuration.maximumNumberOfTrackedImages = 1
            print("Images Found")
        }
        
        // Run the view's session
        sceneView.session.run(configuration)
}
```

### Video Rendering

- We need `Sprite Kit` to play video

```swift
//
//  ViewController.swift
//  AR_Live_News
//
//  Created by shin seunghyun on 2020/05/03.
//  Copyright © 2020 shin seunghyun. All rights reserved.
//

import UIKit
import SceneKit
import ARKit

/**
 AR로 이미지 recognition하고 그 안에 video play하게 하기
 **/

class ViewController: UIViewController, ARSCNViewDelegate {

    @IBOutlet var sceneView: ARSCNView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Set the view's delegate
        sceneView.delegate = self
        
        // Show statistics such as fps and timing information
        sceneView.showsStatistics = true
        
        // Create a new scene
        let scene = SCNScene(named: "art.scnassets/ship.scn")!
        
        // Set the scene to the view
        sceneView.scene = scene
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        // Create a session configuration
        let configuration = ARImageTrackingConfiguration()

        if let trackedImages = ARReferenceImage.referenceImages(inGroupNamed: "NewsPaperImages", bundle: Bundle.main) {
            configuration.trackingImages = trackedImages
            configuration.maximumNumberOfTrackedImages = 1
            print("Images Found")
        }
        
        // Run the view's session
        sceneView.session.run(configuration)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        
        // Pause the view's session
        sceneView.session.pause()
    }

    func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        
        
        
        let node = SCNNode()
        
        if let imageAnchor = anchor as? ARImageAnchor {
            
            /** Video Node Initialization, 기본적으로 spritekit으로 들어옴 **/
            /** Sprite Kit은 2D 게임 개발 프레임워크다.  그래서 2D처럼 평평하게 비디오를 play할 수 있음 **/
            
            //SKVideoNode 만들어줘서 video play
            let videoNode = SKVideoNode(fileNamed: "harrypotter.mp4")
            videoNode.play()
            //Scene 만들기
            let videoScene = SKScene(size: CGSize(width: 480, height: 360))
            
            /** 위치 잡아주기 **/
            videoNode.position = CGPoint(x: videoScene.size.width / 2, y: videoScene.size.height / 2) //center alignment
            videoNode.yScale = -1.0
            
            //추가
            videoScene.addChild(videoNode)
            
            //새로운 object 생성
            let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height)
            //object의 값을 videoScene을 넣어줌으로서 비디오가 안에서 재생되게 만듬
            plane.firstMaterial?.diffuse.contents = videoScene
            plane.firstMaterial?.diffuse.contents = UIColor(white: 1.0, alpha: 0.5)
            
            //실제로 object를 보여준다.
            let planeNode = SCNNode(geometry: plane)
            //object에 딱 붙게 만들어줌.
            planeNode.eulerAngles.x = -.pi / 2
            node.addChildNode(planeNode)
            
        }
        
        return node
        
    }
    

}
```
