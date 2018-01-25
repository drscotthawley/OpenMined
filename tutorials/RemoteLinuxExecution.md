# Executing OpenMined on a Remote Linux Server.

You've fully installed Unity, PySyft, and OpenMined on your Linux (e.g., Ubuntu 16.04) server, and you're logged in over ssh.


0. Your Linux machine has an X (X11) server running.  If it doesn't, you need to install one.  But we're not going to use X11 forwarding, i.e. no "ssh -Y" sort of connection; we will instead run Unity on the machine's local monitor (even though you won't be able to see it).  If your server *has no* monitor, then [follow this guide to set up a 'fake' X display](https://towardsdatascience.com/how-to-run-unity-on-amazon-cloud-or-without-monitor-3c10ce022639). 


1. Create a new directory UnityProject/Assets/Editor/ and inside place a C# script containing a method (MyEditorScript.Start) that will push the "Play" button for you.


        $ cat Assets/Editor/MyEditorScript.cs
        using UnityEngine;
        using System.Collections;
        using UnityEditor;

        class MyEditorScript { 
            static void Start() { 
                  UnityEditor.EditorApplication.ExecuteMenuItem("Edit/Play"); 
             }
        }

[credit: https://answers.unity.com/questions/1136444/automating-playmode-from-batch-and-c-executemethod.html]


2. Unity needs a MyEditorScript.cs.meta file as well, which in theory you can only get by running the Unity GUI.   **OR** you can just copy over a *different* .meta file, and manually make up a new "guid" string which is probably a bad idea but it works: (I changed a leading '7' to a '6' LOL)...

        $ cat Assets/Editor/MyEditorScript.cs.meta 
        fileFormatVersion: 2
        guid: 61d2145c185b043f8956955bd1c464b3
        timeCreated: 1512580258
        licenseType: Free
        MonoImporter:
          externalObjects: {}
          serializedVersion: 2
          defaultReferences: []
          executionOrder: 0
          icon: {instanceID: 0}
          userData: 
          assetBundleName: 
          assetBundleVariant: 

Same with the Editor/ directory: it needs an Editor.meta file which you can get by copying a different directory's .meta file and modifying the guid.  Probably better to actually let the GUI assign guids, but if you literally have no way to get there, this works. 

3. Invoke Unity from the command line, *using the main console's display* for the project you want, and execute the Play button script:

        $ DISPLAY=:0 ~/Unity-2017.3.0b1/Editor/Unity -projectPath ~/OpenMined/UnityProject -executeMethod MyEditorScript.Start

5. That's it!  You can now try the various OpenMined demos & tutorials remotely, e.g. by [port-forwarding your jupyter notebooks](https://drscotthawley.github.io/How-To-Port-Forward-Jupyter-Notebooks/). 

Now, you won't be able to see the OpenMined log messages that appear in the Unity window.  Presumably there's a  find a way to access those logs from the command line...? 
