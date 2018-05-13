---
title: leetcode-021
date: 2018-05-08 22:21:38
categories: 刷题
tags: [leetcode, linked list]
---
# code everyday 002-021
[合并两个有序链表](https://leetcode.com/problems/merge-two-sorted-lists/description/)  
 - **方法一 递归的思路**
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {//将l1 和l2 两个链表合并起来
       //用递归的思路解决
	   if(l1 == nullptr) return l2;
	   if(l2 == nullptr) return l1;
	   ListNode *head;
	   if(l1->val <= l2->val){
			head = l1;
			head->next = mergeTwoLists(l1->next, l2);
	   }
	   else{
		head = l2;
		head->next = mergeTwoLists(l1, l2->next);
	   }
	   return head;
	   }
};
```
- **方法二 直接将小的插入到新链表中**
```

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {//将l1 和l2 两个链表合并起来
        if(l1==nullptr) return l2;
        if(l2==nullptr) return l1;
        ListNode *p1, *p2, *head, *cur;
		p1 = l1;
		p2 = l2;
		head = p1;
		if(p1->val <= p2->val){
			head = p1;
			p1 = p1->next;
		}
		else{
			head = p2;
			p2 = p2->next;
		}
		cur = head;
		while(p1&&p2){
			if(p1->val <= p2->val){
				cur->next = p1;
				cur = p1;
				p1 = p1->next;
			}
			else{
				cur->next = p2;
				cur = p2;
				p2 = p2->next;
			}
		}
		if(p1 == nullptr)
			cur->next = p2;
		else cur->next = p1;
        return head;
		}
};
```
- **下面是自己最初的解法**
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {//将l1 和l2 两个链表合并起来
        //返回l1 将l2插入到l1种
		if(l1 == nullptr&&l2 != nullptr) return l2;//01
		if(l2 == nullptr) return l1;//10 00
		//l1和l2都非空
		ListNode *p1,*p2;
		p1 = l1;
		p2 = l2;
		while(p1!=nullptr&&p2!=nullptr){//这个过程中l1是不断增加的(l2慢慢插入)，l2不断减小，p2始终指向l2头部，
			if(p2->val >= p1->val){
				while(p1->next!=nullptr && p1->next->val <= p2->val) p1 = p1->next;
				ListNode *p11,*p22;
				if(p1->next == nullptr){
					p1->next = p2;
					return l1;
				}
				p11=p1->next;
				p22 = p2;
				while(p22->next!=nullptr && p22->next->val <= p11->val) p22 = p22->next;
				//把l2中片段插入
				p1->next = p2;
				if(p22->next == nullptr){
					p22->next = p11;
					return l1;
				}
				p2 = p22->next;
				p22->next = p11;
				p1 = p11;
			}
			else{//p2->val < p1->val
				ListNode *p22,*p11;
				p11 = p1;
				p22 = p2;
				while(p22->next!=nullptr && p22->next->val <= p1->val) p22 = p22->next;
				if(p22->next == nullptr){
					p22->next = p11;
					return p2;
				}
				p1 = p11;
				l1 = p2;
				p22->next = p11;
				p2 = p22->next;
				
			}
		}
		if(p1 == nullptr) 
        return l1;
    }
};
```
**上面程序是自己写的，思路如下：**
p1始终指向要返回的最后链表的前面
当l1为空的时候，直接返回l2；
当l2为空的时候，直接返回l1;
初始p1=l1,p2=l2
当l1和l2都不为空，循环下面
&emsp;&emsp;当 p1 <= p2时，从p2找出一串介于p1和p1->next之间的数字串，插入其中，然后改变p2，p1位置
&emsp;&emsp;当 p1 > p2时，从
 我
写不下去了  之前写的好乱
sad.
        
---
