
# Building a solver for Hitori

Task to create a [Hitori](https://en.wikipedia.org/wiki/Hitori) solver using a brute force approach, as well as an intelligent approach that uses forward checking and minimum remaining values.

This assignment was originally for an 2013 RIT Artificial Intelligence course.
<br>
<br>
<div align="center">
    &nbsp;
    <img width="320" alt="hitori" src="https://github.com/pxv8780/hitori-solver/assets/22942635/5a5196de-8b5e-46da-aa58-68e8523fee53">    
    &nbsp;&nbsp;
    <img width="320" alt="hitori_solved" src="https://github.com/pxv8780/hitori-solver/assets/22942635/ca26035c-d096-4107-be31-1aa7e670fb0f">
    &nbsp;
    <p><sup>A sample hitori puzzle shown in its original (left) and solved states (right)</sup></p>
    <br>
</div>

### Versioning

Current version has been successfully tested and run with Python 3.11

| Version | Date | Notes |
| ------- | ---- | ----- |
| v1 | 2013-01-05 | Original assignment attempt (it wasn't very good) |
| v2 | 2015-03-06 | First revisit, refactored the brute force solver, still lacked a smart solver |
| v3 | 2021-04-11 to 2021-04-16| Full implementation as defined in the assignment, but too slow for my liking|
| current | 2023-12-20 to 2024-01-01 | Full refactor with optimizations, and fixed a major mistake |

See archive folder for older versions.

### How to use

Downloaded the files and run with `python hitori.py`

Within `hitori.py` within `if __name__ == '__main__':` statement you can set `method = True` to use the brute solver, default is the smart solver (`method = False`).

You can use your own puzzle by setting the `puzzle` variable to a list of lists. Note that the brute force method is significantly slower than the smart method. For example, puzzle 6 as listed in the source code is solved by the brute force method in 2m 10s, while the smart method takes <2s to solve it.

### Details

Hitori is a Japanese puzzle game, which consists of a n-by-n grid of numbers with numbers ranging from 1-n.

The game has three conditions that must be met in order to win:
1. There must be no duplicate numbers in the rows or columns
2. Black values (represented by 0 value) cannot be adjacent (vertically and horizontally) to each other
3. White values (non-black values) must not be isolated (all connected)

This solver implements 2 methods an brute force solver and a smart solver. The brute force solver traverses the entire puzzle and tries each combination to check for a solution. The only optimization to the brute force solver is that it skips cells that are known to be white due to Rule 1. The smart solver uses techniques such as forward checking and minimum remaining values to solve puzzles significantly faster. It labels the entire puzzle board with values, such as `BW`, `B`, `W`, or ` ` (where B is black, and W is white), to keep track of valid values for each cell. Then via a process of elimination, as defined by Hitori's rules, the total number of combinations are reduced. A cell `BW` can still be black or white, while a cell `B` or `W` can *only* be black or *only* be white. As a result, cells that have been labelled ` ` are empty and can never be valid. Therefore when the solver encounters a ` ` that search traversal is ended, because it cannot contain a solution. The solver then proceeds on with the next (still) valid puzzle state. The solver repeats these steps until a solution if found, or all puzzle states have been exhausted and no solution has been found.

In the current version of the solver, I discovered and solved a major mistake I made in earlier versions. While my smart solver was significantly fast for smaller puzzles, such as 5 by 5, the 8 by 8 puzzles were not being solved within a fair about of time. At first I thought was missing optimizations, then I thought the search space had exponentially grown. But I realized something was off when I found other online solvers solving 8 by 8 puzzles in less than a second. I quickly realized the mistake was with my DFS traversal, but I didn't know what exactly. After much deliberation, I found the error. In my original code, the puzzle is setup as a cyclical graph/tree to traverse for solutions, however, Hitori requires no more than linear traversal. This oversight was a major error and hugely increased my time complexity for larger puzzles. The reason this happened is because Rule 3 indeed requires a cyclical graph in order determine the connectedness of white cells. I had incorrectly applied this algorithm to the main solver. But this is wrong, because for the main solver, we only need to select the next unlabelled cell (i.e. neither black or white), regardless of connectedness. A linear traversal is good enough for this. The solver can now solve 8 by 8 puzzles in <0.1 second, and 20 by 20 puzzles in <1 minute, rather than the end of the universe.
