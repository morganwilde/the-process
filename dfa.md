# The Process

1. ### How to draw a graph so that links are always under nodes?
   - Do it in two passes, first drawing the links and then the nodes.
2. ### How to position nodes?
   - Define a circle around a node and divide it by the number of possible outgoing links.
3. ### How to draw the link's arrowhead?
   - Define a polygon using vector rotation/addition.
4. ### Text alignment next to the link?
   - I'll try to draw a box of fixed size and rotate it so that one edge touches the link.
   - `01:13` I'm having issues with rotation, the red squares don't align with the lines.
   
   ![1.png](1.png)
   
   - `01:23` Fixed the rotation issue, turns out I was misusing `Math.atan()` and accidentally swapped `x` with `y`.
   
   ![2.png](2.png)
   
   - `01:31` I have Vector math related issues. My origin is wrong.
   - `02:01` Fixed the Vector math issues. Turns out my mid-point math was off. Transferred most calculations over to Vector.
   
   ![3.png](3.png)
   
   - `02:05` Came up with the idea to position the link labels in the center of the link itself. Need to add a white background so that there appears to be gap and the position the label in the middle.
   
   ![4.png](4.png)
   
   - `02:16` Happy with the result.
   
   ![5.png](5.png)
   
5. ### How to draw overlapping Links?
   - `11:31` Links overlap each other and there's no way to tell the directionality of the link.
   - `11:41` Still not happy with the label positioning, taking a new approach to try and perfectly center the label.
   
   ![6.png](6.png)
   
   - `12:13` I've changed the font to a monospaced one. Now I can calculate the width of the label without having to render. I now center the text myself.
   
   ![7.png](8.png)
   ![8.png](8.png)
   
   - `13:38` Solved the arrow positioning problem by refactor the code to use the new Vector object.
   
   ![9.png](9.png)
   ![10.png](10.png)
   
   - `13:42` The illustration of how the primitive overlap case looks like.
   
   ![11.png](11.png)
   
   - `14:09` First attempt. The end points don't look right. And since I adjust for the arrow, the length is off also.
   
   ![12.png](12.png)
   
   - `15:31` Will start playing around with arrows. Now logging commits with every change.
   
   ![13.png](13.png)
   
   - `18:37` Realised that it would be too detailed, so I abandoned the curvy approach.
   
   ![14.png](14.png)
   
   - `18:39` Decided to go with shifting the lines around. Still need to test the other positions before I go ahead.
   
   ![15.png](15.png)
   
   - `19:21` I think the overlap issue has been resolved. Not 100% percent (if you have one link on one side, and another that's reciprocal on the opposite, that one link will not be perfectly centered between the two reciprocal links), but sufficiently so.
   
   ![16.png](16.png)
   
6. ### How to draw temporary links?
   - `00:31` This should work for now. I've changed the color and the dot frequency.
   
   ![17.png](17.png)
   
   - `00:33` I now need to come up with a good way to display node placeholders. I think dots could work for the stroke around the circle, and the fill should be transparent with the label always being the same (either empty or some non-alphanumeric character).
   
   - `01:52` On the way I figured to better use a hack and adjust the label's positioning arbitrarily than to leave it in a seamingly incorrect position.
   
   ![18.png](18.png)
   
   - `02:01` The plus sign seems appropriate as a placeholder.
   
   ![19.png](19.png)
   
7. ### A method to draw temporary NodeViews and LinkViews
   - `02:16` To be able to show the temporary links on hover, I need to add them to the hieararchy, but I also need to remove them once the mouse is out. So in addition to the `draw()` method, I'm adding an `update()` method that will be responsible with delta updates to the link and state views.
   - `03:07` I now have a working solution for mouse over/out on an existing node (tested with only one central node). But the mouse out event hides all the nodes and prevents from going over to a specific node you'd like to add. Need to fix this, not sure how yet.
   
   ![20.png](20.png)
   
8. ### The mechanism for adding new nodes to the graph
   - `03:10` One issue that is immediatelly obvious is the reciprocal link not being shifted for the temporary link.
   
   ![21.png](21.png)
   
   - `22.01 12:58` Another way of solving the reciprocal link would be to fit everything on one line, as suggested in this [answer](http://graphicdesign.stackexchange.com/questions/65842/how-to-design-arrow-links-between-two-nodes/65848#65848). But this requires that the symbol be the same, because there is no room for two labels. I now have to rotate the links around the reciprocating node.
   
   - `22.01 13:34` While working on the previous issue, I found the alphabets having an uneven number of letters produce this misaligned picture. I need to always adjust the rotation of the receiving node.
   
   ![22.png](22.png)
   
   - `22.01 14:09` The solution was pretty obvious looking back, just rotate the link 180 degrees.
   
   ![23.png](23.png)
   
   - `22.01 14:33` Solved the hover issue by introducing a timer once you leave a node.
   
   ![24.png](24.png)
   
   - `22.01 14:45` Now I need to create the node itself and the link leading up to it.
   
   - `22.01 15:08` The functionality for adding new nodes is complete. But there are other issues related to rotating the origin for new links.
   
   ![25.png](25.png)
   
   - `22.01 15:39` Rotation now works in all tested cases.
   
   ![26.png](26.png)
   
   - `22.01 15:48` I need to set the reciprocal flag on back links when I create them.
   
   - `22.01 16:08` I've added an update method on `LinkView` to allow me to redraw it when properties change. Otherwise only new additions to the graph would be drawn.
   
   ![27.png](27.png)
   
9. ### The mechanism for moving existing nodes around
   - `22.01 16:10` I first need to change the on mouse down event to not trigger node creation. Change that to mouse up.
   - `22.01 17:17` You can now move nodes. `NodeView` now also has an `update()` method.
   
   ![28.png](28.png)
   
   - `22.01 17:20` Fixed an inconveniece where after moving a node you had to exit and enter the node again to see the interface for adding linked nodes.
   
   ![29.png](29.png)
   
10. ### Change `LinkView` design
   - `22.01 17:24` I will change the overall look for the link as per suggestions in this SE [answer](http://graphicdesign.stackexchange.com/questions/65842/how-to-design-arrow-links-between-two-nodes/65848#65848).