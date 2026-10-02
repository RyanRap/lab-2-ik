# Lab 2

Be sure to read lab instructions carefully. Questions in the lab should be
answered in your write-up with full sentences.

Learning objectives:

- Practice with Python
- Practice with Pixi, a package manager we'll be using through the course
- Learning about URDF files
- Learning to use an inverse kinematics solver

Deliverables:

- Undergraduates: PDF or Markdown file, with code and outputs included as formatted text or
screenshots.
- Graduates: ZIP folder containing your writeup (including code as formatted
text or screenshots) and video.
- You do not need to submit your Jupyter notebook or other source code.
- Feel free to fork this repository to keep a version of your work on your own GitHub profile.

## Part 0: Pixi

First, we must install a package manager to install software for us.
[Pixi](https://pixi.prefix.dev/latest/) is
a relatively new cross-platform "environment management" tool that will come in handy
this quarter as we work with various robotic systems. It is especially good for
managing Python packages and dependencies (if you are familiar with uv for
Python, pixi is basically uv + a bit more functionality for projects that need
non-Python software; if you are familiar with Docker, Pixi is similar but a bit
 more lightweight than Docker).

### Environment Configuration

IMPORTANT: If you are working on a CSCI Debian lab machine (in our classroom or
any computer lab), you should work in a local directory specific to the actual
machine you are sitting at (or SSH-ed into). This helps preserve your active
directory storage space that is shared across all lab machines, and helps avoid
us slowing down the fileserver by downloading too many things to shared
fileserver directories. Here is how to make sure you're working locally:

1. Open a terminal and run the command `source set_pixi_env`. This command sets
the pixi cache directory to `/local/$USER/cache`.
2. Change directories to your local directory folder: `cd /local/$USER`
3. From that directory, run the rest of the instructions in this lab. Pixi is
already installed on all the CSCI lab machines.

Keep in mind that this environment, and any modifications you make to the repo
files, will only exist on the *specific machine* you set up your environment on.
Recommended workflows include:

1. sitting and working at the same machine for the entire lab; or
2. using SSH to access the same machine from home or another
   lab machine (make sure to set up X-forwarding if you choose this route); or
3. Forking the repository on your own github account, and using git to track
   changes across machines (will have to run `source set_pixi_env` on each machine
   and clone repo).

### Installing Pixi on your own computer

To install pixi on linux, run the following command in a shell:

```bash
curl -fsSL https://pixi.sh/install.sh | sh
```

or use wget:

```bash
wget -qO- https://pixi.sh/install.sh | sh
```

See the [Pixi docs](https://pixi.prefix.dev/latest/installation/) for install instructions on other operating systems.

### Tasks:

1. Install Pixi or set up your environment on a lab machine.
2. Clone this repository (to `/local/$USER` if on lab machine).
3. In the top level directory of the repository, run `pixi install` to initialize the packages we will need.
4. Run `pixi shell` to start a shell with our software. Work in this shell for the rest of the lab.

## Part 1: URDF

**URDF** stands for **Unified Robot Description Format.** It is an XML-based
text format for representing a robot model. [The full specification can be found
here](https://wiki.ros.org/urdf/XML).

### Optional: Visually Inspect the Robot Arm Model

There are many tools for creating, viewing, and editing URDF files. One tool
for viewing the models defined by URDF files is `yourdfpy`, included in this pixi package.

In the terminal running your pixi shell, run the command

```bash
yourdfpy ./ur5/ur5_gripper.urdf
```

and a new window should open, allowing you to visually inspect the robot model.
This is a visualizer to help you get an idea of the robot geometry. You can
click and drag to rotate, and scroll to zoom.

This robot is a Universal Robots model ur5e robot arm (no relationship between the acronyms
URDF and ur5e). It's similar, but slightly different, to the robot arms we
have in the lab (Universal Robots model ur3e).

If you can't get the visualizer working or otherwise cannot
see it: the robot arm consists of a base and a sequence of joints and links.
The links are cylindrical and
straight between the joints, except a few that are shaped like a 90-degree
turn so that the links do not all lie perfectly in a line. There are six joints: a base joint,
shoulder joint, elbow, and three wrist joints. Like a human arm, the two longest
links are from the shoulder to the elbow, and from the elbow to the wrist. The two longest
links are slightly offset from each other when the robot arm is stretched out as
far as it can reach, due to the 90 degree turns at some of the joints.

### Analyze the URDF File

Open the repo's URDF file, `./ur5/ur5_gripper.urdf`, in a text editor and skim through the file. As you may notice,
these files are not the nicest to read and write by humans.

Our URDF file was created using a macro language for describing
XML, commonly called a "xacro file". Inspect the xacro file
[here](https://github.com/fmauch/universal_robot/blob/kinetic-devel/ur_description/urdf/ur5.urdf.xacro).

1. (5 points) From the xacro file, find the lengths of each link of the robot
arm and include a list of these lengths in your write-up.
Given the link lengths, and possibly your knowledge of the robot arm geometry from the
visualizer, compute a maximum possible overall length of the robot arm. Note that units are in meters.
This does not need to be the actual overall length of the arm, but rather a
somewhat close upper bound.

## Part 2: Inverse Kinematics

In a terminal at the top level of the repository, run `pixi run setup` then `jupyter lab`.
The second commmand will open a browser window. This is the Jupyter Lab
interface; Jupyter notebooks are interactive browser-based files that can contain text,
images, and executable Python (or Julia) code.

If not already open (with title "IKpy Quickstart"), use the sidebar to open the file `my_IK.ipynb`
by double-clicking on the filename.

Click the "497 Kernel" button at the top right of the notebook in your browser.
On the window that opens, click the drop-down button, and select the kernel "497
Kernel" under the heading "Start Python Kernel". This tells Jupyter Lab what
Python environment to use. Click "Select".

In Jupyter notebooks, you execute cells of code by clicking within the cell and
 pressing the `Shift` + `Enter` keys simultaneously. The gray block to the left
of the cell will look like `[*]` while the cell code is executing, and will
display a number in the brackets when the code has finished executing. These
numbers track the order that cells were executed. Most of the time, you want to
execute cells in order from top to bottom of the notebook.

Execute all the cells of Python code in the notebook while reading along and
looking at the output of the code cells (printed below the cell when execution
finishes).

When you get to the final cell, feel free to play around with different target positions,
and see what effect they have on the resulting robot arm poses. You can click
and drag the plot to rotate and use additional controls that appear in the top
left of the plot when you have your mouse cursor in the plotting cell.

2. (5 points) Can you get your robot end effector to the maximum range you estimated in Q1,
along any of the x/y/z axes? Why or why not?

3. (25 points) Write code to estimate the boundaries of the 3D volume of points that are reachable
by the robot arm. You probably want to sample from points on a large sphere around the robot,
and attempt to get the robot to reach those points, such that the arm is fully "stretched".
Your code should return your results: at least, print out all the reachable
points, but I encourage you to find more sophisticated ways to plot or summarize
your data. You might fit an oblate spheroid to the data, or display the convex
hull, or generate linear constraints containing the data - this part is up to
you! Include your code and results in your write-up (screenshots are OK).
    - Hint: the forward kinematics function gives you the actual
      position of the end effector for a given target position. The target position is
      visualized as a red dot in the original plotting code. Record the
      actual positions.
    - You may look into the "Convex Hull" method in the "scipy" Python library, as well as the method
      `plot_trisurf` from the matplotlib library.
    - AI is allowed for producing the visualization code, but not for
      sampling from the sphere. Sampling points on the surface of a sphere is a
      non-trivial problem; scroll to the bottom of [this Wolfram explainer](https://mathworld.wolfram.com/SpherePointPicking.html)
      for a direct way to sample uniformly at random. You may also consider
      sampling deterministically.

From here on out, try not to use LLMs/AI except to ask specific questions about
syntax, library methods, and Python syntax: you will benefit from trying to
solve the problem on your own.

## Part 3: Collision Checking

4. (65 points) Define and visualize a plane in the 3D plot with the robot arm; the plane should not
intersect the robot arm when all the joints are set to zero position. Write code
to determine if **any part of** the robot arm is intersecting the plane for an arbitrary pose of
the robot arm, and include at least three test cases for your method. You may
assume the robot arm links have zero width; in other words, as long as all the
joints are on the same side of the plane, you can assume the robot arm is not
colliding with the plane.

Hint 1: [defining and plotting a plane](https://stackoverflow.com/questions/53698635/how-to-define-a-plane-with-3-points-and-plot-it-in-3d)

Hint 2: the following code will create `nodes`, a list of arrays where each array contains
the `[x,y,z]` positions of a joint.

```python
from ikpy.utils import geometry
nodes = []
# "chain" is the Chain object returned by ikpy when it loads URDF file
# "joints" is the solution from your inverse kinematics solver
transformation_matrixes = chain.forward_kinematics(joints, full_kinematics=True)

# Get the nodes and the orientation from the transformation matrix
for (index, link) in enumerate(chain.links):
    (node, orientation) = geometry.from_transformation_matrix(transformation_matrixes[index])

    # Add node corresponding to the link
    nodes.append(node)
```

## Part 4: Trajectory Generation **(Graduate Students Only)**

5. (40 points) Imagine there is a marker attached to the end of the robot arm
and you are trying to get the robot arm to draw something on a whiteboard.

Write code to generate a trajectory for the end-effector on the
plane. In other words, generate a sequence of points that you can feed
into your inverse kinematics solver. The shape of the trajectory can be anything
you want, but should include at least three points. The total trajectory should
span enough distance that you can see the simulated robot moving when it executes the
trajectory (don't just put three points 0.0001 meters apart). Recommended
trajectories include triangles, circles, or heart shapes (in order of
increasing difficulty).

The execution of the full trajectory should move the end effector very near to the plane,
but no part of the robot arm should intersect the plane.

Your trajectory should start from the "zero position" of the robot ($\theta=0$
for all joints, the default position when URDF is loaded).

Include your code in your write-up, but also, create an `mp4` video of your robot arm moving (either [directly in
python](https://matplotlib.org/stable/users/explain/animations/animations.html),
or by saving static images in a loop using `savefig` to use as frames in a
video. If going with the second
option, you can get fancy and animate your frames with ffmpeg, capcut, etc, or you
can do something like screen record a sequence of frames in a slideshow.
Submit this animation along with your writeup in a zip folder.

Hint: manually define a few "safe" waypoints, starting from the zero position.
Look at the [inverse kinematics
documentation](https://ikpy.readthedocs.io/en/latest/inverse_kinematics.html) and try
setting the `starting_nodes_angles` using previous "safe" solutions.

Optional extension: You may
also try setting the [orientation
mode](https://github.com/Phylliade/ikpy/wiki/Orientation), which would be
necessary to actually control the orientation of a marker on the end effector.

(Extra credit, up to 10 points) Visualize the trajectory of the robot end effector by "tracing a
line" that persists in your visualization.
