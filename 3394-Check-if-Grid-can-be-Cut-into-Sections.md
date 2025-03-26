# Check if Grid can be Cut into Sections (Problem #3394)

## Problem Definition

**[Link to Problem on LeetCode](https://leetcode.com/problems/check-if-grid-can-be-cut-into-sections/)** 

### Description
You are given an integer `n` representing the dimensions of an `n x n` grid, with the origin at the bottom-left corner. You are also given a 2D array `rectangles`, where `rectangles[i] = [startx, starty, endx, endy]` represents a rectangle on the grid:
- `(startx, starty)`: Bottom-left corner of the rectangle.
- `(endx, endy)`: Top-right corner of the rectangle.

The rectangles do not overlap. Determine if it’s possible to make **two horizontal** or **two vertical cuts** on the grid such that:
1. Each of the three resulting sections contains at least one rectangle.
2. Every rectangle belongs to exactly one section.

Return `true` if such cuts can be made; otherwise, return `false`.

### Example
Input: n = 5, rectangles = [[1,0,5,2], [0,2,2,4], [3,2,5,3], [0,4,4,5]]
Output: true
Explanation:

Make two horizontal cuts at y = 2 and y = 4:
Section 1 (y < 2): Contains [1,0,5,2].
Section 2 (2 ≤ y < 4): Contains [0,2,2,4] and [3,2,5,3].
Section 3 (y ≥ 4): Contains [0,4,4,5].
Each section has at least one rectangle, and all rectangles are assigned uniquely.


### Constraints
- `1 <= n <= 10^5`
- `1 <= rectangles.length <= 10^5`
- `0 <= startx < endx <= n`
- `0 <= starty < endy <= n`
- Rectangles do not overlap.

## Approach

### Intuition
To divide the grid into three sections with two cuts (either horizontal or vertical), we need at least three "gaps" or separations between rectangles along one dimension (x or y). A gap exists when one rectangle’s end doesn’t overlap with the next rectangle’s start. If we can find two such gaps, we can place cuts there to ensure each section has at least one rectangle.

### Algorithm
1. **Check Both Dimensions**: Test for both horizontal (y-axis) and vertical (x-axis) cuts. Return `true` if either is possible.
2. **Sort Rectangles**: For a given dimension (x or y), sort rectangles by their start coordinate (`startx` or `starty`).
3. **Track Gaps**:
   - Initialize `furthestEnd` as the end coordinate of the first rectangle.
   - Iterate through sorted rectangles:
     - If the current rectangle’s start is beyond `furthestEnd`, a gap exists; increment `gapCount`.
     - Update `furthestEnd` to the maximum end coordinate seen so far.
4. **Validate**: Return `true` if `gapCount >= 2` (two gaps allow three sections).

### Time Complexity
- **O(n log n)**: Sorting the rectangles takes `O(n log n)` where `n` is the number of rectangles. The subsequent scan is `O(n)`.
- Total: `O(n log n)` for each dimension, so overall `O(n log n)`.

### Space Complexity
- **O(1)**: Only a few variables are used beyond the input array, assuming in-place sorting.

## Solution

### Code
```cpp
class Solution {
public:
    bool checkValidCuts(int n, vector<vector<int>>& rectangles) {
        return checkCuts(rectangles, 0) || checkCuts(rectangles, 1);
    }

private:
    bool checkCuts(vector<vector<int>>& rectangles, int dim) {
        int gapCount = 0;

        // Sort by startx (dim=0) or starty (dim=1)
        sort(rectangles.begin(), rectangles.end(),
             [dim](const vector<int>& a, const vector<int>& b) {
                 return a[dim] < b[dim];
             });

        int furthestEnd = rectangles[0][dim + 2]; // endx or endy

        for (size_t i = 1; i < rectangles.size(); i++) {
            vector<int>& rect = rectangles[i];
            if (furthestEnd <= rect[dim]) { // Gap found
                gapCount++;
            }
            furthestEnd = max(furthestEnd, rect[dim + 2]);
        }
        return gapCount >= 2; // Need 2 gaps for 3 sections
    }
};
