<h1 id="linked-list-cycle">Linked List Cycle</h1>
<p><a href="https://leetcode.com/problems/linked-list-cycle/description/">https://leetcode.com/problems/linked-list-cycle/description/</a></p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/9bae629d-2d42-47e0-9471-594fa78b93af/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>문제가 좀 개판임
문제 설명이 개같음</p>
<p>말을 드럽게 어렵게 해놨음</p>
<p>그냥 딱 이거임</p>
<ol>
<li><code>pos</code>는 매개변수로 주어지지 않음</li>
<li><code>tail</code>노드에서 <code>next</code>가 있을때 이때 <code>next</code>가 가리키는 노드를 <code>pos</code>라고 임의로 말함</li>
<li>그러니까, 그냥 <code>tail</code>노드에서 
<code>next</code>가 <code>nullptr</code>이면 싸이클이 없는거고 
<code>next</code>가 특정 노드면 싸이클이 존재하는거임</li>
</ol>
<h2 id="1차-풀이">1차 풀이</h2>
<p>그냥 set을 써서 $O(n\log n)$으로 풀음</p>
<pre><code class="language-cpp">class Solution {
public:
    bool hasCycle(ListNode *head) {

        set&lt;ListNode*&gt; set;

        ListNode* n = head;

        bool isFound = false;

        while(n)
        {
            if(set.contains(n))
            {
                isFound = true;
                break;
            }

            set.insert(n);
            n = n-&gt;next;
        }

        return isFound;
    }
};</code></pre>
<h2 id="2차-풀이">2차 풀이</h2>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/cb1fa198-ff6d-45f9-978b-c6b6be2c6317/image.png" /></p>
<p>문제에서 추가 도전사항을 제시해줌</p>
<p>지금은 set을 사용해서 $O(n)$의 메모리 공간을 사용함
이걸 $O(1)$, 즉, 추가 메모리 없이 해결해보라는거임</p>
<p>이건 투포인터를 써야함
핵심은 아래 내용임</p>
<ol>
<li>어짜피 tail-&gt;next는 싸이클이 있거나, nullptr임</li>
</ol>
<p>그러니까 포인터 2개를 둬서
하나는 1칸씩 움직이고, 하나는 2개씩 움직여서 
싸이클이 만들어지면 true, 
2개씩 움직인 노드가 nullptr이면 false를 리턴하면 됨</p>
<blockquote>
<p>왜 2개씩 움직이는거임? 3개는 안됨?</p>
</blockquote>
<p>2개씩 움직여야하는 이유는
nullptr을 편하게 찾기 위해서임</p>
<blockquote>
</blockquote>
<p>nullptr-&gt;next는 에러터짐
근데 이걸 3개, 4개씩 하면 예외처리를 위해 if문이 오지게 쓰임</p>
<blockquote>
</blockquote>
<p>편하게 하려고 ㅇㅇ</p>
<pre><code class="language-cpp">class Solution {
public:
    bool hasCycle(ListNode *head) {

        if(!head) return false;

        ListNode* n = head;
        ListNode* nn = head-&gt;next;

        while(nn &amp;&amp; nn-&gt;next)
        {
            if(n == nn) return true;

            n = n-&gt;next;
            nn = nn-&gt;next-&gt;next;

            if(!nn) return false;
        }

        return false;
    }
};</code></pre>
<p>끝!</p>