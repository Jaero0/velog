<h1 id="문제">문제</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/825d32c1-9d6c-4eaa-bcf5-e4913bd0b797/image.png" />
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/663afea1-8386-4377-88b6-ef48f6a6c495/image.png" /></p>
<p>최상위 root노드를 기준으로 
하위 left묶음 트리와 right묶음 트리가 거울반전(미러링)된 상태인지 확인하면 되는 문제임</p>
<h1 id="풀이">풀이</h1>
<p>재귀로 풀려다가 감이 안와서
먼저 DFS, BFS로 풀려고하다가 답이 안나와서 포기함...</p>
<p>그래서 답지를 봄</p>
<p>근데 답이 너무 간단해서 개화남</p>
<p>개빡침 지금
그래서 얼음물 들이키고 포스팅하는중</p>
<p>그냥 진짜 간단함</p>
<p>root를 기준으로 미러링된 이진트리인지 확인하는거거덩?</p>
<p>항상 재귀를 할땐, 그 경우에서 답이 나오는거처럼 풀면됨</p>
<p>위 문제의 핵심은 다음과 같음</p>
<ol>
<li>왼쪽 트리의 left가 오른쪽 트리의 right에 위치해야한다</li>
<li>왼쪽 트리의 right가 왼쪽 트리의 left에 위치해야한다</li>
</ol>
<p>그래서 핵심 로직은 다음과 같음</p>
<pre><code class="language-cpp">bool same(TreeNode* left, TreeNode* right)
{
    return same(left-&gt;left, right-&gt;right) &amp;&amp;
           same(left-&gt;eight, right-&gt;left);
}</code></pre>
<p>그럼 이제 종료조건을 찾아야함</p>
<p>3가지 경우가 생김</p>
<ol>
<li>두 노드가 모두 null일때</li>
<li>두 노드가 모두 null이 아닐때,</li>
<li>두 노드 중 한개만 null일때</li>
</ol>
<p>여기서 중요한건
<code>left-&gt;left</code>, <code>right-&gt;right</code>와
<code>left-&gt;right</code>, <code>right-&gt;left</code>를 비교한다는거임</p>
<p>즉,</p>
<ol>
<li>두 노두가 모두 null인경우는 미러링이 되었다고 볼 수 있음
<code>l-&gt;left = null</code> == <code>r-&gt;right = null</code>이라는 말과
<code>l-&gt;right = null</code> == <code>r-&gt;left = null</code>이라는 말과 같음</li>
<li>두 노드 모두 null이 아닐때, 두 노드의 값이 다르다면 그건 미머링이 되었다고 볼 수 없음
위의 1번에서 설명한거처럼 각 경우를 생각해보면 됨</li>
<li>두 노드 중 1개만 null일때, 마찬가지로 미러링이 되었다고 볼 수 없음</li>
</ol>
<p>근데 2번 종료조건을 확인하려면 3번 종료조건이 우선시 되어야함
둘중 하나라도 <code>null</code>이면 <code>return</code>을 시켜야 값 확인이 확실하게 되긌지?</p>
<p>따라서 종료코드를 추가하면 다음과 같음</p>
<pre><code class="language-cpp">bool same(TreeNode* l, TreeNode* r)
{
    //1. 두 노드가 모두 null일때
    if(!l &amp;&amp; !r) return true;

    //3번 종료조건과 바꾼...
    //2. 두 노드 중 하나라도 null일때
    if(!l || !r) return false;

    //2번 종료조건과 바꾼...
    //3. 두 노드 모두 존재하고, 값이 다른경우
    if(l-&gt;val != r-&gt;val) return false;

    return same(l-&gt;left, r-&gt;right) &amp;&amp;
           same(l-&gt;right, r-&gt;left);
}</code></pre>
<p>따라서 정답은 다음과 같음</p>
<h1 id="정답">정답</h1>
<pre><code class="language-cpp">class Solution {
public:
    bool same(TreeNode* l, TreeNode* r)
    {
        //1. 두 노드가 모두 null일때
        if(!l &amp;&amp; !r) return true;

        //3번 종료조건과 바꾼...
       //2. 두 노드 중 하나라도 null일때
        if(!l || !r) return false;

        //2번 종료조건과 바꾼...
        //3. 두 노드 모두 존재하고, 값이 다른경우
        if(l-&gt;val != r-&gt;val) return false;

        return same(l-&gt;left, r-&gt;right) &amp;&amp;
               same(l-&gt;right, r-&gt;left);
    }

    bool isSymmetric(TreeNode* root) 
    {
        if(!root) return true;

        return same(root-&gt;left, root-&gt;right);
    }
};</code></pre>