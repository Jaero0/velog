<h1 id="first-bad-version">First Bad Version</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/47113e75-4420-494c-acbe-46c5408e96a0/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>또또또 문제 설명 아주그냥 뭐같은 문제임</p>
<p>그냥 핵심은 이진탐색하라는거임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6fc7e29b-9887-4b0f-8dc6-4a7f587b6489/image.png" /></p>
<blockquote>
<p>you should minimize the number of calls to the API</p>
</blockquote>
<p>라고 함</p>
<ul>
<li>가장 최소의 bad version을 1~n사이의 값 중 찾아야함</li>
<li>bad version은 <code>isBadVersion</code>메서드의 호출을 통해 해당 숫자가 bad version인지 아닌지 알 수 있음</li>
</ul>
<pre><code class="language-cpp">class Solution {
public:

    int firstBadVersion(int n) {

        long l = 1;
        long mid = 0;

        while(l &lt;= n)
        {
            mid = (l + n) / 2;

            if(isBadVersion(mid))
            {
                n = mid - 1;
            }
            else
            {
                l = mid + 1;
            }
        }

        return l;

    }
};</code></pre>
<p>중요한건 l을 리턴한다는거임</p>
<p>왜? l을 리턴함? mid리턴해야하는거 아님??</p>
<p>ㄴㄴ
mid는 단순히 해당 수가 bad version인지 아닌지 판가름 하는 용도로 이 풀이에서는 사용중임</p>
<p>n은 version중 bad version이 발견되면, 
바로 bad version의 <code>상한선</code>이 됨</p>
<p>l은 version중 bad version에 관계없이 
모든 수의 <code>하한선</code>이 됨</p>
<p>따라서 상한선을 계속 낮추면서 하한선을 올리면서
하한선이 상한선을 추월하는 상황이 바로 bad version의 최하값이 되는거임</p>