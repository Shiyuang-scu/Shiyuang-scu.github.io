---
title: 【增强学习】AirSim搭建
date: 2018-06-03 00:44:25
tags: 世界之内
---
![record screenshot](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/AirSimDroneManual.gif)

先承接上文，双特生面试很惨淡，老师们一直让我说我对数据科学的理解、我在数据科学方面有何突出。双特生选拔的是川大金字塔尖的人才，我本就是打肿脸充胖子，不过在候考期间认识了易佬，总体而言，收获很大。

最近加入了空天学院飞行模拟协会，本打算做基于增强学习的飞行器自动对战。因为微软的AirSim与我们的主题很契合，我们便打算将AirSim+Unreal作为实验平台。近期老师计划让我研究AirSim的接口，以便把P3D模型移植到Unreal中。至于为什么要用P3D作为模型，事情就很复杂了，在此不赘述。原本的初衷是在编程中学习机器学习，却发现大部分时间做的都是和机器学习无关的偏向于工程的事情，每天在自己完全不熟悉的地方疲于奔命，还是让人不舒服。但话说回来，我的工程能力很差，这次项目也一定会增强我的代码能力。其次，如果真如老师所说，只要将自动对战做出来就能发SCI，那我虽然撑死算是二作，也死而无憾了。
<!--more-->
今天搭建AirSim花了很多功夫，起先是看CSDN的搭建教程，安装了很多无用的包和库，而之后却总是遇到编译失败、无法找到Module文件等等错误，学长如数家珍地讲各种错误，真是令人心疼的熟练度。后来回归到AirSim的官方文档，才安安稳稳地搭建好了环境。他们加上我花了3、4天的时间来搭建一个小小的AirSim，算是令人啼笑皆非了。事成之后回想搭建过程，不禁另感慨我的学习能力和搜索能力实在过于低下，如果是其他人，相信1小时内绝对没问题。

言归正传，我们被CSDN的教程坑坏了，不想让别人也被坑了，下面是亲测有效的搭建流程，实际上是微软官方的Tutorial，能在我们头发寸断的情况下出马，令我这个渣感激涕零。

# Build AirSim on Windows

## Install Unreal Engine

1. [Download](https://www.unrealengine.com/download) the Epic Games Launcher. While the Unreal Engine is open source and free to download, registration is still required.

2. Run the Epic Games Launcher, open the Library tab from left, click on the "Add Versions" which should show the option to download Unreal 4.18 as shown below. If you have multiple versions of Unreal installed then make sure 4.18 is "Current" by clicking down arrow next to the Launch button for the version.

   **Note**: If you have UE 4.16 or older projects, please see the [upgrade guide](https://github.com/Microsoft/AirSim/blob/master/docs/unreal_upgrade.md) to upgrade your projects.

   ![Unreal Versions](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/unreal_versions.png)

## Build AirSim

  1. You will need Visual Studio 2017 (make sure to install VC++) or newer.
  2. Start `x64 Native Tools Command Prompt for VS 2017`. Create a folder for the repo and run `git clone https://github.com/Microsoft/AirSim.git`.
  3. Run `build.cmd` from the command line. This will create ready to use plugin bits in the `Unreal\Plugins` folder that can be dropped into any Unreal project.

## Setup Remote Control

Because We want to make program to control the vehicle, we didn't setup the remote control.
So if you want to fly manually. See the [remote control setup](https://github.com/Microsoft/AirSim/blob/master/docs/remote_control.md) for more details.

We use [APIs](https://github.com/Microsoft/AirSim/blob/master/docs/apis.md) for programmatic control or use the so-called [Computer Vision mode](https://github.com/Microsoft/AirSim/blob/master/docs/image_apis.md) to move around using the keyboard.

## Setup Unreal Environment

Finally, you will need an Unreal project that hosts the environment for your vehicles. AirSim comes with a built-in "Blocks Environment" which you can use, or you can create your own. We met a lot of problems in this section.

### Option 1: Built-in Blocks Environment
To get up and running fast, you can use the Blocks project that already comes with AirSim. This is not very highly detailed environment to keep the repo size reasonable but we use it for various testing all the times and it is the easiest way to get your feet wet in this strange land. 
Blocks environment is available in repo in folder `Unreal/Environments/Blocks` and is designed to be lightweight in size. That means its very basic but fast.

Here are quick steps to get Blocks environment up and running:

#### Windows

1. Make sure you have [installed Unreal and built AirSim](https://github.com/Microsoft/AirSim/blob/master/docs/build_windows.md).
2. Navigate to folder `AirSim\Unreal\Environments\Blocks` and run `update_from_git.bat`.
3. Double click on generated .sln file to open in Visual Studio 2017 or newer.
4. Make sure `Blocks` project is the startup project, build configuration is set to `DebugGame_Editor` and `Win64`. Hit F5 to run.
5. Press the Play button in Unreal Editor and you will see something like in below video. Also see [how to use AirSim](https://github.com/Microsoft/AirSim/#how-to-use-it).

##### Changing Code and Rebuilding
For Windows, you can just change the code in Visual Studio, press F5 and re-run. There are few batch files available in folder `AirSim\Unreal\Environments\Blocks` that lets you sync code, clean etc.

#### Linux
1. Make sure you have [built the Unreal Engine and AirSim](https://github.com/Microsoft/AirSim/blob/master/docs/build_linux.md).
2. Navigate to your UnrealEngine repo folder and run `Engine/Binaries/Linux/UE4Editor` which will start Unreal Editor.
3. On first start you might not see any projects in UE4 editor. Click on Projects tab, Browse button and then navigate to `AirSim/Unreal/Environments/Blocks/Blocks.uproject`. 
4. If you get prompted for incompatible version and conversion, select In-place conversion which is usually under "More" options. If you get prompted for missing modules, make sure to select No so you don't exit. 
5. Finally, when prompted with building AirSim, select Yes. Now it might take a while so go get some coffee :).
6. Press the Play button in Unreal Editor and you will see something like in below video. Also see [how to use AirSim](https://github.com/Microsoft/AirSim/blob/master/#how-to-use-it).


##### Changing Code and Rebuilding
For Linux, make code changes in AirLib or Unreal/Plugins folder and then run `./build.sh` to rebuild. This step also copies the build output to Blocks sample project. You can then follow above steps again to re-run.

#### Chosing Your Vehicle: Car or Multirotor
By default AirSim spawns multirotor. You can easily change this to car and use all of AirSim goodies. Please see [using car](https://github.com/Microsoft/AirSim/blob/master/docs/using_car.md) guide.



### Option 2: Create Your Own Unreal Environment
If you want to setup photo-realistic high quality environments, then you will need to create your own Unreal project. This is little bit more involved but worthwhile! 
The following contains the complete instructions start to finish for setting up Unreal environment with AirSim. The Unreal Marketplace has [several environment](https://www.unrealengine.com/marketplace/content-cat/assets/environments) available that you can start using in just few minutes. It is also possible to use environments available on websites such as [turbosquid.com](https://www.turbosquid.com/) or [cgitrader.com](https://www.cgtrader.com/) with bit more effort (here's [tutorial video](https://www.youtube.com/watch?v=y09VbdQWvQY&feature)). In addition there also several [free environments](https://github.com/Microsoft/AirSim/issues/424) available.

Below we will use a freely downloadable environment from Unreal Marketplace called Landscape Mountain but the steps are same for any other environments. You can also view these steps performed in [Unreal AirSim Setup Video](https://youtu.be/1oY8Qu5maQQ).

#### Note for Linux Users
There is no `Epic Games Launcher` for Linux which means that if you need to create custom environment, you will need Windows machine to do that. Once you have Unreal project folder, just copy it over to your Linux machine. 

#### Step by Step Instructions

1. Make sure AirSim is built and Unreal 4.18 is installed as described in [build instructions](https://github.com/Microsoft/AirSim/blob/master/docs/build_windows.md).
2. In `Epic Games Launcher` click the Learn tab then scroll down and find `Landscape Mountains`. Click the `Create Project` and download this content (~2GB download).

![current version](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/landscape_mountains.png)

3. Open `LandscapeMountains.uproject`, it should launch the Unreal Editor.

![unreal editor](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/unreal_editor.png)

4. From the `File menu` select `New C++ class`, leave default `None` on the type of class, click `Next`, leave default name `MyClass`, and click `Create Class`. We need to do this because Unreal requires at least one source file in project. It should trigger compile and open up Visual Studio solution `LandscapeMountains.sln`.

5. Go to your folder for AirSim repo and copy `Unreal\Plugins` folder in to your `LandscapeMountains` folder. This way now your own Unreal project has AirSim plugin.

6. Edit the `LandscapeMountains.uproject` so that it looks like this

```
{
	"FileVersion": 3,
	"EngineAssociation": "4.18",
	"Category": "Samples",
	"Description": "",
	"Modules": [
		{
			"Name": "LandscapeMountains",
			"Type": "Runtime",
			"LoadingPhase": "Default",
			"AdditionalDependencies": [
				"AirSim"
			]
		}
	],
	"TargetPlatforms": [
		"MacNoEditor",
		"WindowsNoEditor"
	],
	"Plugins": [
		{
			"Name": "AirSim",
			"Enabled": true
		}
	]
}
```

7. Close Visual Studio and the  `Unreal Editor` and right click the LandscapeMountains.uproject in Windows Explorer and select `Generate Visual Studio Project Files`.  This step detects all plugins and source files in your Unreal project and generates `.sln` file for Visual Studio.

![regen](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/regen_sln.png)

Tip: If the `Generate Visual Studio Project Files` option is missing you may need to reboot your machine for the Unreal Shell extensions to take effect.  If it is still missing then open the LandscapeMountains.uproject in the Unreal Editor and select `Refresh Visual Studio Project` from the `File` menu.

8. Reopen `LandscapeMountains.sln` in Visual Studio, and make sure "DebugGame Editor" and "Win64" build configuration is the active build configuration.

![build config](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/vsbuild_config.png)

9. Press `F5` to `run`. This will start the Unreal Editor. The Unreal Editor allows you to edit the environment, assets and other game related settings. First thing you want to do in your environment is set up `PlayerStart` object. In Landscape Mountains environment, `PlayerStart` object already exist and you can find it in the `World Outliner`. Make sure its location is setup as shown. This is where AirSim plugin will create and place the vehicle. If its too high up then vehicle will fall down as soon as you press play giving potentially random behaviour.

![lm_player_start_pos.png](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/lm_player_start_pos.png)

10. In `Window/World Settings` as shown below, set the `GameMode Override` to `AirSimGameMode`:

![sim_game_mode.png](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/sim_game_mode.png)

11. Be sure to `Save` these edits. Hit the Play button in the Unreal Editor. See [how to use AirSim](https://github.com/Microsoft/AirSim/#how-to-use-it).

Congratulations! You are now running AirSim in your own Unreal environment.

#### Chosing Your Vehicle: Car or Multirotor
By default AirSim spawns multirotor. You can easily change this to car and use all of AirSim goodies. Please see [using car](https://github.com/Microsoft/AirSim/blob/master/docs/using_car.md) guide.

#### Updating Your Environment to Latest Version of AirSim
Once you have your environment using above instructions, you should frequently update your local AirSim code to latest version from GitHub. Below are the instructions to do this:

1. First put [clean.bat](https://github.com/Microsoft/AirSim/blob/master/Unreal/Environments/Blocks/clean.bat) (or [clean.sh](https://github.com/Microsoft/AirSim/blob/master/Unreal/Environments/Blocks/clean.sh) for Linux users) in the root folder of your environment. Run this file to clean up all intermediate files in your Unreal project.
2. Do `git pull` in your AirSim repo followed by `build.cmd` (or `./build.sh` for Linux users).
3. Replace [your project]/Plugins folder with AirSim/Unreal/Plugins folder.
4. Right click on your .uproject file and chose "Generate Visual Studio project files" option. This is not required for Linux.

## Changing Code and Development Workflow
To see how you can change and test AirSim code, please read our [recommended development workflow](https://github.com/Microsoft/AirSim/blob/master/docs/dev_workflow.md).

OK, wish you a good day!