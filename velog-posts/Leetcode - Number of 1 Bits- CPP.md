<h1 id="문제">문제</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/4e641882-a232-45cc-832b-fdb47d06faf0/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>당연히 $O(\log n)$으로 푸는건줄 알았음</p>
<pre><code class="language-cpp">class Solution {
public:
    int hammingWeight(int n) 
    {
        string s;
        while(n != 0)
        {
            int t = n%2;
            n /= 2;
            if(t == 0) continue;
            s += &quot;1&quot;;
        }

        return s.size();
    }
};</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6b9333bd-9736-4638-99e9-13a057153d2d/image.png" /></p>
<p>근데 이렇게 나오는거임...
이거 logn이 아니라
딱 상수 시간복잡도라는걸 느낌</p>
<p>그래서 고민해본결과 비트연산을 하면 된다는걸 깨달아버림</p>
<p>입력 변수 n을 오른쪽으로 한칸씩 땡기면서
그 수랑 1과 <code>&amp;</code>연산을 했을때 1이면 count증가,
아니면 <code>continue</code>를 하면 된다는걸 찾아벌임</p>
<pre><code class="language-cpp">class Solution {
public:
    int hammingWeight(int n) 
    {
        int count = 0;
        for (int i = 0; i &lt; 32; i++) 
        {
            if ((n &gt;&gt; i) &amp; 1) 
            {
                count++;
            }
        }
        return count;
    }
};</code></pre>
<p>이러면 $O(1)$의 상수 타임에 가능해져버림</p>