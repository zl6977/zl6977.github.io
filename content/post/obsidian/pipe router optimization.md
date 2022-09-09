---
date: 2022-04-25 15:46:00
tags:  [2022-04]
title: pipe router optimization
aliases: 
---


## Initialize_points() is time-consuming
3 ways to try:
1. tags and points are different. initialize tags, not points.
    - hard to know when obstacle list is changed. -> ==The caller should know.==
2. do not use points, use `if pt in obstacle_list` to judge. which is faster?
    - `if pt in obstacle_list` is slower, so it is not helpful.
    - sometimes the ==goal point is also an obstacle point==, so this is not robust.
    - neighbors[] should include `goalPt + goalVec` not `goalPt`
3. How about only generate the points on the boundary of the equipment?
    - very useful

## Fix bugs:
### IndexError
IndexError [(49, 8, 32), (51, 8, 32), (50, 7, 32), (50, 9, 32), (50, 8, 31), (50, 8, 33)]->(50,8,32)
IndexError [(49, 8, 34), (51, 8, 34), (50, 7, 34), (50, 9, 34), (50, 8, 33), (50, 8, 35)]->(50,8,34)
IndexError [(49, 35, 49), (51, 35, 49), (50, 34, 49), (50, 36, 49), (50, 35, 48), (50, 35, 50)]->(50,35,49)

- It may be because end_list is updated, but vec_list is not.
- Is_out_of_boundary() not working? why `[(50, -1, 2), (49, 0, 2), (49, -1, 3)]` are still in `neighbor[]`?
    - for i in aList: aList.remove(i) 这会导致aList中的元素没有被完全遍历，因为删除操作改变了元素的index。示例如下：
```python
neighborsToCheck = [(48, -1, 12), (50, -1, 12), (49, -2, 12), (49, 0, 12), (49, -1, 11), (49, -1, 13)]
for pt in neighborsToCheck:
    print(pt)
    print(Is_out_of_boundary(x_size, y_size, z_size, pt))
    if Is_out_of_boundary(x_size, y_size, z_size, pt):
        neighborsToCheck.remove(pt)
```

```json
{
    "dimension": [[50, 50, 50], "dimension"],
    "start_point": [[0, 1, 0], "start_point"],
    "goal_point": [[49, 49, 49], "goal_point"],
    "startVec": [[1, 0, 0], "startVec"],
    "goalVec": [[-1, 0, 0], "goalVec"],
    "eqNum": [11, "eqNum"],
    "productID": ["p001", "productID"],
    "eq_origin_1": [[5, 37, 35], "eq_origin_1"],
    "eq_size_1": [[4, 4, 3], "eq_size_1"],
    "inlet_1": [[0, 0, 0], "inlet_1"],
    "outlet_1": [[4, 4, 3], "outlet_1"],
    "inletVec_1": [[-1, 0, 0], "inletVec_1"],
    "outletVec_1": [[1, 0, 0], "outletVec_1"],
    "eq_origin_2": [[36, 38, 9], "eq_origin_2"],
    "eq_size_2": [[4, 3, 4], "eq_size_2"],
    "inlet_2": [[0, 0, 0], "inlet_2"],
    "outlet_2": [[4, 3, 4], "outlet_2"],
    "inletVec_2": [[-1, 0, 0], "inletVec_2"],
    "outletVec_2": [[1, 0, 0], "outletVec_2"],
    "eq_origin_3": [[31, 33, 34], "eq_origin_3"],
    "eq_size_3": [[3, 2, 3], "eq_size_3"],
    "inlet_3": [[0, 0, 0], "inlet_3"],
    "outlet_3": [[3, 2, 3], "outlet_3"],
    "inletVec_3": [[-1, 0, 0], "inletVec_3"],
    "outletVec_3": [[1, 0, 0], "outletVec_3"],
    "eq_origin_4": [[15, 3, 26], "eq_origin_4"],
    "eq_size_4": [[3, 2, 4], "eq_size_4"],
    "inlet_4": [[0, 0, 0], "inlet_4"],
    "outlet_4": [[3, 2, 4], "outlet_4"],
    "inletVec_4": [[-1, 0, 0], "inletVec_4"],
    "outletVec_4": [[1, 0, 0], "outletVec_4"],
    "eq_origin_5": [[2, 11, 2], "eq_origin_5"],
    "eq_size_5": [[2, 3, 4], "eq_size_5"],
    "inlet_5": [[0, 0, 0], "inlet_5"],
    "outlet_5": [[2, 3, 4], "outlet_5"],
    "inletVec_5": [[-1, 0, 0], "inletVec_5"],
    "outletVec_5": [[1, 0, 0], "outletVec_5"],
    "eq_origin_6": [[38, 33, 35], "eq_origin_6"],
    "eq_size_6": [[2, 3, 4], "eq_size_6"],
    "inlet_6": [[0, 0, 0], "inlet_6"],
    "outlet_6": [[2, 3, 4], "outlet_6"],
    "inletVec_6": [[-1, 0, 0], "inletVec_6"],
    "outletVec_6": [[1, 0, 0], "outletVec_6"],
    "eq_origin_7": [[5, 2, 14], "eq_origin_7"],
    "eq_size_7": [[4, 2, 2], "eq_size_7"],
    "inlet_7": [[0, 0, 0], "inlet_7"],
    "outlet_7": [[4, 2, 2], "outlet_7"],
    "inletVec_7": [[-1, 0, 0], "inletVec_7"],
    "outletVec_7": [[1, 0, 0], "outletVec_7"],
    "eq_origin_8": [[6, 30, 13], "eq_origin_8"],
    "eq_size_8": [[4, 3, 3], "eq_size_8"],
    "inlet_8": [[0, 0, 0], "inlet_8"],
    "outlet_8": [[4, 3, 3], "outlet_8"],
    "inletVec_8": [[-1, 0, 0], "inletVec_8"],
    "outletVec_8": [[1, 0, 0], "outletVec_8"],
    "eq_origin_9": [[15, 20, 21], "eq_origin_9"],
    "eq_size_9": [[2, 2, 4], "eq_size_9"],
    "inlet_9": [[0, 0, 0], "inlet_9"],
    "outlet_9": [[2, 2, 4], "outlet_9"],
    "inletVec_9": [[-1, 0, 0], "inletVec_9"],
    "outletVec_9": [[1, 0, 0], "outletVec_9"],
    "eq_origin_10": [[22, 10, 1], "eq_origin_10"],
    "eq_size_10": [[3, 4, 4], "eq_size_10"],
    "inlet_10": [[0, 0, 0], "inlet_10"],
    "outlet_10": [[3, 4, 4], "outlet_10"],
    "inletVec_10": [[-1, 0, 0], "inletVec_10"],
    "outletVec_10": [[1, 0, 0], "outletVec_10"],
    "eq_origin_11": [[42, 5, 30], "eq_origin_11"],
    "eq_size_11": [[4, 3, 2], "eq_size_11"],
    "inlet_11": [[0, 0, 0], "inlet_11"],
    "outlet_11": [[4, 3, 2], "outlet_11"],
    "inletVec_11": [[-1, 0, 0], "inletVec_11"],
    "outletVec_11": [[1, 0, 0], "outletVec_11"],
    "bestPathShort": [[[0, 1, 0], [-4, 1, 0], [5, 37, 35], [5, 37, 35]], [[9, 41, 38], [13, 41, 38], [13, 41, 9], [13, 38, 9], [36, 38, 9]], [[40, 41, 13], [41, 41, 13], [41, 41, 34], [30, 41, 34], [30, 33, 34], [31, 33, 34]], [[34, 35, 37], [35, 35, 37], [35, 32, 37], [13, 32, 37], [13, 3, 37], [13, 3, 26], [15, 3, 26]], [[18, 5, 30], [20, 5, 30], [20, 5, 2], [2, 5, 2], [2, 11, 2]], [[4, 14, 6], [5, 14, 6], [5, 14, 35], [37, 14, 35], [37, 33, 35], [38, 33, 35]], [[40, 36, 39], [41, 36, 39], [41, 32, 39], [4, 32, 39], [4, 2, 39], [4, 2, 14], [5, 2, 14]], [[9, 4, 16], [10, 4, 16], [10, 17, 16], [7, 17, 16], [7, 17, 13], [6, 17, 13], [6, 30, 13]], [[10, 33, 16], [14, 33, 16], [14, 20, 16], [14, 20, 21], [15, 20, 21]], [[17, 22, 25], [18, 22, 25], [18, 22, 1], [18, 10, 1], [22, 10, 1]], [[25, 14, 5], [26, 14, 5], [26, 14, 30], [41, 14, 30], [41, 5, 30], [42, 5, 30]], [[46, 8, 32], [47, 8, 32], [47, 8, 49], [47, 49, 49], [49, 49, 49]]]
}
```

### How about not consider vec_list in space?
Redesign space class, let space only include end_pt, pass (end_pts + vec_list) into space.
