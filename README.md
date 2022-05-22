# Panda
Algorithmic Soundmaking Application written in Pure Data
# What is Panda?

Panda is a program written and running in [Pure Data](https://puredata.info/). It enables algorithmic sound processing in realtime for both composition and performance. It can be used as a standalone software via a its graphical user interface, but can also be easily integrated into and extended by other Pd-programs. Most generally speaking, Panda can be seen as a sequencer for algorithms rather than soundfiles.

# Quickstart

If you want to use Panda, simply install [Pure Data](https://puredata.info/) (Pd) on your computer. Pd is free and cross-platform (Linux/Mac/Windows). Then download Panda [here](https://github.com/simonbahr/Panda) and place it in a directory where Pd will find it. (On unix systems this will usually be the ,,Pd"-directory in your home directory.) On Linux, you can use ,,Panda.desktop" to integrate Panda into your launcher. Otherwise, simply load ,,Panda.pd" from Pd.

# General Usage

When using Panda, two Pd-files must at least be open: Panda.pd (the main program window) and a Panda-project.

A Panda-project can be created using the ,,New Project"-button in the main program window. Although any Pd-file may serve as a Panda-project, it is strongly recommended to always start new projects using this feature unless you really know what you are doing: The projects timeline and pandac\~mixer will be stored in the project file rather than Panda itself, so Panda may not properly work if you create projects another way.

## Panda-objects, Items and the Timeline

Once you have created a Panda-project, you can start to add Panda-objects: Panda-objects are specific Pd-abstractions that meet certain criteria (see [\[sec:List-of-Panda-objects\]](#sec:List-of-Panda-objects){reference-type="ref" reference="sec:List-of-Panda-objects"} for a list of existing Panda-objects / see [\[sec:Creating-new-objects\]](#sec:Creating-new-objects){reference-type="ref" reference="sec:Creating-new-objects"} on how to create one). All Panda-objects take a unique name as first argument, followed by any amount of numbers or symbols. A numeric value can usually be replaced by a symbol, allowing the user to sent a continuous stream of numbers to that symbol (like: \[s symbol\] / this is possible using the \[rorf\]-object). This way, Panda-objects that generate data can be used to modulate Panda-objects generating sound, etc.

Along with a Panda-object, you must usually create one or more corresponding items for it to take any effect. An item can be created in the main window, by specifying its name, a length (in pixels) and hit ,,Add". Items show up in the timeline-window when created.

The timeline-window can be opened using the ,,Timeline"-button in the main-window. Any items will show up here. An item is a reference to a Panda-object placed in the timeline. This way, Panda-objects can be sequenced in time.

In the main-window, you can set the current position of the timeline, the playback tempo, the project length in pixels and whether the project should loop or not. You can also start and stop playback here. (Playback can also be started by pressing Ctrl+Space and stopped pressing Space again.) When an item becomes active during playback, the corresponding Panda-object will be activated. If this object produces sound, you should be able to hear its output via pandac\~.

## pandac\~ and pandac\~mixer

The pandac\~ is Pandas main audio output. It comes with a a mixer that can be opened via the main-window. Any newly generated Panda-object will be automatically create its own channel in the pandac\~mixer. (Up to this point, deleting a Panda-object will not automatically delete its corresponding channel in the mixer, this must be done by hand. Also, any new channel will be created in the upper left corner of the mixer window, meaning channels will overlay one another if not dragged to a different position.)

A channel in pandac\~mixer can be used to set the audio output of the Panda-object to ,,Solo" or ,,Mute", control its output volume and toggle on the object permanently (useful in some cases).

In the main-window, the pandac\~ displays a waveform of the current output of the left and right channels and provides main mute and volume controls as well as an option to quickly record the output.

## Generating Multiple Items[]{#subsec:Generating-Multiple-Items label="subsec:Generating-Multiple-Items"}

By clicking on the ,,Add more\..."-button in the item generation section of the main-window, you can open up the ,,Add Multiple Items"-window. You can use it to generate multiple items in a row, create some items in random positions or specify formulas to calculate positions and lengths of multiple items.

## Quantisation

Using the quantise-menu (,,Quantise\..."-button in main-window), you can generate a custom grid of qlines (= quantisation lines), similarly to creating multiple items (see [\[subsec:Generating-Multiple-Items\]](#subsec:Generating-Multiple-Items){reference-type="ref" reference="subsec:Generating-Multiple-Items"}). You can choose the stickiness of the grid and than quantise all items accordingly. This can be a very powerfull tool, e. g. when combined with the option to generate multiple items in random positions and quantise them to a more or less sticky grid, etc. It is also possible to only quantise all selected items (meaning: selected in timeline-window).

## Performance Mode

In Performance Mode, the timeline in the timeline-window will be accompanied by its horizontal sibling: the scanline. Both can be programmed to react to any sort of control input accessible in Pd. Items will be activated when colliding with the timeline OR the scanline. This two-dimensional control can be used to freely combine different Panda-objects for generating control data with Panda-objects producing sound. Other functionality like tempo, playback (on/off), loop, etc. may also be controlled live (see [\[sec:List-of-Panda-messages\]](#sec:List-of-Panda-messages){reference-type="ref" reference="sec:List-of-Panda-messages"} for a list of available messages to sent to Panda and link them to whatever input you have).

# List of Panda-objects[]{#sec:List-of-Panda-objects label="sec:List-of-Panda-objects"}

#### Sound Objects

  ------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  soundplay\~   A sample player. Outputs stereo sound via pandac\~ and to \[r\~ {itemname}-1\] / \[r\~ {itemname}-2\]\
                Arguments: name, path to wav-file, amplitude (0 to 1), initial offset (in milliseconds), loop duration (in milliseconds), playback speed (1=normal speed, 0,5=half speed, -1=reverse, etc.)

  ------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### Data Objects

  ------- --------------------------------------------
  tempo   Changes the playback tempo automatically.\
          Arguments: name, value (pixels/second)

  ------- --------------------------------------------

# List of other abstractions

  item            The item-object is used to turn a normal pd-abstraction into a Panda-object. (It must always be defined as \[item \$1\] in order to tell Panda the correct name of the instance when the abstraction is used in a project.)
  --------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  pandac\~        The pandac\~-object is used to send the output of a Panda-object to Pandas main audio output: the pandac\~. (It must always be defined as \[pandac\~ \$1\] in order to tell Panda the correct name of the instance when the abstraction is used in a project.)
  pandaswitch\~   The pandaswitch\~-object is used to turn audio output of a Panda-object on and off, depending on its activity: Its left inlet takes in sound and outputs it only when its right input is toggled on (usually by the output value of the corresponding item-object). Its right outlet can be safely connected to a switch-object to turn off DSP for the abstraction, because it is delayed in order to prevent clicks.
  rorf            ,,rorf" stands for: receive or float. The object can either take a number or a symbol as argument. If a number is given, it will output that number when loaded (as an initial value). If a symbol is given, it will behave just like a receive object and output values sent to it. Using this abstraction, arguments of Panda-objects can take either be set to a static value or linked to any other data coming in via the given receive channel.

# List of available keyboard-shortcuts

  Ctrl+Space          toggle playback on / off
  ------------------- ----------------------------------
  Space               stop playback
  Arrow left          move timeline position backwards
  Arrow right         move timeline position forward
  Ctrl+Arrow left     jump to position 0
  Ctrl+Shift (left)   toggle loop on / off

# List of Panda messages[]{#sec:List-of-Panda-messages label="sec:List-of-Panda-messages"}

You can send the messages below to panda (like: \[s panda\]):

  new-project                Start a new Panda project.
  -------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------
  timeline-position          Set the current position of the timeline cursor.
  tempo                      Set the current tempo in pixels per second.
  length                     Set the the total length (= pixels in timeline) of the current project.
  play                       Start/stop playback.
  loop                       If set to 1: Start at beginning when end of project is reached during playback.
  volume                     Set the main output volume of pandac\~ (0 to 1).
  mute                       Mute the output of pandac\~ (0/1).
  pandac\~test               Trigger pandac\~ testsound.
  \[name\]-volume            Set volume of object called name in pandac\~ mixer. (Only for sound-objects.)
  \[name\]-mute              Set mute (0/1) of object called name in pandac\~ mixer. (Only for sound-objects.)
  \[name\]-solo              Set solo (0/1) of object called name in pandac\~ mixer. (Only for sound-objects.)
  \[name\] active            Set \[name\] to active/inactive manually.
  \[name\]-always active     If set to 1, the corresponding object will always be active during playback, also if it is not referenced by an item in the timeline.
  record                     Start/stop recording to output-file. If recording is started, playback will also be started.
  browse-for-output-file     Send a bang to browse for an output-file.
  output-file                Specify an output-file-path (.wav) for recording (symbol).
  performance-mode           Turn performance mode on/off.
  scanline-position          Set y-position of scanline in performance-mode.
  add                        Add a new item. Arguents: name, length (in pix.), x-position, y-position. Without arguments, current default values (as displayed in Panda-window) will be used.
  item-name                  Set name of object to add items for.
  item-length                Set length of items to add.
  add-multiple-amount        Set amount of multiple items to add.
  add-multiple-y             Set y-position of items to add (when adding multiple).
  add-multiple1              Sent bang to add items using add-multiple1.
  add-multiple1-gap          Set gap between items for add-multiple1.
  add-multiple1-length-inc   Set length increment per item for add-multiple1.
  add-multiple1-gap-inc      Set gap increment per item for add-multiple1.
  add-multiple2              Sent bang to add items using add-multiple2.
  add-multiple2-xform        Set formula to use for computing x positions of items using add-multiple2 (symbol).
  add-multiple2-lenform      Set formula to use for computing lengths of items using add-multiple2 (symbol).
  quantise                   Sent bang to quantise items to nearest qlines.
  add-qline                  Add a new q-line (quantisation-line). Arguents: position on x-Axis (default: 0).
  qline-stickiness           Maximum amount of pixels between a q-line and an item for the q-line to be effective when quantising.
  add-qlines-amount          Set amount of qlines to add via add-qlines1 or add-qlines2.
  add-qlines1-gap            Set gap between qlines when adding via add-qlines1.
  add-qlines1-gap-inc        Set increment of gap per qline when adding via add-qlines1.
  add-qlines1                Sent bang to add qlines using add-qlines1.
  add-qlines2-form           Set formula to use for positions of qlines when using add-qlines2 (symbol).
  add-qlines2                Sent bang to add qlines using add-qlines2.
  show-main-win              Open the main window of the current project.
  show-setup-win             Open the setup window.
  show-quantise-win          Open the quantisation window.
  show-pandac\~mixer         Open the pandac mixer of the current project.
  show-timeline              Show a numeric timeline in main-win.
  timeline-marker-height     Set the height of the timeline-marker
  show-item-positions        Display the position of each item (in pixels) above it in main-win.
  show-item-lengths          Display the length of each item (in pixels) above it in main-win.
  set-fade-in-duration       Set duration for fade-in in pandaswitch\~.
  set-fade-out-duration      Set duration for fade-out in pandaswitch\~.
  debug                      Print all Panda messages for debugging.

# Creating new objects for Panda[]{#sec:Creating-new-objects label="sec:Creating-new-objects"}

Creating new Panda-objects is as easy as writing any other PD-patch, as long as the following criteria are met:

-   The first argument (\$1) of any Panda-object is always a unique name (symbol).

-   The unique name is passed to an \[item\]-object (-\> \[item \$1\]). The output of \[item\] indicates, if the Panda-object is active.

-   Controlrate-objects may have any number of inlets and outlets, but will usually have none.

-   Signalrate-objects usually have two (or more) signal outlets (and any number of control inlets and outlets, but usually none too).

-   All numeric values passed as arguments should be taken as numbers or receive symbols of number streams (see \[rorf\]-object).

-   Each signal-rate outlet MUST be preceded by a \[pandaswitch\~\]. Its left outlet outputs the audio signal of its left inlet or if inactive silence. Its right inlet should be connected to the outlet of \[item\]. One right \[pandaswitch\~\]-outlet per patch should be connected to a \[switch\~\] object, turning off DSP when the object is inactive. This practice prevents clicks when objects are toggled on and off.
