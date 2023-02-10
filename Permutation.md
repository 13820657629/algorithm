# 回溯法

回溯法是优先搜索的一种情况，常用于需要记录节点状态的**深度优先搜索**。

**排列、组合和选择**问题使用回溯方较为方便。

核心：在搜索到某一节点的时候，如果我们发现目前的节点（及其子节点）并不是需求目标时，我们回退到原来的节点继续搜索，并且把在目前节点修改的状态还原。

在具体的写法上，它与普通的深度优先搜索一样，都有 [修改当前节点状态]→[递归子节

点] 的步骤，只是多了回溯的步骤，变成了 [修改当前节点状态]→[递归子节点]→[回改当前节点

状态]



Leetcode 46 **Permutation**

**题目描述**

给定一个无重复数字的整数数组，求其所有的排列方式。

**输入输出样例**

输入是一个一维整数数组，输出是一个二维数组，表示输入数组的所有排列方式。

Input: [1,2,3]

Output: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]

可以以任意顺序输出，只要包含了所有排列方式即可。

题解：

```c++
class Solution {
public:

    void backtracking(vector<vector<int>>& ans, vector<int>& perm, vector<int>& vis, int level, vector<int>& nums){
        if(level == nums.size()){ //说明已经填完了n个位置，找到了一个可行的解，将perm放入到最ans最终结果。level记录了需要填写的位置在哪里
            ans.push_back(perm);
            return;
        }

        for(int i = 0; i < nums.size(); i++){
            if(vis[i]){ //若vis[i]为1，则代码vis[i]这个数据已经在前面填入到空格中，那么就需要跳过当前的循环，让i+1,继续判断。
                continue;
            }
			
            perm.push_back(nums[i]);//将此时的元素vis[i]放入到中间结果perm中
            vis[i] = 1;//同时将vis[i]的标记置为1
            backtracking(ans, perm, vis, level + 1, nums);
            vis[i] = 0;//此时回溯，让vis[i]的标记置为0。 注：执行到此行代码，代表着上一个backtracking已经迭代完毕，意味着得到了一个结果，因此回溯
            perm.pop_back();//同时删除元素的
        }

    }

    vector<vector<int>> permute(vector<int>& nums) {
        //可以将排列问题看成这样的一个问题：有n个排列成一行的空格，需要从左往右依次填入题目给定的n个数，每个数只能使用一次。
        vector<vector<int>> ans; // ans是最终返回的结果。
        vector<int> perm; // perm是满足要求的一个序列的结果，是中间结果。
        vector<int> vis(nums.size(), 0); //vis记录了每个元素是否使用过，若前面使用过则记1，否则记录0。
        backtracking(ans, perm, vis, 0, nums);
        return ans;
    }
};
```



Leetcode 47 **Permutations II** 

给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。

示例 1：

输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
示例 2：

输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]



解答：

```c++
class Solution {
public:
    //本题需要满足全排列不重复的要求，因此需要去重，代码和leetcode 46上题一模一样，唯二的不同就在于for循环里加了个判断条件，同时需要在一开始对数组进行排序
    void backtracking(vector<vector<int>>& ans, vector<int>& nums, vector<int>& vis, int level, vector<int>& perm){
        if(level == nums.size()){
            ans.push_back(perm);
            return;
        }

        for(int i = 0; i < nums.size(); i++){
            if(vis[i] || (i > 0 && nums[i] == nums[i - 1] && !vis[i - 1])){
                /*这里解释下i>0 && nums[i] == nums[i - 1] && !vis[i -1]这个条件
                1.i > 0是为了保证后面的nums[i - 1]和vis[i - 1]访问的时候不越界，也就是nums[0]没有前面的元素，因此不会出现和前面元素重复的情况，所以不需要后面的nums[i] == nums[i - 1] && !vis[i - 1]的判断。
                2. 由于nums数组在一开始已经排序了，因此元素相同的会出现在邻近位置，若nums[i] == nums[i - 1]，则表明此时的nums[i]和前面的元素已经相同，需要结合后面的!vis[i - 1]来判断是否需要跳过这个元素。
                3. !vis[i - 1],当执行到这个判断的时候，前面的nums[i] == nums[i - 1]一定成立。若vis[i - 1]是1，则代表nums[i - 1]这个元素已经被选在了前面，虽然元素相同，但是i这个位置完全可以放入nums[i],因此不需要执行continue;相反，若vis[i - 1]是0，则代表此时的nums[i - 1]没有被标记，i这个空的待选位置既可以选择nums[i - 1]，也可以选择nums[i]，但是实际情况不能选nums[i]，因为在前面的执行过程中，i这个位置已经被选过nums[i - 1]这个元素，vis[i - 1]置为1，然后输出一个结果，回溯，然后vis[i - 1] = 0。因此，这个位置不能再被选择为nums[i]，否则会出现重复，所以才跳过。
                */
                continue;
            }

            perm.push_back(nums[i]);
            vis[i] = 1;
            backtracking(ans, nums, vis, level + 1, perm);
            vis[i] = 0;
            perm.pop_back();
        }


    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> perm;
        vector<int> vis(nums.size(), 0);
        sort(nums.begin(), nums.end()); //需要一开始对数组进行排序，保证元素相同的数据存放在相邻的位置，方便后续的判断。
        backtracking(ans, nums, vis, 0, perm);
        return ans;
    }
};
```

