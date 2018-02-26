---
layout: post
title: Windows Powershell
date: 2018-02-16
---

# Windows Powershell

***Powershell is one of the most overlooked windows apps for ops. This approach doesn’t have any extra features but can be perfect for opening a quick commandlet window and keeping an eye on the status of a file.***


### Tailing Logs

- Use the following simple syntax to show the tail end of a log file in real-time.

	`Get-Content myTestLog.log –Wait`

- You can also filter the log right at the command line using regular expressions:

	`Get-Content myTestLog.log -Wait | where { $_ -match “WARNING” }`


### Unreal Editor

[11:00 - Powershell Launch Commands](https://www.udemy.com/unrealmultiplayer/learn/v4/t/lecture/7830362?start=0)

- Start game server
`& "C:\Program Files\Epic Games\UE_4.18\Engine\Binaries\Win64\UE4Editor.exe" S:\Udemy\UnrealMultiplayer\PuzzlePlatforms\PuzzlePlatforms.uproject /Game/ThirdPersonCPP/Maps/ThirdPersonExampleMap -server -log`

- Join game 
`& "C:\Program Files\Epic Games\UE_4.18\Engine\Binaries\Win64\UE4Editor.exe" S:\Udemy\UnrealMultiplayer\PuzzlePlatforms\PuzzlePlatforms.uproject 192.168.1.5 -game -log`

- Join game
`& "C:\Program Files\Epic Games\UE_4.18\Engine\Binaries\Win64\UE4Editor.exe" S:\Udemy\UnrealMultiplayer\PuzzlePlatforms\PuzzlePlatforms.uproject -game -log`

