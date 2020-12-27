```java
class Solution {
    public static void main(String[] args) {
        int[] a = new int[]{1, 2, 3, 2, 1, 4, 4, 5};
        Solution solution = new Solution();
        System.out.println(solution.oddEvenJumps(a));
    }

    public int oddEvenJumps(int[] A) {
        if (A == null) {
            return 0;
        }
        int n = A.length;
        if (n == 0) {
            return 0;
        }

        Item[] oddSorted = new Item[n];
        Item[] evenSorted = new Item[n];
        for (int i = 0; i < n; i++) {
            evenSorted[i] = oddSorted[i] = new Item(i, A[i]);
        }
        Arrays.sort(oddSorted, (item1, item2) -> {
            if (item1.value != item2.value) {
                return item1.value - item2.value;
            } else {
                return item1.index - item2.index;
            }
        });
        Arrays.sort(evenSorted, (item1, item2) -> {
            if (item1.value != item2.value) {
                return item2.value - item1.value;
            } else {
                return item1.index - item2.index;
            }
        });

        Stack<Item> stack = new Stack<>();
        int[] oddNext = new int[n];
        Arrays.fill(oddNext, -1);
        this.updateNextArray(oddSorted, oddNext, stack);

        stack = new Stack<>();
        int[] evenNext = new int[n];
        Arrays.fill(evenNext, -1);
        this.updateNextArray(evenSorted, evenNext, stack);

        int ans = 1;
        boolean[] evenCan = new boolean[n];
        boolean[] oddCan = new boolean[n];
        for (int i = n - 2; i >= 0; i--) {
            if (oddNext[i] == n - 1 || (oddNext[i] != -1 && evenCan[oddNext[i]])) {
                ans++;
                oddCan[i] = true;
            }

            if (evenNext[i] == n - 1 || (evenNext[i] != -1 && oddCan[evenNext[i]])) {
                evenCan[i] = true;
            }
        }
        return ans;
    }

    private void updateNextArray(Item[] sortedArray, int[] nextArray, Stack<Item> stack) {
        for (Item x : sortedArray) {
            while ((! stack.isEmpty()) && x.index > stack.peek().index) {
                Item temp = stack.pop();
                nextArray[temp.index] = x.index;
            }
            stack.push(x);
        }
    }
}

class Item{
    int index;
    int value;
    public Item(int index, int value) {
        this.index = index;
        this.value = value;
    }
}
```
# 核心数据结构 - 单调栈
对于某个元素x进行odd jump，规则为找到其后方的某个y，使得y在所有的Y >= x中为最小
所以对于odd jump，我们先对所有元素升序、下标升序进行排序。这样，在排序后的序列中，在x之后的第一个下标大于x下标的元素就是x的odd jump目标
很显然，我们可以用单调栈，对于排序后的序列，当查看到某一个元素时，如果栈顶元素的下标小于它，那么就标记该元素的目标点就是它，然后弹出。直到栈为空或者栈顶元素的下标大于它，最后将它放入栈中。
even jump同理，只是在排序时需要按照元素降序，下标升序进行排序。
以此，我们便获得了每个元素在两种类型的jump下的目标点了
最后，按照倒序进行统计，统计能够到达终点的数量

space complexity: O(N)

time complexity: 排序的复杂度为O(NlogN)，之后，遍历序列操作单调栈的复杂度为O(N)，统计的时候复杂度为O(N)，故，TC = O(NlogN)
