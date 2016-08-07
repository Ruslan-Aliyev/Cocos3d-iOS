# Cocos3D Camera, Simple object and Imported object's Animation

## Preliminaries Setup:

- Install XCode, then Cocos2D, then Cocos3D. 
- Install Blender, then download and install PowerVR SDK from the 'imgtec' website (so you can export 3D models as .pod format out of blender). 
- Install PowerVR SDK, then copy all files from PowerVR's plugins (for Blender) folder into Blender's 'Addons' folder.					

## Results: camera orbits around the rotating cube and the rotating blender-model

![](https://raw.githubusercontent.com/atabegruslan/Cocos3d-iOS/master/Illustrations/cocos3d1.PNG)
						
## Key pieces of codes: The Scene .m file: 

```
-(void) initializeScene {

	CC3Camera* cam = [CC3Camera nodeWithName: @"Camera"];
	cam.location = cc3v( 0.0, 0.0, 18.0 );
	[self addChild: cam];

	CC3ResourceNode* rezNode = [CC3PODResourceNode nodeFromFile: @"monkeyblend.pod"];
	[self addChild: rezNode];
    
    CC3BoundingBox bounds = {{-1, -2.0, -2.0}, {0.0, 0.0, 0.0}};
    CC3MeshNode *cube = [[CC3MeshNode alloc] init];
    [cube populateAsSolidBox:bounds];
    [self addChild: cube];

	self.opacity = kCCOpacityFull;
	[self selectShaders];
	[self createBoundingVolumes];
	[self createGLBuffers];
	[self releaseRedundantContent];
    
    CCActionInterval* anim1 = [CC3RotateBy actionWithDuration:1.0 rotateBy: cc3v(30.0, 30.0, 0.0)];
    [rezNode runAction:[CCRepeatForever actionWithAction:anim1]];

    CCActionInterval* anim2 = [CC3RotateBy actionWithDuration:1.5 rotateBy: cc3v(40.0, 20.0, 0.0)];
    [cube runAction:[CCRepeatForever actionWithAction:anim2]];
	
	...
}

-(void) updateBeforeTransform: (CC3NodeUpdatingVisitor*) visitor {
    self.activeCamera.targetLocation = cc3v(0,0,0);
}

-(void) updateAfterTransform: (CC3NodeUpdatingVisitor*) visitor {
    [CC3Backgrounder.sharedBackgrounder runBlock: ^{ [self addSceneContentAsynchronously]; }];
    self.activeCamera.targetLocation = cc3v(0,0,0);   
    CCActionInterval* orbit = [CC3RotateBy actionWithDuration:30.0 rotateBy:cc3v(0.0, 360.0, 0.0)];
    [self.activeCamera runAction:[CCRepeatForever actionWithAction:orbit]];
}

-(void) onOpen {
	[CC3Backgrounder.sharedBackgrounder runBlock: ^{ [self addSceneContentAsynchronously]; }];

    self.activeCamera.targetLocation = cc3v(0,0,0);  
    CCActionInterval* orbit = [CC3RotateBy actionWithDuration:30.0 rotateBy:cc3v(0.0, 360.0, 0.0)];
}
```
