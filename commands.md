# Comandi Scripts per Space Engineers

SCENARIO SCRIPTS
Scenario scripts were introduced in SpaceEngine 0.990. Older versions does not supports scripts.

This manual describes how to make scenario scripts in SpaceEngine. Before continuing, it is recommended to read this manual: Introduction

SpaceEngine console and *.se files
SpaceEngine 0.990 introduced a new type of file – *.se. These are scenario script files. They contains code which can be executed by SpaceEngine. A great example of what scenarios are capable of are Tutorials, introduced in 0.990. The default location for *.se files is data/scripts/ or addons/*/scripts/. You can run an *.se file from SpaceEngine by opening the console ([~] key by default) and typing this command:

Code
run filename
If the file filename.se exists in the mentioned folders, it will be executed. Another way to run *.se files is by passing them into SpaceEngine’s executable through a command line argument. The best way to do that is to associate the *.se extension with SpaceEngine.exe in your system. In that case double-clicking on the file will launch SpaceEngine and order it to execute the scenario script. If SpaceEngine is already running, this running instance will execute the script.

All commands could be executed in the 'direct mode' by typing them in the console, or executed from a *.se script file. Commands and most of their parameters are not case-sensitive, but string constants such as names of GUI widgets and names of triggers are case-sensitive.

Some commands can have (or even must have) additional parameters, provided in the curly braces block (i.e. those commands are actually tags). Example:

Code
Goto
{
Time 5.5
Dist 25000
}

Additional parameters could be typed in a single row:

Code
Goto { Time 5.5 Dist 25000 }
Using the single-row style sometimes makes the script code more compact and easy to read and understand.

All commands are split into groups according to their functions. Unroll the blocks below to read details.

SCRIPT CONTROL COMMANDS
Run
Runs a *.se script. It is possible to run one script from another, up to 16 nested calls are allowed. This is useful for executing a common set of commands, or to add a camera spline path from another script file (see the Camera spline path chapter for details). Syntax:
Run filename - executes the filename.se script file located in the data/scripts/ or addons/*/scripts/ folder.
Run "path/filename.sc" - executes the script file located at the provided path. Path is relative to SpaceEngine's folder.

Break
Interrupts the currently executed script. If there was multiple scripts running, they will all be interrupted. Used primarily in direct mode (from the console) to break the script, but could be called from the *.se file.

Wait
Delays script execution by specified time in seconds. Syntax:
Wait 3.5 - delays script by 3.5 seconds.
This is one of the most useful command in SE scripts. You will use it after many commands, which require delays before the next command. For example, when you call the Goto command with parameter Time 10 (duration of the flight - 10 seconds), you probably want to follow it by Wait 10 to delay the script execution until the Goto command completes.

CheckVersion
Checks if the current version of SpaceEngine corresponding the version number provided in the command. If not, the following error message will be displayed to the user: "This script was designed for a different version of SpaceEngine. It cannot be run in the current version, sorry". Syntax:
CheckVersion 990 - for SE version 0.990

if, elif, else, endif
This is the conditional operator statement. Syntax:
if { Variable == 1.0 }

if_commands
elif { Variable >= 2.0 }

elif_commands
else

else_commands
endif
The Variable is a name of SpaceEngine variable (see Variables chapter for details). The == is a comparison operator, possible operators are:
== (equal)
!= (not equal)
< (less)
> (greater)
<= (not greater)
>= (not less)
The value to the right of the operator is numerical for the integer, float and double variables, or 0/1 or true/false for boolean variables (see Variables chapter for details).
If the first condition (after if) is satisfied, then all the if_commands will be executed; after that control will be given to the first command after endif.
If the first condition is not satisfied, then control will be given to the first elif, if present, or to the else, if present, otherwise to the first command after endif. The elif statement works like if: if condition is satisfied, then all the elif_commands will be executed; after that control will be given to the first command after endif. Otherwise control will be given to the next elif or else, if they are present, otherwise - to the first command after endif. So you may use multiple elif statements. They can have conditions for other variables than if, statements are fully independent.
If the else statement is present and neither of the if and/or elif conditions were satisfied, all the else_commands will be executed, and after that control will be given to the first command after endif.
Curly braces for the conditions and the endif are mandatory, otherwise the "Syntax error" message will be shown. The elif and else statements are optional.
Important: nested if statement is not yet supported, i.e. you cannot do something like this:
if { Var1 == 1 } if { Var2 == 0 } command endif endif.
The error message "Nested IF statement is not supported" will be shown in this case.

Loop, EndLoop
This is a simple loop statement. Syntax:
Loop

commands
EndLoop
This repeats all commands enclosed between the Loop and EndLoop infinitely. Use Wait command there to delay execution.

CAMERA CONTROL COMMANDS
FOV
Sets field-of-view in degrees. Syntax:
FOV 90

Select
Selects a space object, ship, waypoint or spline path. Equivalent of a mouse click on the object, selecting it using search tool, etc. If the command succeeds, the selection pointer will be switched to the selected object. If the command fails, an error message will be shown in the console, and further execution of the scenario could be wrong. Syntax:
Select Earth - selects a space object by its name. Quotes can be skipped if there are no spaces in object's name.
Select "RS 8474-2738-5-27954-210 1" - selects a space object by its name. Use quotes if there are spaces in the object's name.
Select "Waypoint 1" - selects a previously defined waypoint (see Waypoint).
Select "Path 1" - selects a previously defined spline path (see SplinePath).
To resolve name ambiguity, prepend object's name with its parent name, separated by |. For example:
Select "Saturn|Pandora" - selects Pandora (satellite of Satrun)
Select "Sol|Pandora" - selects asteroid (55) Pandora.

Unselect
Removes any selection. The selection pointer will disappear.

Goto
Moves the camera to the selection. If there is no object selected, an error message will be shown in the console, and further execution of the scenario could be wrong. Syntax:
Goto - starts a flight with default parameters.
Goto { ... } - starts a flight with custom parameters provided below:
{

Time 15.0 - duration of the flight in seconds; default is 2.
AccelTime 2.0 - duration of acceleration phase in seconds; default is Time/2.
DecelTime 2.0 - duration of deceleration phase in seconds; default is Time/2.
DriftTime 10.0 - duration of constant speed phase in seconds; default is 0.
AccelPower 2.0 - steepness of acceleration; default is determined by the parameter GotoSpeed or GotoSpeedVR in the config file.
DecelPower 2.0 - steepness of deceleration; default is determined by the parameter GotoSpeed or GotoSpeedVR in the config file.
Dist 0.001 - stop point distance from the target in pc; no default.
DistKm 12500.0 - stop point distance from the target in km; no default.
DistRad 2.5 - stop point distance from the target in multiples of the target's radii; default depends of object type, 2 for planets.
HeightKm 150.0 - stop point height from the target in km; no default.
Lon 86.4 - stop point longitude in degrees; no default.
Lat 13.5 - stop point latitude in degrees; no default.
Lon 86 25 13 - stop point longitude in degrees minutes seconds; no default.
Lat 13 30 29 - stop point latitude in degrees minutes seconds; no default.
Lon ( 86 25 13 ) - stop point longitude in degrees minutes seconds; no default.
Lat ( 13 30 29 ) - stop point latitude in degrees minutes seconds; no default.
Up 0.871, 0.124, -0.355 - stop point up vector; no default.
Up ( 0.871, 0.124, -0.355) - stop point up vector; no default.
Yaw 60.3 - stop point yaw Euler angle in degrees; no default.
Pitch 21.6 - stop point Pitch angle in degrees; no default.
Roll 35.5 - stop point Roll angle in degrees; no default.
Center false - do not rotate camera to the target; default is true (do rotation).
}
If Lon and/or Lat are provided, the end point binding mode will be "SyncRot".
If no stop-point rotation-parameters were provided, the camera will end up rotated towards the center of the selected object.
If selection is a spline path, camera will go to its first knot point position and change its orientation to the first knot point orientation.
If you want to instantly "teleport" to the selection, use Time 0.
If you want to end the flight with the camera looking straight at the object, don't specify any orientation parameters, or use Yaw 0 Pitch -90 Roll 0.
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command, with wait time equal to those in the Time parameter.

Center
Starts a turn toward the selection. If there is no object selected, an error message will shown in the console, and further execution of the scenario could be wrong. Syntax:
Center - starts a turn with default parameters.
Center { ... } - starts a turn with custom parameters provided below:
{

Time 15.0 - duration of the turn in seconds; default is 2.
}
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command, with wait time equal to those in the Time parameter.

Horizon
Starts a turn toward the selection's horizon. If were no object selected, an error message will shown in the console, and further execution of the scenario could be wrong. Syntax:
Horizon - starts a turn with default parameters.
Horizon { ... } - starts a turn with custom parameters provided below:
{

Time 15.0 - duration of the turn in seconds; default is 2.
}
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command , with wait time equal to those in the Time parameter.

GotoLocation
Immediately moves (teleports) the camera to the specified location. Depending on the complexity of the location scene, loading may take a while. You may use FadeOut/FadeIn combined with WaitTrigger "LoadingComplete" to hide the loading process. If the current SpaceEngine version differs from the version number specified in the location code, a warning message will be shown to the user. of Syntax:
GotoLocation "Volcano under rings" - go to a location from the Locations browser (F6 menu).
GotoLocation "Location name" { ... } - go to a location, whose code is defined directly in the scenario script in the curly braces block. Always use this method if the location is not one of the default SE locations, otherwise your scenario will not work for other users. To generate the code, add a location in the Location browser, then press "Share" -> "Copy script code to clipboard", and paste the location code from the clipboard to the scenario script. Then change Place to GotoLocation
Note: the location saves the current stereobase (sense of a scale in 3D/VR modes). You may use this fact to setup a custom stereobase when starting a scenario. Another way to setup the stereobase is using the Stereobase variable (the command Set Stereobase XXXX, see the Variables chapter for details).

GotoURL
Similar to GotoLocation, but uses a location URL instead of code. If the current SpaceEngine version differs from the version number specified in the URL, a warning message will be shown to the user. Syntax:
GotoLocation "se://v=981&m=1&n=%CB%F3%ED%E0%20%C3%..." - go to a specified location URL. Use this as a more compact alternative of the GotoLocation. To generate the URL, add a location in the Location browser, then press "Share" -> "Copy URL to clipboard", and paste the location URL from the clipboard to the scenario script. Enclose it with quotes and add GotoURL before it.

Follow
Changes camera binding mode to "Follow": the camera moves together with the selected object's center, i.e. ignoring its rotation. If a new object is selected, the currently followed object changes to that object, otherwise only the binding mode changes.

SyncRot
Changes camera binding mode to "SyncRot": the camera moves together with the selected object's surface, i.e. keeps itself in rest relative to selection's rotating surface. If a new object is selected, the currently followed object changes to that object, otherwise only binding mode changes.

Free
Changes camera binding mode to "Free": no binding to any object, camera stays at rest in space. The currently followed object clears.

Track
Enables tracking the selected object: camera rotates so that the tracking object stays in the center of the screen. Changing the selected object will not affect tracking.

Untrack
Disables tracking mode.

MoveMode
Changes camera motion mode (free/spacecraft/aircraft). Syntax:
MoveMode 2
Possible values:
1 - free mode (no inertia, flight direction follows the orientation);
2 - spacecraft mode (motion and rotation inertia, flight direction is independent of orientation);
3 - aircraft mode (motion and rotation inertia, flight direction follows the orientation).

Speed
Sets assigned camera speed in parsecs per second. Syntax:
Speed 2.5 - set a speed of 2.5 pc/s in the Spectator motion mode, otherwise acceleration of 0.25 pc/s2 will be set (in modes with inertia - Spacecraft and Aircraft).

SpeedKm
Sets assigned camera speed in kilometers per second. Syntax:
SpeedKm 123.4 - set a speed of 123.4 km/s in the Spectator motion mode, otherwise acceleration of 0.01234 km/s2 will be set (in modes with inertia - Spacecraft and Aircraft).

Fly
Starts camera moving in a view direction. Syntax:
Fly - starts moving with default parameters.
Fly { ... } - starts moving with custom parameters provided below:
{

Axis (0, 0.707, 0.707) - motion direction vector, relative to the current camera orientation; default is (0, 0, 1) - flying forward.
Speed 5.5 - motion speed in parsecs per second; no default.
SpeedKm 17.6 - motion speed in kilometers per second; default is 100.
FadeTime 1.5 - smooth start duration in seconds; default is 2.
Func "Cubic" - smooth start function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command, with some wait time to see the motion effect.

StopFly
Smoothly stops camera motion in the motion modes that have inertia, or cancels out the Fly command. Syntax:
StopFly
StopFly { ... } - starts moving with custom parameters provided below:
{

FadeTime 1.5 - smooth stop duration in seconds; default is 2.
Func "Cubic" - smooth stop function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}

Turn
Starts turning (rotating) camera around its own axes. Syntax:
Turn - starts turning with default parameters.
Turn { ... } - starts turning with custom parameters provided below:
{

AngularSpeed 45.0 - turn rate in degrees per second; default is 10.
Axis (0, 0.707, 0.707) - axis around which turn occurs; default is (0, 1, 0) - vertical axis of the camera.
FadeTime 1.5 - smooth start duration in seconds; default is 2.
Func "Cubic" - smooth start function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command, with some wait time to see the motion effect.

StopTurn
Cancels out the Turn command. Syntax:
StopTurn - stops orbiting with default parameters.
StopTurn{ ... } - starts orbiting with custom parameters provided below:
{

FadeTime 1.5 - smooth stop duration in seconds; default is 2.
Func "Cubic" - smooth stop function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}

Orbit
Starts circular motion ('orbiting') around selected object. Syntax:
Orbit - starts orbiting with default parameters.
Orbit { ... } - starts orbiting with custom parameters provided below:
{

AngularSpeed 45.0 - orbiting rate in degrees per second; default is 10.
Axis (0, 0.707, 0.707) - axis around which orbiting occurs; default is (0, 1, 0) - polar axis of an object.
FadeTime 1.5 - smooth start duration in seconds; default is 2.
Func "Cubic" - smooth start function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}
During orbiting, camera orientation changes in that way so the object remains static on the screen. Use the Goto/GotoLocation/GotoURL commands before this one to set a proper initial orientation.
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait command, with some wait time to see the motion effect.

StopOrbit
Cancels out the Orbit command. Syntax:
StopOrbit - stops orbiting with default parameters.
StopOrbit { ... } - starts orbiting with custom parameters provided below:
{

FadeTime 1.5 - smooth stop duration in seconds; default is 2.
Func "Cubic" - smooth stop function; default is "Linear", possible values: "Linear", "Quadric", "Cubic", "Sin".
}

UserMoveControl
Limits user movement ability. Sytax:
UserMoveControl "disabled" - disables user's movement ability. Camera cannot be moved by user.
UserMoveControl "free" - restores user's movement ability.
UserMoveControl "limited" { ... } - limits user's movement ability by a some distance from the specified object, and/or limits user movement speed:
{

Center "Mars" - name of object, relative to which movement will be limited. Format is same as in the Select command (prepend name with a parent object name and | to resolve naming ambiguity).
Dist 0.1 - the greatest allowed movement distance from the object in parsecs.
DistLy 0.3 - the same in light years.
DistKm 10000 - the same in kilometers.
DistRad 2.5 - the same in object's radii.
Speed 0.5 - maximum user movement speed in parsecs per second.
SpeedKm 500 - the same in kilometers per second.
}
You can use either distance limiting or speed limiting, or both.

UserRotationControl
Limits user rotation ability. Sytax:
UserRotationControl "disabled" - disables user's rotation ability. Camera cannot be turned by user.
UserRotationControl "free" - restores user's rotation ability.

UserTimeControl
Limits user time control ability. Sytax:
UserTimeControl "disabled" - disables user's time control ability. Simulation date/time cannot be altered by user.
UserTimeControl "free" - restores user's time control ability.
UserTimeControl "limited" { ... } - limits user's time control ability by a specified date/time range and/or time speed range:
{

TimeRange ("2017.02.01 12:00:00", "2017.02.28 11:59:59") - date/time range, array of two strings representing starting and ending dates, between which user is allowed to set the simulation time.
TimeRateRange (1.0, 10.0) - time speed range, array of two floating-point nubmers representing minimum and maximum allowed time speed.
}
You can use either time range limiting or time speed limiting, or both.

UserStereobaseControl
Limits user stereobase control ability. Sytax:
UserStereobaseControl "disabled" - disables user's stereobase control ability. 3D deteobase cannot be altered by user.
UserStereobaseControl "free" - restores user's stereobase control ability.
UserStereobaseControl "limited" { ... } - limits user's stereobase control ability by a specified range:
{

Range (1.0, 1000.0) - stereoabse range, array of two floating-point nubmers representing minimum and maximum allowed stereoabse.
}

WAYPOINTS
Waypoints are a special 'virtual' objects that can be displayed to the user and used in the script. Waypoints can be selected with the Select command using its name, then the Goto or Center command can be called to move/rotate the camera to that waypoint. They also can be used with triggers to detect certain events, such as a reaching a specific point in space, by using the WaitTrigger "Object|Approach" command. See the Triggers chapter for details. Waypoint's name can contain abbreviations and BB codes, including the [LOC] code to make translations to other languages. The text label near the waypoint can be controlled independently from the waypoint itself (i.e. can be hidden, faded out, changed in color etc).

Waypoint script commands
Waypoint
Adds (defines) a new waypoint, or modifies an existing one. If a waypoint with such name has been defined in the script previously, it will be updated with the provided data, otherwise a new waypoint will be added. Syntax:
Waypoint "Name" { ... } - adds/modifies a waypoint with name "Name", with parameters provided below:
{

Parent "Pandora|Sol" - name of a parent object, i.e. object to which this waypoint is bound. It has the same format as in the Select command ("Parent|Object").
Visible false - is the waypoint visible to the user or not. Use false to hide previously defined waypoint, and true to show it again. Default is true.
Label true - is the waypoint's label visible (text, equal to its Name). Default is true.
Radius 0.25 - radius in parsecs.
RadiusKm 500.0 - radius in kilometers; default is 1.
Roll 45.0 - rotation angle of the waypoint model in degrees; default is 0.
Color (1.0, 0.8, 0.0, 0.6) - color in (R, G, B, A) format (A is opacity); default is white - (1, 1, 1, 1).
ColorEmpty (0.2, 0.2, 0.0, 0.6) - color in the empty (non-filled) animation state in (R, G, B, A) format (A is opacity); default is transparent black - (0, 0, 0, 0).
LabelColor (1.0, 0.8, 0.0, 0.6) - color of the text label in (R, G, B, A) format (A is opacity); default is equal to Color, if specified.
FadeRange (10, 20, 900, 1000) - distance range, at which the waypoint is visible, in parsecs. First two values in vector are distance range of smooth transition form invisible to visible state, last two values are distance range of back transition from visible to invisible. Default values are (1e38, 0, 0, 1e38) effectively disables transitions (makes waypoint visible at all distances).
FadeRangeKm (10, 20, 900, 1000) - the same as FadeRange, in kilometers.
LabelFadeRange (10, 20, 900, 1000) - the same as FadeRange, in parsecs, for waypoint's label.
LabelFadeRangeKm (10, 20, 900, 1000) - the same as LabelFadeRange, in kilometers, for waypoint's label.
LineWidth 10 - width of the line in pixels; default is 2.
Shape "Diamond" - shape of the waypoint which will be displayed to the user. Allowed shapes are: "Circle", "Triangle", "Square", "Diamond", "Hexagon", "Billboard", "Sphere", "WireSphere". The latter two are 3-dimensional. Default is "Circle".
StaticPosXYZ (1.0, 0.5, 0.7) - static Cartesian coordinates (x, y, z) relative to the parent object in parsecs.
StaticPosXYZKm (110.5, 57.3, 16.7) - static Cartesian coordinates (x, y, z) relative to the parent object in kilometers.
StaticPosPolar (32.9, 60.3, 1.65) - static polar coordinates (latitude, longitude, distance) relative to the parent object; lat/lon in degrees, distance in parsecs.
StaticPosPolarKm (32.9, 60.3, 7500.0) - static polar coordinates (latitude, longitude, distance) relative to the parent object; lat/lon in degrees, distance in kilometers.
FixedPosXYZ (1.0, 0.5, 0.7) - fixed Cartesian coordinates (x, y, z) relative to the parent object in parsecs.
FixedPosXYZKm (110.5, 57.3, 16.7) - fixed Cartesian coordinates (x, y, z) relative to the parent object in kilometers.
FixedPosPolar (32.9, 60.3, 1.65) - fixed polar coordinates (latitude, longitude, distance) relative to the parent object; lat/lon in degrees, distance in parsecs.
FixedPosPolarKm (32.9, 60.3, 7500.0) - fixed polar coordinates (latitude, longitude, distance) relative to the parent object; lat/lon in degrees, distance in kilometers.
Texture "data/textures/common/logo0990.*" - path to the image file for the billboard waypoint.
TexCoord (0.5, 0.5, 0.2, 0.5) - texture coordinates in relative units: (x offset, y offset, width, height). Value of 0.5 means 50% of the texture dimension (width or height), 0.25 means 25% and so on. The specified area of the texture will be displayed. Default is (0, 0, 1, 1) - display the whole texture.
TexCoordPix (0.5, 0.5, 0.2, 0.5) - texture coordinates in pixels: (x offset, y offset, width, height). The specified area of the texture will be displayed.
}
Static coordinates are equivalent to the 'Follow' mode, while fixed coordinates are equivalent to the 'SyncRot' mode. In the first case, waypoint is bound to the parent object's center, i.e. moves together with the object, ignoring its rotation. In the second case, it moves and rotates together with the object, i.e. remains fixed relative to the object's surface.
Waypoint with shape "Circle" supports filling-like animation, when used with the WaitTrigger "Object|See" (see Triggers for details). It starts as the circle of the color ColorEmpty, and while user looking on it during the Delay seconds (this is the trigger's parameter), it filling up clockwise with the color Color.
Waypoint with shape "Billboard" is alpha-blended textured billboard (a rectangle always oriented to the user). Parameter Texture specifies the image used to texture the billboard. If not specified or image file does not exist, the default 'No image' texture will be used. Parameters TexCoord or TexCoordPix specifies which part of the image to use. If not specified, the whole image will be displayed. Parameter Color specifies color modulation of the texture and its alpha (opacity) channel.

DeleteWaypoint
Deletes a previously added waypoint. Syntax:
DeleteWaypoint "Name"

ClearWaypoints
Deletes all waypoints.

CAMERA SPLINE PATHS
The camera spline path is a powerful tool to make custom flybys. Using the Camera path editor, you can 'record' your manual flight, and save it to a script file for a further use. This includes:

- replaying a recorded path in the Camera path editor;
- replaying a recorded path while simultaneously capturing video in a high quality rendering mode (offline video rendering);
- replaying a recorded path in a script using the PlaySplinePath command.

Spline is a mathematical function which is used for a smooth interpolation of some value (in SpaceEngine, camera coordinates and orientation) between a discrete number of data points, called knots. In other words, you may have, say, only 50 knots - data points which represents fixed position and orientation in space - and perform a smooth movement between them. This is a very compact and convenient way to record a flights.

Spline path script commands
SplinePath
Adds (defines) a camera spline path. Syntax:
SplinePath "SplineName" { ... } - adds a spline path with a name "SplineName" with parameters provided below:
{

Body "PI Hya 7" - name of a reference object.
Parent "PI Hya" - name of a reference object's parent.
SyncRot true - spline path binding mode: SyncRot if true, Follow if false.
Duration 52.0 - replay duration in seconds.
PosSpline "Catmull-Rom" - a type of position interpolation spline.
RotSpline "B-spline" - a type of orientation interpolation spline.
FollowQuat (1, 0, 0, 0) - initial camera orientation in the Follow binding mode.
SplineData { ... } - array of knot points: multiple lines in the form of (time, pos.x, pos.y, pos.z, rot.w, rot.x, rot.y, rot.z), enclosed in a curly braces.
}
You may type all the data points into the SplineData { } tag manually, but using the Camera path editor is a more convenient way.

DeleteSplinePath
Deletes a camera spline path. Syntax:
DeleteSplinePath "SplineName"

ClearSplinePaths
Deletes all camera spline paths. Syntax:
ClearSplinePaths

PlaySplinePath
Starts flight through the spline path. Syntax:
PlaySplinePath "SplineName" - starts flight through the spline path "SplineName" with default parameters.
PlaySplinePath "SplineName" { ... } - starts flight through the spline path "SplineName" with custom parameters provided below:
{

Time 30.0 - overrides the flight duration; default value is a spline duration described in the SplinePath command.
}
Seeing as the camera is being teleported to the first spline's knot point and starts smooth moving through other knot points, it is a good idea to smoothly move the camera to the first knot point before calling this command. To do this, use the Goto "SplineName" command.
Important: the next script command will be executed immediately after this one. You must follow this command with the Wait, with a wait time equal to the Time parameter of the spline path duration, to delay the execution of the next commands until the spline flight completes. Other commands may be executed during the spline flight, except commands which change the camera position/orientation.

Using the Camera path editor
Open the Camera path editor in the Main menu > Editor menu. Configure the path parameters: its name, types of splines, how often to add a knot point. Start recording a path by pressing the [Record] (red round) button; when done, stop recording by pressing the [Stop] (white square) button. You may visualize the recorded path by ticking the 'Display path curve', and replay it by pressing the [Play] (white triangle) button. It is possible to add knot points manually while recording .



In the current version of SpaceEngine, it is impossible to edit the recorded spline, but this functionality will be added in future. You may only change the spline type and replay duration.

Once completed, save the file to the desired directory, for example addons/my_addon/scripts/path.ssp. The saved file is a usual SE scenario script file, which contains only one command SplinePath with some parameters and data points. It has the *.ssp extension just to distinguish it from a usual script files *.se in the open/save file dialog. You may use this file further, either to it load back into the editor, or to replay the recorded path from another script. There are two ways to achieve this:

- simply open the saved file, copy its content and paste into your script;
- call the script file with a recorded path from your script using the Run command:

Code
Run "path.ssp"
After defining the spline path in the script by one of these methods, you may replay it by the PlaySplinePath command:

Code
PlaySplinePath "PathName"
Here "PathName" is the same name used in the SplinePath command (either in the *.ssp file or directly in your script). It is a good idea to smoothly move camera to the first spline's knot point before starting the replay. Use the Goto "PathName" to do this.

The spline path is bound to some space object. By default, it is the same object that is bound to the camera when you started recording the path. It has two binding modes: Follow and SyncRot. In the Follow mode, spline moves together with the object, ignoring its rotation. In the SyncRot, it moves and rotates together with the object, i.e. remains fixed relative to the object's surface. The first is useful for a deep-space path recording, while the second is best for flights above the planetary terrain. You may switch modes by the 'SyncRot' checkbox in the Camera path editor.

You may set any custom date/time and speed up the time flow during path replay (either manually or by the time control script commands), and this will not affect the motion of the camera relative to the object or its surface. This is useful for making a 'timelapse' effect.

Usage of the Camera path editor and recording a video with it is summarized in this video. Note that it may be out of date; the tools shown in your version of SpaceEngine are likely much more advanced.



TIME CONTROL COMMANDS
StopTime
Pauses timeflow.

StartTime
Resumes timeflow: sets the time rate to the value it has before pausing by the StopTime command or by user actions.

TimeScale
Sets a time rate (speed). Syntax:
TimeScale 100.0 - sets a time rate of 100x times faster than real time.
Use negative values to reverse direction of timeflow. Use value of 1 to set the real time rate. Don't use a value of zero to stop the time flow, use the StopTime command instead.
TimeScale is also a variable, so it's possible to use Set TimeScale XXX, Interpolate TimeScale { ... }, if { TimeScale > XXX } and other commands to manipulate variables. See Variables chapter for details.

Date, Time
Sets specified date and time. Both commands are equal. Syntax:
Date "2017.02.08 17:29:34.65" - sets date and time with millisecond precision. Format is "YYYY.MM.DD hh:mm:ss.sss". You may omit the smaller values (i.e. limit precision up to seconds, minutes, days and months); in that case omitted terms will be considered as zero (hours, minutes and seconds) or one (months, days). Example:
Date "2017.02.08 17:29" - sets 17:29:00 on 08 February 2017.
Date "2017.02.08" - sets 00:00:00 on 08 February 2017.
Date "2017.02" - sets 00:00:00 on 01 February 2017.
Date "2017" - sets 00:00:00 on 01 January 2017.
Date "current" - sets the current date according to the system clock.

TRIGGERS
Triggers are events which can occur while SpaceEngine is running: user inputs, variable changes, position/time matches, etc.

WaitTrigger
Delays script execution until some event happens. Syntax:
WaitTrigger "TriggerName" - wait until event "TriggerName" happens, some default parameters could be used.
WaitTrigger "TriggerName" { ... } - wait until event "TriggerName" happens, with parameters provided below:
{

Object "Sol|Earth" - name of a SpaceEngine object (planet, galaxy etc) or a waypoint.
Type "Planet|Desert" - type of a SpaceEngine object. See the table below for details.
Dialog "Universe map" - name of a dialog window, toolbar or any GUI widget. See SetWidgetStyle for details.
Mode "AND" - modifies default behaviour or interval checking for some triggers. Possible values: "OR", "AND", "Crossed", "Earlier", "Later", "InRange", "OutRange". Defaults are "OR" and "Crossed".
Time "2017.02.09 22:05:15" - date/time value, format is identical to the Date command.
TimeRange ("2017.02.09 22:05:00", "2017.02.09 22:06:00") - date/time range, array of two strings representing two dates.
MaxWaitTime 5.5 - maximum wait time in seconds.
Value 5.5 - value of a variable.
ActivationDelay 5.5 - delay before trigger activation in seconds.
AnimateObject "Fill" - animate of the Object (typically, a waypoint) while delay before activation is in progress.
Rect (0.2, 0.8, 0.1, 0.9) - rectangular area on the screen, in format (left, right, top, bottom); default is (0, 0, 1, 1).
Dir (0.27, 0.63, -0.46) - direction vector.
Direction (0.27, 0.63, -0.46) - the same as Dir.
Range (28.5, 36.4) - range of change of some value; units are parsecs, parsecs per second etc (depends on a trigger).
RangeKm (28500, 36400) - the same as Range for some triggers, units are kilometers or kilometers per second.
RangeRad (1.5, 2.5) - the same as Range for some triggers, units are object's radii.
LonLatRange (48, 49, 36, 38) - range of change in a longitude and latitude of a camera position over a planet; units are degrees; format is (min lon, max lon, min lat, max lat).
YawRange (-15, 15) - range of change in a camera yaw angle in degrees.
PitchRange (-20, 20) - range of change in a camera pitch angle in degrees.
RollRange (-5, 5) - range of change in a camera roll angle in degrees.
}

If the ActivationDelay parameter is provided, the system waits specified amount of seconds after the trigger activation before throwing the actual activation event (giving control to the next script command). If the trigger activation conditions were broken (for example, user stopped looking at the object in "Object|See" trigger), this delay timer resets. The delay times can be used to animate the object, to which the trigger is linked (typically, the waypoint). The animation style is defined by the AnimateObject parameter:
"Fill" - fill the circle-shaped waypoint clockwise with another color (ColorEmpty to Color);
"FadeIn" - fade the waypoint in;
"FadeOut" - fade the waypoint ont;
"Rotate" - rotate the waypoint (works only with 2D shapes).

BeginMultitrigger
Start a sequence of multiple WaitTrigger commands. Script execution will be delayed until one or all triggers in the section are triggered. Syntax:
BeginMultitrigger "OR" - wait for one of the provided triggers.
BeginMultitrigger "AND" - wait for all of the provided triggers.

EndMultiTrigger
Start a sequence of multiple WaitTrigger commands. Syntax:
EndMultiTrigger - wait for one of the provided triggers.

Use the multi-trigger system if you want to wait for multiple triggers. For example, if you want to wait until the user selects Earth, then centers on it, use this code:

Code
BeginMultiTrigger "AND"
WaitTrigger "Object|Select" { Object "Earth-Moon|Earth" }
WaitTrigger "Control|Center"
EndMultiTrigger
List of triggers
There are many triggers available. Choose a needed trigger by specifying its name. Here is a description of all triggers, events which activate them, and parameters which triggers use.

Trigger name	Activation event	Used parameters
"Control|Rotate"	Camera rotates	Mode, YawRange, PitchRange, RollRange
Mode "OR" - activates if one of the provided angles are out of range
Mode "AND" - activates if all of the provided angles are out of range
"Control|Orbit"	Camera orbits some object
"Control|Move"	Camera moves	Range, Dir, Direction
Activates if camera passes a specified distance range, and/or moves in a specified direction (with accuracy of 1%)
"Control|StopRotate"	Camera stops rotation	
"Control|StopOrbit"	Camera stops orbiting	
"Control|StopMove"	Camera stops moving	
"Control|Follow"	Camera binding mode changes to "Follow"	
"Control|SyncRot"	Camera binding mode changes to "SyncRot"	
"Control|Free"	Camera binding mode changes to "Free"	
"Control|Track"	Tracking of the object enables	
"Control|Untrack"	Tracking of the object disables	
"Control|ChangeSpeed"	Assigned camera speed changes	Range
Activates if the assigned camera speed falls within the range
"Control|ChangeFOV"	Camera FOV changes	Range
Activates if the camera FOV falls within the range
"Control|ResetFOV"	Camera FOV resets to default value	
"Control|Goto"	The 'Go to' command activates	Object, Type
If Object is not specified, then the currently selected object is used.
Trigger activates if the command is applied to that object and/or type of objects.
"Control|GotoCenter"	The 'Go to center' command activates
"Control|Land"	The 'Land' command activates
"Control|Center"	The 'Center' command activates
"Control|Horizon"	The 'Horizon' command activates
"Control|MapCenter"	The Universe map centeres on object
"Control|MapFocus"	The Universe map focuses on object
"Control|MapGoto"	The Universe maps 'Go to' command activates
"Control|ChartCenter"	The Chart centeres on object
"Control|ChartFocus"	The Chart focuses on object
"Control|MapZoom"	The Universe map zoom changes	Range
Activates if the map/chart zoom is out of range
"Control|ChartZoom"	The Chart zoom changes
"Control|ChartMoveX"	The Chart scrolls in X direction
"Control|ChartMoveY"	The Chart scrolls in Y direction
"Control|ChartMoveZ"	The Chart scrolls in Z direction
"Control|MapHorizon"	The Universe map rotates to horizon (not implemented!)	
"Control|ChartRotate"	Planets in the Chart rotates	
"Control|SetTime"	Simulation date/time changes	
"Control|SetCurrentTime"	Simulation data/time sets to the current system clock	
"Control|SetTimeRate"	Time flow speed changes	Range
Activates if the time flow speed fits in the range
"Control|PauseTime"	Time flow pauses	
"Control|ResumeTime"	Time flow resumes	
"Control|ReverseTime"	Time flow direction reverses	
"Time"	Simulation time crosses the specified date or range of dates	Mode, Time, TimeRange
Mode "Crossed" - activates if sim time crosses the Time value
Mode "Earlier" - activates if sim time becomes earlier than the Time value
Mode "Later" - activates if sim time becomes later than the Time value
Mode "InRange" - activates if sim time goes in the TimeRange range
Mode "OutRange" - activates if sim time goes out of the TimeRange range
"Object|Select"	Object is selected	Object
If Object is not specified, trigger activates when any object is selected.
"Object|See"	Object appeared on the screen	Object, Rect, ActivationDelay, AnimateObject
If Object is not specified, the currently selected object is used.
Trigger activates if the object's position on the screen fits into Rect, and user keeps it inside the Rect during Delay seconds. During these seconds, the Object is animated by the method AnimateObject. See Waypoints for details.
"Object|Approach"	User approaches the object	Object, Type, Range, RangeKm, RangeRad, LonLatRange
If Object name is not specified, then the nearest detected object of specified type is used as the target.
If Type is "Selection", then currently selected object is used as the target.
Trigger activates if the distance to target fits the Range, RangeKm or RangeRad, and/or camera longitude/latitude fits the LonLatRange.
"GUI|Open"	Dialog window opens	Dialog
Dialog is a widget name, see SetWidgetStyle for details.
"GUI|Close"	Dialog window closes
"GUI|LockPanel"	Toolbar locks
"GUI|HalfLockPanel"	Toolbar half-locks
"GUI|UnlockPanel"	Toolbar unlocks
"GUI|LeftClick"	User clicks the widget with the left mouse button
"GUI|RightClick"	User clicks the widget with the right mouse button
"GUI|SelectObject"	User selects object or type of objects using any interface function	Dialog, Object, Type
Dialog is a widget name.

If Object is not specified, trigger activates when any object is selected. In the Location browser, Object is a name of the location.
"GUI|SelectElement"	User selects table cell
"GUI|ChangeValue"	User moves slider, clicks slider's arrow buttons, selects new item in the drop-down box, or clicks checkbox/tickbox	Dialog, Value, Range
Dialog is a widget name.

Trigger activates if the the new control's value matches the Value or fits into the Range.
"GUI|ActionEnter"	User enters the next level in the multi-level dialog (Solar system browser)	Dialog
Dialog is a widget name (use "Solar system browser").
"GUI|ActionBack"	User enters the previous level in the multi-level dialog (Solar system browser)
"GUI|ActionStart"	User starts job in the dialog	Dialog
Dialog is a widget name.
Dialog "Star browser" - user starts/stops searching.
Dialog "Music player" - user starts/stops the playback.
"GUI|ActionStop"	User stops job in the dialog
"GUI|ActionNext"	User executes next job in the dialog	Dialog
Dialog is a widget name.
Dialog "Music player" - user plays next/previous track.
"GUI|ActionPrev"	User executes previous job in the dialog
"EnterPlanetarium"	User enters the planetarium mode	
"EnterSinglePlayer"	User enters the simulator (game) mode	
"EnterMenu"	User enters the main menu	
"LoadingComplete"	Loading of the scene completes	MaxWaitTime
Trigger activates when the loader task list becomes empty (scene loading is complete).
If MaxWaitTime is provided, trigger waits up to specified period of time, then activates even if scene is still loading.
"ScreenshotComplete"	Saving screenshot to the disk completes	
Object type list
Some triggers require a Type parameter, describing a type of a SpaceEngine object. Possible values are summarized in the table below. Each type can be followed by a subtype or class, separated by '|'. For example: "Galaxy|SBc", "Star|G2V", "Body|Asteroid", "Planet|Terra".

Type	Subtype/class	Usage example
Selection	Not used	"Selection" - currently selected object
Waypoint	Not used	"Waypoint" - any waypoint
Galaxy	Galaxy Hubble class. See Creating a DSO for details.	"Galaxy" - any type of galaxy
"Galaxy|Sa" - any Sa galaxy
"Galaxy|E5" - any E5 galaxy
Cluster	Star cluster type: Open, Globular, Kern, Part. See Creating a DSO for details.	"Cluster" - any type of cluster
"Cluster|Open" - any open cluster
"Cluster|Globular" - any globular cluster
Nebula	Nebula type: Diffuse, Planetary, SNR. See Creating a DSO for details.	"Nebula" - any type of nebula
"Nebula|Diffuse" - any diffuse nebula
"Nebula|Planetary" - any planetary nebula
Star	Stellar spectral class. See Creating a star for details.	"Star" - any type of star
"Star|G2V" - any G2V-type star
"Star|DA2" - any DA2-type star
Body
Planet
Moon
DwarfPlanet
DwarfMoon
Asteroid
Comet
Barycenter
StarBarycenter	Temperature class: torrid, hot, warm, temperate, cool, cold, frigid.
Volatiles class: airless, arid, lacustrine, marine, oceanic, superoceanic, nonarid.
Size class: micro, mini, sub, norm, super, mega.
Bulk composition class: astroidal, ferria, terra, carbonia, aquaria, neptune, jupiter, browndwarf, sun or legacy classes: desert, selena, oceania, waterworld, titan, iceworld, icegiant, gasgiant, jovian.
See Creating a planet for details.	"Planet" - any planet
"Planet|terra" - any terra (rocky) planet
"Planet|hot|super|terra" - any hot super-earth rocky planet
"Moon|cold|aquaria" - any cold icy moon
"Moon|temperate|marine|norm|terra" - any temperate marine earth-sized rocky moon
"Body" - any type of a planetary body (planet, moon etc)
"Body|terra" - any type of a terra (planet, dwarf planet, moon etc)
"Body|asteroidal|cold" - any type of cold asteroidal object (asteroid, comet, dwarf moon etc)
"Asteroid|cold" - any cold asteroid (note difference with previous example)
"Barycenter" - any barycenter of a binary planetary object
"StarBarycenter" - any barycenter of a binary star
Note the Bulk composition class asteroidal: in SE interface and catalog scripts it is "astroid", but here it is "asteroidal" to distinguish it from the Type Asteroid.

VARIABLES
Variables are program parameters, which controls various rendering and logics aspects of SpaceEngine. Most variables are loaded from the config file at the program startup, some of them are linked to controls in the settings menu, and all of them (except read-only ones) can be changed by a script commands listed below. Variables can be one of these types:

- boolean, has only 2 values: false and true;
- integer, a integer number (32-bit);
- float, a single-precision floating-point number (IEEE 754 FP32);
- double, a double-precision floating-point number (IEEE 754 FP64).

Integer, float and double variables have minimum and maximum value, which are taken into account by the Set command. All variables have default values, which are assigned to them at the program startup or by the Reset command.

Boolean variables can be toggled (switched) just by typing their name in the script (so they work as an individual "commands"):

HardwareCursor - toggle on/off the hardware mouse cursor;
FXAA - toggle on/off FXAA anti-aliasing.

This is useful in the direct mode (typing commands directly to the SE console), but may be used in scripts as well. This in not recommended though, because makes scripts harder to understand, and in most cases you need to assign some initial values anyway.

Set, SetU, SetForce
Sets a variable with (Set) or without the range check (SetU, SetForce) in the current program mode (Set, SetU) or in all modes (SetForce). Syntax:
Set ShowOrbitsDwarfMoons true - assigns a value of true to the boolean variable ShowOrbitsDwarfMoons.
Set ClipHeight 5.0 - assigns a value of 5.0 to the variable ClipHeight.
SetU ClipHeight -5.0 - assigns a value of -5.0 to the variable ClipHeight. Negative values are not allowed for this variable, this may result in bugs.
SetForce BloomBright 1.0 - assigns a value of 1.0 to the variable BloomBright in all program modes, even in the main menu.
Range check: if the value is out of the range which variable allows, it is clamped into the allowed range, and a warning message is printed to the console (except boolean variables, which have no range). The SetU and SetForce ignores this check (this may be unsafe in some cases!).
Program mode: variables have different "slots" in the main menu, planetarium and single player modes. For example, if your script sets variable in the planetarium mode, this will affect only the planetarium. When entering the main menu, a different value for this variable will be used. When returninig back to the planetarium, previously set value will be restored. The SetForce command assigns a new value to all modes (slots).
Read-only variables: some variables are read-only. It is impossible to assign a new value to them. List of read-only variables:
UsedVRRuntime, VRControllerType, ProjectionMode, Exposure, HaveDLC_Steam_PRO.

Reset
Sets a variable to its default value in the current program mode. Syntax:
Reset ClipHeight - assigns a default value to the variable ClipHeight.
Reset ShowOrbitsDwarfMoons - assigns a default value to the boolean variable ShowOrbitsDwarfMoons.
The default values for all variables are listed in the table below. Resetting is impossible for read-only variables.

Interpolate
Interpolates (animates) a variable value with time in the current program mode. Syntax:
Interpolate VariableName { To 10 } - interpolates a variable VariableName to the final value specified by the To parameter.
Interpolate VariableName - interpolates a variable VariableName with custom parameters provided below:
{

Time 3 - duration of the animation in seconds; default is 2.
From 5 - initial value for the animation; default is minimum allowed variable value (see the table below).
To 10 - final value for the animation; default is maximum allowed variable value (see the table below).
Func "quadric" - shape of the interpolation function; allowed values are "linear", "quadric", "cubic", "sin", "exp", "revexp"; default is "linear".
}
The variable name and the To parameter are mandatory. If the From parameter is not provided, interpolation starts from the current variable's value. Initial and final values are floating-point, but will be converted to the variable's type. Interpolation is impossible for boolean and read-only variables.
The next script command executes immidiately. You can interpolate any number of variables simultaneously.

Get
Returns the current value of a variable in the current program mode. Syntax:
Get ClipHeight - returns the current value of the variable ClipHeight and prints it to the console.
Get ShowOrbitsDwarfMoons - returns the current value of the boolean variable ShowOrbitsDwarfMoons and prints it to the console.
For now, this command makes sense only if used in the console (in direct mode), just for the information.

WaitVar
Delays execution of the script until a variable matches the specified value. Syntax:
WaitVar ClipHeight 15 - delays the script until the variable ClipHeight matches the value of 15.
WaitVar ShowOrbitsDwarfMoons false - delays the script until the variable ShowOrbitsDwarfMoons matches the value of false.
If the variable already has the desired value, the command returns immediately (script is not delayed).

SaveVars
Saves current values of all variables in the current program mode and locking states of toolbars. Syntax:
SaveVars
It is recommended to call this command at the beginning of a script, if that script is not allowed to change the normal settings or state of SpaceEngine. When the script finishes, or interrupts by a user or due to an error, all variables will be restored (equivalent of calling of the RestoreVars command).

RestoreVars
Restores values of all variables and locking states of toolbars in the current program mode, previously saved by the command SaveVars. Syntax:
RestoreVars
It is not necessary to call this command at the end of the script which uses SaveVars command, because variables will be restored automatically once script finishes. But you may call this command at any point of the script.

List of variables
The table below has a full list of variables. Some boolean variables are marked as a cheat code. Setting them to true displays a message "Cheater!", and enables some really cheat-y functions in gameplay.

Name	Type	Default	Min	Max
DEAMaxTerms	integer	10000	0	10000
StarColorShift	float	0	-1	1
StarColorSaturation	float	1	0	2
SunIllumSaturation	float	1	0	2
IllumSaturationAuto	boolean	true		
RealTime	boolean	false		
RealTimeFPS	double	30	1	1000
QualityPreset	integer	4	0	4
FullConstellationNames	boolean	true		
ConstelBordersAtInf	boolean	true		
ConstelBordersRadius	float	20	0	1e+06
ConstelBordersFadeStart	float	200	0	1e+06
ConstelBordersFadeEnd	float	800	0	1e+06
ConstelLinesFadeStart	float	4000	0	1e+06
ConstelLinesFadeEnd	float	8000	0	1e+06
ConstLinesAll	boolean	false		
ConstBoundaryAll	boolean	false		
ConstLabelAll	boolean	false		
ConstLinesZodiac	boolean	false		
ConstBoundaryZodiac	boolean	false		
ConstLabelZodiac	boolean	false		
LightDimmingMagn	float	-15	-100	100
LightLimitMagn	float	-10	-100	100
LensFlareFadeBeginMagn	float	-17	-100	100
LensFlareFadeEndMagn	float	-14	-100	100
LensFlareSmallMethodRadius	float	4	0.1	100
LensFlareLargeMethodRadius	float	8	0.1	100
LensFlareScale	float	1	0.1	10
LensFlareBright	float	1	0.1	10
PseudoFlareBright	float	1	0.1	10
SaveAlpha	boolean	false		
JpegQuality	integer	85	0	100
CompressCopyQueue	boolean	false		
FBOResolution	float	0.35	0.1	1
ImpostorResolution	float	0.35	0.1	1
SkyboxResolution	float	0.75	0.1	1
MaxTimePerFrameClean	float	20	1	1000
MaxTimePerFrameCleanVR	float	20	1	1000
LinkStereobaseToMoveSpeed	boolean	true		
LinkStereobaseToTimeScale	boolean	false		
StereobaseToMoveSpeed	float	0.001	0	1
StereobaseToTimeScale	float	0.6	0	1000
PhotoMode	integer	0	0	2
ExposureComp	float	0	-42	4
Exposure	float	0	1e-13	16
ProjectionMode	integer	0	0	3
StereoscopicMode	integer	0	0	6
StereoscopicModeLast	integer	2	0	6
ForceStereoscopicMode	integer	0	0	6
CubemapFormat	integer	0	0	6
VSync	integer	2	0	2
DSOBrightness	float	1	0	2
DSOContrast	float	1	0	2
DSOSaturation	float	1	0	2
DSOGamma	float	1	0	2
GalaxyEllipticalGamma	float	1	0	4
Brightness	float	1	0	2
Contrast	float	1	0	2
Saturation	float	1	0	2
Gamma	float	1	0	2
Sharpness	float	0	0	2
BloomBright	float	0.7	0	1
BloomThreshold	float	0	0	100
ShowBackground	boolean	false		
ShowCubempEdges	boolean	false		
FitCubemapToScreen	boolean	true		
LinesSmoothing	boolean	true		
LinesWidth	float	1	0	10
AnisotropyLevel	integer	0	0	16
StereoInterlaced	boolean	false		
StereoSwapEyes	boolean	false		
StereoIPD	float	6.4	1	10
StereoConvergence	float	1	0.5	2
Stereobase	float	1	1	1e+25
MipmapsGUI	boolean	true		
MipmapsFrame	boolean	true		
MipmapsEnv	boolean	true		
MSAALevel	integer	0	0	32
MSAALevelVR	integer	0	0	32
MSAALevelEnv	integer	0	0	32
MSAALevelEnvVR	integer	0	0	32
MSAALevelImp	integer	0	0	32
FXAA	boolean	true		
FXAAImp	boolean	true		
Dithering	boolean	true		
FastCubemap	boolean	false		
TestGrid	boolean	false		
DistortInterface	boolean	false		
FisheyeFlipX	boolean	false		
FisheyeFlipY	boolean	false		
FisheyeFOV	float	180	40	360
FisheyeGUIAngle	float	90	-180	180
FisheyeZoom	float	1	1	3
FisheyeCrop	float	1	1	2
fxaaSubpix	float	0.75	0	1
fxaaEdgeThreshold	float	0.125	0.0625	0.3333
fxaaEdgeThresholdMin	float	0.0833	0.0312	0.0833
WatermarkOpacity	float	0.5	0	1
OverlayFadeIn	float	0.2	0	60
OverlayFadeOut	float	0.35	0	60
WidgetFadeIn	float	0.2	0	60
WidgetFadeOut	float	0.35	0	60
WidgetSlideIn	float	0.2	0	60
WidgetSlideOut	float	0.02	0	60
BottomInfoHUD	boolean	false		
LessonPassed	integer	-1	-1	100
TypeAtmoClass	boolean	false		
TypeVolTempClass	boolean	false		
GasGiantClassStyle	integer	0	0	1
TerrestrialClassStyle	integer	0	0	1
MusicVolume	integer	20	0	100
MusicRepeatMode	integer	0	0	3
MusicOrderMode	integer	0	0	2
MixDurationGUI	float	2	0.01	60
MixDurationLoop	float	10	0.01	60
MixDurationContext	float	5	0.01	60
MixDurationScript	float	1	0.01	60
MinTrackPlayingTime	float	20	0.01	300
MusicShowTitle	boolean	true		
UnitDistLarge	integer	1	0	1
UnitDistMedium	integer	2	2	3
UnitDistSmall	integer	4	4	6
UnitRadiusLarge	integer	0	0	1
UnitRadiusMedium	integer	1	1	2
UnitRadiusSmall	integer	2	2	3
UnitMassLarge	integer	0	0	1
UnitMassMedium	integer	1	1	2
UnitMassSmall	integer	2	2	3
UnitTemperature	integer	1	0	2
UnitPressure	integer	0	0	5
SelectionRadius	boolean	false		
AtmoHeightHarb	boolean	true		
AtmoBottomOffset	float	0.0001	0	0.1
AtmoDistanceLimit	float	30	1	10000
AtmoMinWidthPix	float	1	0	10
AuroraBright	float	1	0.01	100
PlanetShineUpdateFrames	integer	5	1	100
GalaxyFrontTexBright	float	1	0	10
StarFrustumClipFOV	float	30	0	120
CometTailBright	float	1	0.01	100
EngineSampleStep	float	0.1	0.001	1
UseClusNebIBO	boolean	false		
LandLOD	float	0	-1	1
LandLODmaxRes	integer	1080	480	6480
LandLODmaxResVR	integer	1080	480	6480
MipmapsMode	integer	2	0	2
MipmapsLand	boolean	true		
LandFiltNearest	boolean	false		
LandProcDetail	boolean	true		
PlanetVSDouble	integer	12	0	25
PlanetReverseLoading	boolean	true		
LandSpareFactor	double	1	0	10
PlanetHeightmapFormat	integer	13	1	16
LandEnvMapCachedOnly	boolean	true		
LandLoadPriorityMode	integer	3	0	3
WaterVisibilityHeight	float	10	0.001	1e+06
DisplaceDetailHeight	float	0.1	0	1
PlanetTexelOffset	integer	-10000	-1024	1024
PlanetShineBright	float	1	0.1	10
SelfEmissionBright	float	1	0.1	10
ThermalEmissionShift	float	0	-2	2
ExposureFix	float	2	0	10
AutoExpNightVision	float	1	1	10
AutoExpSensitivity	float	0.18	0.01	100
AutoExpMaskSize	float	1	0.01	100
AutoExpMaskValue	float	2.5	0.01	100
FrameDynamicRange	float	10000	1	1e+06
BloomDynamicRange	float	50	1	1e+06
hdrGalaxySpriteBright	float	0.45	1.17549e-38	3.40282e+38
hdrNebulaSpriteBright	float	0.45	1.17549e-38	3.40282e+38
hdrGalaxyRaymarchBright	float	0.45	1.17549e-38	3.40282e+38
hdrNebulaRaymarchBright	float	0.45	1.17549e-38	3.40282e+38
hdrStarSurfaceBright	float	1000	1.17549e-38	3.40282e+38
hdrPlanetSurfaceBright	float	1	1.17549e-38	3.40282e+38
hdrWaterSurfaceBright	float	1	1.17549e-38	3.40282e+38
hdrShipModelBright	float	1	1.17549e-38	3.40282e+38
hdrAtmoScatterBright	float	1	1.17549e-38	3.40282e+38
hdrAtmoIllumBright	float	1	1.17549e-38	3.40282e+38
hdrPlanetRingsBright	float	1	1.17549e-38	3.40282e+38
hdrCometTailBright	float	1	1.17549e-38	3.40282e+38
hdrAccretionJetBright	float	1	1.17549e-38	3.40282e+38
hdrStarCoronaBright	float	1	1.17549e-38	3.40282e+38
hdrAuroraBright	float	1	1.17549e-38	3.40282e+38
hdrEmissionBright	float	1	1	10000
hdrShipEnginesBright	float	1	1	10000
realGalaxySpriteBright	float	0.45	1.17549e-38	3.40282e+38
realNebulaSpriteBright	float	0.45	1.17549e-38	3.40282e+38
realGalaxyRaymarchBright	float	0.45	1.17549e-38	3.40282e+38
realNebulaRaymarchBright	float	0.45	1.17549e-38	3.40282e+38
realStarSurfaceBright	float	1e+09	1.17549e-38	3.40282e+38
realPlanetSurfaceBright	float	1	1.17549e-38	3.40282e+38
realWaterSurfaceBright	float	1	1.17549e-38	3.40282e+38
realShipModelBright	float	1	1.17549e-38	3.40282e+38
realAtmoScatterBright	float	1	1.17549e-38	3.40282e+38
realAtmoIllumBright	float	1	1.17549e-38	3.40282e+38
realPlanetRingsBright	float	1	1.17549e-38	3.40282e+38
realCometTailBright	float	0.05	1.17549e-38	3.40282e+38
realAccretionJetBright	float	1	1.17549e-38	3.40282e+38
realStarCoronaBright	float	100	1.17549e-38	3.40282e+38
realAuroraBright	float	10	1.17549e-38	3.40282e+38
realEmissionBright	float	3	1	10000
realShipEnginesBright	float	100	1	10000
AmbientLighting	float	0	0	1
PointOverbright	float	1	0	5
MapPointOverbright	float	1	0	5
PointFlareBright	float	1	0.01	100
PointFlareDecay	float	1	0.01	100
PointDesaturation	float	1	0.01	100
PlanetMagnLimit	float	6	-10	20
StarMagnLimit	float	6	-10	20
ClusterMagnLimit	float	6	-10	20
NebulaMagnLimit	float	6	-10	20
GalaxyMagnLimit	float	6	-10	20
GlowMagnLimit	float	6	-10	20
CheatsMenu	boolean	false	cheat code
Collisions	boolean	true	cheat code
Gravity	boolean	true	cheat code
Aero	boolean	true	cheat code
UFO	boolean	false	cheat code
FTL	boolean	false	cheat code
God	boolean	false	cheat code
TARDIS	boolean	false	cheat code
ShipDrawMode	integer	1	0	4
WaypointsOpacity	float	1	0	1
ExitToMenuOnScriptError	boolean	false		
BoolVar0	boolean	false		
BoolVar1	boolean	false		
BoolVar2	boolean	false		
BoolVar3	boolean	false		
IntVar0	integer	0	-2147483648	2147483647
IntVar1	integer	0	-2147483648	2147483647
IntVar2	integer	0	-2147483648	2147483647
IntVar3	integer	0	-2147483648	2147483647
FloatVar0	float	0	-3.40282e+38	3.40282e+38
FloatVar1	float	0	-3.40282e+38	3.40282e+38
FloatVar2	float	0	-3.40282e+38	3.40282e+38
FloatVar3	float	0	-3.40282e+38	3.40282e+38
SoundVolume	integer	20	0	100
FlySpeed	double	0	1.17549e-38	3.40282e+38
TurnSpeed	double	0	1.17549e-38	3.40282e+38
OrbitSpeed	double	0	1.17549e-38	3.40282e+38
ChartShowStars	boolean	true		
ChartShowPlanets	boolean	true		
ChartShowDwarfPlanets	boolean	true		
ChartShowMoons	boolean	true		
ChartShowDwarfMoons	boolean	true		
ChartShowAsteroids	boolean	false		
ChartShowComets	boolean	false		
ChartShowPlanBary	boolean	true		
ChartLockedCamera	boolean	true		
ChartLightingAngle	integer	35	-90	90
ChartDisplayMode	integer	0	0	4
MapExploreMode	boolean	false		
MapShowProcedural	boolean	true		
MapLabelsMode	integer	1	0	2
MapDensity	integer	80	10	200
MapDensityScaleExpl	integer	100	1	1000
Use3Dmenu	boolean	true		
testFloat	float	0	-1000	1000
testInt	integer	0	-1000	1000
TextMode	integer	1	0	2
DebusScreen	integer	1	0	2
FPS	boolean	false		
HardwareCursor	boolean	false		
HideCursor	boolean	false		
MotionBlurLength	float	0	-10	10
StarFadeDuration	float	0.5	0.01	10
PlanetShineMode	integer	1	0	2
AutoExposureSpeed	float	2.5	0	300
PointScale	float	1	0.5	2
LinkScaleToScreenRes	boolean	true		
StarPointMethod	integer	2	0	3
PlanetPointMethod	integer	1	0	3
AutoGalaxyMagnitude	boolean	true		
AutoGalaxyMagnSpeed	float	3	0.01	100
TextureCompress	boolean	true		
PlanetDetailTextures	boolean	true		
DisplaceDetailTextures	boolean	true		
ShowCatGalaxies	boolean	true		
ShowProcGalaxies	boolean	true		
ShowCatClusters	boolean	true		
ShowProcClusters	boolean	true		
ShowCatNebulae	boolean	true		
ShowProcNebulae	boolean	true		
ShowCatStars	boolean	true		
ShowProcStars	boolean	true		
ShowCatPlanets	boolean	true		
ShowProcPlanets	boolean	true		
ShowProcCSPlanets	boolean	true		
ShowSpacecraft	boolean	true		
OrbitTrailsLength	float	0.725	0	1
OrbitMajorThickness	float	5	2	20
OrbitMinorThickness	float	2	2	20
ShowOrbitsShips	boolean	true		
ShowOrbitsPlanets	boolean	true		
ShowOrbitsMoons	boolean	true		
ShowOrbitsDwarfPlanets	boolean	true		
ShowOrbitsDwarfMoons	boolean	true		
ShowOrbitsAsteroids	boolean	true		
ShowOrbitsComets	boolean	true		
ShowOrbitsSuns	boolean	true		
ShowOrbitsBarycenters	boolean	true		
ShowLabelsShips	boolean	true		
ShowLabelsPlanets	boolean	true		
ShowLabelsMoons	boolean	true		
ShowLabelsDwarfPlanets	boolean	true		
ShowLabelsDwarfMoons	boolean	true		
ShowLabelsAsteroids	boolean	true		
ShowLabelsComets	boolean	true		
ShowLabelsSuns	boolean	true		
ShowLabelsBarycenters	boolean	true		
ShowLabelsStars	boolean	true		
ShowLabelsClusters	boolean	true		
ShowLabelsNebulae	boolean	true		
ShowLabelsGalaxies	boolean	true		
ShowLabelsWaypoints	boolean	true		
ShowMarkersShips	boolean	true		
ShowMarkersPlanets	boolean	true		
ShowMarkersMoons	boolean	true		
ShowMarkersDwarfPlanets	boolean	true		
ShowMarkersDwarfMoons	boolean	true		
ShowMarkersAsteroids	boolean	true		
ShowMarkersComets	boolean	true		
ShowMarkersSuns	boolean	true		
ShowMarkersBarycenters	boolean	true		
ShowMarkersStars	boolean	true		
ShowMarkersClusters	boolean	true		
ShowMarkersNebulae	boolean	true		
ShowMarkersGalaxies	boolean	true		
AuroraQuality	integer	1	0	1
BlackHoleQuality	integer	2	0	3
ShipWarpQuality	integer	2	0	3
SilentVarChange	boolean	false		
EditGUI	boolean	false		
FindEarths	boolean	false	cheat code
NoClip	boolean	false	cheat code
ClipHeight	double	0.001	0	1
ClipZNear	double	0.005	0	1
AutoHorizon	boolean	false		
SparseStarSystemsSearch	boolean	true		
SparseGalaxiesSearch	boolean	true		
MaxGraphics	boolean	false		
PipelineMode	integer	0	0	1
DrawLand	boolean	true		
DrawWater	boolean	true		
DrawClouds	boolean	true		
DrawSky	boolean	true		
DrawFlares	boolean	true		
DrawFrontTex	boolean	true		
DrawSpriteModel	boolean	true		
ForceGasGiantClouds	boolean	true		
LandRLSort	integer	1	0	3
CloudsSpeed	float	1	-1e+09	1e+09
ShowMessages	boolean	true		
MessageTimeout	float	2	0	10
ShowBBoxLabels	integer	0	0	4
ShowLandQuadtree	integer	0	0	2
ShowStarOctree	boolean	false		
ShowCatStarOctree	boolean	false		
ShowGalOctree	boolean	false		
ShowCatGalOctree	boolean	false		
ShowGalSysOctree	boolean	false		
ShowClusOctree	boolean	false		
ShowGalModelBoxes	boolean	false		
ShowNebModelBoxes	boolean	false		
CacheSizeLandNode	integer	0	0	100000
CacheSizeStarNode	integer	0	0	100000
CacheSizeGalNode	integer	0	0	100000
CacheSizeGalModels	integer	32	20	256
CacheSizeGalSysModels	integer	32	20	256
CacheSizeAtmoModels	integer	0	10	64
TimeScale	double	1	-1e+12	1e+12
TimeScaleGame	double	1	0	10000
logFarPlane	float	1e+20	0	1e+30
LogTimeStamp	boolean	false		
LogThreadStamp	boolean	true		
MarkersSize	float	7	0.01	100
MarkersSizeVR	float	10	0.01	100
LabelsSize	float	1	0.01	100
LabelsSizeVR	float	1	0.01	100
LabelsPosPlanet	integer	2	0	3
LabelsPosDSO	integer	2	0	3
NebulaLighting	boolean	false		
BlackHoleJets	boolean	true		
BlackHoleSmallNoiseTex	boolean	true		
CosmHorizon	float	4.2e+09	0	3.40282e+38
dbgCheckGLErrors	boolean	true		
dbgShowMipLevel	float	0	-1	100
dbgShowLandDetTexArray	integer	1	0	3
dbgShowLandDetTexLayer	integer	0	0	1024
dbgShowLandTexCacheArray	integer	8	0	8
dbgShowLandTexCacheLayer	integer	-2	-2	4096
dbgSaveCompressedLandTex	boolean	false		
OpenMenuIfScriptError	boolean	false		
SelectedObjectType	integer	9	0	9
BuildingCollision	boolean	true		
ShowBuildingOctree	boolean	false		
ShowBuildingPolys	boolean	true		
ShowBuildingLines	boolean	false		
ShowBuildingVerts	boolean	false		
TestBuildingOctree	boolean	false		
TestBuildingDraw	boolean	false		
DrawBuildingLightRadius	boolean	false		
ShowBuildingEntity	boolean	true		
BuildingTestBrush	integer	0	0	0
BuildingTestPoly	integer	0	0	0
BuildingTestLeaf	integer	0	0	0
BuildingCollisionSteps	integer	1	1	100
LoaderAsyncTextures	boolean	false		
LoaderAsyncMeshes	boolean	false		
LoaderAsyncShaders	boolean	false		
PauseLoader	boolean	false		
Benchmark	boolean	false		
MaxThreads	integer	10	1	10
TimingStrategy	integer	0	0	1
MaxTilesPerFrame	integer	8	1	20
MaxTilesPerFrameVR	integer	2	1	20
MaxTimePerFrame	integer	8	1	100
MaxTimePerFrameVR	integer	2	1	100
VRRuntime	integer	0	0	2
UsedVRRuntime	integer	0	0	2
VRControllerType	integer	0	0	5
VRMirror	integer	3	0	4
VRInterfaceCopies	integer	4	1	8
VRMoveDirection	integer	2	0	2
VRMouseCursorMode	integer	1	0	2
VRDiscreteTurns	boolean	false		
VRLimitFOV	boolean	true		
VRSwapHands	boolean	false		
VRUseHWProxSensor	boolean	true		
VRAutoPause	boolean	true		
VRAutoExitToMenu	boolean	false		
VRRenderScaleAuto	boolean	false		
VRControllerSmoothing	float	0.5	0	1
VRRenderScale	float	1	0.7	1
VRRenderScaleMin	float	0.5	0	2
VRRenderScaleMax	float	1	0	2
VRRenderScaleMinFPS	float	0.33	0	1
VRRenderScaleMaxFPS	float	0.5	0	1
VRInfoTimeout	float	10	0	60
VRMoveController	integer	0	0	1
VRSelectController	integer	1	0	1
Controller1RayDir	integer	0	0	1
Controller2RayDir	integer	0	0	1
HaveDLC_Steam_PRO	boolean	false		
RENDERING COMMANDS
Hide, Show
Smoothly hides or shows space object type, effect or overlay. Syntax:
Hide Clouds - hides clouds.
Show Constellations - shows constellations lines, boundaries and labels.
All possible objects/effects/overlays are: Planets, Stars, Clusters, Nebulae, Galaxies, Atmo, Atmospheres, Clouds, Water, Aurora, CometTails, AccretionDisks, Jets, Orbits, OrbitMarks, EclipseMask, Vectors, SelPointer, VelVector, Grids, Constellations, Labels, Markers, NightLights, Eclipses, PlanetShine, LensFlares, PseudoFlares.
Hide/show animation is smooth, duration is controlled by the OverlayFadeIn and OverlayFadeOut variables. It is possible to show/hide many more effects/overlays than provided in this list by using corresponding variables.

HideObject, ShowObject
Smoothly hides or shows a specified space object. Syntax:
Hide "Name" - hides object with a name "Name" with default parameters.
Hide "Name" { ... } - hides object with a name "Name" with custom parameters provided below:
{

Time 3.0 - duration of hiding animation in seconds; default is 0.5.
}
Examples:
Hide "Mars" - hides Mars.
Show "RS 8474-2738-5-27954-210 1" { Time 3.5 } - shows planet 1 of a star RS 8474-2738-5-27954-210 during 3.5 seconds.
Hide/show animation is smooth. To resolve name ambiguity, prepend object's name with its parent name, separated by |. For example:
Hide "Jupiter|Io" - hides Io (satellite of Jupiter).
Hide "Sun|Io" - hides Io (asteroid, named also "85 Io").

FadeOut, FadeIn
Fades out the scene into a specified solid color, or fades in the scene back. Syntax:
FadeOut - fade out with default parameters.
FadeOut { ... } - fade out with custom parameters provided below:
{

Time 3.0 - duration of fade out in seconds; default is 0.25.
Color (0.2, 0.0, 0.2) - fade color in (R, G, B) format; default is black - (0, 0, 0).
}

ResetVRPose
Resets user pose (position and orientation) in VR.

VRControllerMode
Changes VR controller mode. Syntax:
VRControllerMode X { ... } - changes the mode of the VR controller number X with custom parameters provided below:
FadeOut { ... } - fade out with custom parameters provided below:
{

Mode "off" - sets befaviour: "on" - always visible, "off" - always hidden, "menu" - visible in the Main menu and hidden in game.
Color (0.2, 0.0, 0.2) - sets color of the rendered controller model in (R, G, B) format; default is red for controller 0 and green for controller 1.
RayColor (2.0, 0.0, 2.0) - sets color of the controller's "laser ray" effect in (R, G, B) format; default is red for controller 0 and green for controller 1. Values larger than 1 will reslult in a glowing effect.
}
Allowed controller numbers are 0 and 1. They corresponds the "movement" and "selection" controllers (left and right, if controllers are not swapped in settings).

VRControllerDefault
Changes VR controller mode. to default Syntax:
VRControllerMode X - changes the mode of the VR controller number X to default. Allowed controller numbers are 0 and 1.

INTERFACE COMMANDS
Print
Displays the on-screen message. Syntax:
Print "blabla" - displays a message "blabla" with a default parameters.
Print "blabla" { ... } - displays a message "blabla" with custom parameters provided below:
{

Time 10.0 - timeout of the message: it will be hidden after the specified time in seconds; default is 5.
Color (1.0, 0.8, 0.3, 1.0) - color of the message text in (R, G, B, A) format (A is opacity); default is white - (1, 1, 1, 1).
PosX 0.8 - horizontal position of the message in screen fractions (i.e. 0.0 is leftmost, 1.0 is rightmost); default - see remarks.
PosY 0.2 - vertical position of the message in screen fractions (i.e. 0.0 is topmost, 1.0 is bottommost); default - see remarks.
AlignX "center" - horizontal align; possible values: "left", "center", "right"; default is "center".
AlignY "down" - vertical align; possible values: "top", "center", "down"; default is "down".
}
If the PosX/PosY are provided, then AlignX/AlignY defines the alignment of the message text relative to the static point specified by PosX/PosY parameters. Otherwise the message text will use dynamic positioning in the free screen area and will be aligned according to provided or default values of AlignX/AlignY. This dynamic position means that if the user opens some toolbars, the displayed text will be moved so it will not overlap the toolbars. You may use any combination of these 4 parameters, i.e. define static/dynamic position and alignment for X and Y coordinates independently.
Message text supports BBCode: you may change the color, font, size, use lower and upper index, special symbols. See the BBCode table in the Appendix for more details.

HidePrint
Removes the onscreen message displayed previously by the Print command.

WaitMessage
Displays a message box dialog with a specified text and button [Next]. Syntax:
WaitMessage "blabla" - displays a message box with a text "blabla".
The script execution delays until user presses the [Next] button. The message box will hide itself after that. Pressing the close button interrupts the script.
Message text supports BBCode: you may change the color, font, size, use lower and upper index, special symbols. See the BBCode table in the Appendix for more details.

ShowMessage
Displays a message box dialog with a specified text. Syntax:
ShowMessage "blabla" - displays a message box with a text "blabla".
The script execution continues immediately to the next command. The message box remains visible until the command HideMessage or another WaitMessage/ShowMessage is executed, or until the user presses the close button on the dialog. Pressing the close button interrupts the script.
You may use this command to display a message box before some WaitTrigger command.
Message text supports BBCode: you may change the color, font, size, use lower and upper index, special symbols. See the BBCode table in the Appendix for more details.

HideMessage
Hides the message box displayed previously by the WaitMessage/ShowMessage.

ShowDialog, HideDialog
Shows or hides a specified dialog window or toolbar. Syntax:
ShowDialog "Dialog name" - shows the specified dialog by its name.
ShowDialog "Dialog name" { ... } - shows the specified dialog by its name with additional parameters provided below:
{

Tab "tab_name" - shows the dialog and switches it to the specified tab, if it has any. Not case-sensitive.
Mode "mode_name" - shows the dialog and switches it to the modal or non-nodal mode, if supported. Not case-sensitive.
}
The name of a dialog is usually visible on its header. The English localization must be used to determine the name for this command; switch to English in User settings. Names are case-sensitive. Note: some interface skins forced the UPPER CASE font in the dialog's window header. Use skins which don't do this to determine the name of the dialog.
The list of dialogs and toolbars which have no header: "Main toolbar", "Navigation toolbar", "Brightness toolbar", "Filters toolbar", "Solar system browser", "Universe map", "System chart", "Nebula editor", "Planet editor", "Ship editor", "Message box".
Examples:
ShowDialog "Find object" - opens the Find object dialog.
HideDialog "Solar system browser" - hides the Solar system browser.
ShowDialog "Settings" { Tab "graphics" } - opens the settings dialog on the 'Graphics' tab. Possible tab names for the settings dialog are: "player", "style", "camera", "filter", "graphics", "display", "sound", "controls", "controls|common", "controls|camera", "controls|ship", "controls|chart".
ShowDialog "Settings" { Mode "modal" } - opens the settings in the modal mode. Possible mode names for the settings dialog are: "modal" (normal), "menu" (frameless for the main menu).
ShowDialog "Locations" { Tab "featured" } - opens the locations dialog on the 'Featured' tab. Possible tab names for the locations dialog are: "featured", "user".
ShowDialog "Locations" { Mode "modal" } - opens the locations dialog in the modal mode. Possible mode names for the locations dialog are: "modal" (normal), "load" (frameless for the main menu), "save" (frameless for the main menu), .

HideAllDialogs
Hides all dialog windows; toolbars will not be hidden. Syntax:

HideAllToolbars
Hides all toolbars; dialog windows will not be hidden. Syntax:

OpenMenu
Opens the main menu or a specified sub-menu. Syntax:
OpenMenu "Main" - opens the Main menu.
OpenMenu "Main|Settings" - opens the Main menu > Settings sub-menu.
OpenMenu "Main|Editor" - opens the Main menu > Editor sub-menu.
OpenMenu "Main|Planetarium|Tutorial" - opens the Main menu > Planetarium > Tutorial sub-menu.

CloseMenu
Closes the main menu or a sub-menu.

EnterPlanetarium, EnterSinglePlayer, SessionContinue
Smoothly closes main menu and enters the planetarium mode (EnterPlanetarium), single player mode (EnterSinglePlayer), or last used mode (SessionContinue). Loading screen is shown if needed. Script execution will be delayed and continue when scene completes loading or user presses the Esc button to cancel out the loading screen. Simultaneously, scene fade-in animation starts. Syntax:
EnterPlanetarium - enters planetarium with default values.
EnterPlanetarium { ... } - changes defaults by a parameters provided below:
{

ShowLoadingScreen false - disables loading screen; user may see scene loading process. Script execution continues when menu fade-out animation completes. Default is true.
AutoFadeIn false - disables scene fade-in after loading completes or menu fade-out animation completes. Screen stays black, so you must call FadeIn later in the script. Default is true.
FadeInTime 2.0 - modifies scene fade-in animation time. Default is -1, which means using the WidgetFadeIn variable value.
}

SetWidgetStyle
Changes style of a specified widget (interface element). Syntax:
SetWidgetStyle "Widget name" { ... } - changes the style of the widget "Widget name"; the style is specified by a parameters provided below:
{

WinColor (1.0, 0.0, 0.0, 0.8) - color of the widget's contour and filling in (R, G, B, A) format (A is opacity); modulates the current skin color.
TextColor (0.0, 1.0, 0.0, 1.0) - color of the widget's text in (R, G, B, A) format (A is opacity); modulates the current skin color.
Pos (0.5, 0.3) - position of the widget relative to parent, in units of parent's size.
PosPix (600, 300) - position of the widget relative to parent, in pixels.
Visible false - changes visibility of the widget: false hides it, true restores back.
Disabled true - changes enablement of the widget: false disables it (makes it grayed out), true enables back.
Highlighted true - changes hightlighting (render state) of the widget.
Active true - changes activation (render state) of the widget.
}
You may change the style of a dialog window or toolbar by specifying its name, as described in ShowDialog. You may also change style of any control (button, checkbox, label etc). To do this, first determine its name. Usually it matches the name of this control in English localization; sometimes it is some generic name such as "#35". The name of an icon button can be determined if you hold mouse cursor over it: the hint appearing with the button name. Separate its name from a parent widget name with a '|'. Names are case-sensitive. Examples:
SetWidgetStyle "Main toolbar" - the Main toolbar.
SetWidgetStyle "Main toolbar|System chart" - the System chart button on the Main toolbar.
SetWidgetStyle "Graphics|Planets" - the Planets group contour line on the Graphics settings dialog.
SetWidgetStyle "Graphics|Planets|Eclipse shadows" - the Eclipse shadows checkbox on the Graphics settings dialog (its parent is the Planets group).
If a widget is a dialog window, its parent is the screen (SpaceEngine's window client area), so Pos (0.5, 0.5) moves window to the center of screen (more precisely, its top-left corner will be in the center of the screen). If the widget is a button, checkbox etc, Pos/PosPix parameters move it relative to its parent. Warning: be careful with moving/coloring the interface components, the only way to restore their positions and colors is restarting SpaceEngine.

HighlightWidget
Makes specified widget blink. Syntax:
HighlightWidget "Widget name" - starts widget blinking with default parameters.
HighlightWidget "Widget name" { ... } - starts a widget blinking with custom parameters provided below:
{

Time - blinking duration in seconds. Default is infinity (widget will not stop blinking until another HighlightWidget command).
Freq - blinking frequency in Hertz (blinks per second). Default is 4.
Color (1.0, 0.5, 1.0, 0.8) - blinking color in (R, G, B, A) format (A is opacity); modulates the current skin color; default is red - (1.0, 0.2, 0.2, 1.0).
}
To stop widget blinking, use HighlightWidget "".
The message box dialog shown by WaitMessage and ShowMessage commands has a dynamic header, text label and buttons. Use these names to refer to them in the HighlightWidget and SetWidgetStyle commands: "Message box", "Message box|Message text", "Message box|Button 1", "Message box|Button 2".

StarBrowserReset
Clears the Star browser search results and sets default search parameters. Syntax:
StarBrowserReset - use default parameters.
StarBrowserReset { ... } - modifies defaults with the additional parameters provided below:
{

SearchRadius 20 - sets search radius in parsecs. Default is 10.
MatchOnly false - sets the "Filter" checkbox state. Default is true (ticked).
}

ShowDonate
Opens the http://spaceengine.org/support/ web page in default browser (or http://spaceengine.org/ru/support/ if SpaceEngine interface language is Russian).

ShowFlightSchool
Opens the http://spaceengine.org/manual/space-flight-school/ web page in default browser.

OpenStoreOverlay
Opens the platform store overlay in VR on the page for a specified DLC. Syntax:
OpenStoreOverlay
{

Platform "Steam" - name of the platform. Allowed values: "Steam", "Oculus", "VivePort". SpaceEngine supports only "Steam" now.
DLC "PRO" - name of the DLC the store page will be opened for. If the DLC name is invalid or this parameter is not provided, the main app store page will be opened.
}

SOUND COMMANDS
PlayMusic
Switches the music playback to a specified track. Syntax:
PlayMusic "filename.ogg" - switch to a file filename.ogg, the file must be in the data/music/ or addons/*/music/ folder.

PauseMusic
Pauses the music playback.

ResumeMusic
Resumes the music playback.

SetAudioDevice
Set the audio ountut device. Syntax:
SetAudioDevice "Name" - switches audio output to a device named "Name".
Device name is that reported by the system. You can see list of devices in log file, for example "Generic Software on LG TV (NVIDIA High Definition Audio)" or "Oculus Rift CV1". The name provided to this command may contain only part of the full device name, for example SetAudioDevice "LG TV" switches audio to the "Generic Software on LG TV (NVIDIA High Definition Audio)".
The special value "Default" switches to a default audio device (those selected in the Windows sound manager).

UTILITY COMMANDS
Screenshot
Takes a screenshot and saves to the disk. Syntax:
Screenshot - takes a screenshot without interface and saves to the default folder screenshots/, with automatic file name and with default format (specified in the Player settings dialog).
Screenshot { ... } - takes a screenshot with custom options:
{

GUI true - takes a screenshot with interface visible. Default is false (without interface).
Format "tif" - set a screenshot format. Possible values: "jpg", "jpeg", "tif", "tiff", "tga", "png", "dds". Default is whatever is set in the Player settings dialog.
Name "prefix" - set a screenshot file name prefix. Default is "scr".
}
The path and file name of the screenshot have a form of screenshots/prefixNNNNN.format where prefix is specified by the Name parameter (default is scr), format is specified by the Format parameter (from the Player settings dialog), and NNNNN is an automatically increased number from 00000 to 99999. Path is relative to the screenshots/ folder, so you may specify a custom relative path as a part of the prefix, for example Name "../scr" will save screenshot to the main SpaceEngine folder.

SavePNG, SaveTGA, SaveJPG, SaveDDS, SaveTIF
Loads a specified image file and saves it to the format, specific for each command. Syntax:
SavePNG "addons/my_addon/textures/spacecraft/texture.tga" - saves the image file to the PNG format. If there are no errors, the file texture.png will appear in the same folder.
SaveJPG "addons/my_addon/textures/spacecraft/texture.*" - saves the latest modified image whith path matches the provided mask to the JPG format. If there are no errors, the file texture.jpg will appear in the same folder. If there are multiple files with the same file name texture and different extensions, the latest (newest) file will be loaded. If that file is not of supported image format (dds, tga, png, jpg, jpef, tif, tiff), the command fails.

OptimizeSM
Loads spacecraft SM model and saves it to a smaller (optimized) format.Syntax:
OptimizeSM "addons/MyShip/models/spacecraft/Battlecruser.sm" - saves specified file to a default (int) format.
OptimizeSM "addons/MyShip/models/spacecraft/Battlecruser.sm" { ... } - use additional options:
{

Format "int_full" - change format. Possible values: "float_full", "half_full", "int_full", "float_no_uv", "half_no_uv", "int_no_uv". Default is "int_full".
Recenter true - re-centers the model.
}
When importing ship model from obj, SpaceEngine creates the sm file which uses 32-bit floating-point (FP32) values for all vertex attributes: coordinates, normals, texture UVs. Usually such accuracy is redundant and model can be saved using 16-bit floating point (FP16) or even 16-bit integer values. This reduces file size significantly, and also reduces its memory footprint in SE. The OptimizeSM command converts file to the specified format and saves it to the same directory as the original file and with the same name, adding suffix "_int_full", "_half_full" or whatever format has been chosen. The conversion process and possible errors are typed to the log file (system/se.log). To test new model in SE, you must update the ship's model cfg file - specify the new file name by the Mesh parameter (see importing ship model from obj for details). After you done with debugging, you may remove the original sm file and rename the new file by removing the suffix.
The formats "float_no_uv", "half_no_uv", "int_no_uv" are designed for meshes without texture UVs, but still experimental. Don't use them.

Watermark
Loads a custom image and displays it on screen as an transparent overlay. Useful to make "reality comparison" screenshots. Syntax:
Watermark
{

Path "C:/images/Pluto New Horizons.jpg" - path to the image file.
Opacity 0.5 - opacity of the overlay. Default is 0.5.
Visible false - enable/disable overlay. Default is true (enable).
}
You may make a simple script which will display and hide the overlay texture every second (simulation of the blink-comparator technique widely used in astronomy). Use the sequence of Watermark { Visible true }, Wait 0.5, Watermark { Visible false }, Wait 0.5 enclosed by the Loop / EndLoop commands.

SetLocale
Switches SpaceEngine interface language to the specified one. Syntax:
SetLocale "fra" - switches to French.
Accepted values are abbreviation of locales: "eng", "rus", "fra", "ger", "ita", "espsp", "espla", "cat", "pol", "cht", "chs", "jpn", "cro", "cze", "fin", "ned", "swe", "srb", "ukr", "hun", "ind", "nor", "ptpt", "ptbr", "ro", "sk", "tur". This list is generated from the localiztion files *-gui.cfg located in the data/locale/ folder.

ExportLocale
Exports current localization to the CSV format used by the Localizor.io service. The command creates files export/Localizor/loc-gui.csv and export/Localizor/loc-scripts.csv, where loc is abbreviation of the current localization. The first file corresponds the Translations { } section of the localization file, the second one - the ScriptTranslations { } section.

ImportLocale
Imports the SCV files from the Localizor.io service to the current localization. The command reads two files import/Localizor/loc-gui.csv and import/Localizor/loc-scripts.csv, where loc is abbreviation of the current localization The command, if succeed, backups the current localization file to the data/locale/loc-gui.bak and creates the new data/locale/loc-gui.cfg file with the Translations { } and ScriptTranslations { } sections replaced by the data from the corrsponding CSV file.
Note the difference in file path with the ExportLocale function: import/Localizor/ and export/Localizor/.

PrintTranslations
Prints to the log file a list of text strings and their translations appeared in ShowMessage and WaitMessage commands in a specified script file. Syntax:
PrintTranslations "data/scripts/myscript.sc" - doing this for the script file myscript.sc.
Path to the script file is relative to the SpaceEngine folder. The log file will be filled with a set of lines of this format:
"Message 1" "Сообщение 1"
"Message 2" "Сообщение 2"
"Message 3" "NO TRANSLATION"
"Message 4" "Сообщение 4"
Here "Message X" is original text from all ShowMessage and WaitMessage commands from the script file, "Сообщение X" - their corresponding translations located in the localization file. Currently selected localization is used. If no translation is found in a file, "NO TRANSLATION" text will be typed next to the original string.
Use this command to create/inspect translation of a user script.

DisplayMode
Switches SpaceEngine to a specified display mode. Syntax:
DisplayMode "VR"
Accepted values are: "Normal", "CubeMap", "FishEye", "Cylinder", "VR", "CrossEye", "VerPair", "HorPair", "Anaglyph", "Shutter".

ToggleStereoscopic3D
Switches display mode to the last used 3D or VR mode or back to the 2D mode.

UpdateVRControllerType
Updates the value of the read-only VRControllerType variable. VR controller must be switched on and connected. Possible values which VRControllerType will have are:
0 (controller not detected), 1 (Oculus Touch), 2 (Windows Mixed Reality), 3 (HTC Vive), 4 (HTC Cosmos), 5 (Valve Index), 6 (unknown type).

Bind
Assigns new key/axis binding for a keyboard key, mouse slide, game/VR controller button, stick or touchpad. Syntax:
Bind "Action Name" - changes binding for SpaceEngine action "Action Name" (see below):
{

Controller "XBox 360 controller" - name of the controller. Allowed are names of joysticks/gamepads reported by a system, or a special values: "Mouse", "VR Controller 1", "VR Controller 2". If not provided, this command assigns binding for a keyboard key.
Button "A" - [if Controller is provided] name of a controller button to bind (see below).
Axis "+ Left X" - [if Controller is provided] name of a controller analog axis or mouse movement direction to bind (see below).
Key1 "Ctrl-A" - [if Controller is NOT provided] name of a promary keyboard button to bind (see below).
Key2 "9" - [if Controller is NOT provided] name of a secondary keyboard button to bind (see below).
}

The full list of allowed action names for the Bind command can be seen in the config/keys.cfg, for example "MainMenu", "ToggleToolbars" etc.

Allowed values for the Button parameter:

XBox 360 style gamepads: "A", "B", "X", "Y", "Back", "Guide", "Start", "Left Stick", "Right Stick", "Left Shoulder", "Right Shoulder", "Pad Up", "Pad Down", "Pad Left", "Pad Right".
Steam VR controllers (HTC Vive stick): "A", "System", "Menu", "Grip", "Trigger", "Joystick", "Trackpad left", "Trackpad up", "Trackpad right", "Trackpad down", "Trackpad center".
Left Oculus Touch controller: "X", "Y", "Left Thumbstick", "Left Shoulder", "Menu", "X touch", "Y touch", "Left Thumbstick touch", "Left Trigger touch", "Left Trigger untouch", "Left untouch all", "Left Touchpad", "Left Trigger", "Left Trigger -", "Left Grip", "Left Grip -", "Left Thumbstick right", "Left Thumbstick left", "Left Thumbstick up", "Left Thumbstick down".
Right Oculus Touch controller: "A", "B", "Right Thumbstick", "Right Shoulder", "Home", "A touch", "B touch", "Right Thumbstick touch", "Right Trigger touch", "Right Trigger untouch", "Right untouch all", "Right Touchpad", "Right Trigger", "Right Trigger -", "Right Grip", "Right Grip -", "Right Thumbstick right", "Right Thumbstick left", "Right Thumbstick up", "Right Thumbstick down".
Generic: "But 1" ... "But 128" and hat click directions "Hat 0" ... "Hat 127".
Allowed values for the Axis parameter:

Mouse: "+ Mouse X", "- Mouse X", "+ Mouse Y", "- Mouse Y".
XBox 360 style gamepads: "+ Left X", "- Left X", "+ Left Y", "- Left Y", "+ Right X", "- Right X", "+ Right Y", "- Right Y", "+ Left Trigger", "+ Right Trigger".
Steam VR controllers (HTC Vive stick): "Trigger", "Trackpad down", "Trackpad left", "Trackpad right", "Trackpad up".
Oculus Touch controllers: "Trigger", "Grip", "Thumbstick left", "Thumbstick right", "Thumbstick up", "Thumbstick down".
Generic: "+ Axis 1" ... "+ Axis 128", "- Axis 1" ... "- Axis 128".
Generic buttons/axes names can be used with any other joysticks/controllers, or to access additional organs of the aforementioned devices.
Note: SteamVR controllers are now configured from outside of SpaceEngine, using SteamVR app, so it is impossible to assign bindings to them using SE scripts.

Allowed values for Key1, Key2 parameters:
"0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "R", "S", "T", "U", "V", "W", "X", "Y", "Z", ":", "+", ",", "-", ".", "?", "~", "[", "\", "]", "'", "Left Win", "Right Win", "Win Apps", "Sleep", "Numpad 0", "Numpad 1", "Numpad 2", "Numpad 3", "Numpad 4", "Numpad 5", "Numpad 6", "Numpad 7", "Numpad 8", "Numpad 9", "Numpad *", "Numpad +", "Separator", "Numpad -", "Numpad .", "Numpad /", "Backspace", "Tab", "Clear", "Enter", "Shift", "Ctrl", "Menu", "Pause", "Caps Lock", "Esc", "Convert", "Nonconvert", "Accept", "Mode change", "Spacebar", "Page Up", "Page Down", "End", "Home", "Left Arrow", "Up Arrow", "Right Arrow", "Down Arrow", "Select", "Print", "Execute", "Print Screen", "Insert", "Delete", "Help", "Num Lock", "Scroll Lock", "F1", "F2", "F3", "F4", "F5", "F6", "F7", "F8", "F9", "F10", "F11", "F12", "F13", "F14", "F15", "F16", "F17", "F18", "F19", "F20", "F21", "F22", "F23", "F24".
Button names can be prepended with modifiers "Ctrl-", "Shift-", "Alt-" or their combination, for example "Ctrl-Shift-A".

Edit
Toggles an editor for a selected object: the planet editor for a planetary object, nebula editor for a raymarched nebula model, and ship editor for a space ship.

Quit, Exit
Closes SpaceEngine.

DEBUG COMMANDS
These commands are used by developers, you really don't need them, except maybe the first one.

Log
Prints the message to the log file. Syntax:
Log "message".
The log (system/se.log) file will be appended with the line like this:
"LOG: data/scripts/myscrpt.sc, line 45: message"
This can be used to debug scripts.

Map
Loads an indoor map file and switches engine to it. Syntax:
Map filename - loads a map file filename.map, the file must be in the data/models/buildings/ or addons/*/models/buildings/ folder.

Scene
Switches a specified scene. Syntax:
Scene map - switches to an indoor map previously loaded by the Map command.
Scene space - switches back to the Universe (planetarium).
Scene universe - switches back to the Universe (planetarium).

DebugGen
Launches a debugging tool of the procedural terrain texture generator.

ReloadConstellations
Reloads constellation files.

Rebuild
Rebuilds the cache. Syntax:
Rebuild nebulae - clears the nebulae model cache in memory and removes the cache/nebulae/ folder.
Rebuild shaders - reloads all shaders which are currently in use.

PrintTexCacheLog
Prints to the console list of textures which are currently cached.

PrintNames
Prints to the console all forms of the 'name string' of a selected object. Syntax:
PrintNames true - print names with replacement of abbreviations to actual characters.
PrintNames false - print names without replacement of abbreviations.

PrintFont
Prints to the console all glyphs of the specified font size (only ASCII characters). Syntax:
PrintFont - print all glyphs of a font of size 1 in 16 columns.
PrintFont 2 - print all glyphs of a font of size 2 in 16 columns.
PrintFont 2 32 - print all glyphs of a font of size 2 in 32 columns.
Possible font sizes are: 0, 1, 2, 3.

ExportFont
Prints all cached font glyphs to the texture and saves it to the export/font.png file.

ExportVRActionManifest
Updates the file config/VR/action_manifest.json, used by SteamVR input system. This command exports all SpaceEngine control binding actions to the SteamVR.

PrintVars
Prints to the console all SpaceEngine variables. Syntax:
PrintVars - print all variables.
PrintVars { ... } - print variables with additional options:
{

Type "bool" - print only boolean variables.
Type "int" - print only integer variables.
Type "float" - print only float variables.
Type "double" - print only double variables.
Attachment true - print only variables with internal attachments.
html true - print variables in the html table format, used in this manual (for quick update of the manual).
}

dbSort
Sorts and saves the local database file (data/locale/loc-db.cfg and config/user-loc-db.cfg, where loc is a current localization). Syntax:
dbSort "none" - no sort.
dbSort "name" - sort by object's name.
dbSort "locname" - sort by object's localized name.
dbSort "parname" - sort by object's parent name; if parent name matches, sort by object's name.
dbSort "parlocname" - sort by object's parent name; if parent name matches, sort by object's localized name.
