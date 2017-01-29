.. @+leo-ver=5-thin
.. @+node:ekr.20170129112253.1: * @file ..\doc\demo.md
.. @@language rest
.. @@wrap
.. @+others
.. @+node:ekr.20170129112412.1: ** Overview
This plugin runs dynamic demos from Leo files.

The **script tree** controls the demo. Pressing the space bar executes the next script in the tree. Demo scripts alter the outline, freeing the presenter from the burden of having to type correctly or remember the sequence of desired actions. 

To run a demo, you create a **top-level script** that creates an instance of the Demo class, or a subclass of the Demo class. The top-level script calls Demo.start(p), where p is the root of the script tree.

Demo scripts have access to the 'demo' variable, which is bound to the Demo instance. This allows demo scripts to use any of the many **helper methods** in the Demo class. Here is a sample of what helper methods can do:

- Animate typing in headlines, body text, the minibuffer, or anywhere else.
- Overlay a scaled image on the screen.
- Open any Leo menu, selecting particular menu items.
- Scale font sizes.
- Open another .leo file and present the demo in the new outline window.





.. @+node:ekr.20170129112922.1: ** The Demo class
The Demo class controls key handling during screencasts and
executes demo scripts as the demo moves from node to node.

During a demo, the Demo class traps the RtArrow and LtArrow keys, passing all other keys to Leo's key-handling code. This allows key
handling in key-states during the execution of a screencast. For example::

```python
    demo.single_key('Alt-X')
    demo.plain_keys('ins\\tno\\t\\n')
```

executes the insert-node command!
.. @+node:ekr.20170129113435.1: ** Helper methods
Demo scripts may use the following helper methods:


**demo.body(s)**, **demo.log(s)** and **demo.tree(s)** create a caption with text s
in the indicated pane. A **caption** is a text area that overlays part of
Leo's screen. By default, captions have a distinctive yellow background.
The appearance of captions can be changed using Qt stylesheets. See below.

**demo.body_keys(s,n1=None,n2=None)** Draws the string s in the body pane of
the presently selected node. n1 and n2 give the range of delays to be
inserted between typing. If n1 and n2 are both None, values are given that
approximate a typical typing rate.

**demo.command(command_name)** Executes the named command.

**demo.dismiss_menubar()** Dismisses the menu opened with demo.open_menu.

**demo.focus(pane)** Immediately forces focus to the indicated pane. Valid
values are 'bodly', 'log' or 'tree'.

**demo.image(pane,fn,center=None,height=None,width=None)** Overlays an image
in a pane. The valid values for `pane` are 'body', 'log' or 'tree'. `fn` is
the path to the image file, resolved to the leo/Icons directory if fn is a
relative path. If `height` is given, the image is scaled so it is height
pixels high. If `width` is given, the image is scaled so it width pixels
wide. If `center` is True, the image is centered horizontally in the given
pane.

**demo.head_keys(s,n1=None,n2=None)** Same as demo.body_keys, except that the
keys are "typed" into the headline of the presently selected node.

**demo.open_menu(menu_name)** Opens the menu whose name is given, ignoring
case and any non-alpha characters in menu_name. This method shows all
parent menus, so demo.open_menu('cursorback') suffices to show the
"Cmds\:Cursor/Selection\:Cursor Back..." menu.

**demo.plain_keys(s,n1=None,n2=None,pane='body')** Same as demo.body_keys, except
that the keys are typed into the designated pane. The valid values for the
'pane' argument are 'body','log' or 'tree'.

**demo.quit** ends the screencast. By definition, the last slide of screencast
is the screencast node that calls demo.quit.

**demo.redraw(p)** Forces an immediate redraw of the outline pane. If p is
given, that position becomes c.p, the presently selected node.

**demo.selectPosition(p)** Same as demo.redraw(p)

**demo.single_key(setting)** generates a key event. Examples::

   demo.single_key('Alt-X') # Activates the minibuffer
   demo.single_key('Ctrl-F') # Activates Leo's Find command

The 'setting' arg can be anything that would be a valid key setting. The
following are equivalent: "ctrl-f", "Ctrl-f", "Ctrl+F", etc., but "ctrl-F"
is different from "ctrl-shift-f".

**demo.start(p)** Starts a screencast at node p, regardless of whether p is an
\@screencast node. This is useful during development while testing the
script in node p.
.. @+node:ekr.20170129112613.1: ** demo-start (probably obsolete)
The demo-start command starts a screencast. This command first
searches backwards for the nearest \@screencast node. If no such node is
found, the command searches forwards for the next \@screencast node. This
command then executes the script in the body text, and pauses. Thereafter,
the Right Arrow key executes the script in the next slide node (in outline
order). The Left Arrow key executes the script in the previous slide node.
The Escape or Ctrl-G keys terminate any screencast.
.. @+node:ekr.20170129112514.1: ** The program counter, demo.p
Most presenters will want to keep the nodes of the presentation tree
hidden. Instead, presentation will make *other* nodes visible by calling
demo.selectPosition(p) or demo.redraw(p).

Thus, there must be a sharp distinction between the presently *selected*
node, c.p, and the present screencast node, demo.p. You can think of demo.p as
the program counter for the screencast.

By default, after executing a screencast script, the Demo class advances demo.p to the next non-empty, non-ignored node in the \@screencast tree. However, if the just-executed screencast script has set demo.p to a new, non-empty value, that value will be the new value of demo.p.
.. @+node:ekr.20170129112546.1: ** Style sheets
Presenters may alter the appearance of captions by using changing the
following stylesheet::

    QPlainTextEdit#screencastcaption {
        background-color: yellow;
        font-family: DejaVu Sans Mono;
        font-size: 18pt;
        font-weight: normal; /* normal,bold,100,..,900 */
        font-style: normal; /* normal,italic,oblique */
    }

You will find this stylesheet in the node @data
``qt-gui-plugin-style-sheet`` in leoSettings.leo or myLeoSettings.leo.


.. @+node:ekr.20170129113314.1: ** Acknowledgements
This plugin was inspired by [demo-it](https://github.com/howardabrams/demo-it/blob/master/demo-it.org). Or perhaps demo-it was inspired by Leo's earlier screencast plugin.
.. @-others
.. @-leo