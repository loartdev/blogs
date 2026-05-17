---
title: "How to Set Up EOS Integration Kit in Unreal Engine 5"
seoTitle: "EOS Integration in Unreal Engine 5 Setup Guide"
seoDescription: "Learn to set up the EOS Integration Kit in Unreal Engine 5 for seamless Epic Online Services integration"
datePublished: 2024-12-18T18:18:57.156Z
cuid: cm4u7v6ro001009mn0hdi2vqh
slug: how-to-set-up-eos-integration-kit-in-unreal-engine-5
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734545779036/0421ead0-2a70-4d35-a1eb-b382ee9b6b87.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1734545873579/e69b27e5-3262-4c68-9790-cfb6abd9c900.png
tags: tutorial, plugins, game-development, games, unreal-engine, game-design, multiplayer-games, eos, 2articles1week, ue5-tutorials, ue5, epiconlineservices, eik, eos-integration-kit

---

When implementing [Epic Online Services (EOS)](https://www.loart.dev/blog/enhancing-online-gaming-with-epic-online-services-a-developers-perspective) in your Unreal Engine game, you might want to consider using a third party plugin like EIK (EOS Integration Kit). Because [this third party integrations make the process of implementing EOS SDK much easier](https://www.loart.dev/blog/boost-your-unreal-engine-game-best-eos-plugin-options), reducing the amount of code you have to do, and helping you implement EOS from blueprints.

## Why EOS Integration Kit?

After I tried many plugins that simplify the process of implementing EOS, I have to say that this one is the best one so far. Not only is the community active, but the developers are constantly updating the plugin to keep with engine and SDK updates, new features, and solving bugs. The plugin is also in progress to have console support allowing you to use EOS features in Xbox, PlayStation, and Nintendo.

## Installing EIK

To install EIK you have 2 options, you can either use the marketplace (Fab.com) or get the GitHub version! In case you need to get the GitHub Version, I recommend you to follow the [Docs Tutorial](https://eikv4.betide.studio/getting-started/plugin-installation), but I will do my best to guide you!

### Installing from the marketplace

To get EIK on the marketplace you can visit the [EIK posting on Fab.com](https://www.fab.com/listings/70689002-04d3-4098-ad9e-62792fe0b669), here you will be able to acquire the plugin! I will recommend you to support the developers of this plugin so they can continue working on it!

![Screenshot of the EOS Integration Kit page on Fab, featuring the plugin's title by Betide Studio, a 5-star rating, and download options.](https://cdn.hashnode.com/res/hashnode/image/upload/v1733602109911/c9df084b-2f26-4943-af6e-310162e316b7.png align="center")

After you have acquired the Plugin in Fab.com you can now proceed to install it on your Unreal Engine version from the Epic Games Launcher!

![Screenshot of the Epic Games Launcher showing the Library tab. It displays the Fab Library section with options to install "EOS Integration Kit" to the Unreal Engine.](https://cdn.hashnode.com/res/hashnode/image/upload/v1733604474490/dafb10f6-9ad3-43e0-8852-125923196c30.png align="center")

### Installing from GitHub

First you will have to download the GitHub version, you can do this by going to the git repo and choosing your engine version from the release section or by downloading the source code and compiling the plugin. We are going to download the latest version for UE 5.5.1 this means we will have to compile the plugin.

To do so, we have to download the Plugin files, you can use this command if you have [Git](https://git-scm.com) installed.

```powershell
git clone https://github.com/betidestudio/EOSIntegrationKit.git
```

Or you can use any other option from the GitHub Repo using the “Code <>“ drop down

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734299734056/da882afa-13f3-4ff3-849b-311321e5ac59.png align="center")

I used the “git clone” command

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734299902540/69dee916-5576-40ab-b5c2-230c78423bbc.png align="center")

Now that we have the plugin we have to download the latest EOS SDK version from the [Epic Developer Portal](https://dev.epicgames.com/portal/en-US/). Remember to download the versions of the SDK for the platform you are going to develop for!

> *   **Windows** : C SDK
>     
> *   **Mac** : C SDK
>     
> *   **Linux** : C SDK
>     
> *   **Android** : Android SDK
>     
> *   **iOS** : iOS SDK
>     

![Screenshot of a Developer Portal interface showing options to manage products for the Epic Games Store and Epic Online Services. There are buttons for "Player Search," "SDK & Release Notes," and "Create Product." The latest update is dated December 8, 2024.](https://cdn.hashnode.com/res/hashnode/image/upload/v1734300121016/d449c354-09b5-4b9e-af01-c80eb2e5fe13.png align="center")

![Screenshot of an SDK & Release Notes page. It provides download options for SDKs compatible with various platforms including Windows, OSX, Linux, PlayStation, and others. A dropdown menu lists SDK types for C, C#, iOS, and Android. A note advises users to configure settings like Client ID and Sandbox for integration.](https://cdn.hashnode.com/res/hashnode/image/upload/v1734300271946/7286f9ce-3ca0-479d-9b92-c2c2c9100553.png align="center")

In my case, I will download the C SDK only

![Dropdown menus on a user interface show "C SDK" as the selected SDK Type and "1.16.4-CL36651368 (Latest)" as the SDK Version. A link below asks, "Interested in the console SDK? Request access."](https://cdn.hashnode.com/res/hashnode/image/upload/v1734300316403/07a86007-9108-4118-81d7-2c84854cbb8d.png align="center")

Now we need to copy the SDK files to the plugin folder. The SDK files are located in the `SDK` folder of the downloaded ZIP archive. The SDK files are different for each platform.

<details data-node-type="hn-details-summary">
<summary>Android SDK and iOS SDK</summary>
<p>If you want to develop for these two platforms, here are the respective links to the documentation so you can install the EOS SDK in EIK: <a target="_blank" rel="noopener noreferrer nofollow" class="text-primary underline underline-offset-2 hover:text-primary/80 cursor-pointer" href="https://eikv4.betide.studio/getting-started/plugin-installation#ios-sdk" style="pointer-events: none;">iOS SDK</a>, <a target="_blank" rel="noopener noreferrer nofollow" class="text-primary underline underline-offset-2 hover:text-primary/80 cursor-pointer" href="https://eikv4.betide.studio/getting-started/plugin-installation#android-sdk" style="pointer-events: none;">Android SDK</a>.</p>
</details>

To install the C SDK, extract the downloaded ZIP archive and then copy the elements of the `SDK` folder to the `ThirdParty/EIKSDK` folder of the plugin.

```plaintext
[...PluginPath]\Source\ThirdParty\EIKSDK
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734301012052/710d5972-4662-427c-8dd5-18346bf4a1bd.png align="center")

Now let’s build the plugin, to do so the easiest way is to move the plugin into your project’s Plugins folder,

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734301287587/18c0733f-7995-4b91-a1a7-8414ef494ebf.png align="center")

Now right-click on the `.uproject` file of your project and select `Generate Visual Studio project files`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734301627518/de976d3d-907e-4ec2-8c69-2f4d9fc19150.png align="center")

Now open the generated `.sln` file, this will open Visual Studio, Here you have to right-click on your project name, then click on rebuild.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734301752158/f0e2918f-b3f7-4e23-be14-65c866d49f4e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734301977307/e92ff2fb-c7db-46e9-bbd2-45aa91ad6815.png align="center")

Now your plugin is ready to go!

<details data-node-type="hn-details-summary">
<summary>In case it fails 😨</summary>
<p>In case this fails, or you don’t want to use this method, here is a tutorial on how to<a target="_self" rel="noopener noreferrer nofollow" class="text-primary underline underline-offset-2 hover:text-primary/80 cursor-pointer" href="https://dev.epicgames.com/community/learning/tutorials/qz93/unreal-engine-building-plugins" style="pointer-events: none;"> build plugins from the Epic Dev Community</a>.</p>
</details>

## Configure EIK

Once you install EIK on Unreal Engine/Your Project, you can the project, then go to Plugins and look for EOS Integration Kit and enable it. Then proceed to restart the engine.

![Screenshot of Unreal Engine's interface showing the Plugins window with the EOS Integration Kit plugin highlighted. The main panel displays plugin information, while the sidebar lists various categories like 2D, AI, and Animation. The Outliner panel on the right lists scene objects.](https://cdn.hashnode.com/res/hashnode/image/upload/v1734298711951/0d1d3665-4671-4af6-9815-dc8684a6c834.png align="center")

Now we have to configure EIK, to do so its really simple, first we have to go to the project settings, you can do this by going to Edit > Project Settings, here we are going to go Game > EOS Integration Kit

![A screenshot of the Unreal Engine Project Settings menu, showing categories like "Project" and "Game" with sub-options such as "Description," "Encryption," and "EOS Integration Kit."](https://cdn.hashnode.com/res/hashnode/image/upload/v1734298924051/78861c68-0fdd-43ed-92f4-ff00b3e6ca5a.png align="center")

Now you will see some options, the first option will be the automatic set up, so check that option, when you check it will prompt you to restart the engine again to apply the changes to the Engine.ini file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734299018336/dacc00fd-da01-4b95-80ab-2ad5333f92c6.png align="center")

Now we have some extra configurations you can check out, we are going to focus on the main config to set up the connection with EOS, and do the login, but I recommend you to check the [plugin documentation](https://eikv4.betide.studio) to learn how to use all the other cool features EIK has!

### Connect EOS

We have to go back to the [Epic Developer Portal](https://dev.epicgames.com/portal/en-US/) and Create a product (in case you have an existing product you want to use, feel free to use it and skip this steps!) by clicking on the Create Product button on the dashboard.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734302357998/f3bc1907-65cb-4d95-bddb-f3e14807e317.png align="center")

Add a name and click on create, wait for EOS to create your project and then click on it and go to the Product Setting

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734302439688/eba6c56e-0e2f-474c-ad5f-5e6e81375795.png align="center")

Now we have to create a Client, an Application, and a Client Policy! So lets start!

#### Client Policy

For this example, I am going to create a Peer-2-Peer Policy. So let’s go to Clients, then look for the `Add new client policy` button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734302666235/fb72e556-b565-4726-9502-18dc9c55b12c.png align="center")

Now we have to name our policy, and select the type, as I said before I am choosing the Peer-2-Peer policy. After you configure it, you can click on add new client policy

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734302702451/3be1ae84-6d53-4cc3-b5cd-56c0e2713de2.png align="center")

#### Client

For creating our client we have to click on the Add new client Button, Here we have to name the client, then select the policy and click on `Add new client`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734302956862/f3ba194f-ff6b-44b5-a75c-d5977d602c76.png align="center")

#### Application (Only if you are planning on using the Epic Account Services)

Now we have to go to the Epic Account Services section to create a new Application, here you have to click on `Create Application`, Then follow the steps to get your app approved, you can test things without adding your website and privacy policy but if you want to use Epic Account Services you need to do all the verification process.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734303064572/76d0a462-86b1-4d89-87d0-465c1f34d8a3.png align="center")

You will be required to add some data about your Company/Brand, your Privacy Policy (As an example of a Privacy Policy you can visit [Party Madness Privacy Policy](https://www.loart.dev/legal/party-madness-privacy-policy), It is a game I did that uses EOS Services), and some Information about your game project. You don’t need to put all the actual info for the draft and testing, but if you want to test with other people that are not part of your company, you will have to submit your brand for review.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734327423271/24d0e684-f9b2-4abc-9cd2-7182fc1b4a64.png align="center")

Likewise, you will also be asked to choose the **Permissions** this will allow you to get things like user location, friends list, and Online Presence.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734327529178/24e508e7-c3e6-4908-a608-38944753a4d6.png align="center")

The last thing we have to do so we can start testing is to link our client, this can be done on the last tab.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734327867539/ae444836-ed3d-47a7-9ed2-a191cb748623.png align="center")

We are now done in this section! When you go back to the Epic Account Services tab, you should see something like this with your own data!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734328176068/ca5c7e10-d5bd-4320-b6a3-89fb22568a43.png align="center")

#### Connecting with EIK

Let’s set up the connection between EOS and Unreal Engine (EIK/EOS SDK), first we have to go to **Product Settings,** here you should see the something like this (I obscure part of the IDs because this is information you should keep private)

![Dashboard displaying product, client, application, sandbox, and deployment information with unique IDs and client secrets partially obscured.](https://cdn.hashnode.com/res/hashnode/image/upload/v1734328360472/ccc1d1db-cc5d-4de2-b2d9-c24f1017af6a.png align="center")

Now in we have to go back to our EIK plugin configuration (in the Unreal Engine Project Settings), and start configuring our first artifact.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734544050112/adc054ba-2611-4ff9-b7d6-fcbe4502c820.png align="center")

For this tutorial, I will use `DefaultArtifact` as the name for the Artifact, but you can use any name that is meaningful to you. So create an Artifact by clicking the plus button, and add put the name, The name you use should match the Default Artifact Name option, so the plugin knows what to use.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734544245604/daa2827b-8a15-4d94-9d4c-670f74d3eb44.png align="center")

Now let’s start adding the IDs and Secrets from the Epic Online Services to the fields, just ensure the field name matches what you are adding. (That means Client ID with Client ID, Client Secrete with Client Secrete and so on) At the end it should look something like this (IDs and Secretes have been obscured to protect privacy)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734544529299/8ffc6ee1-f848-469f-a159-850653b697b2.png align="center")

Just like that, you have set up EOS Integration Kit on your game, Now it’s your responsibility to start coding the behaviors of your game, if you need some guides on what to do next here is a list of things you might want to do!

1.  Set up Login: [https://eikv4.betide.studio/Authentication/basics](https://eikv4.betide.studio/Authentication/basics)
    
2.  Set up EOS Dev Tool: [https://eikv4.betide.studio/Authentication/autologin](https://eikv4.betide.studio/Authentication/autologin)
    
3.  Matchmaking: [https://eikv4.betide.studio/multiplayer/matchmaking/lobbies](https://eikv4.betide.studio/multiplayer/matchmaking/lobbies)
    
4.  Create Achievements: [https://eikv4.betide.studio/playerinformation/progression/achievements](https://eikv4.betide.studio/playerinformation/progression/achievements)
    
5.  Set up Voice Chat: [https://eikv4.betide.studio/multiplayer/voicechat/basics](https://eikv4.betide.studio/multiplayer/voicechat/basics)
    

## Important Resources

*   Unreal Engine Multiplayer Docs: [https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine](https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine)
    
*   EOS Docs: [https://dev.epicgames.com/docs/epic-online-services](https://dev.epicgames.com/docs/epic-online-services)
    
*   EOS Dev Portal: [https://dev.epicgames.com/portal](https://dev.epicgames.com/portal)
    
*   Multiplayer Tutorial EIK: [https://www.youtube.com/playlist?list=PLx9KZWpnHHA2mhvlMFeyIuPFJncz4dWKz](https://www.youtube.com/playlist?list=PLx9KZWpnHHA2mhvlMFeyIuPFJncz4dWKz)
    
*   EIK Docs: [https://eikv4.betide.studio](https://eikv4.betide.studio)
    
*   EIK Store Page: [https://www.fab.com/listings/70689002-04d3-4098-ad9e-62792fe0b669](https://www.fab.com/listings/70689002-04d3-4098-ad9e-62792fe0b669)
    
*   EIK GitHub: https://github.com/betidestudio/EOSIntegrationKit
    
*   [The best Plugin options for implementing EOS](https://www.loart.dev/blog/boost-your-unreal-engine-game-best-eos-plugin-options)