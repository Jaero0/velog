<h1 id="diameter-of-binary-tree">Diameter of Binary Tree</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/bccc7f7c-74c4-43f7-afb0-7a25a8186714/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>가장 큰 둘레를 가지는 이진트리의 길이를 반환하면 된다</p>
<p>bfs로 하려고 햇으나 코드가 너무 복잡해져서 dfs로 해결하기로 함</p>
<h2 id="재귀">재귀</h2>
<p>재귀의 핵심은 DP와 같음</p>
<ul>
<li>각 재귀의 반환값은 이미 정답임</li>
<li>그럼 어떤 값을 반환해야 원하는 값으로 이용할 수 있을지를 생각</li>
</ul>
<hr />
<p>먼저 코드를 보자</p>
<pre><code class="language-cpp">class Solution {
public:

    int dfs(int &amp;ans, TreeNode* root) {
        if (!root) return 0;

        int l = dfs(ans, root-&gt;left);
        int r = dfs(ans, root-&gt;right);

        ans = max(ans, l + r);

        return max(l, r) + 1;
    }

    int diameterOfBinaryTree(TreeNode* root) {
        int ans = 0;
        dfs(ans, root);
        return ans;
    }
};</code></pre>
<h2 id="1-각-재귀의-반환값은-이미-정답임">1. 각 재귀의 반환값은 이미 정답임</h2>
<pre><code class="language-cpp">int l = dfs(ans, root-&gt;left);
int r = dfs(ans, root-&gt;right);</code></pre>
<p>이 부분을 생각하기전에, 먼저 이진트리고, left, right가 있음</p>
<p>그러니까, left를 계속 타고가면 어떠한 트리이든, 
해당 노드를 루트로 가지는 이진트리의 가장 깊은 왼쪽 노드가 반환되는거임</p>
<p>반대는 오른쪽이고 ㅇㅇ</p>
<pre><code class="language-cpp">dfs(root-&gt;left);
dfs(root-&gt;right);</code></pre>
<p>를 하면 그냥 최고 깊이의 노드가 나타나는거 ㅇㅇ</p>
<h2 id="2-어떤-값을-반환해야-원하는-값으로-이용할-수-있을지를-생각">2. 어떤 값을 반환해야 원하는 값으로 이용할 수 있을지를 생각</h2>
<p>지금 원하는 값은 최고 길이가 되는 이진트리의 길이를 구하는거임</p>
<p>즉, 특정노드 n을 기준으로 최고길이가 되는 경우를 따지면 되는데, 
이말은 루트노드를 거쳐가며 만들 수 있는 이진트리중 길이가 가장 길이가 긴 이진트리가 정답인 이진트리라는거임</p>
<blockquote>
<p>즉, 루트노드에서부터 가장 깊은 left, right노드의 깊이를 합하면 최고 길이를 가지는 이진트리의 길이가 됨</p>
</blockquote>
<p>그럼 반환값으로 무엇을 반환해야할까?</p>
<p>현재 노드의 깊이를 반환하면 됨</p>
<pre><code class="language-cpp">dfs(root-&gt;left);
dfs(root-&gt;right);</code></pre>
<p>여기서 dfs메서드의 반환 타입은 노드의 깊이니까 int타입이 되어야함</p>
<pre><code class="language-cpp">int l = dfs(root-&gt;left);
int r = dfs(root-&gt;right);</code></pre>
<p>그리고 dfs에서 반환을 해줘야하니,</p>
<pre><code class="language-cpp">int l = dfs(root-&gt;left);
int r = dfs(root-&gt;right);

return std::max(l,r) + 1;</code></pre>
<p>자신 노드의 자식 노드 중 더 깊은 위치에 있는 노드의 깊이를 반환하면 됨</p>
<p>하지만 이걸론 안됨</p>
<h2 id="3-정답">3. 정답</h2>
<p>가장 깊이가 깊은 노드의 깊이를 구하는 문제가 아님</p>
<p>가장 긴 길이를 가지는 이진트리의 길이를 구하는 문제임</p>
<p>즉, 현재 <code>l</code>과 <code>r</code>의 합이 자신 노드에서 가장 길이가 긴 노드가 됨</p>
<p>따라서</p>
<pre><code class="language-cpp">int dfs(int &amp;ans, TreeNode* root) 
{
    if (!root) return 0;

    int l = dfs(ans, root-&gt;left);
    int r = dfs(ans, root-&gt;right);

    ans = max(ans, l + r);

    return max(l, r) + 1;
}</code></pre>
<p>이런 코드가 완성됨</p>
<p>ans는 변수로 빼도 상관없음</p>
<pre><code class="language-cpp">class Solution {
public:
    int ans = 0;

    int dfs(TreeNode* root) {
        if (!root) return 0;

        int l = dfs(root-&gt;left);
        int r = dfs(root-&gt;right);

        ans = max(ans, l + r);

        return max(l, r) + 1;
    }

    int diameterOfBinaryTree(TreeNode* root) 
    {
        dfs(root);
        return ans;
    }
};</code></pre>
<h1 id="정리">정리</h1>
<ol>
<li>재귀에서 반환되는 값이 뭐가 되어야 문제에서 말하는 값을 구할 수 있을지 정의<ul>
<li><strong>서브트리의 깊이</strong></li>
</ul>
</li>
<li>반환된 값을 어떻게 처리해야 문제에서 말하는 값을 구할 수 있을지 정의<ul>
<li><strong>자식 서브트리의 합 중 가장 큰 값</strong></li>
</ul>
</li>
</ol>