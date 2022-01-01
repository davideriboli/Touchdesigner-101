# TouchDesigner and Open Stage Control

This guide is for setup a basic environment for live performance using [TouchDesigner](https://derivative.ca/) and [Open Stage Control](https://openstagecontrol.ammd.net/) to receive and send OSC messages via UDP protocol.

There are already some software that enables this kind of comunication, like TouchOSC and the TouchOSC Bridge, both of which I've personally tried and they sure do their job flawlessy.

But many people don't wanna spend money on apps for experiments, or maybe they want more customization than the one provided by TouchOSC Editor.

In this case, Open Stage Control is the perfect choice to go: it's Open Source, and it's written and compiled in Electron, which gives us some of the cool features of Javascript and CSS for styling.

Index of contents:
- [Installation](#installation)
- [Open Stage Control Setup](#open-stage-control-setup)
 	- [Setup a basic controller](#setup-a-basic-controller)
- [TouchDesigner Setup](#touchdesigner-setup)
- [Importing and Exporting Layouts](#importing-and-exporting-layouts)

## Installation
First you must download both TouchDesigner and Open Stage Control.
When you have them, run TouchDesigner and create a blank project.

*For Windows only* 
Since the OSC messages will be passed over the local network, we have to make sure that the firewall of Windows is not blockin them.
To do so go to Control Panel>System and Security> Windows Firewall > Allow App through Windows Firewall.
![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/cNz2IewUzQtBWC9/download)

This will bring you to a list, scroll down until you find TouchDesigner and allow by clicking the checkbox for both public and private network.

## Open Stage Control Setup
When you open Open Stage Control you will be prompted to a setup window.
The only thing that we wanna insert here is in the first row where it says "send" which defines to which address should the next window of panel editor send the messagges.
In our example I've inserted the following:

`localhost:3746`

meaning that we'll send to our local machine at the port `3746`.
![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/H2z1KDFa5MnZprz/download)

Now we just have to press the play icon on the top left of the window in order to start the Open Stage Control controller window.

### Setup a basic controller

Once we've started the controller we'll be prompted to a  blank window

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/vfLUZy2OB5bOQcu/download)

In order to create a new controller we have to click on the three dots icon on the top left and then click Session>New Session

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/2tY5O4VAepmkT3r/download)

In this way, we enter in the **"Editor Mode"**, the shortcut to jump from Editor Mode to Controller Mode is **CTRL+E** on Windows and **CMD+E** on Mac.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/lUlqAfHXlF246w9/download)

Now, if we click anywhere on the centered part of the editor, a dialog window shows up.
There we can add all of the supported **widgets** and **tabs** of this program, such as buttons, faders, leds etc.
For this basic setup, we're going to Add widget>Sliders>Fader

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/BIkqaKS76A9utof/download)

We can see that in the grid has appeard a little blue slider.
Now we can modify all of the values and style of this element by changing its parameters on the **Inspector** window on the right side of the window.
If you have any familiarity with Web Development and particularly CSS rules, you'll find many similarities with this setup.
Keep in mind that the editore threats all of its child elements as parent of the **root** (as you can see in the left view of **Project Tree**), so if you can't see the indicators that we'll talk about right now, see if you are with the correct element selected.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/VXVbnftBMgOhQWO/download)

If we scroll down to the end the **Inspector** tab, while we have the fader element selected in our **Project Root**, we should see a blue tab with the **osc** title that defines where does this fader addresses the value.
Here we are simply going to modify the **adress** that this fader will have from `/fader_1` to `/red_channel`.

**NOTE**
Everytime we make some changes to some paramters of an element, remember to press **Enter** on your keyboard to apply the changers, otherwise it will not be stored.



That's it for the Open Stage Control side of this basic setup, next we'll configure the receveing part for TouchDesigner.

## TouchDesigner Setup

We start with a blank project in TouchDesigner.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/wrRYBwe4wp2Wf9a/download)

We add a **OSCin CHOP** to the project.
Once we add it, change de default parameter of **Network Port** from **1000** to **3746** as defined in the Open Stage Control setup window.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/l7KtXcxoiqMF1kh/download)

Next add the very basic for compositing a 3D scene in TouchDesigner.
So, first add a **Geometry COMP** followed by a **Camera COMP** and a **Light COMP**, link everything with a **Camera TOP**.
Lastly we add a **Phong MAT** that will copy its material parameters to the **Geometry COMP**.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/1RZjYlN6x63QBAY/download)

If we now tweak the slider back in Open Stage Control (you can move it even in **Editor Mode**, but if you wanna stay sure from moving it atound, remember that you can exit the Editor Mode by pressing CTRL+E [win] or CMD+E [Mac]), we should see the **oscin1 CHOP** receiveing these values in real time (remember to press the SpaceBar in TouchDesigner to see the live render of the project)

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/svCv72CosUah5TW/download)

Now we have to connect to the **oscin1 CHOP** a **Select CHOP** and change the *red_channel* name to timply *r*.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/oDr9tUFjZK5SUuo/download)

Now, back to the **phong1 MAT**, we have to expand the Diffuse parameters under RGB Section, and manually type in the **diffr** channel the following:
`op("select1")["r"]`

This will copy a reference to the just created select chop, that will hold the Red value received from the OSC controller that we'll paste onto the Red Diffuse channel of the Material.
If we now move around the slider while playing the project we can see how the slider affects live the color of the material.

Connect the **Render TOP** to an **Out TOP** to see this while in **Perform Mode** in TouchDesigner.

![](http://dcaccademia.abaurbino.it/owncloud/index.php/s/TkRTOVA1frETlwt/download)

## Importing and Exporting Layouts

One of the cool features of Open Stage Control is that, being written in full JavaScript, it gives us the possibility to import and export our layouts in .JSON format.
To do this, simply click on the three dots icon in the top left corner of the controller window, there we have **Session>Import** and **Session>Export**.
Here we are prompted to a dialog window where we can choose the file we wish to import/location to export our layout.

In this guide, you'll find inside the **examples** directory other directories with both the **.toe** file for TouchDesigner and the **.json** file for the layout of that Open Stage Control controller.
NOTE
Remember to set the right port number in the Open Stage Control setup window, in all of my examples I've used the port **3746**.
