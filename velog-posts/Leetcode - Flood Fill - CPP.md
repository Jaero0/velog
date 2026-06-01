<h1 id="flood-fill">Flood Fill</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6dc0f4fe-5497-4263-a7da-362e791db48e/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>간단한 BFS문제이다</p>
<pre><code class="language-cpp">class Solution {
public:
    const int moveX[4] {-1,1,0,0};
    const int moveY[4] {0,0,-1,1};

    vector&lt;vector&lt;int&gt;&gt; floodFill(vector&lt;vector&lt;int&gt;&gt;&amp; image, int sr, int sc, int color) {

        int maxX = image.size();
        int maxY = image[0].size();

        bool visited[51][51] = {false};
        std::queue&lt;pair&lt;int,int&gt;&gt; q;
        int startColor = image[sr][sc];

        q.push(make_pair(sr,sc));
        visited[sr][sc] = true;

        while(q.size() &gt; 0)
        {
            auto srsc = q.front();
            q.pop();

            int x = srsc.first;
            int y = srsc.second;
            image[x][y] = color;

            for(int i = 0; i &lt; 4; i++)
            {
                int mx = x + moveX[i];
                int my = y + moveY[i];

                if (mx &lt; 0 || mx &gt;= maxX || 
                my &lt; 0 || my &gt;= maxY || 
                visited[mx][my] == true || 
                image[mx][my] != startColor) continue;

                visited[mx][my] = true;
                q.push(make_pair(mx,my));
            }
        }

        return image;
    }
};</code></pre>
<p>이렇게 코드를 작성했는데</p>
<p>뭔가... 공간복잡도가 맘에 안들음</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a02a2243-2d74-410a-a884-60b2e1bc6df2/image.png" /></p>
<p>그래서 최적화를 고민해봄</p>
<p>먼저 핵심은 2가지임</p>
<ol>
<li><code>startColor</code>가 제공되는 <code>color</code>와 같으면 <code>startColor</code>의 주변 타일은 볼 필요도 없이 바로 return가능</li>
<li>따라서 <code>visited</code>는 <code>startColor != color</code>인 타일만 찾으면 되므로 필요가 없음</li>
</ol>
<p>예를들어</p>
<p>| | |
|-|-|-|
|1|2|1|
|0|1|0|
|1|1|0|</p>
<p>이런 타일이 있을때,</p>
<p>주어진 좌표가 <code>1,1</code>에 color가 <code>1</code>이라고 해보자</p>
<p>이미 주어진 좌표는 color와 같은 색상인 <code>1</code>이다.</p>
<p>따라서 주어진 좌표 주변에 같은 색상을 가지는 좌표가 있다고 하더라도 
주어진 좌표와 색상이 동일하므로,
바로 return을 때릴수 있는것임</p>
<h2 id="개선된-코드">개선된 코드</h2>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/16400c66-571a-4b62-a16f-75502af1ceec/image.png" /></p>
<pre><code class="language-cpp">class Solution {
public:
    const int moveX[4] {-1,1,0,0};
    const int moveY[4] {0,0,-1,1};

    vector&lt;vector&lt;int&gt;&gt; floodFill(vector&lt;vector&lt;int&gt;&gt;&amp; image, int sr, int sc, int color) {

        if(image[sr][sc] == color) return image;

        int maxX = image.size();
        int maxY = image[0].size();

        std::queue&lt;pair&lt;int,int&gt;&gt; q;
        int startColor = image[sr][sc];

        q.push(make_pair(sr,sc));

        while(q.size() &gt; 0)
        {
            auto srsc = q.front();
            q.pop();

            int x = srsc.first;
            int y = srsc.second;
            image[x][y] = color;

            for(int i = 0; i &lt; 4; i++)
            {
                int mx = x + moveX[i];
                int my = y + moveY[i];

                if (mx &lt; 0 || mx &gt;= maxX || 
                my &lt; 0 || my &gt;= maxY || 
                image[mx][my] != startColor) continue;

                q.push(make_pair(mx,my));
            }
        }

        return image;
    }
};</code></pre>
<p>이 됨</p>
<h1 id="bfs말고-재귀로-해결">BFS말고 재귀로 해결</h1>
<pre><code class="language-cpp">class Solution {
public:
    void fill(std::vector&lt;std::vector&lt;int&gt;&gt;&amp; image, int sr, int sc, int color, int newColor) {
        if (sr &lt; 0 || sr &gt;= image.size() || sc &lt; 0 || sc &gt;= image[0].size() || 
            image[sr][sc] != color || image[sr][sc] == newColor) {
            return;
        }

        image[sr][sc] = newColor;

        fill(image, sr - 1, sc, color, newColor);
        fill(image, sr + 1, sc, color, newColor);
        fill(image, sr, sc - 1, color, newColor);
        fill(image, sr, sc + 1, color, newColor);
    }

    std::vector&lt;std::vector&lt;int&gt;&gt; floodFill(std::vector&lt;std::vector&lt;int&gt;&gt;&amp; image, int sr, int sc, int color) {
        fill(image, sr, sc, image[sr][sc], color);
        return std::move(image);
    }
};</code></pre>
<p>이렇게 재귀를 이용한 재귀로 해결할수도 있음</p>
<p>더빠르고, 공간복잡도 최상ㅇㅇ</p>