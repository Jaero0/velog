<h1 id="lowest-common-ancestor-of-binary-tree">Lowest Common Ancestor of Binary Tree</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/fb1af6c8-9036-4c45-90ff-b89324447303/image.png" />
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/faa8da74-531a-4c97-ad37-75bae022625e/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>재귀형태로 접근하는게 쉬움</p>
<p>재귀의 핵심은 DP와 같음</p>
<ul>
<li>각 재귀의 반환값은 이미 정답임</li>
<li>그럼 어떤 값을 반환해야 원하는 값으로 이용할 수 있을지를 생각</li>
</ul>
<p>그럼 이 문제에서 정답이 되려면, 
각 재귀에서 반환값은 TreeNode가 되어야함</p>
<ol>
<li>p 혹은 q가 발견되면 현재 노드를 리턴</li>
<li>자신을 기준으로 왼쪽 노드와 오른쪽 노드를 재귀 탐색</li>
<li>둘 중 하나에서만 값이 반환되었으면, 아직 자신이 조상이 아니므로, 반환된 값 그대로 부모노드로 반환</li>
<li>둘 다 모두 반환되었으면, 자신이 최고깊이에 있는 최소 공통 조상 노드이므로, 자신을 반환</li>
</ol>
<p>이를 코드로 나타내면 다음과 같음</p>
<pre><code class="language-cpp">class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {

        //안전장치
        if (!root) return nullptr;

        // 1. p 혹은 q가 발견되면 현재 노드를 리턴
        if (root == p || root == q) return root;

        // 2. 왼쪽 노드와 오른쪽 노드를 재귀 탐색
        auto left = lowestCommonAncestor(root-&gt;left, p, q);
        auto right = lowestCommonAncestor(root-&gt;right, p, q);

        // 4. 둘 다 모두 반환되었으면, 자신이 최고깊이에 있는 최소 공통 조상 노드이므로, 자신을 반환
        if (left &amp;&amp; right) return root;

        // 3. 둘 중 하나에서만 값이 반환되었으면, 아직 자신이 조상이 아니므로, 반환된 값 그대로 부모노드로 반환
        if (left) return left;
        return right;
    }
};</code></pre>
<p>이 문제는 미디엄 난이도인데,
처음으로 알고리즘의 개념을 접하면 접근하기 어려워서 인것같음</p>
<p>푸는 법과 어떤 개념인지 알면, 쉽네</p>