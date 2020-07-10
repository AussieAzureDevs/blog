---
layout: post
title:  "Cognitive Pinball - Inside the Brain"
date:   2020-07-13 08:00:00 +1000
categories: pinball
comments: true
author: JT

future: true
published: true

slug: inside-the-brain
permalink: /:categories/:slug

image:
  path: assets/photos/cognitive-pinball.jpg
  height: 75
  width: 100
---

*This is the second of our posts about Cognitive Pinball. In this article we will give you an overview of the software we used to prove the concepts. By the end, you'll be able to run your own Cognitive Pinball game in the cloud. You can read our first post [here][firstPost]{:target="_blank"}.*

In our [previous post][firstPost]{:target="_blank"} we introduced Cognitive Pinball, a retro pinball machine which uses a Deep Reinforcement Learning model to create an AI capable of learning to play a game without the need for supervision. If we piqued your interest, you might be wondering "How can I build my own?". Good news! This post will not only explain the software components that fit together to make our pinball machine work, we'll also step you through getting your own software version up and learning.

## Getting Started

The first you're going to need is a Windows machine. This is because we're going to go hard on nostalgia and train the brain on the old Windows 95 [Space Cadet Pinball][spaceCadet]{:target="_blank"}. If you don't have a Windows machine because you're a Mac or Linux fan, setting one up on [Azure][freeAzure]{:target="_blank"} is super easy!

Once you've got a Windows machine to work with you'll need the following software downloaded and installed to get started:

- [3D Space Cadet Pinball][pinballDownload]{:target="_blank"} - the game itself
- [Unity][unity]{:target="_blank"} - we recommend version 2019.2.10f1 for building the 3D and AI-agents components
- [Python 3.6 Virtual Environment][python]{:target="_blank"} - While you won't deal with Python code in the software only version, it's used by the underlying ML tooling.

Once you've got the prerequisites in place, you can grab the source code from [GitHub][githubRepo]{:target="_blank"} and start work.

## The Logical Pieces
Before we dive into the code, it’s worth thinking about the functions we are going to replicate through software. The physical pinball machines we built had two primary software components.

  1. **The Brain**

     This is the AI component of the machine. It manages the learning utilising a Deep Reinforcement Learning model. In this model, we reward (using a point system) the brain for doing good things (i.e. increasing the current score) and punish it for doing bad things (i.e. letting the ball fall out of play).

  2. **The Eyes**

     We added web cams to the machine’s exterior to act as the ‘eyes’. In the software-only version, web cams are replaced with code that gives you the ability to track action on the screen. The eyes provide a level of feedback to the brain, so that it understands what's happening both on the playing board and the scoreboard.

The eyes pass information to the brain so it can make decisions about what the next action should be, based on what it has learned from its training. The more training the brain gets, the better the decisions it makes and the higher score it achieves.

### Code Parts

So how does the code match up to the Pieces we listed above?

The [`PinballAgent.cs`][pinballAgent]{:target="_blank"} contains the "brain" functionality of the machine. The bulk of the logic happens inside of the `AgentAction()` method. This method does the following things:
1. It executes the action that it receives. This includes launching the ball, triggering a flipper or even just sitting idle.
2. It will then get a read of the score board to determine whether a reward or punishment is required. If the score has gone up, reward! Otherwise, punish!
3.	It will then determine if the ball has been dropped. We want the ball in play as long as possible so if the ball is out of play, a punishment is again applied. If the ball remains in play based on its action, it will apply a reward.
4. Finally, it will check whether the game has ended, and reset the table for a new game if required.

The [`ExternalWindowsManager.cs`][windowManager]{:target="_blank"} contains the "eyes" behaviour. In this software-only version, it captures what’s happening in the game within the visual context of the window in which the pinball machine is playing. It does this by:

1.	Capturing the graphics displayed in a rectangle that surrounds the scoreboard[^1].
2.	Performing a diff to determine if the graphics have changed. I.e. has the score changed?
3.	Performing basic conversion of the pixels on the scoreboard to numbers - the OCR function we noted in our first blog - so that the score can then be calculated.

`ExternalWindowManager` has a secondary job in the software-only training version of this game. It handles the execution of Actions sent by the brain. The reason it takes this responsibility is because we want to send key-presses to the window running the game to control flippers and the ball launcher. Since the `ExternalWindowManager` behaviour already has a reference to the game window, it made sense to include this behaviour here as well.

The instructions within the GitHub repo should have allowed you to get some basic training up and running. If you've had any issues along the way, feel free to raise them on GitHub or even submit a Pull Request with your changes.

In the not too distant future, we'll be publishing a video tour of the Star Wars Pinball machine hardware, and showing you how you can build your very own physical Cognitive Pinball.

In the meantime, check out Microsoft AU Developer on [Twitter][msDevTwitter]{:target="_blank"} or [sign up][newsletter]{:target="_blank"} for our community newsletter to get access to more content like this as well as tips, tricks and inspiration. More importantly, go forth and build your own software pinball AI and have some fun.


<!-- Footnotes -->
[^1]: This can be user configured by pressing 'c' while the game is running and drawing a Rectangle around the scoreboard.

<!-- Links -->
[firstPost]: http://msft.it/6015TgHRZ
[freeAzure]: https://azure.microsoft.com/en-au/free/ "Free Azure Trial"
[pinballDownload]: https://www.groovypost.com/howto/windows-7-3d-pinball-space-cadet-game/ "Install 3D Space Cadet Pinball"
[unity]: https://unity3d.com/get-unity/download "Unity 3D engine and development tooling"
[spaceCadet]: https://en.wikipedia.org/wiki/Full_Tilt!_Pinball#3D_Pinball_for_Windows_–_Space_Cadet "Space Cadet Pinball for Windows"

[python]: https://github.com/Azure/3DPinballAI/blob/main/Docs/Using-Virtual-Environment.md "Setting up a Python Virtual Environment"
[githubRepo]: https://github.com/Azure/3DPinballAI "Source code"

[pinballAgent]: https://github.com/Azure/3DPinballAI/blob/main/Assets/Scripts/PinballAgent.cs "Pinball Agent source code"

[windowManager]: https://github.com/Azure/3DPinballAI/blob/main/Assets/Scripts/ExternalWindowManager.cs "External Window Manager source code"

[msDevTwitter]: https://twitter.com/MicrosoftAUDev  "MS Dev Twitter"

[newsletter]: https://azure.microsoft.com/en-au/resources/join-the-azure-developer-community/?ocid=AID2423470 "Microsoft.Source Community newsletter"
