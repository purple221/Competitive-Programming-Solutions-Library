This problem is probably most easily solved through a well-optimized combination of recursive brute force and "reasoning" techniques. 
A typical procedure might look something like:

  1. Fill in naked/hidden singles until it is no longer possible to do so;
  2. Choose a square with as few possibilities as possible, and try one of them; recurse and go back to step 1.
  
It is also possible to solve this problem efficiently with Algorithm X with Dancing Links, a technique for solving the exact cover problem 
proposed by Donald Knuth. Sudoku has an exact cover representation, hence it can be solved efficiently. The exact cover matrix for Sudoku 
has 729 rows and 324 columns. Each row represents "the element at row r and column c has value x", for all possible combinations of r, c, 
and x. The columns are divided into four blocks. The first 81 columns represent "uniqueness constraints": each corresponds to some square 
in the Sudoku and any row representing that square has a 1 that column (ensuring that there will be at most one digit per square). 
The next 81 columns represent "row constraints": each combination of a row of the Sudoku and a digit (so 81 combinations in all) gets a 
column and any row of the matrix corresponding to a given combination has a 1 in the corresponding column. Likewise the next 81 columns 
are "column constraints" ensuring that each digit appears exactly once in each column and the last 81 are "cell constraints" ensuring that
each digit appears exactly once in each cell.
