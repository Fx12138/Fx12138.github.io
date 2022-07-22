---
title: JS实现几种排序
date: 2022-04-24 16:40:56
tags: 算法
---

# 快速排序

快速排序运用了分而治之的思想,分治法的基本思想是：将原问题分解为若干个规模更小但结构与原问题相似的子问题。递归地解这些子问题，然后将这些子问题的解组合为原问题的解。

"快速排序"的思想很简单，整个排序过程只需要三步：

​		（1）在数据集之中，选择一个元素作为"基准"（pivot,一般选择中间的值）。

　　（2）所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。

　　（3）对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

```js
//快排
        let nums = [-14, -10, -8, -6, -3, 3, 6, 8]
        function quickSort(nums) {
            if (nums.length <= 1) { return nums }
            var pivotIndex = Math.floor(nums.length / 2)	//基准的位置
            var pivot = nums.splice(pivotIndex, 1)[0]	//基准的值
            var left = []
            var right = []
            for (let i = 0; i < nums.length; i++) {
                if (nums[i] <= pivot) {
                    left.push(nums[i])
                } else {
                    right.push(nums[i])
                }
            }

            return quickSort(left).concat([pivot], quickSort(right))
        }
        console.log(quickSort(nums));
```

空间复杂度：O(1)
时间复杂度：最好O(nlog²n)，最坏O(nlog²n)，平均O(nlogn)
使用场景：数据量大于1000
稳定性：不稳定。

# 冒泡排序

- 比较相邻元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较

```js
        //冒泡
        let nums1 = [23, 4, 54, 11, 65, 76, 89, 3, 6]
        function bubbleSort(nums) {
            if (nums.length <= 1) return nums
            let temp = null
            for (let i = 0; i < nums.length; i++) {
                for (let j = 0; j < nums.length - 1; j++) {
                    if (nums[j] > nums[j + 1]) {
                        temp = nums[j]
                        nums[j] = nums[j + 1]
                        nums[j + 1] = temp
                    }


                }
                console.log(nums);	//一次循环后最大的数在最后一位
            }
            return nums
        }
        console.log(bubbleSort(nums1));
```

空间复杂度：因为只涉及到交换，只需要常量级的临时空间，所以空间复杂度为O(1)
时间复杂度：最好O(n)，最坏O(n²)，平均O(n²)
使用场景：数据量小于1000
稳定性：稳定

# 选择排序

首先在元素中找到最小的元素，把他放在最前边，然后再从剩下的元素中寻找最小的元素，依次放在最左边...

```js
let nums = [-14, -10, -8, -6, -3, 3, 6, 8]
function chooseSort(nums){
  for(let i =0;i<nums.length;i++){
    let minIndex = i
    for(let j = i;j<nums.length;j++){
      if(nums[j]<nums[minIndex]){
        minIndex = j
      }
    }
    let temp = nums[i]
    nums[i] = nums[minIndex]
    nums[minIndex] = temp
  }
  return nums
}
   console.log(chooseSort(nums))
```

