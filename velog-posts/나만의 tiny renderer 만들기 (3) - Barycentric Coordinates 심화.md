<h1 id="barycentric-coordinates">Barycentric Coordinates</h1>
<h2 id="1차원-무게중심-좌표">1차원 무게중심 좌표</h2>
<p>앞에서도 설명했지만
더 자세한 공식을 다뤄보겠음</p>
<p>두 점 A, B가 있고, 이는 같은 한 1차원 선분 위에 있음</p>
<p>그리고 이 선분 위에 점 P가 존재함</p>
<p>이 때, 점 P는 점 A, B에 가중치를 고려한 합과 같음</p>
<blockquote>
<p>$P = \alpha A + \beta B$</p>
</blockquote>
<p>만약 $P = 3, A = 1, B = 5$라면
$1\alpha + 5\beta = 3$이 됨</p>
<blockquote>
</blockquote>
<p>$\alpha = 4, \beta = \frac{1}{5}$일때 해답이 될 수 있음
또 $\alpha = 1, \beta = \frac{2}{5}$일때도 해답이 될 수 있음</p>
<p>하지만 여기서 문제가 있음</p>
<p>$\alpha$와 $\beta$가 여러 값이 될 수 있으면 
이후 계산에 오차가 생김</p>
<p>이거를 해결하기위해 사용하는 핵심 개념이</p>
<h2 id="0--alpha--1-0--beta--1-alpha--beta--1">$0 &lt;= \alpha &lt;= 1$, $0 &lt;= \beta &lt;= 1$, $\alpha + \beta = 1$</h2>
<p>임</p>
<p>$\alpha$와 $\beta$를 정규화를 해서 1이라는 총합으로 제한을 두어
어느 상황에서든 값을 통일시키는거임</p>
<p>앞서 살펴본</p>
<blockquote>
<p>$P = \alpha A + \beta B$</p>
</blockquote>
<p>를 이용해보자</p>
<blockquote>
<p>$P = \alpha A + \beta B$
$\alpha + \beta = 1$
$\beta = 1 - \alpha$</p>
</blockquote>
<p>$\alpha = 0$ =&gt; $P = B$
$\alpha = 0.5$ =&gt; $P = 0.5A + 0.5B$
$\alpha = 1$ =&gt; $P = A$</p>
<p>이렇게 선형보간이 됨</p>
<p>그럼 이$\alpha$와 $\beta$를 어떻게 구하지?</p>
<h2 id="alpha-beta-전개">$\alpha, \beta$ 전개</h2>
<p>먼저 $\alpha$를 살펴보자</p>
<h3 id="alpha--fracb---pb---a">$\alpha = \frac{B - P}{B - A}$</h3>
<blockquote>
<p>$P = \alpha A + (1-\alpha)B$
$P = \alpha A + B - \alpha B$
$P - B = \alpha A - \alpha B$
$P - B = -\alpha(-A + B)$
$-\alpha = \frac{P - B}{-A + B}$
$\alpha = \frac{B - P}{B - A}$</p>
</blockquote>
<p>혹은
$\alpha = \frac{(B - P) \cdot (-1)}{(B - A) \cdot (-1)} = \frac{P - B}{A - B}$</p>
<blockquote>
<p>$\alpha = \frac{B - P}{B - A}$</p>
</blockquote>
<h3 id="beta--fracp---ab---a">$\beta = \frac{P - A}{B - A}$</h3>
<blockquote>
<p>$P = (\frac{B - P}{B - A}) \cdot A + \beta B$
$\beta B = P - (\frac{B - P}{B - A}) \cdot A$
$\beta B = P \cdot (\frac{B - A}{B - A}) - (\frac{B - P}{B - A}) \cdot A$
$\beta B = \frac{P \cdot(B-A) - A \cdot (B - P)}{B - A}$
$\beta B = \frac{PB - PA - AB + AP}{B - A}$
$\beta B = \frac{B(P - A) - PA + AP}{B - A}$
$\beta B = \frac{B(P - A)}{B - A}$
$\beta = \frac{P - A}{B - A}$</p>
</blockquote>
<blockquote>
<p>$\beta = \frac{P - A}{B - A}$</p>
</blockquote>
<h2 id="코드">코드</h2>
<pre><code class="language-cpp">void line(int ax, int ay, int bx, int by, TGAImage &amp;framebuffer, TGAColor color)
{
    for (int x = ax; x &lt;= bx; x++)
    {
        float alpha = (bx - x) / static_cast&lt;float&gt;(bx - ax);
        int y = alpha * ay + (1 - alpha) * by; //barycentric again
        framebuffer.set(x, y, color);
    }
}</code></pre>
<p>이렇게 쉽게 증가하는 직선을 그릴 수 있음</p>
<p><code>line(72, 13, 127, 127, framebuffer, white);</code></p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/669dc5f7-1260-420e-99e0-1e028ce07e01/image.png" /></p>
<h1 id="2차원-무게중심-좌표">2차원 무게중심 좌표</h1>
<p>1차원은 점 2개였지?</p>
<p>2차원은 점 3개임</p>
<p>즉 점 A, B, C에 대해 무게중심좌표를 구하는거임
그 외엔 모든게 같음</p>
<blockquote>
<p>$P = \alpha A + \beta B + \gamma C$
$\alpha + \beta + \gamma = 1$</p>
</blockquote>
<p>임</p>
<p>이건 점 2개를 이용할때처럼
$\alpha , 1 - \alpha$이런식으로 접근하기엔 변수가 많음</p>
<p>그래서 2가지 방법을 사용해볼 수 있음</p>
<h2 id="행렬-이용비추천">행렬 이용(비추천)</h2>
<h3 id="행렬-변환">행렬 변환</h3>
<p>행렬 이용하는 거임</p>
<p>연립일차 방정식은 지립일차 방정식은 계수, 미지수, 상수 부로 나눠서 행렬로 표현할 수 있음</p>
<p>먼저 아래 식은 x좌표와 y좌표가 합쳐져 있음</p>
<blockquote>
<p>$P = \alpha A + \beta B + \gamma C$
$\alpha + \beta + \gamma = 1$</p>
</blockquote>
<p>따라서 행렬로 표현하기 위해선 미지수부를 위해 x, y로 나눠야 함</p>
<blockquote>
<p>$\left{
\begin{array}{ll}
    \alpha A_x + \beta B_x + \gamma C_x &amp;= P_x\
    \alpha A_y + \beta B_y + \gamma C_y &amp;= P_y\
    \alpha + \beta + \gamma &amp;= 1
\end{array}\right.$</p>
</blockquote>
<p>이렇게 나타낼 수 있음</p>
<p>이걸 행렬로 표현해보자</p>
<blockquote>
<p>$\begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}
\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix}
=\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}$</p>
</blockquote>
<p>가 됨</p>
<h3 id="수반-행렬과-역행렬">수반 행렬과 역행렬</h3>
<p>우리한테 필요한건 $\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix}$ 임
좌변에 $\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix}$만 남기고 우변으로 이항정리 해보자</p>
<blockquote>
<p>역행렬을 곱하면 됨</p>
</blockquote>
<p>$\begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}
\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix}
=\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}$</p>
<blockquote>
</blockquote>
<p>$\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix} = \begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}^{-1}\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}$</p>
<blockquote>
</blockquote>
<blockquote>
<p>역행렬을 수반행렬로</p>
<blockquote>
<p>수반행렬과 역행렬의 관계 : $A^{-1} = \frac{1}{detA} adjA$</p>
</blockquote>
</blockquote>
<p>$\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix} = \frac{adj\begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}}{\begin{vmatrix} 
    A_x &amp; B_x &amp; C_x \
    A_y &amp; B_y &amp; C_y \
    1&amp;1&amp;1
\end{vmatrix}}$</p>
<p>이걸 전개하면 됨</p>
<h4 id="소행렬-소행렬식-행렬식-계산-여인수-전개-수반행렬">소행렬, 소행렬식, 행렬식 계산, 여인수 전개, 수반행렬</h4>
<p>선형대수에서 배우는건데 
혹시 모르는 사람 있을까봐 설명함</p>
<p>$A = \begin{pmatrix}
    1&amp;2&amp;3\
    4&amp;5&amp;6\
    7&amp;8&amp;9
\end{pmatrix}$
라는 행렬이 있다고 하자</p>
<p>행렬은 기본적으로 행과 열의 조합으로 나타낼 수 있음</p>
<p>이를 $A_{ij}$와 같은 식으로 표현함</p>
<h5 id="소행렬">소행렬</h5>
<blockquote>
<p>$A_{22}$의 소행렬</p>
</blockquote>
<p>$A_{22} = \begin{pmatrix}
    \textcolor{blue}{1}&amp;\textcolor{red}{2}&amp;\textcolor{blue}{3}\
    \textcolor{red}{4}&amp;\textcolor{red}{5}&amp;\textcolor{red}{6}\
    \textcolor{blue}{7}&amp;\textcolor{red}{8}&amp;\textcolor{blue}{9}
\end{pmatrix}$</p>
<p>여기서 빨간색 글자를 제외한
파란색 글자인 부분만 사용하는게 $A_{22}$에 대한 소행렬임</p>
<h5 id="소행렬식">소행렬식</h5>
<blockquote>
<p>$A_{22}$의 소행렬식</p>
</blockquote>
<p>$A_{22} = \begin{vmatrix}
    1&amp;3\
    7&amp;9
\end{vmatrix}$</p>
<p>이렇게 행렬식 기호를 이용해 소행렬을 표현하면 그게 행렬식임</p>
<h5 id="행렬식-계산">행렬식 계산</h5>
<blockquote>
<p>$A_{22}$ 행렬식 계산</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7eee1d3e-2de7-4b61-a8b4-7b4a76d62349/image.png" />
이런 형태로 계산하면됨</p>
<blockquote>
</blockquote>
<p>$1 * 9 - 3 * 7 = -12$가 $A_{22}$의 행렬식 결과임</p>
<p>이때 중요한게 부호임</p>
<h5 id="여인수-전개">여인수 전개</h5>
<blockquote>
<p>여인수 전개</p>
</blockquote>
<p>여인수 전개할때, 필요한건 부호임
$A_{ij}$의 소행렬식의 계산결과를 구하기위해선
$(-1)^{i+j}$의 부호를 붙여 계산해야함</p>
<blockquote>
</blockquote>
<p>예를들어 ${11}$이면, $(-1)^2$이 되어, $(1,1)$위치의 수반행렬에 $* 1$을 해야함
예를들어 ${34}$이면, $(-1)^7$이 되어, $(4,3)$위치의 수반행렬에 $* -1$을 해야함</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/10513dbf-80b2-41db-91cf-13e85f202ceb/image.png" />
즉, 이런 패턴을 가짐</p>
<h5 id="수반행렬">수반행렬</h5>
<p>이렇게 모든 행렬의 소행렬에 행렬식 계산시에 필요한 부호를 계산하여 사용하여 
모든 행렬식을 계산한것이 수반행렬임</p>
<p>이때 중요한건 행과 열이 바뀐다는거임</p>
<blockquote>
<p>수반행렬</p>
</blockquote>
<p>$A_{23}$의 소행렬을 계산했다면
이 계산은 수반행렬의 $adjA_{32}$에 저장됨</p>
<p>$A = \begin{pmatrix}
    1&amp;2&amp;3\
    4&amp;5&amp;6\
    7&amp;8&amp;9
\end{pmatrix}$
이걸 위 모든과정을 거쳐 수반행렬 $adjA$로 만들어보자</p>
<p>모든걸 다 할수없으니, 2행에 대해서만 소행렬, 전개, 수반행렬로 만드는 과정을 보여줌</p>
<p>$C_{21} = -\begin{vmatrix} 2 &amp; 3 \ 8 &amp; 9 \end{vmatrix} = -((2 \times 9) - (3 \times 8)) = -(18 - 24) = 6$
$C_{22} = +\begin{vmatrix} 1 &amp; 3 \ 7 &amp; 9 \end{vmatrix} = (1 \times 9) - (3 \times 7) = 9 - 21 = -12$
$C_{23} = -\begin{vmatrix} 1 &amp; 2 \ 7 &amp; 8 \end{vmatrix} = -((1 \times 8) - (2 \times 7)) = -(8 - 14) = 6$</p>
<p>$\text{adj}(A) = \begin{pmatrix} C_{11} &amp; \mathbf{C_{21}}(6) &amp; C_{31} \ C_{12} &amp; \mathbf{C_{22}}(-12) &amp; C_{32} \ C_{13} &amp; \mathbf{C_{23}}(6) &amp; C_{33} \end{pmatrix}$</p>
<p>이 됨</p>
<p>전체를 전부 이런식으로 수반행렬로 만들면</p>
<p>$\text{adj}(A) = \begin{pmatrix} -3 &amp; {6} &amp; -3 \ 6 &amp; {-12} &amp; 6 \ -3 &amp; {6} &amp; -3 \end{pmatrix}$</p>
<p>이렇게 됨</p>
<hr />
<p>다시 돌아와서</p>
<blockquote>
<p>$\begin{pmatrix}\alpha \ \beta \ \gamma\end{pmatrix} = \frac{adj\begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}}{\begin{vmatrix} 
    A_x &amp; B_x &amp; C_x \
    A_y &amp; B_y &amp; C_y \
    1&amp;1&amp;1
\end{vmatrix}}$</p>
</blockquote>
<p>이 식의 수반행렬을 전개해보자</p>
<p>마지막 행이 1행이라 아주 좋음</p>
<blockquote>
<p>$$
\begin{aligned}
\begin{pmatrix}\alpha \ \beta \ \gamma \end{pmatrix}
&amp;=
\begin{pmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{pmatrix}^{-1}
\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}
\
&amp;=
\frac{
    \mathrm{adj}
    \begin{pmatrix}
        A_x &amp; B_x &amp; C_x\
        A_y &amp; B_y &amp; C_y\
        1 &amp; 1 &amp; 1
    \end{pmatrix}
    \begin{pmatrix}
        P_x \ P_y \ 1
    \end{pmatrix}
}{
\begin{vmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
}
\
&amp;=
\frac{
    \begin{pmatrix}
        B_y - C_y &amp; - (B_x - C_x) &amp;  B_x C_y - B_y C_x\
        -(A_y -C_y) &amp; A_x - C_x &amp;  - (A_x C_y  - A_y C_x) \
        A_y - B_y &amp; - (A_x - B_x) &amp;  A_x B_y - A_y B_x
    \end{pmatrix}
    \begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}
}{
\begin{vmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
}
\end{aligned}$$</p>
</blockquote>
<h3 id="행렬-곱">행렬 곱</h3>
<p>행렬곱은 어떻게 계산하지?</p>
<p>두 행렬 $A, B$가 있을 때, 
$A$의 $i$번째 행과 $B$의 $j$번째 열의 원소들을 차례대로 곱한 뒤 모두 더하면 
결과 행렬의 $(i, j)$번째 성분이 됨.</p>
<p>예를들어
$A = \begin{pmatrix}
    1&amp;2\
    3&amp;4
\end{pmatrix}$,
$B = \begin{pmatrix}
    4&amp;5\
    6&amp;7\
\end{pmatrix}$</p>
<p>이 있으면</p>
<p>$A \times B = 
\begin{pmatrix}
    1 * 4 + 2 * 6 &amp; 1 * 5 + 2 * 7\
    3 * 4 + 4 * 6 &amp; 3 * 5 + 4 * 7
\end{pmatrix} = \begin{pmatrix}
    16 &amp; 19\
    36 &amp; 43
\end{pmatrix}$</p>
<p>가 되는거임</p>
<hr />
<p>그럼 다시 돌아와서</p>
<p>$$\frac{\small
\begin{pmatrix}
B_y - C_y &amp; - (B_x - C_x) &amp;  B_x C_y - B_y C_x\
-(A_y -C_y) &amp; A_x - C_x &amp;  - (A_x C_y  - A_y C_x) \
A_y - B_y &amp; - (A_x - B_x) &amp;  A_x B_y - A_y B_x
\end{pmatrix}
\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}
}{\small
\begin{vmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
}$$</p>
<p>을 살펴보면</p>
<p>분자의 행렬에 곱셈을 할 수 있음</p>
<p>분자부분만 살펴보자</p>
<p>$\begin{pmatrix}
B_y - C_y &amp; - (B_x - C_x) &amp;  B_x C_y - B_y C_x\
-(A_y -C_y) &amp; A_x - C_x &amp;  - (A_x C_y  - A_y C_x) \
A_y - B_y &amp; - (A_x - B_x) &amp;  A_x B_y - A_y B_x
\end{pmatrix}
\begin{pmatrix}P_x \ P_y \ 1\end{pmatrix}$
임</p>
<p>그럼 곱을 진행해볼까나...</p>
<p>$\begin{pmatrix}
P_x (B_y - C_y) + P_y(C_x - B_x) +  1(B_x C_y - B_y C_x) &amp;\
P_x(C_y - A_y) + P_y(A_x - C_x) +  1(-(A_x C_y  - A_y C_x)) &amp;\
P_x(A_y - B_y) + P_y(B_x - A_x) +  1(A_x B_y - A_y B_x)
\end{pmatrix}$
(길이가 너무 길어서 한줄씩 띄움)
(기본적으로, 1행만 존재하는 행렬임)
가 됨</p>
<p>그걸 행렬식으로 표현하면</p>
<blockquote>
<p>$$ 
\frac{\small
\begin{pmatrix}
\begin{vmatrix}
    P_x &amp; B_x &amp; C_x\
    P_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
&amp;
\begin{vmatrix}
    P_x &amp; C_x &amp; A_x\
    P_y &amp; C_y &amp; A_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
&amp;
\begin{vmatrix}
    P_x &amp; A_x &amp; B_x\
    P_y &amp; A_y &amp; B_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
\end{pmatrix}^\top
}{\small
\begin{vmatrix}
    A_x &amp; B_x &amp; C_x\
    A_y &amp; B_y &amp; C_y\
    1 &amp; 1 &amp; 1
\end{vmatrix}
}$$</p>
</blockquote>
<p>처럼 마무리 되는거지</p>
<p>근데 복잡하지?
저렇게 만들고, 계산하려고 해도 행렬식이 4개라
4개를 다 계산해야함</p>
<p>이거 안씀</p>
<p>실제로는 비율을 더 자주 이용함</p>
<h2 id="비율--신발끈-공식-더-나은-선택">비율 &amp; 신발끈 공식 (더 나은 선택)</h2>
<p>1차원에서의 무게중심 좌표 구하는 것과 비슷한 맥락임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/154e9f6b-5574-453d-ad3a-b3b30fd2dad5/image.png" /></p>
<p>이런 삼각형이 있다고 가정해보자</p>
<p>점 A를 기준으로
$\overline{BC}$가 밑변이고 높이가 $h_A$일때,
$\triangle ABC = \frac {1}{2} (\overline{BC} * h_A)$가 됨</p>
<p>이때, 점 P를 기준으로 
$\overline{BC}$가 밑변이고 높이가 $h_P$일때,
$\triangle PBC = \frac {1}{2} (\overline{BC} * h_P)$가 됨</p>
<p>즉 $\frac{area(PBC)}{area(ABC)} = \frac{\frac {1}{2} (\overline{BC} * h_P)}{\frac {1}{2} (\overline{BC} * h_A)} = \frac{h_P}{h_A}$임</p>
<p>$P = \alpha A + \beta B + \gamma C$임</p>
<p>위에서 $\overline{BC}$가 밑변일때 $\frac{area(ABC)}{area(PBC) }=\frac{h_A}{h_P}$이므로,
$h_P = \alpha h_A + \beta h_B + \gamma h_C$가 성립됨</p>
<p>이때,$\overline{BC}$는 밑변이므로 높이가 존재하지 않음
$h_B = 0, h_C = 0$임
$h_P = \alpha h_A$, $\alpha = \frac{h_p}{h_A}$가 됨</p>
<p>즉, $\alpha$는 $\triangle PBC$가 $\triangle ABC$에 차지하는 비율에 의해 결정됨</p>
<blockquote>
<p>$\alpha = \frac{area(PBC)}{area(ABC)}$
$\beta = \frac{area(PCA)}{area(ABC)}$
$\gamma = \frac{area(PAB)}{area(ABC)}$</p>
</blockquote>
<p>로 결정되는거임</p>
<h3 id="신발끈-공식">신발끈 공식</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/c7e450c4-22fe-46f7-abea-20897436d8db/image.png" /></p>
<p>평면위에 다각형의 각 꼭지점 좌표가 있을때,</p>
<p>빨간선으로 이어진 부분을 곱하고 그 결과들을 더한것에서
파란선으로 이어진 부분을 곱하고 그 결과들을 더한것을 빼고
1/2를 곱하면 그게 다각형의 넓이임</p>
<p>이때 중요한건, 꼭짓점의 좌표가 한방향으로 흘러가야한다는거임</p>
<blockquote>
<p>$\triangle ABC \ = \frac{1}{2} * |((A_x * B_y) + (B_x * C_y) + (C_x * A_y) - ((B_x * A_y) + (C_x * B_y) + (A_x * C_y)))|
\ = \frac{1}{2} * |(A_x B_y + B_x C_y + C_xA_y - B_xA_y - C_xB_y - A_xC_y)|
\ = \frac{1}{2} * |(B_y(A_x - C_x) + C_y(B_x - A_x) + A_y(C_x - B_x))|$</p>
</blockquote>
<p>이 됨</p>
<p>예시로 $A = (16, 0), B = (0, 12), C = (0,0), P = (4,3)$
이라고 했을때 
위의 공식을 적용해서 $\triangle ABC, \triangle PBC, \triangle PCA, \triangle PAB$를 구하면
$\triangle ABC = 96, \triangle PBC = 24, \triangle PCA = 24, \triangle PAB = 48$이 나옴</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/4c2f158f-f313-4e9a-bbe1-55d0f4c9dfca/image.png" /></p>
<p>$\alpha, \beta, \gamma$는  각각 순서대로
$\triangle PBC, \triangle PCA, \triangle PAB$ 의 전체 삼각형 너비에 대한 비율임</p>
<h3 id="코드-1">코드</h3>
<pre><code class="language-cpp">double get_triangle_area(Triangle t) 
{
    return 0.5 * (
        (t.b.y-t.a.y)*(t.b.x+t.a.x) + 
        (t.c.y-t.b.y)*(t.c.x+t.b.x) + 
        (t.a.y-t.c.y)*(t.a.x+t.c.x)
    );
}</code></pre>
<p>먼저 삼각형 너비 구하는 공식임</p>
<p>이거 저번 포스트에서 설명했음</p>
<p><a href="https://velog.io/@vfx_master/%EB%82%98%EB%A7%8C%EC%9D%98-tiny-renderer-%EB%A7%8C%EB%93%A4%EA%B8%B0-2-%EC%82%BC%EA%B0%81%ED%98%95-%EA%B7%B8%EB%A6%AC%EA%B8%B0#%EC%BD%94%EB%93%9C-%EC%88%98%EC%A0%95">나만의 tiny renderer 만들기 (2) - 삼각형 그리기 - 코드 수정</a> 참고!</p>
<p>우리가 위에서 살펴본 식으로 살펴보면</p>
<pre><code class="language-cpp">double get_triangle_area_v2(Triangle t)
{
    return 0.5 * (
        (t.b.y * (t.a.x - t.c.x)) + 
        (t.c.y * (t.b.x - t.a.x)) + 
        (t.a.y * (t.c.x - t.b.x))
    );
}</code></pre>
<p>인거임</p>
<h2 id="삼각형-내부-원하는-색으로-칠하기">삼각형 내부 원하는 색으로 칠하기</h2>
<pre><code class="language-cpp">void triangle(Triangle t, TGAImage &amp;framebuffer, TGAColor color) 
{
    int minX = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int minY = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int maxX = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int maxY = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_triangle_area(t);

    if (total_area &lt; 0) return;

#pragma omp parallel for
    for (int x=minX; x&lt;=maxX; x++) 
    {
        for (int y=minY; y&lt;=maxY; y++) 
        {
            double alpha = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.b.x, t.b.y, 0), Vec3(t.c.x, t.c.y, 0))) / total_area;
            double beta  = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.c.x, t.c.y, 0), Vec3(t.a.x, t.a.y, 0))) / total_area;
            double gamma =get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.a.x, t.a.y, 0), Vec3(t.b.x, t.b.y, 0))) / total_area;
            if (alpha&lt;0 || beta&lt;0 || gamma&lt;0) continue;


            framebuffer.set(x, y, color);
        }
    }
}</code></pre>
<p>지금 삼각형을 칠하는 코드는 이런 형태임
alpha, beta, gamma가 위에서 말한 무게중심좌표에 해당함</p>
<p>각 좌표에 대해 먼저 z축을 설정하고
이걸 이용해서 
각 z축을 계산한다음, 이걸 색상으로 이용해볼거임</p>
<p>먼저 main.cpp에 다음과 같은 삼각형 코드를 넣음</p>
<pre><code class="language-cpp">int main(int argc, char** argv)
{

    Model model(argv[1]);
    TGAImage framebuffer(width, height, TGAImage::RGB);


    triangle(Triangle(Vec3(7, 45, 255), Vec3(35, 100, 255), Vec3(45,  60, 255)), framebuffer);

    framebuffer.write_tga_file(&quot;framebuffer.tga&quot;);
    return 0;
}</code></pre>
<p>그리고 triangle을 아래처럼 바꿈</p>
<pre><code class="language-cpp">void triangle(Triangle t, TGAImage &amp;framebuffer) 
{
    int minX = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int minY = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int maxX = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int maxY = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_triangle_area(t);

    if (std::abs(total_area) &lt; 1e-5) return; //backface culling

#pragma omp parallel for
    for (int x = minX; x &lt;= maxX; x++) 
    {
        for (int y = minY; y &lt;= maxY; y++) 
        {
            Vec3 p(x, y, 0);

            double area_a = get_triangle_area(Triangle(p, t.b, t.c)); //PBC
            double area_b = get_triangle_area(Triangle(p, t.c, t.a)); //PCA
            double area_c = get_triangle_area(Triangle(p, t.a, t.b)); //PAB

            double alpha = area_a / total_area;
            double beta  = area_b / total_area;
            double gamma = area_c / total_area;

            if (alpha &lt; -1e-5 || beta &lt; -1e-5 || gamma &lt; -1e-5) continue;

            framebuffer.set(x, y, {
                static_cast&lt;uint8_t&gt;(alpha * t.a.z), 
                static_cast&lt;uint8_t&gt;(beta * t.b.z), 
                static_cast&lt;uint8_t&gt;(gamma * t.c.z), 
                255
            });
        }
    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6a926399-2d5e-48a7-a8e1-ac9c6dc91ad9/image.png" /></p>
<p>굳<del>~</del></p>