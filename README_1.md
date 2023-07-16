# GASDocumentation
My understanding of Unreal Engine 5's GameplayAbilitySystem plugin (GAS) with a simple multiplayer sample project. This is not official documentation and neither this project nor myself are affiliated with Epic Games. I make no guarantee for the accuracy of this information.

The goal of this documentation is to explain the major concepts and classes in GAS and provide some additional commentary based on my experience with it. There is a lot of 'tribal knowledge' of GAS among users in the community and I aim to share all of mine here.

The Sample Project and documentation are current with **Unreal Engine 5.2**. There are branches of this documentation for older versions of Unreal Engine, but they are no longer supported and are liable to have bugs or out of date information.

[GASShooter](https://github.com/tranek/GASShooter) is a sister Sample Project demonstrating advanced techniques with GAS for a multiplayer FPS/TPS.

The best documentation will always be the plugin source code.

<a name="table-of-contents"></a>
## Table of Contents

> 1. [Intro to the GameplayAbilitySystem Plugin](#intro)
> 1. [Sample Project](#sp)
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Concepts](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [Setup and Initialization](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute Definition](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Responding to Attribute Changes](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set Definition](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set Design](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [Subcomponents with Individual Attributes](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [Adding and Removing AttributeSets at Runtime](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [Item Attributes (Weapon Ammo)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Defining Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Initializing Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Removing Gameplay Effects](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [Activation Failed Tags](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [Enum names are now represented by path name](#troubleshooting-enumnamesarenowpathnames)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [5.2](#changelog-5.2)  
>    * [5.1](#changelog-5.1)  
>    * [5.0](#changelog-5.0)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. Intro to the GameplayAbilitySystem Plugin
From the [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>The Gameplay Ability System is a highly-flexible framework for building abilities and attributes of the type you might find in an RPG or MOBA title. You can build actions or passive abilities for the characters in your games to use, status effects that can build up or wear down various attributes as a result of these actions, implement "cooldown" timers or resource costs to regulate the usage of these actions, change the level of the ability and its effects at each level, activate particle or sound effects, and more. Put simply, this system can help you to design, implement, and efficiently network in-game abilities as simple as jumping or as complex as your favorite character's ability set in any modern RPG or MOBA title.

The GameplayAbilitySystem plugin is developed by Epic Games and comes with Unreal Engine 5 (UE5). It has been battle tested in AAA commercial games such as Paragon and Fortnite among others.

The plugin provides an out-of-the-box solution in single and multiplayer games for:
* Implementing level-based character abilities or skills with optional costs and cooldowns ([GameplayAbilities](#concepts-ga))
* Manipulating numerical `Attributes` belonging to actors ([Attributes](#concepts-a))
* Applying status effects to actors ([GameplayEffects](#concepts-ge))
* Applying `GameplayTags` to actors ([GameplayTags](#concepts-gt))
* Spawning visual or sound effects ([GameplayCues](#concepts-gc))
* Replication of everything mentioned above

In multiplayer games, GAS provides support for [client-side prediction](#concepts-p) of:
* Ability activation
* Playing animation montages
* Changes to `Attributes`
* Applying `GameplayTags`
* Spawning `GameplayCues`
* Movement via `RootMotionSource` functions connected to the `CharacterMovementComponent`.

**GAS must be set up in C++**, but `GameplayAbilities` and `GameplayEffects` can be created in Blueprint by the designers.

Current issues with GAS:
* `GameplayEffect` latency reconciliation (can't predict ability cooldowns resulting in players with higher latencies having lower rate of fire for low cooldown abilities compared to players with lower latencies).
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* Lack of boilerplate templates, multiplayer examples, and documentation. Hopefully this helps with that!

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2. Sample Project
A multiplayer third person shooter sample project is included with this documentation aimed at people new to the GameplayAbilitySystem Plugin but not new to Unreal Engine 5. Users are expected to know C++, Blueprints, UMG, Replication, and other intermediate topics in UE5. This project provides an example of how to set up a basic third person shooter multiplayer-ready project with the `AbilitySystemComponent` (`ASC`) on the `PlayerState` class for player/AI controlled heroes and the `ASC` on the `Character` class for AI controlled minions.

The goal is to keep this project simple while showing the GAS basics and demonstrating some commonly requested abilities with well-commented code. Because of its beginner focus, the project does not show advanced topics like [predicting projectiles](#concepts-p-spawn).

Concepts demonstrated:
* `ASC` on `PlayerState` vs `Character`
* Replicated `Attributes`
* Replicated animation montages
* `GameplayTags`
* Applying and removing `GameplayEffects` inside of and externally from `GameplayAbilities`
* Applying damage mitigated by armor to change health of a character
* `GameplayEffectExecutionCalculations`
* Stun effect
* Death and respawn
* Spawning actors (projectiles) from an ability on the server
* Predictively changing the local player's speed with aim down sights and sprinting
* Constantly draining stamina to sprint
* Using mana to cast abilities
* Passive abilities
* Stacking `GameplayEffects`
* Targeting actors
* `GameplayAbilities` created in Blueprint
* `GameplayAbilities` created in C++
* Instanced per `Actor` `GameplayAbilities`
* Non-Instanced `GameplayAbilities` (Jump)
* Static `GameplayCues` (FireGun projectile impact particle effect)
* Actor `GameplayCues` (Sprint and Stun particle effects)

The hero class has the following abilities:

| Ability              | Input Bind         | Predicted | C++ / Blueprint | Description                                                                                                                                              |
| -------------------- | ------------------ | --------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                 | Space Bar          | Yes       | C++             | Makes the hero jump.                                                                                                                                     |
| Gun                  | Left Mouse Button  | No        | C++             | Fires a projectile from the hero's gun. The animation is predicted but the projectile is not.                                                            |
| Aim Down Sights      | Right Mouse Button | Yes       | Blueprint       | While the button is held, the hero will walk slower and the camera will zoom in to allow more precise shots with the gun.                                |
| Sprint               | Left Shift         | Yes       | Blueprint       | While the button is held, the hero will run faster draining stamina.                                                                                     |
| Forward Dash         | Q                  | Yes       | Blueprint       | The hero dashes forward at the cost of stamina.                                                                                                          |
| Passive Armor Stacks | Passive            | No        | Blueprint       | Every 4 seconds the hero gains a stack of armor up to a maximum of 4 stacks. Receiving damage removes one stack of armor.                                |
| Meteor               | R                  | No        | Blueprint       | Player targets a location to drop a meteor on the enemies causing damage and stunning them. The targeting is predicted while spawning the meteor is not. |

It does not matter if `GameplayAbilities` are created in C++ or Blueprint. A mixture of the two were used here for example of how to do them in each language.

Minions do not come with any predefined `GameplayAbilities`. The Red Minions have more health regen while the Blue Minions have higher starting health.

For `GameplayAbility` naming, I used the suffix `_BP` to denote the `GameplayAbility's` logic was created in Blueprint. The lack of suffix means the logic was created in C++.

**Blueprint Asset Naming Prefixes**

| Prefix | Asset Type      |
| ------ | --------------- |
| GA_    | GameplayAbility |
| GC_    | GameplayCue     |
| GE_    | GameplayEffect  |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. Setting Up a Project Using GAS
Basic steps to set up a project using GAS:


1. Enable GameplayAbilitySystem plugin in the Editor
1. Edit `YourProjectName.Build.cs` to add `"GameplayAbilities", "GameplayTags", "GameplayTasks"` to your `PrivateDependencyModuleNames`
1. Refresh/Regenerate your Visual Studio project files
1. Starting with 4.24, it is now mandatory to call `UAbilitySystemGlobals::Get().InitGlobalData()` to use [`TargetData`](#concepts-targeting-data). The Sample Project does this in `UAssetManager::StartInitialLoading()`. See [`InitGlobalData()`](#concepts-asg-initglobaldata) for more information.

That's all that you have to do to enable GAS. From here, add an [`ASC`](#concepts-asc) and [`AttributeSet`](#concepts-as) to your `Character` or `PlayerState` and start making [`GameplayAbilities`](#concepts-ga) and [`GameplayEffects`](#concepts-ge)!

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
The `AbilitySystemComponent` (`ASC`) is the heart of GAS. It's a `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)) that handles all interactions with the system. Any `Actor` that wishes to use [`GameplayAbilities`](#concepts-ga), have [`Attributes`](#concepts-a), or receive [`GameplayEffects`](#concepts-ge) must have one `ASC` attached to them. These objects all live inside of and are managed and replicated by (with the exception of `Attributes` which are replicated by their [`AttributeSet`](#concepts-as)) the `ASC`. Developers are expected but not required to subclass this.

The `Actor` with the `ASC` attached to it is referred to as the `OwnerActor` of the `ASC`. The physical representation `Actor` of the `ASC` is called the `AvatarActor`. The `OwnerActor` and the `AvatarActor` can be the same `Actor` as in the case of a simple AI minion in a MOBA game. They can also be different `Actors` as in the case of a player controlled hero in a MOBA game where the `OwnerActor` is the `PlayerState` and the `AvatarActor` is the hero's `Character` class. Most `Actors` will have the `ASC` on themselves. If your `Actor` will respawn and need persistence of `Attributes` or `GameplayEffects` between spawns (like a hero in a MOBA), then the ideal location for the `ASC` is on the `PlayerState`.

**Note:** If your `ASC` is on your `PlayerState`, then you will need to increase the `NetUpdateFrequency` of your `PlayerState`. It defaults to a very low value on the `PlayerState` and can cause delays or perceived lag before changes to things like `Attributes` and `GameplayTags` happen on the clients. Be sure to enable [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency), Fortnite uses it.

Both, the `OwnerActor` and the `AvatarActor` if different `Actors`, should implement the `IAbilitySystemInterface`. This interface has one function that must be overriden, `UAbilitySystemComponent* GetAbilitySystemComponent() const`, which returns a pointer to its `ASC`. `ASCs` interact with each other internally to the system by looking for this interface function.

The `ASC` holds its current active `GameplayEffects` in `FActiveGameplayEffectsContainer ActiveGameplayEffects`.

The `ASC` holds its granted `Gameplay Abilities` in `FGameplayAbilitySpecContainer ActivatableAbilities`. Any time that you plan to iterate over `ActivatableAbilities.Items`, be sure to add `ABILITYLIST_SCOPE_LOCK();` above your loop to lock the list from changing (due to removing an ability). Every `ABILITYLIST_SCOPE_LOCK();` in scope increments `AbilityScopeLockCount` and then decrements when it falls out of scope. Do not try to remove an ability inside the scope of `ABILITYLIST_SCOPE_LOCK();` (the clear ability functions check `AbilityScopeLockCount` internally to prevent removing abilities if the list is locked).

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode
The `ASC` defines three different replication modes for replicating `GameplayEffects`, `GameplayTags`, and `GameplayCues` - `Full`, `Mixed`, and `Minimal`. `Attributes` are replicated by their `AttributeSet`.

| Replication Mode | When to Use                             | Description                                                                                                                    |
| ---------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`           | Single Player                           | Every `GameplayEffect` is replicated to every client.                                                                          |
| `Mixed`          | Multiplayer, player controlled `Actors` | `GameplayEffects` are only replicated to the owning client. Only `GameplayTags` and `GameplayCues` are replicated to everyone. |
| `Minimal`        | Multiplayer, AI controlled `Actors`     | `GameplayEffects` are never replicated to anyone. Only `GameplayTags` and `GameplayCues` are replicated to everyone.           |

**Note:** `Mixed` replication mode expects the `OwnerActor's` `Owner` to be the `Controller`. `PlayerState's` `Owner` is the `Controller` by default but `Character's` is not. If using `Mixed` replication mode with the `OwnerActor` not the `PlayerState`, then you need to call `SetOwner()` on the `OwnerActor` with a valid `Controller`.

Starting with 4.24, `PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 Setup and Initialization
`ASCs` are typically constructed in the `OwnerActor's` constructor and explicitly marked replicated. **This must be done in C++**.

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

The `ASC` needs to be initialized with its `OwnerActor` and `AvatarActor` on both the server and the client. You want to initialize after the `Pawn's` `Controller` has been set (after possession). Single player games only need to worry about the server path.

For player controlled characters where the `ASC` lives on the `Pawn`, I typically initialize on the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `PlayerController's` `AcknowledgePossession()` function.

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

For player controlled characters where the `ASC` lives on the `PlayerState`, I typically initialize the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `Pawn's` `OnRep_PlayerState()` function. This ensures that the `PlayerState` exists on the client.

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

If you get the error message `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` then you did not initialize your `ASC` on the client.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) are hierarchical names in the form of `Parent.Child.Grandchild...` that are registered with the `GameplayTagManager`. These tags are incredibly useful for classifying and describing the state of an object. For example, if a character is stunned, we could give it a `State.Debuff.Stun` `GameplayTag` for the duration of the stun.

You will find yourself replacing things that you used to handle with booleans or enums with `GameplayTags` and doing boolean logic on whether or not objects have certain `GameplayTags`.

When giving tags to an object, we typically add them to its `ASC` if it has one so that GAS can interact with them. `UAbilitySystemComponent` implements the `IGameplayTagAssetInterface` giving functions to access its owned `GameplayTags`.

Multiple `GameplayTags` can be stored in an `FGameplayTagContainer`. It is preferable to use a `GameplayTagContainer` over a `TArray<FGameplayTag>` since the `GameplayTagContainers` add some efficiency magic. While tags are standard `FNames`, they can be efficiently packed together in `FGameplayTagContainers` for replication if `Fast Replication` is enabled in the project settings. `Fast Replication` requires that the server and the clients have the same list of `GameplayTags`. This generally shouldn't be a problem so you should enable this option. `GameplayTagContainers` can also return a `TArray<FGameplayTag>` for iteration.

`GameplayTags` stored in `FGameplayTagCountContainer` have a `TagMap` that stores the number of instances of that `GameplayTag`. A `FGameplayTagCountContainer` may still have the `GameplayTag` in it but its `TagMapCount` is zero. You may encounter this while debugging if an `ASC` still has a `GameplayTag`. Any of the `HasTag()` or `HasMatchingTag()` or similar functions will check the `TagMapCount` and return false if the `GameplayTag` is not present or its `TagMapCount` is zero.

`GameplayTags` must be defined ahead of time in the `DefaultGameplayTags.ini`. The UE5 Editor provides an interface in the project settings to let developers manage `GameplayTags` without needing to manually edit the `DefaultGameplayTags.ini`. The `GameplayTag` editor can create, rename, search for references, and delete `GameplayTags`.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

Searching for `GameplayTag` references will bring up the familiar `Reference Viewer` graph in the Editor showing all the assets that reference the `GameplayTag`. This will not however show any C++ classes that reference the `GameplayTag`.

Renaming `GameplayTags` creates a redirect so that assets still referencing the original `GameplayTag` can redirect to the new `GameplayTag`. I prefer if possible to instead create a new `GameplayTag`, update all the references manually to the new `GameplayTag`, and then delete the old `GameplayTag` to avoid creating a redirect.

In addition to `Fast Replication`, the `GameplayTag` editor has an option to fill in commonly replicated `GameplayTags` to optimize them further.

`GameplayTags` are replicated if they're added from a `GameplayEffect`. The `ASC` allows you to add `LooseGameplayTags` that are not replicated and must be managed manually. The Sample Project uses a `LooseGameplayTag` for `State.Dead` so that the owning clients can immediately respond to when their health drops to zero. Respawning manually sets the `TagMapCount` back to zero. Only manually adjust the `TagMapCount` when working with `LooseGameplayTags`. It is preferable to use the `UAbilitySystemComponent::AddLooseGameplayTag()` and `UAbilitySystemComponent::RemoveLooseGameplayTag()` functions than manually adjusting the `TagMapCount`.

Getting a reference to a `GameplayTag` in C++:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

For advanced `GameplayTag` manipulation like getting the parent or children `GameplayTags`, look at the functions offered by the `GameplayTagManager`. To access the `GameplayTagManager`, include `GameplayTagManager.h` and call it with `UGameplayTagManager::Get().FunctionName`. The `GameplayTagManager` actually stores the `GameplayTags` as relational nodes (parent, child, etc) for faster processing than constant string manipulation and comparisons.

`GameplayTags` and `GameplayTagContainers` can have the optional `UPROPERTY` specifier `Meta = (Categories = "GameplayCue")` that filters the tags in the Blueprint to show only `GameplayTags` that have the parent tag of `GameplayCue`. This is useful when you know the `GameplayTag` or `GameplayTagContainer` variable should only be used for `GameplayCues`.

Alternatively, there's a separate structure called `FGameplayCueTag` that encapsulates a `FGameplayTag` and also automatically filters `GameplayTags` in Blueprint to only show those tags with the parent tag of `GameplayCue`.

If you want to filter a `GameplayTag` parameter in a function, use the `UFUNCTION` specifier `Meta = (GameplayTagFilter = "GameplayCue")`. `GameplayTagContainer` parameters in functions can not be filtered. If you would like to edit your engine to allow this, look at how `SGameplayTagGraphPin::ParseDefaultValueData()` from `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` calls `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` and passes `FilterString` to `SGameplayTagWidget` in `SGameplayTagGraphPin::GetListContent()`. The `GameplayTagContainer` version of these functions in `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` do not check for the meta field properties and pass along the filter.

The Sample Project extensively uses `GameplayTags`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 Responding to Changes in Gameplay Tags
The `ASC` provides a delegate for when `GameplayTags` are added or removed. It takes in a `EGameplayTagEventType` that can specify only to fire when the `GameplayTag` is added/removed or for any change in the `GameplayTag's` `TagMapCount`.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

The callback function has a parameter for the `GameplayTag` and the new `TagCount`.
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute Definition
`Attributes` are float values defined by the struct [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html). These can represent anything from the amount of health a character has to the character's level to the number of charges that a potion has. If it is a gameplay-related numerical value belonging to an `Actor`, you should consider using an `Attribute` for it. `Attributes` should generally only be modified by [`GameplayEffects`](#concepts-ge) so that the ASC can [predict](#concepts-p) the changes.

`Attributes` are defined by and live in an [`AttributeSet`](#concepts-as). The `AttributeSet` is responsible for replicating `Attributes` that are marked for replication. See the section on [`AttributeSets`](#concepts-as) for how to define `Attributes`.

**Tip:** If you don't want an `Attribute` to show up in the Editor's list of `Attributes`, you can use the `Meta = (HideInDetailsView)` `property specifier`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
An `Attribute` is composed of two values - a `BaseValue` and a `CurrentValue`. The `BaseValue` is the permanent value of the `Attribute` whereas the `CurrentValue` is the `BaseValue` plus temporary modifications from `GameplayEffects`. For example, your `Character` may have a movespeed `Attribute` with a `BaseValue` of 600 units/second. Since there are no `GameplayEffects` modifying the movespeed yet, the `CurrentValue` is also 600 u/s. If she gets a temporary 50 u/s movespeed buff, the `BaseValue` stays the same at 600 u/s while the `CurrentValue` is now 600 + 50 for a total of 650 u/s. When the movespeed buff expires, the `CurrentValue` reverts back to the `BaseValue` of 600 u/s.

Often beginners to GAS will confuse `BaseValue` with a maximum value for an `Attribute` and try to treat it as such. This is an incorrect approach. Maximum values for `Attributes` that can change or are referenced in abilities or UI should be treated as separate `Attributes`. For hardcoded maximum and minimum values, there is a way to define a `DataTable` with `FAttributeMetaData` that can set maximum and minimum values, but Epic's comment above the struct calls it a "work in progress". See `AttributeSet.h` for more information. To prevent confusion, I recommend that maximum values that can be referenced in abilities or UI be made as separate `Attributes` and hardcoded maximum and minimum values that are only used for clamping `Attributes` be defined as hardcoded floats in the `AttributeSet`. Clamping of `Attributes` is discussed in [PreAttributeChange()](#concepts-as-preattributechange) for changes to the `CurrentValue` and [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) for changes to the `BaseValue` from `GameplayEffects`.

Permanent changes to the `BaseValue` come from `Instant` `GameplayEffects` whereas `Duration` and `Infinite` `GameplayEffects` change the `CurrentValue`. Periodic `GameplayEffects` are treated like instant `GameplayEffects` and change the `BaseValue`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
Some `Attributes` are treated as placeholders for temporary values that are intended to interact with `Attributes`. These are called `Meta Attributes`. For example, we commonly define damage as a `Meta Attribute`. Instead of a `GameplayEffect` directly changing our health `Attribute`, we use a `Meta Attribute` called damage as a placeholder. This way the damage value can be modified with buffs and debuffs in an [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) and can be further manipulated in the `AttributeSet`, for example subtracting the damage from a current shield `Attribute`, before finally subtracting the remainder from the health `Attribute`. The damage `Meta Attribute` has no persistence between `GameplayEffects` and is overriden by every one. `Meta Attributes` are not typically replicated.

`Meta Attributes` provide a good logical separation for things like damage and healing between "How much damage did we do?" and "What do we do with this damage?". This logical separation means our `Gameplay Effects` and `Execution Calculations` don't need to know how the Target handles the damage. Continuing our damage example, the `Gameplay Effect` determines how much damage and then the `AttributeSet` decides what to do with that damage. Not all characters may have the same `Attributes`, especially if you use subclassed `AttributeSets`. The base `AttributeSet` class may only have a health `Attribute`, but a subclassed `AttributeSet` may add a shield `Attribute`. The subclassed `AttributeSet` with the shield `Attribute` would distribute the damage received differently than the base `AttributeSet` class.

While `Meta Attributes` are a good design pattern, they are not mandatory. If you only ever have one `Execution Calculation` used for all instances of damage and one `Attribute Set` class shared by all characters, then you may be fine doing the damage distribution to health, shields, etc. inside of the `Execution Calculation` and directly modifying those `Attributes`. You'll only be sacrificing flexibility, but that may be okay for you.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Responding to Attribute Changes
To listen for when an `Attribute` changes to update the UI or other gameplay, use `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`. This function returns a delegate that you can bind to that will be automatically called whenever an `Attribute` changes. The delegate provides a `FOnAttributeChangeData` parameter with the `NewValue`, `OldValue`, and `FGameplayEffectModCallbackData`. **Note:** The `FGameplayEffectModCallbackData` will only be set on the server.

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

The Sample Project binds to the `Attribute` value changed delegates on the `GDPlayerState` to update the HUD and to respond to player death when health reaches zero.

A custom Blueprint node that wraps this into an `ASyncTask` is included in the Sample Project. It is used in the `UI_HUD` UMG Widget to update the health, mana, and stamina values. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See `AsyncTaskAttributeChanged.h/cpp`.

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes
To make an `Attribute` that has some or all of its value derived from one or more other `Attributes`, use an `Infinite` `GameplayEffect` with one or more `Attribute Based` or [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods). The `Derived Attribute` will update automatically when an `Attribute` that it depends on is updated.

The final formula for all the `Modifiers` on a `Derived Attribute` is the same formula for `Modifier Aggregators`. If you need calculations to happen in a certain order, do it all inside of an `MMC`.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note:** If playing with multiple clients in PIE, you need to disable `Run Under One Process` in the Editor Preferences otherwise the `Derived Attributes` will not update when their independent `Attributes` update on clients other than the first.

In this example, we have an `Infinite` `GameplayEffect` that derives the value of `TestAttrA` from the `Attributes`, `TestAttrB` and `TestAttrC`, in the formula `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`. `TestAttrA` recalculates its value automatically whenever any of the `Attributes` update their values.

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set Definition
The `AttributeSet` defines, holds, and manages changes to `Attributes`. Developers should subclass from [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html). Creating an `AttributeSet` in an `OwnerActor's` constructor automatically registers it with its `ASC`. **This must be done in C++**.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set Design
An `ASC` may have one or many `AttributeSets`. AttributeSets have negligible memory overhead so how many `AttributeSets` to use is an organizational decision left up to the developer.

It is acceptable to have one large monolithic `AttributeSet` shared by every `Actor` in your game and only use attributes if needed while ignoring unused attributes.

Alternatively, you may choose to have more than one `AttributeSet` representing groupings of `Attributes` that you selectively add to your `Actors` as needed. For example, you could have an `AttributeSet` for health related `Attributes`, an `AttributeSet` for mana related `Attributes`, and so on. In a MOBA game, heroes might need mana but minions might not. Therefore the heroes would get the mana `AttributeSet` and minions would not.

Additionally, `AttributeSets` can be subclassed as another means of selectively choosing which `Attributes` an `Actor` has. `Attributes` are internally referred to as `AttributeSetClassName.AttributeName`. When you subclass an `AttributeSet`, all of the `Attributes` from the parent class will still have the parent class's name as the prefix.

While you can have more than one `AttributeSet`, you should not have more than one `AttributeSet` of the same class on an `ASC`. If you have more than one `AttributeSet` from the same class, it won't know which `AttributeSet` to use and will just pick one.

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 Subcomponents with Individual Attributes
In the scenario where you have multiple damageable components on a `Pawn` like individually damageable armor pieces, I recommend that if you know the maximum number of damageable components that a `Pawn` could have to make that many health `Attributes` on one `AttributeSet` - DamageableCompHealth0, DamageableCompHealth1, etc. to represent logical 'slots' for those damageable components. In your damageable component class instance, assign the slot number `Attribute` that can be read by `GameplayAbilities` or [`Executions`](#concepts-ge-ec) to know which `Attribute` to apply damage to. `Pawns` that have less than the maximum number or zero of damageable components are fine. Just because a `AttributeSet` has an `Attribute`, doesn't mean that you have to use it. Unused `Attributes` take up trivial amount of memory.

If your subcomponents need many `Attributes` each, there's potentially an unbounded number of subcomponents, the subcomponents can detach and be used by other players (e.g. weapons), or for any other reason this approach doesn't work for you, I'd recommend switching away from `Attributes` and instead store plain old floats on the components. See [Item Attributes](#concepts-as-design-itemattributes).

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 Adding and Removing AttributeSets at Runtime
`AttributeSets` can be added and removed from an `ASC` at runtime; however, removing `AttributeSets` can be dangerous. For example, if an `AttributeSet` is removed on a client before the server and an `Attribute` value change is replicated to client, the `Attribute` won't find its `AttributeSet` and crash the game.

On weapon add to inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

On weapon remove from inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attributes (Weapon Ammo)
There's a few ways to implement equippable items with `Attributes` (weapon ammo, armor durability, etc). All of these approaches store values directly on the item. This is necessary for items that can be equipped by more than one player over its lifetime.

> 1. Use plain floats on the item (**Recommended**)
> 1. Separate `AttributeSet` on the item
> 1. Separate `ASC` on the item

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 Plain Floats on the Item
Instead of `Attributes`, store plain float values on the item class instance. Fortnite and [GASShooter](https://github.com/tranek/GASShooter) handle gun ammo this way. For a gun, store the max clip size, current ammo in clip, reserve ammo, etc directly as replicated floats (`COND_OwnerOnly`) on the gun instance. If weapons share reserve ammo, you would move the reserve ammo onto the character as an `Attribute` in a shared ammo `AttributeSet` (reload abilities can use a `Cost GE` to pull from reserve ammo into the gun's float clip ammo). Since you're not using `Attributes` for current clip ammo, you will need to override some functions in `UGameplayAbility` to check and apply cost against the floats on the gun. Making the gun the `SourceObject` in the [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) when granting the ability means you'll have access to the gun that granted the ability inside the ability.

To prevent the gun from replicating back the ammo amount and clobbering the local ammo amount during automatic fire, disable replication while the player has a `IsFiring` `GameplayTag` in `PreReplication()`. You're essentially doing your own local prediction here.

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

Benefits:
1. Avoids limitations of using `AttributeSets` (see below)

Limitations:
1. Can not use existing `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Requires work to override key functions on `UGameplayAbility` to check and apply ammo costs against the gun's floats

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 `AttributeSet` on the Item
Using a separate `AttributeSet` on the item that gets [added to the player's `ASC` on adding it to the player's inventory](#concepts-as-design-addremoveruntime) can work, but it has some major limitations. I had this working in early versions of [GASShooter](https://github.com/tranek/GASShooter) for the weapon ammo. The weapon stores its `Attributes` such as max clip size, current ammo in clip, reserve ammo, etc in an `AttributeSet` that lives on the weapon class. If weapons share reserve ammo, you would move the reserve ammo onto the character in a shared ammo `AttributeSet`. When a weapon is added to the player's inventory on the server, the weapon would add its `AttributeSet` to the player's `ASC::SpawnedAttributes`. The server would then replicate this down to the client. If the weapon is removed from the inventory, it would remove its `AttributeSet` from the `ASC::SpawnedAttributes`.

When the `AttributeSet` lives on something other than the `OwnerActor` (say a weapon), you'll initially get some compilation errors in the `AttributeSet`. The fix is to construct the `AttributeSet` in `BeginPlay()` instead of in the constructor and to implement `IAbilitySystemInterface` (set the pointer to the `ASC` when you add the weapon to the player inventory) on the weapon.

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

You can see it in practice by checking out this [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Simple to setup for a very small set of items

Limitations:
1. You have to make a new `AttributeSet` class for every weapon type. `ASCs` can only functionally have one `AttributeSet` instance of a class since changes to an `Attribute` look for the first instance of their `AttributeSet` class in the `ASCs` `SpawnedAttributes` array. Additional instances of the same `AttributeSet` class are ignored.
1. You can only have one of each type of weapon in the player's inventory due to previous reason of one `AttributeSet` instance per `AttributeSet` class.
1. Removing an `AttributeSet` is dangerous. In GASShooter if the player killed himself from a rocket, the player would immediately remove the rocket launcher from his inventory (including its `AttributeSet` from the `ASC`). When the server replicated that the rocket launcher's ammo `Attribute` changed, the `AttributeSet` no longer existed on the client's `ASC` and the game crashed.

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
Putting a whole `AbilitySystemComponent` on each item is an extreme approach. I have not personally done this nor have I seen it in the wild. It would take a lot of engineering to make it work.

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?
> 
> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will have multiple ASCs beneath them.
> 
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags that describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (e.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.

*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Can reuse `AttributeSet` classes (one on each weapon's ASC)

Limitations:
1. Unknown engineering cost
1. Is it even possible?

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Defining Attributes
**`Attributes` can only be defined in C++** in the `AttributeSet's` header file. It is recommended to add this block of macros to the top of every `AttributeSet` header file. It will automatically generate getter and setter functions for your `Attributes`.

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

A replicated health attribute would be defined like this:

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

Also define the `OnRep` function in the header:
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

The .cpp file for the `AttributeSet` should fill in the `OnRep` function with the `GAMEPLAYATTRIBUTE_REPNOTIFY` macro used by the prediction system:
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

Finally, the `Attribute` needs to be added to `GetLifetimeReplicatedProps`:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` tells the `OnRep` function to trigger if the local value is already equal to the value being repped down from the Server (due to prediction). By default it won't trigger the `OnRep` function if the local value is the same as the value being repped down from the Server.

If the `Attribute` is not replicated like a `Meta Attribute`, then the `OnRep` and `GetLifetimeReplicatedProps` steps can be skipped.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Initializing Attributes
There are multiple ways to initialize `Attributes` (set their `BaseValue` and consequently their `CurrentValue` to some initial value). Epic recommends using an instant `GameplayEffect`. This is the method used in the Sample Project too.

See `GE_HeroAttributes` Blueprint in the Sample Project for how to make an instant `GameplayEffect` to initialize `Attributes`. Application of this `GameplayEffect` happens in C++.

If you used the `ATTRIBUTE_ACCESSORS` macro when you defined your `Attributes`, an initialization function will automatically be generated on the `AttributeSet` for each `Attribute` that you can call at your leisure in C++.

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

See `AttributeSet.h` for more ways to initialize `Attributes`.

**Note:** Prior to 4.24, `FAttributeSetInitterDiscreteLevels` did not work with `FGameplayAttributeData`. It was created when `Attributes` were raw floats and will complain about `FGameplayAttributeData` not being `Plain Old Data` (`POD`). This is fixed in 4.24 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` is one of the main functions in the `AttributeSet` to respond to changes to an `Attribute's` `CurrentValue` before the change happens. It is the ideal place to clamp incoming changes to `CurrentValue` via the reference parameter `NewValue`.

For example to clamp movespeed modifiers the Sample Project does it like so:
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
The `GetMoveSpeedAttribute()` function is created by the macro block that we added to the `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes)).

This is triggered from any changes to `Attributes`, whether using `Attribute` setters (defined by the macro block in `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))) or using [`GameplayEffects`](#concepts-ge).

**Note:** Any clamping that happens here does not permanently change the modifier on the `ASC`. It only changes the value returned from querying the modifier. This means anything that recalculates the `CurrentValue` from all of the modifiers like [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) and [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) need to implement clamping again.

**Note:** Epic's comments for `PreAttributeChange()` say not to use it for gameplay events and instead use it mainly for clamping. The recommended place for gameplay events on `Attribute` change is `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` ([Responding to Attribute Changes](#concepts-a-changes)).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` only triggers after changes to the `BaseValue` of an `Attribute` from an instant [`GameplayEffect`](#concepts-ge). This is a valid place to do more `Attribute` manipulation when they change from a `GameplayEffect`.

For example, in the Sample Project we subtract the final damage `Meta Attribute` from the health `Attribute` here. If there was a shield `Attribute`, we would subtract the damage from it first before subtracting the remainder from health. The Sample Project also uses this location to apply hit react animations, show floating Damage Numbers, and assign experience and gold bounties to the killer. By design, the damage `Meta Attribute` will always come through an instant `GameplayEffect` and never the `Attribute` setter.

Other `Attributes` that will only have their `BaseValue` changed from instant `GameplayEffects` like mana and stamina can also be clamped to their maximum value counterpart `Attributes` here.

**Note:** When `PostGameplayEffectExecute()` is called, changes to the `Attribute` have already happened, but they have not replicated back to clients yet so clamping values here will not cause two network updates to clients. Clients will only receive the update after clamping.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` triggers when an `Aggregator` is created for an `Attribute` in this set. It allows custom setup of [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html). `AggregatorEvaluateMetaData` is used by the `Aggregator` in evaluating the `CurrentValue` of an `Attribute` based on all the [`Modifiers`](#concepts-ge-mods) applied to it. By default, `AggregatorEvaluateMetaData` is only used by the `Aggregator` to determine which `Modifiers` qualify with the example of `MostNegativeMod_AllPositiveMods` which allows all positive `Modifiers` but restricts negative `Modifiers` to only the most negative one. This was used by Paragon to only allow the most negative move speed slow effect to apply to a player regardless of how many slow effects where on them at any one time while applying all positive move speed buffs. `Modifiers` that don't qualify still exist on the `ASC`, they just aren't aggregated into the final `CurrentValue`. They can potentially qualify later once conditions change, like in the case if the most negative `Modifier` expires, the next most negative `Modifier` (if one exists) then qualifies.

To use AggregatorEvaluateMetaData in the example of only allowing the most negative `Modifier` and all positive `Modifiers`:

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

Your custom `AggregatorEvaluateMetaData` for qualifiers should be added to `FAggregatorEvaluateMetaDataLibrary` as static variables.

**[⬆ Back to Top](#table-of-contents)**

