Maze
====

I started on this little project as I was reviewing some work I had done on [another reinforcement learning project](https://github.com/rolyatmax/tictactoe). This project applies the same reinforcement learning (RL) concepts to maze learning as the solver attempts to find the shortest path from the top left corner to the bottom right corner of the maze.

While other algorithms will be much faster at solving static mazes (a brute-force method would be faster, for example), this solving algorithm is notable for how it stores its state and its ability to adapt to a changing environment.

The solver's "knowledge" is represented by values associated with location-decision pairs. For each location in the maze, there are one or more moves the solver can make to progress through the maze. After each move, the solver receives a reward (or in this case a punishment - more on that later). Then, taking the reward into account along with the best move from the new location, the solver evaluates its last decision by updating its location-decision value.

For example, let's assume we have a solver which sits at **Location 1** and has two possible moves from that location: moves *a* and *b*. Let's say that after some learning, the solver has updated the relative values for these moves such that *a*'s value is -1 and *b*'s is 2. (Note: these values only mean that *b* is a better choice than *a*. One might also consider *how much better* a particular option is over another.) This policy recommends *b* as the best option for **Location 1**.

So the solver selects *b*, which let's say takes the solver to **Location 2**. From this location, let's say the solver has options *c* and *d* with respective values -5 and -10. Let's say the solver also, having made this move, receives a reward (or in this case a penalty) of -1. The solver can now evaluate its last decision with a function that takes into account the penalty (-1), the value of the last decision (2), and the best choice from the new location (-5). The result is that option *b* from **Location 1** will probably be downgraded somewhat from a value of 2 to something smaller.

The reason why a -1 is handed out as a penalty is to encourage the solver to find the fastest solution. Every step is penalized causing moves which are further away from the maze's exit to *eventually* receive lower values. (This only happens when the solver runs for some time, exploring all parts of the maze sufficiently.) Because there is a certain amount of "exploration" built into this solver, it will explore new areas of the maze. This "exploration" factor is represented by a float between 0.0 and 1.0. A factor of 0.5 causes the solver to choose randomly from its options (instead of following the recommendation of its own policy) 50% of the time. A factor of 1.0 causes the solver to always choose randomly. The exploration factor decays over time to allow the solver to converge on a single solution, which in our case occurs once it has arrived at the same conclusion several times in a row. If the maze were to change over time, however, one could have the exploration factor stay static to encourage the solver to continually explore the maze and adapt to changes.

One interesting aspect of this type of learning is that the values for each location-decision could be stored within each location. The evaluation function just needs to know the resulting reward and what the best options are from the next location. This evaluation function could simply be passed to the solver as a callback to be executed once the solver reaches the next location and is able to access that information. Each location then essentially works as a guide to the solver, recommending directions and having the solver report back on the next step.

This type of "distributed" knowledge could work in routing systems where latency between nodes is variable and requires a routing policy that can constantly adapt to changes. Furthermore, the distributed nature of the policy's "knowledge" might make it a good fit for emergent networks which have no governing controller.

Finally, I've tried to visualize the policy with the "Toggle policy values" button. This overlays the maze with transparent red squares. The transparency represents the value of each location's best move. The more opaque the red overlay, the higher the value. You can see locations near the end of the maze have the highest values. Note that because the solver ceases learning after it "converges" on a policy, the maze is not always thoroughly explored. This can result in gaps in the overlay and sometimes ranks locations higher than they would be had the solver iterated over those paths enough times to downgrade their values.

------------------------

To run this project yourself, just clone the repo and run from the root:

    npm install
    npm run build
    open index.html

------------------------

You can see this project live at [tbaldw.in/maze](https://tbaldw.in/maze) or check out the code at [github.com/rolyatmax/maze](https://github.com/rolyatmax/maze).
