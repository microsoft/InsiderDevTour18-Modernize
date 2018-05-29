# Modernize your app - Demo details

**Branch:** master

**Project:** Knowzy_Engineering_Win32App

## Setup:

### Pre-requisites

* Windows 10 April 2018 Update [10.0.17134]
    * Include Windows subsystem for Linux

* Visual Studio Community 2017 [15.7.1]
    * Universal Windows Platform development 
        * Include Windows 10 SDK 10.0.17134
        * C++ Universal Windows Platform tools
    * .NET desktop development
        * Include .NET Framework 4.6.2 development tools 
    * Desktop development with C++
    * .NET Core cross-platform development

### Packaging your desktop app - Setup

For test purposes ensure you have **Developer mode** turned on in **System settings - Use developer features**.

For the packaging demo, the package creation UI requests a location to host the package, index.html and .appinstaller files. Create a local fileshare to host the files for the demo.

The package generation is slow to demo live, so follow the demo steps once before hand to generate the app package ahead of time. Place the generated files in the share folder manually and keep the folder open and handy for later.

### Modern UX - example: WebView - Setup

Ensure you can browse to **aboutknowzy.azurewebsites.net** ahead of time - you may need to set up a local webserver with the content if there are network issues, although the page is lightweight.

### Modern command-line - Setup

Ensure the "Windows subsystem for Linux" feature has been added in the Windows **Turn Windows features on or off** Control Panel - you will need to reboot afterwards.

Ensure you have a Linux distribution installed. Go to the **Windows Store app**, search for **"Ubuntu"** and install **"Ubuntu 18.04"**. Once it has installed, run it once to initialize. You can pin the Ubuntu app onto your taskbar for fast access. Leave the Store search page open and handy for later.

In **Visual Studio - Extensions and Updates** search for **"Console App (Universal) Project Templates"** in the **Online** marketplace and install - [also available here](https://marketplace.visualstudio.com/items?itemName=AndrewWhitechapelMSFT.ConsoleAppUniversal). This will enable you to build and deploy the UWP command line app.

In the **Microsoft.Knowzy.WPF** solution, right-click the **KnowzyCmd** project and hit **Deploy**.

Open a **Developer Command Prompt** to **..\KnowzyInternalApps** and keep this open for later.

### Modern features - example: Windows Hello - Setup

You will need a TPM chip that is pre-configured in order to demonstrate **Windows Hello**. You will need a web-cam if you want to show **Face sign-in**, or a **PIN** configured for PIN sign-in. Ensure you have this setup in **Sign-in options** in **System settings**.

Open **src\Knowzy_Engineering_Win32App\src\Microsoft.Knowzy.AuthenticationService\MicrosoftPassportHelper.cs** and set a breakpoint on the following line 38:

`KeyCredentialRetrievalResult keyCreationResult = await KeyCredentialManager.RequestCreateAsync(accountId, KeyCredentialCreationOption.ReplaceExisting);`

## Demo Phases:

1. Packaging your desktop app
2. Modern UX - example: WebView
3. Modern features - example Windows Hello
4. Modern command-line

## Packaging your desktop app

### Add the Packaging Project

1. Right-click Solution -> Add -> New Project
2. Add a Windows Application Packaging Project project to your solution.

    You won't have to add any code to it. It's just there to generate a package for you.

    * Found under: `Visual C#` -&gt; `Windows Universal`
    * Name: `Microsoft.Knowzy.PackagingProject`
    * Location: `<path>\KnowzyInternalApps\src\Knowzy_Engineering_Win32App\src\`

    #### Note: This project appears only in Visual Studio 2017 version 15.5 or higher.

3. Set the Target and Min Version of this project to the latest version (2018 April Update). Typically, we would set the **Minimum Version** to `Windows 10 Anniversary Update`. However, for this demo, we want to show the side-by-side app installer updates which only works on the latest version currently.

### Target the desktop application

4. In the packaging project, right-click the `Applications` folder, and then choose `Add Reference`.
5. Choose your desktop application project (`Microsoft.Knowzy.WPF`), and then choose the `OK` button.
6. Right-click the `Microsoft.Knowzy.PackagingProject` project and click `Set as StartUp Project` 

### Build app packages

7. Right-click the `Microsoft.Knowzy.PackagingProject` project -&gt; `Store` -&gt; `Create App Packages...`
8. Select the second radio button: `I want to create packages for sideloading`
9. Check the checkbox: `Enable automatic updates`
10. Click `Next`
11. Click `Next` again
12. Input the network share location to host the files. The field is required even though the generated packages are not actually deposited there, and must be moved manually. Input the share folder path to enable the `Create` button. 
14. Instead of actually generating more packages, close the window and Alt-Tab over to the folder with the pre-generated app package files.
14. Double-click the index.html to open (don't need to actually install).

## Modern UX - example: WebView

1. Open `Views/AboutView.xaml` from the `Microsoft.Knowzy.WPF` project.
2. Add controls namespace to the top of the page
    ```
    xmlns:controls="clr-namespace:Microsoft.Toolkit.Win32.UI.Controls.WPF;assembly=Microsoft.Toolkit.Win32.UI.Controls"
    ```
3. Replace the existing WebBrowser control with a new Win32 WebView control.
    ```
    <!--<WebBrowser Grid.Row="1" Source="http://aboutknowzy.azurewebsites.net" />-->
    <controls:WebView Grid.Row="1" Source="http://aboutknowzy.azurewebsites.net" />
    ```
4. Run the app, and click `Help` in the menu to see the updated Edge WebView.

## Modern features - example: Windows Hello

**NOTE:** If you run the Microsoft.Knowzy.WPF project directly -> Login, you will find that you are prompted with the username / password dialog. If you set **Microsoft.Knowzy.PackagingProject** as your startup project however and run through the same flow, you will see that you get the Windows Hello code path. There is a logic check in **LoginView.xaml.cs, line 34** that determines whether you are running in a package or not - you do not need to show this line of code in the demo, but you can explain that you do a check for a package, which enables the concept of a package identity and access to a broader range of Windows 10 APIs. 

1. Set `Microsoft.Knowzy.WPF` as the start-up project -> run -> Login. Show the username and password boxes are currently present. Stop the solution

2. Set `Microsoft.Knowzy.PackagingProject` as the start-up project -> run -> Login. Show that you are now only prompted with the username as Windows Hello is going to help so you don't need to remember your full password

3. Input the username `sampleUsername` and click Login
4. Provide your PIN or facial recognition to complete the sign in

5. You should hit your breakpoint in (line 38 of **MicrosoftPassportHelper.cs** - see demo setup). Explain that KeyCredentialManager is the Windows 10 API you are using for Windows Hello. Hit F5 to resume.

6. The Login window will dismiss on success and your username will be displayed next to the Logout button.

7. In Visual Studio, navigate to your **Microsoft.Knowzy.AuthenticationService** project and show references. Call out that we had to add Windows 10 API references to our project to make this possible such as **Windows.Foundation.UniversalApiContract**, which is where **KeyCredentialManager** resides. Explain that people can review our docs to understand which APIs require which references.

## Modern command-line

1. Open Store app and show Ubuntu distributions

2. Open your **Developer Command Prompt** to **..\KnowzyInternalApps** and show a few sample WSL commands:

    wsl.exe
    ls
    grep "http://"
    exit

3. Demonstrate the new **tar** and **curl** support like so:

    curl http://aboutknowzy.azurewebsites.net

    tar xf msodbcsql-11.0.2270.0.tar.gz

4. Demonstrate a UWP command line app with arguments:

    KnowzyCmd THANK YOU!
