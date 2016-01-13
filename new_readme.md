Welcome to Teleport (reloaded)
===================

Teleport Reloaded is a fork of the original Teleport Library for Android Wear built by Mario Viviani and available here: https://github.com/Mariuxtheone.
Since Mario is not available anymore I contacted him and decided to move forward and customize Teleport to include features like:

 - Message Brokering
 - Messages and DataItem priority (since google gms version 8.3)
 - Upgrade to the latest Google Play and Android version



> Note: the project is still Open Source using Apache 2.0 license and I
> am claiming any rights on this project but because since the version
> 8.1 of Google Play Service, the library Teleport stopped to work I decided to upgrade it and keep it alive as a new fork of the original
> one.

----------

Overview
-------------

Teleport (reloaded), like the original teleport, can be used in multiple ways:

 - You can use it to send Message and/or DataItem from your Phone to your Smartwatch and vice-versa
 - You can use it to create ListenerServices for both Messages and DataItems
 - You can use it together with Google Api Client to reduce the amount of boiler plate code required to open a communication between two devices

At the moment there are two major classes available:

 - `TeleportClient`
 This class is in charge of opening and closing a connection with Google Api Client plus is capable to send Messages and/or DataItems
 - `TeleportService`
 This class is in charge of providing the basic boilerplate code for a `WearableListenerService` class, including connection with the `GoogleApiClient` class and handling notifications

Table of Contents
=================
 - Setup your project
 - TeleportClient inside an Activity or Fragment
 - TeleportService
 - Data Synchronization
 - Send and Receive Messages
 - Advanced Usage
 - Message Brokering

Original License
----------------
Copyright 2014-2015 Mario Viviani

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.