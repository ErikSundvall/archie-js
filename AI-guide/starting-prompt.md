# Feature package 0001
In a big d3 visualization I want to include a codemirror instance. I want to be able to dynamically make visual arcs/lines between a hoovered node in a d3 tree visualization and related widget decorations in a codemirror instance.

This has now been partly done, but we want to add modify the example shown in the codemirror part so that the arcs show up again, then we will iterate implementation from there

Example schema and data instances:
 - The files in /testdata that contain "response" in the file name are examples that should be selectable (via dropdown) in the (rightmpst) code mirror pane. 
 - The files containing ".wt." in the file name are schema that shouls be selectable to b eloaded in the left (d3-graph) pane.

Other improvements requested:
- Fix the code that #fit-button triggers so that fitting to container works also after switching between layouts and also when resizing the window.
- Have a dropdown for selecting color scale currently used for tree branches and node labels. Keep the current categorical scale (from d3?) as one option and add another interpolated scale that slides from black at the root via medium brown for intermediate branches+leafs at intermediate levels and then medium green for outermost leafs. (Auto adjust number of shades in the scale based on how deep the deepest branch of currently loaded data is.)



# Feature package 0002
Visualize instance data together with (web template) schema. 

# Feature package 0003
TBD