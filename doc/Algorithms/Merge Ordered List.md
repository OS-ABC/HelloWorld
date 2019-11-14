#两个有序链表的合并

## 将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 
    示例：
    输入：1->2->4, 1->3->4
    输出：1->1->2->3->4->4
    
自己的思路是另建立个链表头，然后依次比较链表1和链表2 的大小，往下传递即可。
代码如下：
``` C
	struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
     struct ListNode* head,*p;
      head = (struct ListNode*)malloc(sizeof( struct ListNode ));
      head->next = NULL;
      p = head;
     while(l1 != NULL && l2 != NULL)
     {
         if(l1->val > l2->val)
         {
             p->next = l2;
             l2 = l2->next;    
             p = p->next;
         }
         else {
             p->next = l1;
             l1 = l1->next;    
             p = p->next;
              }
     }
    while(l1 != NULL){
            p->next = l1;
             l1 = l1->next;    
             p = p->next;
}
    while(l2 != NULL){
            p->next = l2;
             l2 = l2->next;    
             p = p->next;
}
    
    return  head->next;
}
``` 
官方题解给出了递归的做法，代码如下：
``` C
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        else if (l2 == null) {
            return l1;
        }
        else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
``` 
比如链表2第一个数小于链表1第一个数，那么链表2的第一个数即该为合并后链表的第一个数，链表2第一个数之后该跟什么呢，这时候对链表2的后继部分和链表1继续进行一个合并操作，链表2第一个数后面就跟这个新的合并链表，依次递归，得到结果。


  



 
 
