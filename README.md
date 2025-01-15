# File-System-Visualization-with-Treemap

# Introduction

Sometimes size is attached to the data in a tree. For example, in a tree representing the departments of a company, the size of a node could be the dollar amount of all sales for that department, or the number of employees in that department. Or in a tree representing a computer's file system, the size of a node could be the size of the file.

A treemapLinks to an external site. is a visualisation technique that shows a tree's structure according to the weights (or sizes) of its data values. It uses rectangles to show subtrees. The rectangles are scaled to reflect the proportional sizes of each piece of data. Some free programs use treemaps to visualise the size of files on your computer, such as (Windows) WinDirStatLinks to an external site., (OSX) Disk Inventory XLinks to an external site., and (Linux) KDirStatLinks to an external site..

The order in which subtrees are shown matters when modelling files, but not when modelling research paper data.
The colours in the visualization are independently randomly-generated for each node
# Visualiser: the Treemap Algorithm
The next component of our program is the treemap algorithm itself. It takes a tree and a 2-D window to fill with the visualization, and returns a list of rectangles to render, based on the tree structure and data_size attribute for each node.

For all rectangles, we'll use the pygame representation of a rectangle, which is a tuple of four integers (x, y, width, height), where (x, y) represents the upper-left corner of the rectangle. In pygame, the origin is in the upper-left corner and the y-axis points down. So, the lower-right corner of a rectangle has coordinates (x + width, y + height). Each unit on both axes is a pixel on the screen.

There are many variations of the treemap algorithm. For this assignment, we'll use a dynamic version – one that lets us see different visualizations of the same tree. However, the process for generating the tree is always the same, even as the displayed-tree changes.

For simplicity we'll use "size" to refer to the data_size attribute in the algorithm below.

The update_rectangles method should work as specified below:

Input: A data tree that is an instance of some subclass of TMTree, and a pygame rectangle (the display area to fill).

Output: None. It will directly modify the `.rect` attributes of the tree objects inside the data tree. 

Algorithm:

Unless explicitly written as "displayed-tree", all occurrences of the word "tree" below refer to a data tree.

If the tree has size 0, then its rect should have an area of 0.
If the tree is a leaf in the displayed-tree, then it should take up the entire area of the given rectangle.
Otherwise, if the display area's width is greater than its height: divide the display area into vertical rectangles, one smaller rectangle per subtree of the displayed-tree, in proportion to the sizes of the subtrees.

Example: suppose the input rectangle is (0, 0, 200, 100), and the displayed-tree for the input tree has three subtrees, with sizes 10, 25, and 15.

- The first subtree has 20% of the total size (10 / (10 + 25 + 15)) so its smaller rectangle has width 40 (20% of 200): (0, 0, 40, 100).
- The second subtree should have width 100 (50% of 200) and starts immediately after the first one: (40, 0, 100, 100).
- The third subtree has width 60 and takes up the remaining space: (140, 0, 60, 100)

Note that the three rectangles' edges overlap!

You can assume that every non-zero-sized subtree will have a large enough size that its computed rectangle has a width and height >= 1.

If the height is greater than or equal to the width, then make horizontal rectangles instead of vertical ones. Divide the space proportionately, as in step 3.
Recurse on each of the subtrees in the displayed-tree, passing in the corresponding smaller rectangle from step 3 or 4.
Note about rounding: because you're calculating proportions here, the exact values will often be floats instead of integers. For all but the last rectangle, always truncate the value (i.e., round down to the nearest integer). In other words, if you calculate that a rectangle should be (0, 0, 10.9, 100), round the width down to (0, 0, 10, 100). Then a rectangle below it would start at (0, 100), and a rectangle beside it would start at (10, 0).

However, the final (rightmost or bottommost) edge of the last smaller rectangle should always be equal to the outer edge of the input rectangle. This means that the last subtree might be a bit bigger than its true proportion of the total size.

You will implement this algorithm in the update_rectangles method in TMTree.

# Visualiser: Displaying the Treemap
The code in treemap_visualiser.py runs the treemap algorithm, and then uses pygame to render a graphical display to the user. You will only edit this file to uncomment or make minor edits to the client code for the TMTree class.

The pygame window is divided into two parts:

The treemap itself (a collection of colourful rectangles).
A text display along the bottom of the window, showing information about the currently selected rectangle. If there is no selected rectangle, nothing is shown in the text display.
Every rectangle corresponds to one subtree (with data_size not zero) in the displayed-tree. If the whole tree has data_size zero (so no rectangles are returned by treemap), the entire screen should appear black.

# Visualiser: User Events
As before, unless explicitly written as "displayed-tree", all occurrences of the word "tree" below refer to a data tree.

In addition to displaying the treemap, the pygame graphical display responds to a few different events:

a. The user can close the window and quit the program by clicking the X icon (like any other window).

b. The user can left-click on a rectangle to select it. If a rectangle is already selected, left-clicking on it again unselects it. If a rectangle is selected, the text display updates to show two things:

    - the names on the path from the root of the data tree to the selected leaf

    - the selected leaf's data size

The following three events allow the user to actually mutate the data tree, and see the changes reflected in the display. The original data is not changed - we are just using the visualization as a tool for simulating changes on a dataset. Once the user performs one of these events, the tree is no longer in sync with the original data set.

Important: All changes described below should only change the tree object. If a rectangle is deleted or its size changes, DO NOT try to change the actual file on your computer!

c. If the user presses the Up Arrow or Down Arrow key when a rectangle is selected, the selected leaf's data_size increases or decreases by 1% of its current value, respectively. This affects the data_size of its ancestors as well, and the visualisation must update to show the new sizes.

    - A leaf's data_size cannot decrease below 1. There is no upper limit on the value of the data_size.

    - The 1% amount is always rounded up before applying the change. For example, if a leaf's data size is 150, then 1% is 1.5, which is rounded up to 2.

d. If the user selects a rectangle that is a leaf in the whole tree, then hovers the cursor over another rectangle that is an internal node in the whole tree and presses m, the selected leaf should be moved to be a subtree of the internal node being hovered over. If the selected node is not a leaf in the whole tree, or if the hovered node is not an internal node in the whole tree, nothing happens.

e. If the user selects a rectangle that is either a leaf or internal node and then presses Del, as long as it has a parent (i.e. it is not the root node), then that node should be deleted by removing it from the parent's subtrees (and hence the visualization).

The next six events change the displayed-tree but not the underlying data tree.

f. If the user selects a rectangle and then presses e, the tree corresponding to that rectangle is expanded in the displayed-tree. If the tree is a leaf, nothing happens.

g. If the user selects a rectangle and then presses c, the parent of that tree is unexpanded (or "collapsed") in the displayed-tree. (Since rectangles correspond to leaves in the displayed-tree, it is the parent that needs to be unexpanded.) If the parent is None because this is the root of the whole tree, nothing happens.

h. If the user selects a rectangle and then presses a, the tree corresponding to that rectangle, as well as all of its subtrees, are expanded in the displayed-tree. If the tree is a leaf, nothing happens.

i. If the user selects any rectangle and then presses x, the entire displayed-tree is collapsed down to just a single tree node. If the displayed-tree is already a single node, nothing happens.

j. If the user selects any rectangle, and then presses q, the selected rectangle will replace the current displayed-tree.

k. If the user presses b at any time, the selected rectangle's parent tree (if any) will replace the current displayed-tree.
