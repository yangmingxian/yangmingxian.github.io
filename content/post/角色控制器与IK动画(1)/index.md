---
title: 角色控制器与IK动画（一）
author: Mingxian Yang
date: 2021-07-30 18:10:00 +0800
description: 我最近在家里闭门造车，想自己尝试一下以前想尝试的新想法，所以在我最近正在做的Demo里面尝试了一下使用IK和Raycast的角色控制器，效果不错。我觉得这个的实现属于通用性很强的角色控制器的解决方案，所以先分享出来一部分。
categories: [Game Design]
tags: [Unity3D]
---


我在角色控制器中添加了 Hand IK、Foot Ik 和 Head IK 以使得角色的动作更加自然，而不是大量使用动画来进行模拟，那样的话会产生极大的工作量和很差的交互体验。

在游戏开发过程中，第三人称角色的控制再也普遍不过了，但是设置第三人称角色控制器可能很麻烦。遍观各种游戏的角色控制，根据不同的需求，实现的复杂程度极为不同。每个人都希望从他们的控制器中实现不同的效果，我今天来分享一下我目前Demo的工作原理。



### **CharacterControllers and Rigidbodies**

首先要说的就是两种最常见的第三人称角色控制方案：
1. Unity3D 内置的 **CharacterController** 模块
2. **Collider + Rigidbody** 的模块组合

要选择角色控制器最主要的指标在于两大方面，一是组件能够移动角色（Movement），另一方面是碰撞体交互（Colliders）。这其中具体细分比如重力，跳跃，阻挡，击倒等方面的内容。
![Character Controller](gifs/008.gif)


通常情况下，我们考虑一些斜坡、台阶、和碰撞的因素。
![Character Controller2](gifs/007.gif)

Unity3D的内置方案 Character Controller 是使用起来最为方便的，使用 Rigidbody 需要我们进行一些改进但是它更为贴近物理.


Character controller可以上台阶或者斜坡. A rigidbody + Capsule的方案有时候不能上斜坡或者台阶。

但是使用Character controller，移动collider或者进行其他碰撞体交互时会遇到麻烦。吐槽一下Unity的示例也会出现移动碰撞或者断断续续的问题。需要继续改进。
![Collider](gifs/009.gif)

我就是需要 Capsule Collider + Rigidbody 的控制和物理特性，因此我使用 Capsule 进行基本碰撞，并通过Raycast的方式进行自定义Controller。

GIF来源：[Youtube Link](https://www.youtube.com/watch?v=e94KggaEAr4)，有兴趣的可以去看一下.

下面精简地介绍一下实现方式。

### **基本移动设置**
#### **输入**
获取输入：摇杆 / WASD / ↑ ↓ ← →
```csharp
    vertical = Input.GetAxis("Vertical");
    horizontal = Input.GetAxis("Horizontal");
```


#### **Camera**
校正角色的移动方向，获取主相机的前和右方向，以便角色能够朝向相机对准的前方移动。
```csharp
    Vector3 correctedVertical = vertical * Camera.main.transform.forward;
    Vector3 correctedHorizontal = horizontal * Camera.main.transform.right;
```
#### **Normalise**
合并校正后的输入并对其进行归一化，因此无论向哪个方向移动都是相同的。保持 Y 输入为 0，保持角色在Y轴的位置，以防角色陷入地板或者空气。
```csharp
    moveDirection = new Vector3((combinedInput).normalized.x, 0, (combinedInput).normalized.z);
```
#### **旋转**
```csharp
    if(moveDirection!=Vector3.zero)
        {
            Quaternion rot = Quaternion.LookRotation(moveDirection);
            Quaternion targetRotation = Quaternion.Slerp(transform.rotation, rot, Time.fixedDeltaTime * inputAmount * rotateSpeed);
            transform.rotation = targetRotation;
        }
```
#### **速度**
```csharp
    rigidbody.velocity=(moveDirection *moveSpeed);
```
这里暂时直接赋值，后面会改变移动的方式。这个使用了rigidbody的物理属性，因此把它放到 **FixedUpdate()** 里面

### **地板光线投射**
![地板光线投射](012.png)
我们使用Raycast来进行探测，找到地板的位置并返回position信息。这里我只写了一条射线，从左脚的位置lpos向下发射，结果返回给leftHit.point。  
项目中我使用了10条射线，左右脚各5条，一个在正中间，4个在周围。这可以方便我们根据射线的碰撞点的信息判断地形。
因为这可以确定角色周围是否存在悬崖地形或者壁架，这样可以让角色播放一些摇摇晃晃的动画，让角色形象更生动起来。
```csharp
    if (Physics.Raycast(lpos, -Vector3.up, out leftHit, 1))
            {
                Debug.DrawRay(lpos, -Vector3.up, Color.green);
                lFPos = leftHit.point;
            }
```
### **移动刚体**
然后将 RigidBody 位置移动到光线投射 Y 位置，仍在 FixedUpdate 里面实现。
```csharp
    floorMovement =  new Vector3(rb.position.x, FindFloor().y + floorOffsetY, rb.position.z);
            // 判断角色是否在地面上
            if(FloorRaycasts(0,0, 0.6f) != Vector3.zero && floorMovement != rb.position)
            {
                // 移动地面上的角色
                rb.MovePosition(floorMovement);
                gravity.y = 0;
            }
```
附加 CharacterMovement Script 并添加 Rigidbody 和 Capsule Collider，确保它离开地面并位于角色的中心，如图所示。我们使用的是Raycast方式判断角色是否在地面上，因此collider不要接地，它仅供其他碰撞检测的交互，不用来和地板接触。 
此外，冻结刚体的旋转并关闭重力，重力是在未接地时手动施加的。冻结刚体后角色不会横向倒地。
![FootIK2](013.png)


并且为了防止粘在墙壁/壁架上，向具有 0 摩擦力的 Capsule Collider 添加物理材质。
![FootIK2](014.png)


### **IK**
项目使用的是** Humanoid ** Rigs，其他rig，如generic不支持如下的解决方法。

![FootIK](gifs/010.gif)
![FootIK2](gifs/011.gif)

#### **Foot IK**
为了让脚适应地板，我们必须设置 IK Override的位置、旋转和权重。

通过从脚部骨骼向下投射光线来找到位置和旋转。并使用 hit.point 作为位置，并与 hit.normal 一起旋转。
```csharp
    leftFoot = anim.GetBoneTransform(HumanBodyBones.LeftFoot);
    rightFoot = anim.GetBoneTransform(HumanBodyBones.RightFoot);
    if (Physics.Raycast(lpos, -Vector3.up, out leftHit, 1))
        {
            Debug.DrawRay(lpos, -Vector3.up, Color.green);
            lFPos = leftHit.point;
            lFRot = Quaternion.FromToRotation(transform.up, leftHit.normal) * transform.rotation;
        }
        if (Physics.Raycast(rpos, -Vector3.up, out rightHit, 1))
        {
            Debug.DrawRay(rpos, -Vector3.up, Color.blue);
            rFPos = rightHit.point;
            rFRot = Quaternion.FromToRotation(transform.up, rightHit.normal) * transform.rotation;
        }
```
然后设置 IKPosition 和 IKRotation
```csharp
    anim.SetIKPositionWeight(AvatarIKGoal.LeftFoot, lFweight);
    anim.SetIKPositionWeight(AvatarIKGoal.RightFoot, rFweight);

    anim.SetIKRotation(AvatarIKGoal.LeftFoot, lFRot);
    anim.SetIKRotation(AvatarIKGoal.RightFoot, rFRot);
```
根据动画的 Curve 设置 IKPosition 和 IKRotation 的 权重
```csharp
    anim.SetIKRotationWeight(AvatarIKGoal.LeftFoot, lFweight);
    anim.SetIKRotationWeight(AvatarIKGoal.RightFoot, rFweight);
```
![Curve](015.png)

#### **Head IK**
和脚的IK相比，头部IK相当简单。  
一种非常简单的混合方法是获取 lookAtThis 位置和 Head 骨骼之间的距离。反转距离以获得良好的动画过渡。这里需要注意的就是调整头部以及身体的权重以获得更好的效果。
```csharp
    anim.SetLookAtWeight(lookIKWeight, bodyWeight, headWeight, eyesWeight, clampWeight);

```
![HeadIK](gifs/016.gif)


#### **Hand IK**
![HeadIK](gifs/017.gif)
类似于 Foot IK 部分，这次从肩部进行光线投射.

这里应该再次基于动画中的 float 参数进行混合，并使用avatar mask，因此在进行全身动画时它不会影响移动手臂。

### **其他的一些东西**
这篇文章实现了角色控制的一些关于移动和交互的方面，其实一个比较完整的控制器还需要更多的工作。  

接下来的工作还被期望实现人物的跳跃，爬坡，以及攀爬的功能，值得一提的是，我们应该再次把目光集中在procedural work上，而不是用动画堆砌出来生硬的角色。类似的例子：
![Jump](gifs/028.gif)

------
### Ref
[Unity 5 Tutorial The Built-In IK System](https://www.youtube.com/watch?v=EggUxC5_lGE)

[How to Move Characters In Unity 3D - Character Controllers Explained](https://www.youtube.com/watch?v=e94KggaEAr4)

[Tim Dawson@ironicaccount](https://twitter.com/ironicaccount/status/938061346297413632)

