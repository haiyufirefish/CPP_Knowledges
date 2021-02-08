### This article is about a mistake in leetcode 86
```
 ListNode* partition(ListNode* head, int x) {
        if(!head)return head;
        ListNode* pre = new ListNode(0);
        ListNode* Currpre = pre;
        ListNode* post = new ListNode(0);
        ListNode* Currpost = post;
        
        while(head!=NULL){
            if(head->val < x){
                Currpre->next = head;
                Currpre = Currpre->next;
                head = head->next;
                Currpre->next = NULL;
            }else{
                Currpost->next = head;
                Currpost = Currpost->next;
                head = head->next;
                Currpost->next = NULL;
            }
            
            // head = head->next;
        }
        Currpre->next = post->next;
        return pre->next;
        
    }
```
What if the head = head->next was written outside? If val < x, now the pointers Currpre == head. 
Currpre->next == head->next.
When head = head->next, it doesn't mean that Currpre = Currpre->next. Currpre stays. 
