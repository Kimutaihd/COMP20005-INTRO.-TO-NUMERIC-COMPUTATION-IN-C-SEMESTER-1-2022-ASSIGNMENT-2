# COMP20005-INTRO.-TO-NUMERIC-COMPUTATION-IN-C-SEMESTER-1-2022-ASSIGNMENT-2
This is an assignment: COMP20005 INTRO. TO NUMERIC COMPUTATION IN C SEMESTER 1, 2022 ASSIGNMENT 2


The solution for this assignment is readly available here: https://bit.ly/COMP20005 or email: jaycodeshd@gmail.com


Learning Outcomes
In this project you will demonstrate your understanding of structures and arrays of structures, and will
develop a computational solution for a non-trivial problem. You are expected to make extensive use of
functions; and to demonstrate that you have adopted a clear and elegant programming style. You will find
it difficult to create a working solution unless you plan your program carefully in advance, and develop
it incrementally.
Robots!
The world is increasingly automated. Robot vehicles undertake deliveries around factories, airports, and
shopping centers; robot vacuum cleaners clean our floors; and robotic agents greet guests arriving in
hotels and convention centers.
To be useful, any self-propelled robot must always know its location, and be able to sense the loca-
tions of obstacles in its environment. Once it has that information it must also be able to undertake path
planning, to navigate the environment and arrive at its next required destination. In this programming
project we will assume that the initial location of a robot, and of a set of obstacles, is given; and that your
program must plan a path for the robot to a destination point.



##############################################33
To keep things simple we will work within a synthetic “grid
world” completely defined by integer coordinates, with robots
15
that can move left, right, up, down, or diagonally one cell at a
time. The location of the robot at any given moment is specified
14
R
by its (x, y) location as two integer coordinates. For example,
in Figure 1 the robot is at location (8, 14). Obstacles are defined
13
as rectangular regions specified as four integer values  


In Figure 1 the shaded region represents an obstacle described
as (7, 7, 13, 14). The robot can move to any of five neighboring cells, but cannot enter the space blocked
by the obstacle (the obstacle might be an actual wall or table, or it might just be a region reserved for
humans, such as a queue or walkway), and nor can it “clip” the corner of the obstacle to get to location
(7, 15). Two moves are required to get to (7, 15), described by (8, 14) → (8, 15) → (7, 15). Obstacles
can touch each other and can overlap – that’s how more complex shapes can be assembled.
Stage 1 – Control of Reading and Printing (marks up to 8/20)
Your first program should read an input file from stdin describing the robot’s world. The input values
will all be integers, two on the first line, and then four on every line after that. The two integers de-
scribe the rectangular “size” of the world, and then the second block of lines describe the obstacles. For
example, the input file test0.txt
7
4
5
3
3
4 0 1
5 1 2
3 2 2


describes a world that is 7 × 3 cells wide×high with three obstacles in it – this is the world that is partly
shown in Figure 2. You may assume that robot worlds will always be at most 100 cells wide and at most
100 cells high, with cells described by coordinate pairs [0 . . . 99] × [0 . . . 99]. (Yes, we are programmers,
we will count from zero.) The first input line with the world dimensions will always appear, and then
there will be between zero and 100 lines each describing an obstacle. In this stage your program should
read all of the available data, and then print it out again. For the file test0.txt the required output is:
S1,
S1,
S1,
S1,
S1,
world is 7 cells wide x 3 cells
world contains 3 obstacles
obstacle 0 covers [ 4, 4] x [ 0,
obstacle 1 covers [ 5, 5] x [ 1,
obstacle 2 covers [ 3, 3] x [ 2,
high
1]
2]
2]
Other examples of the required output are linked from the Assignment 2 page on the LMS. To obtain full
marks you need to exactly reproduce the required output lines. You may assume that the input provided
to your program will always be sensible and correct, and you do not need to perform any data validation.
You can base your program on your Assignment 1 solution, if you wish. But note, for this assignment
you are required to define and use structs to manage the input and computed data.  





Stage 2 – Finding The Zones (marks up to 16/20)
The robot has a home base location at which it
plugs itself in and recharges its battery. For sim-
2
b
a
plicity, let’s take that to be the location (0, 0), as
shown in Figure 2. You may assume that no ob-
1
a
stacle will ever be placed at (0, 0). Relative to the
home base, every cell in the grid then falls into one
0
R
a
a
of three categories: those that contain obstacles,
shown in grey in Figure 2; those that are reach-
0
1
2
3
4
5
6
able from the home base, because the robot can
Figure 2: Reachability zones for test0.txt.
get to them from (0, 0) by moving through other
reachable cells, shown as blanks in Figure 2; and
those that are unreachable because they are (for example) surrounded by obstacles, marked by “a” and
“b” in Figure 2. There might be several zones of unreachable cells, none of which can reach each other
because of the obstacles. Or, it might be that every non-obstacle cell is reachable from the home base.
In this stage your program must compute, for every cell in the robot world, whether it is reachable or
not; and, if not, which non-reachability zone it falls into. For the file test0.txt (Figure 2):
S2,
S2,
S2,
S2,
11
4
1
5
of
of
of
of
21
21
21
21
cells
cells
cells
cells
are
are
are
are
reachable
in unreachable zone a
in unreachable zone b
obstacles
The zones (for example, zone a in Figure 2) are groups of cells within which a robot could get from
any cell to any other cell if it was already in that zone, but to which the robot cannot get to at all when
starting from the home base. The labeling of the zones (“a”, “b”, and so on) is based on the first cell for
that zone that you encounter when moving left to right across each row, processing rows from the bottom
of the grid to the top (that is, (5, 1) is encountered before (4, 2)). You may assume that there will never
be more than 26 different reachability zones, and that labeling them with lower-case letters will always
be possible. See the LMS page for further examples of input and required output.
Hint: start with (0, 0) as a seed cell that is marked as “reachable”. Then iterate over the whole grid,
all rows and columns, and for each cell that is currently marked as reachable, mark all of its neighbors
the same way, keeping track of any changes made. Once you get to the end of the whole grid, if you did
make changes, go back to the beginning of the grid and go right through again. Eventually, when you

go all the way through all the cells without making any more changes, all the reachable cells will have
been located. Then look for any cells have still not been labeled, and if you find any, use the “lowest,
leftest” such cell as a new seed from which to label zone a using exactly the same process. Keep going
until every non-obstacle cell is either in an obstacle, or is labeled as reachable, or is labeled with a zone.
There will be lots of loops, so make sure you use functions carefully so that the loop nesting depth stays
under control.
Stage 3 – Deciding Routes (marks up to 20/20)
Every cell that is reachable from the home base has a corresponding distance, defined by assuming that
rectilinear moves√(left, right, up, down) have a cost of 2, and that diagonal moves have a cost of 3
(approximately 2 2). Each cell’s cost is given by the best way of getting to that cell from the home
base. For example, in Figure 3 cell (2, 2) has a cost of 6 because the cheapest way of getting there
is to use two diagonal moves. And a reminder: diagonal moves are not possible across the corners
of obstacles, which is why Figure 3 shows cell (5, 1) with a cost of 14 – the path to it must include
(3, 2) → (4, 2) → (5, 2) → (5, 1)  

To compute path costs you need to carry out
a similar nested-loop computation as in Stage 2,
first labeling the home base with a “cost” of 0 and 2
4
5
6
8
10
12
14
every other cell with a very high cost, and then
sweeping through every cell checking each its 1
2
3
5
7
14
15
neighbors to see if their current “best cost so far”
can be reduced. After each sweep right through 0
R 2
4
6
16
17
all of the grid, if any changes were made (that is,
cells that had their cost decreased), go back to the
0
1
2
3
4
5
6
beginning and do it all over again. Keep on doing
Figure 3: Path cost calculation.
that until no more changes of cost take place, at
which point you will have reached a stable configuration.
The output of this stage is a map of the robot world, with each cell in the even numbered rows
plotted as a single character, and the odd-numbered rows not plotted at all. (Typical computer fonts
have each letter approximately twice as tall as it is wide, and we want a square robot world to plot as
an approximately square map on the screen.) Each cell in the even numbered rows in the robot world is
plotted as a character according to its cell type:
–
–
–
–
with ’R’ if it is the home base;
with ’|’ if it is an obstacle (the “vertical pipe” character);
with ’a’, ’b’ and so on if it is unreachable, with the letter noting its zone number (see Figure 2);
with a single digit derived from its path cost if it is reachable from the home base.
The rule for the reachable cells involves the last two digits of its decimal value. If the last digit is between
0 and 3 inclusive, then the second to last digit is printed; and if the last digit is between 4 and 9 inclusive,
then a ’.’ is printed. For example, 31 → ’3’, and 54 → ’.’, and 89 → ’.’ and 172 → ’7’ and
240 → ’4’. This rule will lead to output with bands of approximately equal cost becoming visible. Here
are all of the map lines for test0.txt, you can compare this with Figure 2.
S3,
S3,
S3,
| ...|b|a
| 00..||a
0 + R0..|aa
<-- your program does not output this line!
Your final program should actually only print the first and last of these three lines; and the middle line
is included here purely to help you understand the structure of the map. The y-axis numbers should
be printed for cell indexes that are multiples of ten. The LMS Assignment 2 page gives several more

examples that show in more detail what you are to generate, and once you look at them will appreciate
why we are thinking of these as “maps”.
