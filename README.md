# SET UP #

## Clang ##

Clang auto formatter is used to manage code quality. Install Clang-Format by
Xaver Hellauer. Then go to your user settings json file and add the following
settings:

```
"clang-format.executable": "C:/Users/jason/Desktop/Projects/NBodySimulator/.Configuration/Clang/clang-format.exe",
"cmake.configureOnOpen": true
```

When you run the `SetUpDevelopmentEnvironment.sh` script, the up to date clang
formatting file should be moved into your workspace.

## VARIABLE NAMING CONVENTION ##

Variables follow the camel case naming convention with the following format.

`\<name\>_\<frame\>_\<unit\>_\<input/output\>`

Note: If one or more of tags is not required then it can be ommited from the
variable. For example, if a vector is independent of a frame, a frame
tag should not be included. (The obvious exception is the 'name' tag)

Note: These conventions where introduced after start of development and hence
not all the code may follow it. It is advised that the variable naming
conventions be changed at any available oppertunity.

### name ###

This is the name of the variable. Generally it should contain at least 2
descriptive words, although there may be exceptions to having 1. The purpose is
to make it easy to identify what the variable represents.

### frame ###

This is to indicate the frame which the vector is orientated against. It does
not necessairly mean that it is the frame the variable is in as during
transitions you can have a vector that is within an intermediatary frame. If a
vector is in the orientation of frame1 but has its origin in frame 2 then the
'frame' is frame1 but the vector is relative to frame2. Hence the term
'RelTo\<frame2\>' should be used in the name tag.

For variables which represent a rotation this should should clearly indicate the
starting frame and which frame the rotation will result in. For example, say a
quaternion variable represents a rotation from frame1 to frame 2, the frame tag
will say '\<fram1\>To\<Frame2\>'

### unit ###

This part of the tag represents the unit of the variable. All units should be
lower case, except for places where it may be easier to read if camel case was
used. Numbers represent the power to raise the unit to the left.

For example kg m per seconds squared (Newton) is kgms2

### in/out ###

This is to indicate if the variable is an input to a function or an output to a
function. If the variable is defined within a function then this part should be
ommitted.

# TEST CASES #

## test case location and naming ##

All test cases should be located in `SourceCode/TestCase/`. There can be
multiple layers within this directory so that tests can be grouped together. An
example can be found `SourceCode/TestCase/UnitTest/`.

Within a test case, the script which contains the main function shall be called
main.c. This is to clearly indicate the top level of where the test case is
running from.

In the `CMakeLists.txt`, when using the `add_executable()` function, the target
name of the executable shall match the name of the test case. This is so that it
is clear that the executable is linked to a particular test case. This will link
to the `RunTest.sh` bash script which is used to offer an easy way to run the
test case.

## running a test case ##

Running a test case is done using the `RunTest.sh` bash script. Essentially, you type:

```./RunTest.sh <additional_flags> <positional_arguments_to_test_case>```

You can give an absolute path or a relative path as either a single input or
listing each folder of the directory. It should be noted that it is the
directory to the test case folder. This should match up with the directory to
the folder which contains the executable in the `BuildCode` directory.

Also note, that you can give the relative path from the root of the workspace.
i.e:

```./RunTest.sh SourceCode/TestCase/UnitTest/ModuleUnitTest/```

This was implemented to take advantge of the auto tab complete.

Use the -h flag to get an up to date description of what the script does.

## parameters ##

Parameters is an ongoing development. For the latest update on how they are set
up, read `Parameters/README.md`.

# FRAMES #

Unless in very specific situations, all frames can be assumed to follow a right handed orthogonal axis system. For more infomration about rotations, read the **ROTATION SEQUENCES** section.

|       Frame Name      | Abbreviation |                 Description                  |     Origin    |  Definition of x-axis  |   Definition of y-axis   |     Definition of z-axis     |                                     Note                                                           |
|:---------------------:|:------------:|:--------------------------------------------:|:-------------:|:----------------------:|:------------------------:|:----------------------------:|:--------------------------------------------------------------------------------------------------:|
|       Fixed Frame     |     Fix      | Origin which all frames are based around.    |    [0,0,0]    |       [1,0,0]          |          [0,1,0]         |            [0,0,1]           |                                                                                                    |
|       Body Frame      |     Bod      | Frame used to define the body of rigid body. |      COG      |   Longitudinal Axis    |       Lateral Axis       |    Cross Product of x & y    | Note that definition may vary for some bodies.                                                     |
| Celestial Body Frame  | <bodyName>Cb | Body frame of Celestial Body.                |      COG      |   Longitudinal Axis    |       Lateral Axis       |    Cross Product of x & y    | Useful for orbital analysis and offering clarification.                                            |
| Celestial Fixed Frame | <bodyName>Cf | Inertial frame of Celestial Body.            |      COG      |     BODY DEPENDENT     |      BODY DEPENDENT      |        BODY DEPENDENT        | Useful for orbital analysis and offering clarification.                                            |
|   Perifocal Frame     |     Per      | Frame used to project orbits of satellites.  |   Body1 COG   |   Towards Periapsis    |  Cross Product of x & z  | Unit angular momentum vector | y-axis is parrallel to the Semilatus rectum.                                                       |
|      Geo-Centric      |    GeoCen    | Frame centered at body and fixed to sufrace  |   Body COG    |    Dependent on Body   |    Dependent on Body     |       Dependent on Body      | Axis definitions should be documented in Geo-Centric Body Definitions section                      |
|   Inertial-Centric    |   InertCen   | Frame centered around a body.                |   Body COG    |    Dependent on Body   |    Dependent on Body     |       Dependent on Body      | Axis definitions should be documented in Geo-Centric Body Definitions section                      |
|    North-East-Down    |     ned      | Frame which orientates to geographic areas.  |   <varries>   | North of orbiting body | Cross Product of x and z |   To COG of orbiting body    | Shoule be noted that x and y are tangental to orbital body, and z is radial                        |
|     Sensor Frame      |     Sen      | Frame which the sensor is orientated.        | Sensor Origin |     Sensor x-axis      |       Sensor y-axis      |        Sensor z-axis         | This frame is dependent on the sensor it is representing. Also used for describing hardware frames |

## Geo-Centric Body Definitions ##

Geo-Centric frame is a general term for a Celestial Body Frame. This is used
where functions aren't clear about the body that is being used. For example, in
the IGRF model, the primary body which the magnetic field is being simulated for
can be used for planets such as Saturn or Earth. So, for inputs into the
function, the term GeoCen is used as the frame tag in the name convention.
Similar thinking is behind Inertial Centric frame.

GeoCen is bassically a body frame of celestial body. Its origin is at the bodies
COG and it rotates with the body, meaning it is fixed relative to the surface.

InertCen is bassically an inertial frame for a body. Its origin is at the bodies
COG but it does not rotate with the body.

# VECTORS #

# Cartesian Vectors #

All cartesian vectors shall be broken down into the form:

cartesianVector = [

  X_COMPONENTS,

  Y_COMPONENT,

  Z_COMPONENT

]

# Spherical Vectors #

All spherical vectors shall be broken down into the form:

sphericalVector = [

  RADIUS,

  AZIMUTH,

  ELEVATION

]

NOTE: azimuth and elevation can be refered to as longitude and latitude.

**Azimuth**: Defined as the angle between the projection of the radius vector on
the xy plane and x axis, with positive rotation being applied as the right hand
rule around the z axis.

**Elevation**: Defined as the angle between the projection of the radius vector
on the xy plane and the radius vector, with positive rotation applied as the
right hand rule on the x axis.

### Units for a Spherical Vector ###

Their is a problem of tagging the units for spherical vector variables. This is
because their is a distance and an angle in the same vector. To get around this,
the angles will **ALWAYS** be in radians. Hence the unit tag is their to
describe the units of the radius element.

# ROTATIONS #

## QUATERNIONS ##

The standard for quaternions in this project is to have the scalar quaternion at
the end of the quaternion vector, as such:

quaternion = [

  X_QUATERNION_COMPONENT,

  Y_QUATERNION_COMPONENT,

  Z_QUATERNION_COMPONENT,

  S_QUATERNION_COMPONENT

]

where, s represents the scalar component.

If applicable, a quaternion can also be broken down into its scalar and vector
components:

quaternion_gibs_vector = [

  X_QUATERNION_COMPONENT,

  Y_QUATERNION_COMPONENT,

  Z_QUATERNION_COMPONENT

]

quaternion_scaler = S_QUATERNION_COMPONENT

The quaternion frame tag shall always refer to being a rotation from one frame
to another (E.g. from the initial frame to the body frame).

## EULER ANGLES ##

The standard for Euler Angle vectors is 123 (XYZ). The reason for this is the
position within the vector resembles the number convention for rotation.

eulerAngle = [

  ROLL,

  PITCH,

  YAW
  
]

## ROTATION SEQUENCES ##

Whenever euler angles are involved, the rotation sequence is 321 (ZXY). This
means that, starting from the left, a rotation is performed about the Z, then Y
then finally X axis.

Positive rotation is defined with respect to the right hand rule.

Yaw is defined as rotation around the z-axis
Pitch is defined as rotation around the y-axis
Roll is defined as rotation around the x-axis
