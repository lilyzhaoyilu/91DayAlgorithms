## LC 4. Median of Two Sorted Arrays
https://leetcode-cn.com/problems/median-of-two-sorted-arrays/
- [二分寻找中位数](#思路-二分寻找中位数)
- [寻找第k个数](#思路-寻找第k个数)

### 思路 二分寻找中位数
，不难发现最终结果就是 nums1 和 nums2 两个数组交错形成的新数组，也就是说 nums1 和 nums2 的相对位置并不会发生变化，这是本题的关键信息之一。  

设最终找到中位数的时候，在nums1的index是i，nums2的index是j；那么也就有：  
总长度奇数偶数 (m + n) / 2 = len(nums1[0...i]) + len(nums2[0...j]) 注意这里是不包含i，j的 
也就是 (i + 1) + (j + 1) = (m + n ) / 2 
所以j = (m + n) / 2 - i - 2 
验证i，j在正确的位置：`maxLeftA <= minRightB` `&&` `maxLeftB <= minRightA`


本质上是对nums1的切割位置做二分搜索。因为 len(nums1Left) + lens(nums2Left) = (m + n + 1) >> 1 所以j的位置可以推断的求

- [二刷二分](#思路-二刷二分)

### 思路 二刷二分
本质上是对nums1的切割位置做二分搜索。
#### 代码 JavaScript

```JavaScript
var findMedianSortedArrays = function(nums1, nums2) {

    if(nums1.length > nums2.length){
       let temp = nums1
       nums1 = nums2.slice()
       nums2 = temp
    }
    // nums1.length < nums2.length
    const m = nums1.length, n = nums2.length;
    const len = nums1.length + nums2.length;

    // 寻找切割中位数的位置，在nums1中
    let low = 0, high = m ;

    while(low <= high){
        let i = low + ((high - low) >> 1)
        
        let j = ((m + n + 1 ) >> 1) - i

        //[1,2,   3]   len // 2 = 1   (len + 1) // 2 = 2
        //[1,2,   3,4] len // 2 = 2    (len + 1) // 2 = 2
        // nums1:    [0..i - 1] [i...]
        // nums2:    [0..j - 1] [j...]
        const Aleftmax = i == 0 ? -Infinity : nums1[i - 1]
        const Arightmin = i == m ? Infinity: nums1[i ]
        const Bleftmax = j == 0 ? -Infinity : nums2[j - 1]
        const Brightmin = j == n ? Infinity : nums2[j]
        // console.log(i, j)
        if(Aleftmax <= Brightmin && Arightmin >= Bleftmax){
            const res = (len & 1) ? Math.max(Aleftmax, Bleftmax) : ((Math.max(Aleftmax, Bleftmax) + Math.min(Arightmin, Brightmin)) / 2.0)
            return res
        }else if(Aleftmax > Brightmin){
            high = i - 1
        }else{
            low = i + 1
        }

    }
};

```

#### 复杂度分析
时间复杂度： </br>
空间复杂度：

#### 代码 JavaScript

```JavaScript
var findMedianSortedArrays = function(nums1, nums2) {

    //这里是为了优化，不是必须。没有这个就不是 min(nums1,nums2)的log了
    if(nums1.length > nums2.length){
        [nums1, nums2] = [nums2, nums1]
    }

    const m = nums1.length;
    const n = nums2.length;

    //low & high are both length //切割的地方 //index
    let low = 0; high = m;

    while(low <= high){
        //i & j 在这里也是length
        const i = low + Math.floor((high - low) / 2)
        //j 要注意当 m + n 是偶数的时候， + 1 会让结果相同。当m + n 是奇数的时候，其实切割的位置在 m+n+ 1 / 2，比如[1,2,/3]
        const j = Math.floor((m + n + 1) / 2) - i

        //跟 0-based index 对齐 因为0是falsy value，所以写判断的时候要注意
        const maxLeftA = i === 0 ? -Infinity : nums1[i - 1]
        const minRightA = i === m ? Infinity : nums1[i]
        const maxLeftB = j === 0 ? -Infinity : nums2[j - 1]
        const minRightB = j === n ? Infinity : nums2[j]

        if(maxLeftA <= minRightB && minRightA >= maxLeftB){
            const res = (m + n) % 2 === 1 ? Math.max(maxLeftA, maxLeftB) : (Math.max(maxLeftA, maxLeftB) + Math.min(minRightA, minRightB)) / 2
            return res
            //说明需要缩小上界限 -> 降低 maxLeftA
        }else if( maxLeftA > minRightB){
            high = i - 1
            //说明需要缩小下界限
        }else{
            low = low + 1
        }
    }
};

```

#### 复杂度分析
时间复杂度：O(log(Math.min(m, n))) </br>
空间复杂度：O(log(Math.min(m, n)))
### 思路 寻找第k个数

#### 代码 JavaScript

```JavaScript
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
    const len1 = nums1.length
    const len2 = nums2.length
    const totalLen = len1 + len2 
    if(totalLen % 2 == 1){
        //因为写的是求第k小的元素，第1小的元素index是0.
        const midIndex = Math.floor(totalLen / 2)
        const midk = midIndex + 1
        const res = findKthElement(nums1, nums2, midk)
        // console.log(res, midIndex)
        return res
    }else{
        const midIndex1 = Math.floor(totalLen / 2) 
        const midIndex2 = Math.floor(totalLen / 2) -1

        const midk1 = midIndex1 + 1
        const midk2 = midIndex2 + 1

        const a = findKthElement(nums1, nums2, midk1)
        const b = findKthElement(nums1, nums2, midk2)
        // console.log(a,b)
        return (a + b) / 2.0
        
    }
};

var findKthElement = function(nums1, nums2, k){
    const len1 = nums1.length
    const len2 = nums2.length
    let p1 = 0, p2 = 0

    while(true){
        //如果是第一小的，就是开头的两个
        if(k == 1) return Math.min(nums1[p1], nums2[p2])
        // +k - 1 是对0-based index 的补偿
        if(p1 == len1) return nums2[p2 + k - 1]
        if(p2 == len2) return nums1[p1 + k - 1]
        
        const halfk = k >> 1
        // - 1 是对 0-based index 的补偿
        let newp1 = Math.min(p1 + halfk, len1) - 1
        let newp2 = Math.min(p2 + halfk, len2) - 1

        const pivot1 = nums1[newp1]
        const pivot2 = nums2[newp2]

        if(pivot2 >= pivot1){
            // r - l + 1 是长度， k被实际剪掉的个数
            k -= newp1 - p1 + 1
            //p1 从新开始
            p1 = newp1 + 1
        }else{
            k -= newp2 - p2 + 1
            p2 = newp2 + 1
        }
    }
}

```

#### 复杂度分析
时间复杂度：O(log(M + N)) </br>
空间复杂度：O(1)
