---
title: PlayFab Online Subsystem (OSS) Quickstart
description: Guidance on how to use PlayFab Party in your Unreal Engine Project.
author: PushpadantK
ms.author: pkacha
ms.date: 05/10/2022
ms.topic: article
ms.service: playfab
keywords: playfab, multiplayer, networking, unreal, unreal engine, unreal engine 4, unreal engine 5, middleware
---

# QuickStart: PlayFab Online Subsystem (OSS)

This quickstart guide helps you set up and use Multiplayer features such as Lobby, Matchmaking and Party for Xbox, PC, Nintendo Switch, PlayStation®5 and PlayStation®4 games built using Unreal Engine 4 or Unreal Engine 5. For the full list of supported platforms and versions in UE4 or UE5, see [Supported platforms](party-unreal-engine-oss-overview.md).

After following the relevant steps that are outlined in this page for your target platforms, you'll be ready to start using the OSS. Authentication, networking, VOIP, grouping into lobbies, and matchmaking is handled on your behalf with no other changes required.

## Download and install PlayFab Online Subsystem

Go to [PlayFab Online SubSystem](https://github.com/PlayFab/PlayFabMultiplayerUnreal) to download or clone PlayFab Online SubSystem source. The downloaded or cloned repository name is PlayFabMultiplayerUnreal. **The repository has to be renamed to OnlineSubsystemPlayFab**. 

## What you need

* **PlayFab Title ID:** If you don't have a Title ID configured for PlayFab Party, see [Enabling PlayFab Party](enable-party.md).

### GDK, PC, Switch, PlayStation®5 and PlayStation®4

* **Specific platform PlayFab Multiplayer and Party libraries:** These files are provided with the source in [Download and install PlayFab Online Subsystem](#download-and-install-playfab-online-subsystem). If you need updated Party library files, then see [Obtaining PlayFab Party libraries](party-unreal-engine-oss-obtaining-playfab-party-libraries.md).

## Initial setup

* Copy the **OnlineSubsystemPlayFab** folder and its contents from to your UE directory under **Engine\Plugins\Online**
* Apply the following changes to the Plugins section of your ".uproject" file to add the OnlineSubsystemPlayFab to your plugin list.
  * You may remove any platforms that you're not shipping on

```json
{
 "Name": "OnlineSubsystemPlayFab",
 "Enabled": true,
 "WhitelistPlatforms": [
  "XboxOneGDK",
  "WinGDK",
  "XSX",
  "Win64",
  "Switch",
  "PS4",
  "PS5"
 ],
 "SupportedTargetPlatforms": [
  "XboxOneGDK",
  "WinGDK",
  "XSX",
  "Win64",
  "Switch",
  "PS4",
  "PS5"
 ]
}
```

## Game Configuration

* No matter which platform you're targeting, your game needs to configure certain PlayFab specific values in your intended platform target's INI file (located at [yourGameDirectory]/Platforms/[yourPlatform]/Config).
  * **Xbox Series X GDK:** XSXEngine.ini
  * **PC GDK:** WinGDKEngine.ini
  * **Xbox One GDK:** XboxOneGDKEngine.ini
  * **PC Steam:** WindowsEngine.ini
  * **Nintendo Switch** SwitchEngine.ini
  * **PS4™** PS4Engine.ini
  * **PS5™** PS5Engine.ini
* Replace the INI sections in the config if they already exist (for example, Engine.GameEngine) with the ones presented in the following sections.
* Ensure you replace all the *\<REPLACE ME>* fields with your data:

```config
[OnlineSubsystemPlayFab]
bEnabled=true
PlayFabTitleID=<REPLACE ME with your PlayFab title ID>
MaxDeviceCount=<REPLACE ME with your max player count (note: split screen is still 1 device). In the example of an 8 player game, this would be 8.>
MaxDevicesPerUserCount=<REPLACE ME with your max player count per box (note: split screen is still 1 device) In the example of an 8 player game, this would be 1.>
MaxEndpointsPerDeviceCount=<REPLACE ME with your max player count per box (note: split screen is still 1 device)  In the example of an 8 player game, this would be 1.>
MaxUserCount=<REPLACE ME with your max player count (note: split screen is still 1 device)  In the example of an 8 player game, this would be 8.>
MaxUsersPerDeviceCount=<REPLACE ME with your max player count per box (note: split screen is still 1 device)  In the example of an 8 player game, this would be 1.>
DirectPeerConnectivityOptions=<REPLACE ME with your connectivity options, in the form of an array of strings. The default case corresponds to the following:
+DirectPeerConnectivityOptions=AnyPlatformType
+DirectPeerConnectivityOptions=AnyEntityLoginProvider>

[/Script/OnlineSubsystemPlayFab.PlayFabNetDriver]
NetConnectionClassName="OnlineSubsystemPlayFab.PlayFabNetConnection"
ReplicationDriverClassName="<REPLACE ME with your existing replication driver class name>"
ConnectionTimeout=15.0
InitialConnectTimeout=30.0

[/Script/Engine.GameEngine]
!NetDriverDefinitions=ClearArray
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemPlayFab.PlayFabNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
```

## Platform Specific Considerations

With all that done, we're nearly finished. There are only a few key platform-specific parameters left that must be set.  

### GDK

If you're developing games using GDK, define the platform services and, optionally, set your GDK sandbox:

```config
[OnlineSubsystem]
DefaultPlatformService=PlayFab
NativePlatformService=GDK

[OnlineSubsystemPlayFab]
Sandbox=<Optional, REPLACE ME with the sandbox Id used for the title under development >
```

### Steam

If you're developing games for Win64 with Steam, define your platform services:

```config
[OnlineSubsystem]
DefaultPlatformService=PlayFab
NativePlatformService=Steam
```

### Switch

For more information about Switch, see the [ReadMe.md](https://dev.azure.com/PlayFabPrivate/Switch/_git/PlayFabMultiplayerUnrealSwitch?path=/README.md) file that comes with the Switch PlayFab OSS.

### PS5™ and PS4™

For more information about PS5™ and PS4™, see the [ReadMe.md](https://dev.azure.com/PlayFabPrivate/PS5/_git/PlayFabMultiplayerUnrealPlayStation?path=/README.md) file that comes with the PS5™ and PS4™ PlayFab OSS.

### Cross-platform

Finally, if your game makes use PlayFab's cross-platform networking support, define which platforms you permit to connect:

```config
[/Script/OnlineSubsystemUtils.OnlineEngineInterfaceImpl]
!CompatibleUniqueNetIdTypes=ClearArray
+CompatibleUniqueNetIdTypes=STEAM
+CompatibleUniqueNetIdTypes=GDK
+CompatibleUniqueNetIdTypes=SWITCH
+CompatibleUniqueNetIdTypes=PS4
+CompatibleUniqueNetIdTypes=PS5
```

All platforms allow VoIP by default. To disable VoIP for a specific platform, add the platform model name to your UE configuration file as shown in the following example.
```config
[OnlineSubsystemPlayFabVoiceChatDisabledPlatforms]
!Platforms=ClearArray
+Platforms=WIN64
+Platforms=STEAM
+Platforms=SWITCH
+Platforms=PS4
+Platforms=PS5
```
These steps complete the setup of OSS required to be used in your game.  Good luck!

## Troubleshoot: Unreal Engine Installed Builds

Users may face issues when trying to create an Unreal Engine Installed Build with the OnlineSubsystemPlayFab on GDK build flavors. We provide the following guidance to successfully overcome this issue until there's a more complete solution.

 * Locate the directory where Unreal Engine is installed on the machine.
 * Navigate to Engine\Platforms\GDK\Plugins\Online\PlayFabParty
 * Open PlayFabParty.uplugin
 * Replace the key **WhitelistPlatforms** with **BlacklistPlatforms**

Repeat the process for XboxOneGDK (PlayFabParty_XboxOneGDK.uplugin) and XSX (PlayFabParty_XSX.uplugin) if these platforms are required for the Installed Build. If Win64 is also a required platform for the installed build, add Win64 in the array of **BlacklistPlatforms**.

Example of how the part of interest of PlayFabParty.uplugin looks after following these instructions:
```config
	"Modules": [
		{
			"Name": "PlayFabParty",
			"Type": "Runtime",
			"LoadingPhase": "Default",
			"BlacklistPlatforms": ["WinGDK", "Win64"]
		}
	],
```

"PlayStation" is a registered trademark or trademark of Sony Interactive Entertainment Inc.
"PS4" is a registered trademark or trademark of Sony Interactive Entertainment Inc.
"PS5" is a registered trademark or trademark of Sony Interactive Entertainment Inc.
