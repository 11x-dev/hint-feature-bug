************************
**Solution**
************************

You can view the exact code changes needed to solve this coding challenge here: https://github.com/ScriabinOp8No12/hint-button-bug-11xdev/pull/1/files#diff-34647d18e0b712c9aa16cf1d3b09734bdaf6d36962d384e10e4a5fc38112cd25

This branch contains the bug fix to the hint button - you can test that the functionality works as intended in your browser.   

1. Locate the file that contains the hint button, one simple way is to right click the hint button in the web browser, then click inspect.  This will automatically show you the html for that element in the browser. Now let's search for "bottom-graphic" in our IDE.  You'll notice the same className is in both Lesson.tsx and Puzzles.tsx.  Upon further inspection, you'll find that the hint button is located inside the src/views/Lessons/Puzzles.tsx file.  

![Hint button finding in codebase](https://res.cloudinary.com/dxq77puhi/image/upload/v1748893572/Hint_button_annotation_finding_it_in_the_codebase_5_31_2025_m0ho5g.png)

2. Now that we know which file to debug, let's look at the hint button in the JSX portion of our component. We see that the button's onClick is executing the "showHint" function. Once we find that function, we see a Typescript issue where the findBranchesWithWrongAnswer() method does not exist on "MoveTree".  It's not finding the method in the MoveTree.ts file at line 1081.  

3. How can we find this missing code?  Think about it for a little before proceeding.

![showHint method in JSX](https://res.cloudinary.com/dxq77puhi/image/upload/v1748894255/Codesandbox_showHint_1_6_2_2025_vil7yo.png)

![showHint function](https://res.cloudinary.com/dxq77puhi/image/upload/v1748894576/showHint_function_submodule_missing_method_6_2_2025_vfatsu.png)

![Submodule missing method search](https://res.cloudinary.com/dxq77puhi/image/upload/v1748895258/Finding_submodule_method_search_codesandbox_6_2_2025_vwgku0.png)

4. The method is missing from the MoveTree.ts file, which is within the inner "goban" submodule.  So let's go look at that code in the submodule.  First, navigate to the main submodule "online-go.com" in your browser: 

```
https://github.com/online-go/online-go.com
```

Click "Submodules" -> "Goban", and in the search field that says "Go to file", type in "MoveTree.ts", now search the page for "findBranchesWithWrongAnswer()" which is the method we were missing. You should see it just below the "findBranchesWithCorrectAnswer()", around line 1099.

Here's the MoveTree file within the 2nd submodule in case you had issues finding the file: 

```
https://github.com/online-go/goban/blob/19f57ea8d2f66b52f06015e67da2cc54df9b4a1c/src/engine/MoveTree.ts
```

![online-go submodule Github search](https://res.cloudinary.com/dxq77puhi/image/upload/v1748907606/online_go_submodule_bug_1_6_2_2025_jj5sv0.png)
![online-go/goban submodule](https://res.cloudinary.com/dxq77puhi/image/upload/v1748907781/goban_submodule_6_2_2025_fpemgc.png)
![online-go/goban movetree search](https://res.cloudinary.com/dxq77puhi/image/upload/v1748907891/goban_movetree.ts_search_ye2mhv.png)
![online-go/goban find branch with wrong answer found](https://res.cloudinary.com/dxq77puhi/image/upload/v1748907994/findbranches_with_wrong_answer_goban_submodule_6_2_2025_mfxkbv.png)

NOTE: Normally you can use a shortcut on a Github repo to search for methods directly.  Unfortunately, you'll have trouble finding methods that are in the inner submodules when searching from the parent submodule.

5. Add the missing method, and associated function to the MoveTree.ts file.  Here's the code you'll need to add.  

```
    private isBranchWithWrongAnswer(branch: MoveTree): boolean {
        if (branch.wrong_answer) {
            return true;
        }
        if (!branch.branches || branch.branches.length === 0) {
            return false;
        }

        return branch.branches.some((item) => this.isBranchWithWrongAnswer(item));
    }
```

```
    findBranchesWithWrongAnswer(): Array<MoveTree> {
        return this.branches.filter((branch) => this.isBranchWithWrongAnswer(branch));
    }
```

6. Alternatively, you could copy paste in the entire MoveTree.ts file.
7. Verify both bugs are fixed in this branch when testing in your browser!

************************
**Conclusion**
************************

I was very confused when I had this bug happen to me in this project. I usually program on my Windows machine, but sometimes use Mac for mobile testing. The issue was that my Mac had an out of date submodule, despite pulling the most recent code from Github. Unlike regular code, submodules don't automatically update when you git pull.

In this coding challenge, we've integrated the submodule code to avoid compatibility issues, but the missing method represents what happens when submodules get out of sync. In real projects, you can often fix this with:

```
git submodule update --recursive
```

Remember to keep your submodules up to date so things don't break unexpectedly.

Great work, and have fun in the next coding challenge! 
