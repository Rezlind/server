# Development process overview:

Any development work should be done in local feature branches rather than on the main branches used for QA/live builds. For example, if you were working on a boss script, rather than committing directly to the development branch, you should first create a new branch with a descriptive name.

When you have finished working on your fix or feature, you should open a pull request to allow for your code to be automatically compiled by the continuous integration system and reviewed by other developers. After receiving feedback and making any requested changes, your pull request will be squashed and merged to the development branch. Squashing your commits is to ensure that the development branch can be compiled at any point in its history as well as ensuring a clean history.

The development branch is used as the basis for the QA and PTR realms. To ensure the QA team receives ample opportunity to test each set of changes, the development branch will only accept pull requests for one week before being frozen for a week, during which time only essential fixes will be accepted.

Once the development branch has been tested by QA, it will be merged with the stable branch to become the next revision for the live realms. The only changes accepted to the stable branch, outside of the merge window, will be critical hotfixes to resolve game-breaking and stability issues.

As with any open source project, the workflow is subject to change as we receive feedback and discover what works best for the project and its contributors.


Installation Guide
------------------------------------------------------------------------------------------------------------------------------
HEADER >> ELYSIUM LOGO <<


INSTALLATION GUIDE FOR WINDOWS


The guide below will help you build and run the Elysium Core by first cloning this repository. At the end of this guide you should have a working server and authentication server as well as a working MySQL database with four databases: One database for the logon authentication and account tracking, one database for the world environment, one database for logs, and a database to keep track of characters associated with the different accounts and realms.

This guide will assume you have knowledge of cloning or forking a repository and assumes you already have that copy.

There are various additional downloads not included in this repository, but that will be necessary to ultiamtely build the Elysium Core. Below you will find the necessary tools and a convenient download link to those tools. Some of the following are tools, other are dependencies, I will make sure to seperate them as such. You are welcome to substitute the tools with other tools of the same function, ALL TOOLS LISTED BELOW ARE FREE OR OFFER A TRIAL. You may NOT however substitute the dependencies with anything else or your server will not build/run properly.

BOLD larger size >> Tools list <<
1. Visual Studio - You can download any version of Visual Studio and it should work. I personally use Visual Studio 2015 Community edition, but you are welcome to use Visual Studio 2017, it should build regardless. (https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)

2. MySQL - MySQL can be installed using the MSI installer available for your 32 bit or 64 bit version here: https://dev.mysql.com/downloads/installer/

3. CMAKE (with a gui) - https://cmake.org/download/  (Download the appropriate bit for your system. If 32 bit, download https://cmake.org/files/v3.8/cmake-3.8.0-rc2-win32-x86.msi  If you are 64 bit, https://cmake.org/files/v3.8/cmake-3.8.0-rc2-win64-x64.msi ) This tool will be used to generate the build files that will be used by our Visual Studio.

4. Sqlyog - This use to be a free tool and in recent years has become a paid tool. The link here will give you a 14 day trial after you sign up and let them email you the download link: https://www.webyog.com/product/downloads . Remember, you can substitute this tool with any other SQL GUI client you prefer. I personally found Sqlyog's interface to be easy to pick up quickly.

5. 7zip - Use this for quick and efficient options when unzipping files. Downloadable here: http://www.7-zip.org/download.html

BOLD larger size >> DEPENDENCIES <<

1. ACE - http://download.dre.vanderbilt.edu/previous_versions/ACE-6.4.2.zip Download ACE and set it aside for later on in this guide. We will walk through how to compile and make this work with the build.

2. TBB - https://github.com/01org/tbb/releases/download/2017_U5/tbb2017_20170226oss_win.zip  Download TBB and set it aside for later on in this guide. We will walk through how to compile and make this work with the build.




Great! You have all the Tools and dependencies you need. How will we piece this all together? Lets start by making sure you have your forked/cloned repository of the core:
Insert picture of cloning with the github desktop client <<<<
Insert picture of results after cloning with the github desktop client complete <<<


In addition to the above, we will want to pull in the database files in the release section of this repository. Convenience link here: 
https://github.com/elysium-project/server/releases

Specifically, you want to download:
https://github.com/elysium-project/server/releases/download/02.03.2017/WORLD_DB_02_03_2017.7z

and

https://github.com/elysium-project/server/archive/02.03.2017.zip

Insert picture of two files above downloaded.
The above are two different files and BOTH are necessary. These files will be used to build your databases later on in this guide.

Alright! Now we can get to building!

ESTABLISHING DEPENDENCIES

So that we do not get any errors while using the CMAKE tool we downloaded earlier - lets go ahead and establish + build the dependencies now. Lets make a folder for the eventual .dll files we will need from the build process. On your C drive, make a folder called "Dependencies".

Steps for installing dependencies:

Starting with ACE:

1. Take the ACE zip file we downloaded earlier and place it on your C drive.
2. Right click the Ace Zip file on your C Drive and select 7zip, then select 'Extract Here'.
 
3. 7Zip will begin extraction to a newly created folder called "ACE_wrappers". 
>>> Insert picture of extraction process

4. Take a look inside this folder and navigate to C:/ACE_wrappers/ace.

>>Insert image of inside of ACE_wrappers

5. Inside this subdirectory, create a new file in Notepad++ called "config", this file must have the file extension ".h", and inside this file make sure you type the following exactly as is: "#include "ace/config-win32.h". Save that file and move onto the next step. ****** IF YOU DO NOT DO THIS STEP CORRECTLY YOU WILL NOT BE ABLE TO DO STEP 9 AS YOU WILL GET A REPEATED ERROR WHEN TRYING TO BUILD.********

6. Once you've Step 5 go back up to the C:/ACE_wrappers drive. You should see a file called ACE_vc14. Since you installed the Visual Studio file from earlier you should see an icon that looks like the following:

>>> Insert VS Icon close up

7. Double click ACE_vc14 if you see the icon above and it should open up in Visual Studio. Be patient, it may take a while for Visual Studio to open up all the files associated with this solution:

8. Once visual studio is done initializing, you should see a window called "Solution Explorer" on your right hand side. if you do not see this window, then go to the Menu Bar on the top left and click on "View" and click on "Solution Explorer" which should be the first option.

Once you've done this, your screen should look similar to this: 
>>Insert solution picture here

9. Go ahead and right click the first item in that Solution of 721 Projects: "ACE". Hit Build. This process may take a long time and will be dependent on your CPU's processing power. You'll know its working if you take a look at the output pane and see it running through each associated build file:

>>>Insert ACE Build picture.

10. Once the build is complete - if it went successfully you will see the following output in the output pane: 

>>> Insert ACE Build Success.


11. The most relevant result in the output pane is the following: ACE_vc14.vcxproj -> C:\ACE_wrappers\ace\..\lib\ACEd.dll. That .dll file is the file we went through all these steps to obtain. Navigate to that output folder where the ACEd.dll file was made and copy it to your C:/Dependencies folder that you created earlier.

12. There is one final step remaining but we will defer it until we build the TBB dependency so that we can add TBB and ACE to System PATH all at once! I'll show you what this means further down.


NEXT DEPENDENCY: TBB

1. Just like with ACE, move the zipped TBB folder dependency that you downloaded earlier on into your C drive. Using 7zip, once again select the "extract here" option.

2. Rename the new folder created by the extraction to "TBB" for simplicity sake.

3. Go down into C:/TBB/bin/intel64/vc14. Here you will find two .dlls that you need, tbb.dll and tbbmalloc.dll. Grab those and copy them into your C:/Dependencies folder.






FINAL STEPS FOR DEPENDENCIES: Now that we have all the .dll files we needed, its time to add the C:/ACE_wrappers path to our SYSTEM Path so that our CMAKE tool can properly access them. 

To do this navigate to the Windows Control Panel and find "Edit the system environment variables". Click on this and a new window will appear. 

Click "Environment Variables..." at the bottom right.



>>> Insert picture of new window that appears when editing system variables. 


Under System variables (Lower portion of the new window) click "NEW" and type "ACE_ROOT" under Variable name, then "C:\ACE_Wrappers" under Variable value.

It should look like this:

>>> Insert picture of ACE_ROOT Path variable.

Click OK and lets add the next System Path variable by clicking New once again:

Type "TBB_ROOT" under Variable name, then "C:\TBB" under variable value and hit Ok.


After that, we've successfully set up our root Path Variables pointing to our depencies. Now the real fun begins.


Building with a usable Solution with CMAKE
---------------------------------------------------------------------------
A. Create a folder called "C:/Output".
B. Start up the CMake GUI tool downloaded earlier.
C. You should be brought to a screen that looks like the following:

>>> insert CMAKE image.

The numbered steps below correspond to the image above. Follow along!

1. This is the source folder - the core that we cloned/forked at the start of this guide.
2. An output folder for the CMAKE to build to.
3. The first button we will click, which will lead to the following window to popping up:
>>INSERT IMAGE OF Cmake Configure window
	i. Depending on the version of your Visual Studio, and your system's bit version - You will need to select the proper generator for the project. If you are utilizing Visual Studio 2017 on a 64 bit system, then you must select Visual Studio 15 2017 and 64 bit. In my case, I'm using Visual Studio 14 2015 WIN64:
	>>INSERT image of selection.
	ii. After you click configure you will see the following:
	>>Insert CMake Window after configure picutre
	iii. Now click Generate.
	iv. You should now see a new button called "Open" next to "Generate". Click this button and it will open it with the respective IDE you have installed (Visual Studio).

	
	
Building With Visual Studio!
---------------------------------------------------------------------------------

Almost there! 


1. Now that we have made the solution with CMAKE we can now properly build that solution. Inside Visual Studio open up solution explorer and you will see the following:
>>> Insert image of Visual Studio Build screen.
2. Right click "ALL_BUILD" and select the first option "Build".
3. Wait a few minutes while that process completes. You can keep an eye on the progress when your Visual Studio Icon begins to fill up with a Green Bar on your taskbar.
>>insert image of taskbar.
4. Now your .exe's are built! Follow the path to where they were built. In my case, you can see the path in the output pane of Visual Studio below:
>>> Insert visual studio pane. 











