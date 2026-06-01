<h1 id="balanced-binary-tree">Balanced Binary Tree</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/5abc071c-25d0-4bdd-8865-9cbcab41e750/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>또귀임
재귀 그냥 죽여버리고 싶음
근데 공부해야댐</p>
<p>그래픽스에서 재귀 오지게 씀 ㅠㅠ</p>
<pre><code class="language-cpp">class Solution {
public:

    int height(TreeNode* node) {

        if (node == nullptr) {
            return 0;
        }

        int left = height(node-&gt;left);

        if (left == -1) {
            return -1;
        }

        int right = height(node-&gt;right);

        if (right == -1) {
            return -1;
        }

        if (abs(left - right) &gt; 1) {
            return -1;
        }

        return max(left, right) + 1;
    }

    bool isBalanced(TreeNode* root) {
        return height(root) != -1;
    }
};</code></pre>
<ol>
<li><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/5497dabb-818a-41b7-835b-5070acf53505/image.png" /></li>
</ol>
<p>위 경우를 먼저 살펴보자</p>
<blockquote>
<p><code>root</code> = 1
<code>stack</code> = [1]
<code>-&gt;left</code> ~ 4</p>
</blockquote>
<blockquote>
<p><code>root</code> = 3L
<code>stack</code> = [1, 2L, 3L]
<code>-&gt;left</code> = 4L</p>
</blockquote>
<blockquote>
<p><code>root</code> = 4L
<code>stack</code> = [1, 2L, 3L, 4L] <code>//L = 부모 기준 왼쪽노드</code>
<code>-&gt;left</code> = nullptr, 0
<code>-&gt;right</code> = nullptr, 0
<code>return</code> max(0,0) + 1</p>
</blockquote>
<blockquote>
<p><code>root</code> = 3L
<code>stack</code> = [1, 2L, 3L]
<code>-&gt;left</code> = 1
<code>-&gt;right</code> = 4R </p>
</blockquote>
<blockquote>
<p><code>root</code> = 4R
<code>stack</code> = [1, 2L, 3L, 4R]
<code>-&gt;left</code> = nullptr, 0
<code>-&gt;right</code> = nullptr, 0
<code>return</code> max(0,0) + 1</p>
</blockquote>
<blockquote>
<p><code>root</code> = 3L
<code>stack</code> = [1, 2L, 3L]
<code>-&gt;left</code> = 1
<code>-&gt;right</code> = 1
<code>return</code> max(1,1) + 1</p>
</blockquote>
<p>이 과정을 쭉 이어나가면 <code>root</code>가 1일때 왼쪽 노드의 깊이가 3이라는게 구해진다.</p>
<p>하지만 <code>2R</code>로 넘어가면,
1이 반환이 되고, 
<code>root</code>가 1일때 오른쪽 노드의 깊이가 1이 된다.</p>
<p>따라서 두 방향의 깊이의 차가 1보다 크므로 이는 불균형 이진트리가 된다.</p>
<ol start="2">
<li><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/b2a67ff8-be07-40ae-92b8-483fe1aaa5e9/image.png" /></li>
</ol>
<p>이때 1번에서 살펴본 과정을 반복하면 <code>root</code>가 2일때 
왼쪽의 깊이는 2, 오른쪽은 0이 되고
두 깊이의 차가 1보다 크므로, -1이 반환된다</p>
<p>!!!!!! 이때 -1이 반환되는 이유는
미리 값을 걸러내기 위함으로</p>
<pre><code class="language-cpp">if (left == -1) 
{
    return -1;
}

if (right == -1) 
{
    return -1;
}</code></pre>
<p>이런 코드가 없으면
<code>abs(-1 - 0) + 1 = 2</code>가 되어 불균형 이진트리 임에도 불구하고
이상한 깊이가 반환되게 됨</p>
<p>그러므로 -1이라는 값은 중요!</p>
<p>사실 -1말고 -10000000해도 됨</p>