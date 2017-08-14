
### LeetCode题解

##### 234. Palindrome Linked List
* 思路
>  两个指针，一快一慢，慢指针同时调转List方向 <br/>
>  注意分奇数和偶数长度回文两种情况

* 代码

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def isPalindrome(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        fast = head
        slow = head
        rev = None
        while fast and fast.next:
            fast = fast.next.next
            # rev the point
            tmp = rev
            rev = slow
            slow = slow.next
            rev.next = tmp
        if fast:
            slow = slow.next
        while rev and rev.val == slow.val:
            rev = rev.next
            slow = slow.next
        return not rev
```


