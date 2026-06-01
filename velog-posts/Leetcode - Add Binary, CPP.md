<h1 id="add-binary">Add Binary</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a6584854-ba05-416b-8d3f-675a357d37f3/image.png" /></p>
<h1 id="풀이">풀이</h1>
<p>먼저 십진수 변환은 안됨</p>
<p>문자열의 길이가 $10^4$인데, 즉, $2^{10^4}$라는 뜻이라 숫자 개큼 ㅇㅇ</p>
<p>그래서 차례대로 한자리씩 더하면서 계산하면 됨</p>
<p>먼저 Example 1에서 보이듯이 두 문자열 input의 크기가 다를 수 있으므로, 
계산 편의성을 위해 빈 자리를 0으로 채워주도록 함</p>
<p>핵심은 <code>carry</code>라는 올림수임</p>
<p>두 이진수의 각 자리의 합과 carry를 더한 수에 따라 값에 차등을 두면 됨</p>
<p>항상 이진수 연산은 끝자리부터,
연산결과는 항상 마지막에 추가산결과는 항상 마지막에 추가</p>
<p>수행과정은 아래와 같음</p>
<blockquote>
</blockquote>
<p>a : 10111
b : 11011
이라고 해보자</p>
<blockquote>
</blockquote>
<ol>
<li><code>carry</code> = 0
<code>1+1+carry</code> = 2
<code>answer</code> = 0...1<blockquote>
</blockquote>
</li>
<li><code>carry</code> = 1
<code>1+1+carry</code> = 3
<code>answer</code> = 01...1<blockquote>
</blockquote>
</li>
<li><code>carry</code> = 1
<code>1+0+carry</code> = 2
<code>answer</code> = 010...1<blockquote>
</blockquote>
</li>
<li><code>carry</code> = 1
<code>0+1+carry</code> = 2
<code>answer</code> = 0100...1<blockquote>
</blockquote>
</li>
<li><code>carry</code> = 1
<code>1+1+carry</code> = 3
<code>answer</code> = 01001...1<blockquote>
</blockquote>
남은 <code>carry</code> = 1
따라서 <code>answer</code>의 마지막에 올림수에 해당되는 1을 추가해줌<blockquote>
</blockquote>
<code>answer</code> = 010011<blockquote>
</blockquote>
뒤집기
<code>answer</code> = 110010<blockquote>
</blockquote>
<img src="https://velog.velcdn.com/images/vfx_master/post/e4d8b370-3c3c-4a7a-b9d8-08d634eed540/image.png" width="60%/" />


</li>
</ol>
<p>하지만 이는 이진수를 역순으로 연산한 결과라 값이 뒤집혀 있음</p>
<p>그러므로 다시 뒤집어주면됨</p>
<pre><code class="language-cpp">class Solution {
public:
    string addBinary(string a, string b) {

        int bl = b.size() - 1;
        int al = a.size() - 1;

        //연산 편의를 위한 자리수 맞추기 작업
        while(a.size() &lt; b.size()) a = '0' + a;
        while(b.size() &lt; a.size()) b = '0' + b;

        string s = &quot;&quot;;
        int carry = 0;

        //올림수를 이용해 각 자리별로 연산
        for(int i = b.size() - 1; i &gt;= 0; i--)
        {
            int sum = carry + (a[i] - '0') + (b[i] - '0');

            if(sum == 0) 
            {
                s += &quot;0&quot;;
                carry = 0;
            }
            else if(sum == 1)
            {
                s+= &quot;1&quot;;
                carry = 0;
            } 
            else if(sum == 2)
            {
                s += &quot;0&quot;;
                carry = 1;
            }
            else
            {
                s += &quot;1&quot;;
                carry = 1;
            }
        }

        //올림수가 남았을 경우, 결과에 추가해주기
        if(carry &gt; 0)
        {
            s += &quot;1&quot;;
        }

        //연산결과 뒤집기
        reverse(s.begin(), s.end());

        return s;
    }
};</code></pre>
<p>$O(n)$으로 해결!</p>