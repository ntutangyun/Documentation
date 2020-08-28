# Troubleshooting

Important: if you have run a previous version of LGSVL Simulator on your machine, then the user interface will display maps, vehicles, clusters, and simulation configurations from the database of configurations from your previous version. This may result in incompatibility issues. It also will not update default configurations available with the latest release. 

To solve this issue, you can update the link for a particular map or vehicle with the URL for the specified content. For default content, you can find the links on the [LGSVL Simulator Content website](https://content.lgsvlsimulator.com). When you update a link, you will trigger a fresh download of the correct AssetBundle. A simulation configuration will be valid once the map and vehicle(s) specified are valid and it is run (by pressing the play button).

Alternatively, you can delete the folder “LG Silicon Valley Lab”, in the location below:

| Platform | Filepath |
|----------|---------------------------------------------|
| Windows | %APPDATA%\..\LocalLow\LG Silicon Valley Lab |
| Linux | ~/.config/unity3d/LG Silicon Valley Lab |

NOTE: This will delete all existing simulation configurations and log files related to LGSVL Simulator.

You can then restart the LGSVL Simulator binary executable, and the folder will be created again with default configurations.

## Logs

Logs can be found at the following locations:

| Version        | Location                                    |
|----------------|---------------------------------------------|
| Windows Binary | %APPDATA%\..\LocalLow\LG Silicon Valley Lab |
| Windows Editor | ~/.config/unity3d/LG Silicon Valley Lab     |
| Linux Binary   | ~/.config/unity3d/Unity/Editor/Player.log   |
| Linux Editor   | ~/.config/unity3d/Editor.log                |

## Unity Help
Please see our Unity Help [document](unity-help.md) for additional tips and troubleshooting guides when working with Unity Editor in Developer Mode.

## Frequently Asked Questions (FAQ)

You can find our FAQ [here](faq.md), which may be helpful in answering common questions or issues that may arise.

