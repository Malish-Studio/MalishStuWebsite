# Pixel Animation Baker Documentation

---

### Table of Content

<!-- toc -->

---

## **1/Getting started**

**1.**  Open scene **AnimationBakerScene**

   > [!TIP]
   > By default, the scene is at AnimationBaker/Scenes/AnimationBakerScene.unity

**2.** Drag **your model** onto the scene, adjust your model’s transform, and **\[SampleCamera]**’s FOV/transform to your liking

**3.** Open the **\[\[\[SAMPLER]]]** object and drag your model’s reference into **AnimationSampleRequester**’s **Root Model Transform** field and the AnimationClip you want to sample into **Sampler Clips** field

   > [!NOTE]
   > The **\[\[\[SAMPLER]]]** object contains the animation sampler and animation sample requester
    
   >>![SamplerPath.png](assets/SamplerPath.png)
 
**4.** **Enter Play Mode** and tap button **Start Sampling on AnimationSampleRequester** component
   >>![SampleRequesterButton.png](assets/SampleRequesterButton.png)

**5.** Navigate to the **Bake Path** to see your exported spritesheet!
   >> ![OutputPath](assets/OutputPath.png)
   
   > [!NOTE]
   >After you're done baking, you can view the baked animation data to the right hand side, and buttons to navigate the list of baked animations to the left hand side.
   >
   > You can also click on the animation data to copy the spritesheet name to your clipboard.
   >>> ![Overview.png](assets%2FOverview.png)

   >If you cannot see them, chances are the game view is too high-res, you may consider changing to a low-res resolution for the game view
   >> ![Resolution.png](assets%2FResolution.png)

  

  
---

## **2/Configuration**

 ### 2.0/ Unity Compability Feature Config |
By default, the tool should be compatible with various Unity built-in features, such as post-processing volume, Renderer Features, Renderer Layer, Line Renderer, etc.

Please send me an email if you have a problem using a Unity built-in features with the tool.

> **Scene Post-processing volume**
> ![PPVolume.png](assets%2FPPVolume.png)

> **URP Renderer feature**
> 
> See [here](documentation.md#4example-scenes) for example

 ### 2.1/Model\&Animation Config

  #### 2.1.1/ Root Model Transform | 
 Reference to the model to be sampled

  #### 2.1.2/ Sampler Clips | 
 The list of AnimationClips to be sampled

   > [!CAUTION]
   > Animator component **must** use non-legacy AnimationClips
   > 
   > Animation component **must** use legacy AnimationClips

  #### 2.1.3/ Collect Animations on Bind | 
 if enabled, everytime the model is bound, it will also collect all the AnimationClips referenced by the Animation/Animator component and add them to Sampler Clips. The model is bound in these three occasions if a model reference is assigned (a) on Awake, (b) when validate config, and (c) when user clicks the Bind Model button 

 ### 2.2/Bake Config

  #### 2.2.1/Sprite Resolution

The size of exported sprites. Be cautious when increasing the size of sprite, as this will increase the exported sprite sheet proportionally.

  #### 2.2.2/Target Framerate

The framerate of the exported spritesheet animation. Be cautious when increasing the target framerate, as this will increase the exported sprite sheet proportionally.

When using mode Wait Frame, the highframe rate is not guaranteed to be stable. The usual safe threshold is to be equal or below **60fps**, depending on the machine.

  #### 2.2.3/Save Path

Save path is relative to the Project directory

  #### 2.2.4/Exported Name Format

You can use string format to name the exported files. The formatted argument is as below:

0 - Model Root transform name

1 - Sampling AnimationClip name

2 - Sprite Resolution

3 - Target Framerate

  #### 2.2.5/Exported Texture Format

The texture format to use for the exported spritesheet

 ### 2.3/Sampling Resource Template

  #### 2.3.1/Sampling RenderTexture Configs

Any subsequent RenderTextures created for sampling purpose will copy the **Color Fomat**, **Depth Stencil Format**, and **Filter Mode** of this original SampleRenderTexture.

You can adjust theses config to better suit your usage https://docs.unity3d.com/6000.4/Documentation/ScriptReference/Experimental.Rendering.GraphicsFormat.html

---

 ### 2.4/Frame Seeking Mode
This option defines how the sample find the animation frame to blit

**Mode Wait Frame** |
<details><summary>
In this mode, animation is played **synchronously**. 
Each update, the sampler increments the timer by deltaTime, and checks if it should blit the current frame based on the timer, if yes perform blit and reset the timer
</summary>

> [!IMPORTANT]
>  Sampling via this mode provides the **most visual similarity** to how you view the animation/effects in scene view.
>
>  However, it's also **sensitive to the running framerate**, as the baked animation's frametime is always a multiple of the running frametime

</details>


**Mode Active Sampling** |
<details><summary>
In this mode, each update the sampler would request the animator to jump to the frame it needs, and blit the previous rendered frame
</summary>

> [!NOTE]
> Active Sampling flow
>>  1._Update 0_ : Sampler request frame 0
>>
>>  2._End of Update 0_ : frame 0 get rendered to the graphics buffer
>>
>>  3._Update 1_ : blit from the graphics buffer to the output buffer and request next frame

</details>

### 2.5/Buffer Frame End
In 3D looping animation, the frame 0 and the very last frame of the animation is usually the same for seamless looping. 
When baking into 2D animation, these animations will create two similar frames.
Tick this option if you want to blit the final frame, and omit it if not.

 ## **3/Troubleshoot Manual**

  ### Common Problems

**1. Baked spritesheet’s background is not transparent**

First, verify that your configuration is correct:

* **\[SampleCamera]**’s background color (clear color) is transparent (alpha is zero)
* **SampleRenderTexture** is referenced as **\[SampleCamera]**’s Output Texture and its color format has alpha component
* In your **URP Render Asset**, make sure that you tick **Alpha Processing** in the Post-processing dropdown

**2. The baked animation is unstable/Framerate is hitching**

**3. The baked animation start frame or end frame doesn’t match**

---

 ## **4/Example Scenes**

The package comes with several sample scenes, each showcasing various features you can use with the package. For these sample scenes, it's a must that your project use the URP RenderPipeline Asset that comes with the package. 

> **Activating URP, HDRP, or a custom render pipeline based on SRP**
>
>    In your Project folder, locate the Render Pipeline Asset(s) that you want to use.
>
>    Set the default render pipeline, which Unity uses when there is no override for a given quality level
>        Select **Edit > Project Settings > Graphics**.
>        Set Default Render Pipeline to the Render Pipeline Asset you want to use.
>
>    Set override Render Pipeline Assets for different quality levels.
>        Select **Edit > Project Settings > Quality**.
>        Set Render Pipeline Asset to the Render Pipeline Asset you want to use.


  ###   1/AnimationBakerScene_GradientResampleColor

This example adds a URP RendererFeature to resample the value of the blit image (in other words, the lightness/darkness of the image) to match the gradient provided in the post-processing material

> [!NOTE]
> Due to  copyright reason, the gradient image is not provided with the distributed source code. You can either create your own gradient image, or download the pallets available online from site such as Lospec 

![ResampleColor.png](assets%2FResampleColor.png)

> [!NOTE]
> You can change the gradient image to another image of your choosing. This example uses a Lospec pallete.

> To add this feature to your scene, simply add the FullScreenPassRendererFeature to your active RendererData asset with the configurations as below
> 
> ![ResampleRenderFeature.png](assets%2FResampleRenderFeature.png)


  ###   2/AnimationBakerScene_DepthBlit
_(in progress...)_
>This example adds a URP RendererFeature to blit the depth texture of the camera to the view. You can sample animation as per normals to get the depth textures of the animation sequence
>![depthView.png](assets%2FdepthView.png)
---

