# Pixel Animation Baker Documentation

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
   

  

  

  
---

## **2/Configuration**

## 2.1/Sampling Mode
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

## 2.2/Model\&Animation Config

 ### 2.2.1/ Root Model Transform | 
 Reference to the model to be sampled

 ### 2.2.2/ Sampler Clips | 
 The list of AnimationClips to be sampled

   > [!CAUTION]
   > Animator component **must** use non-legacy AnimationClips
   > 
   > Animation component **must** use legacy AnimationClips

 ### 2.2.3/ Collect Animations on Bind | 
 if enabled, everytime the model is bound, it will also collect all the AnimationClips referenced by the Animation/Animator component and add them to Sampler Clips

## 2.3/Bake Config

### 2.3.1/Sprite Resolution

The size of exported sprites. Be cautious when increasing the size of sprite, as this will increase the exported sprite sheet proportionally.

### 2.3.2/Target Framerate

The framerate of the exported spritesheet animation. Be cautious when increasing the target framerate, as this will increase the exported sprite sheet proportionally.

When using mode Wait Frame, the highframe rate is not guaranteed to be stable. The usual safe threshold is to be equal or below **60fps**, depending on the machine.

### 2.3.3/Save Path

Save path is relative to the Project directory

### 2.3.4/Exported Name Format

You can use string format to name the exported files. The formatted argument is as below:

0 - Model Root transform name

1 - Sampling AnimationClip name

2 - Sprite Resolution

3 - Target Framerate

### 2.3.5/Exported Texture Format

The texture format to use for the exported spritesheet

## 2.4/Sampling Resource Template

### 2.4.1/Sampling RenderTexture Configs

Any subsequent RenderTextures created for sampling purpose will copy the **Color Fomat**, **Depth Stencil Format**, and **Filter Mode** of this original SampleRenderTexture.

You can adjust theses config to better suit your usage https://docs.unity3d.com/6000.4/Documentation/ScriptReference/Experimental.Rendering.GraphicsFormat.html

---

## **3/Troubleshoot Manual**

## Common Problems

**1. Baked spritesheet’s background is not transparent**

First, verify that your configuration is correct:

* **\[SampleCamera]**’s clear color is transparent (alpha is zero)
* **SampleRenderTexture** is referenced as **\[SampleCamera]**’s Output Texture and its color format has alpha component
* In your **URP Render Asset**, make sure that you tick **Alpha Processing** in the Post-processing dropdown

**2. The baked animation is unstable/Framerate is hitching**

**3. The baked animation start frame or end frame doesn’t match**

---

## **4/Customization**

---

## **5/Script Documentation**
