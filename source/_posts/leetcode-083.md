---
title: leetcode-083
date: 2018-04-26 21:36:38
categories: 刷题
tags: [leetcode,linked list]
---
# code everyday 001-083  
[将有序链表中重复元素删除](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)  
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
 //给出一个已排好序的列表 整理成一个无重复元素的列表
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode *p, *q;
		if(head == NULL) return nullptr;//1.考虑初始链表为空的情况
		p = q = head;
		if(q->next==NULL) return head;//2.考虑只有一个数的情况
		p = q->next;
		while(p!=NULL){
			if(p->val == q->val){//如果和前一个数相等 跳过这个数
				p = p->next;
				q->next = p;
			}
			else{//如果和前一个数不相等 
				q = p;
				p = p->next;
			}
		}
		return head;
    }
};
```
* **思路&心得**
在原链表上进行操作，不论原链表是否为空，**返回值始终是头节点**；  
对于链表 口->口->口->口->口  前-->后；
设置两个指针p和q,p始终指向q后面一个节点；
当p所指节点的值和q所指节点的值相等时，从链表删除p，且q不动，p指向之前的下一个节点;
当p所指节点的值和q所指节点的值不相等时,p和q都向后挪一个；
当p为空时，则所有节点判断完毕，退出循环。
通过discuss区域查看别人代码发现，有种思路时，p和q不一定挨着，p不断向后推，当找到一个和q值不想同的位置时，再把p、q中间那段删了。
___  
 &emsp;&emsp;参考了知乎上很多前辈的建议，从今天起按照tag刷leetcode，从easy到hard。 