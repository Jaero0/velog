<h1 id="z-buffer">Z-buffer</h1>
<p>간단한 개념임</p>
<p>3D모델, 평면의 물체는 물체를 보는 시야에 더 가깝거나 멀다는 개념이 존재함</p>
<p>페인터 알고리즘처럼 시야와의 거리인 z축을 기준으로 정렬을 하고, 순서대로 그리는 알고리즘도 존재함</p>
<p>근데 페인터 알고리즘은 문제가 있음</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f4025070-dbd6-4eb6-b615-071d66994066/image.png" /></p>
<p>이런 종류의 물체는 어디가 먼 물체가 가까운 물체인거임??</p>
<p>그래서 등장한게 zbuffer임</p>
<p>zbuffer는 픽셀단위로 z축을 계산하고, 해당 좌표의 픽셀에 z축 중 더 앞에 있는걸 색상계산, 그리는 방법임</p>
<pre><code class="language-cpp">void triangle(Triangle t, TGAImage &amp;zbuffer, TGAImage &amp;framebuffer) 
{
    int minX = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int minY = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int maxX = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int maxY = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_triangle_area(t);

    if (std::abs(total_area) &lt; 1e-5) return;

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

            //z-buffer
            unsigned char z = static_cast&lt;unsigned char&gt;(alpha * t.a.z + beta * t.b.z + gamma * t.c.z);

            if (z &lt;= zbuffer.get(x, y)[0]) continue;

            zbuffer.set(x, y, {z});

            framebuffer.set(x, y, {
                static_cast&lt;uint8_t&gt;(alpha * t.a.z), 
                static_cast&lt;uint8_t&gt;(beta * t.b.z), 
                static_cast&lt;uint8_t&gt;(gamma * t.c.z), 
                255
            });
        }
    }
}

std::tuple&lt;int,int,int&gt; project(Vec3 v)
{
    // First of all, (x,y) is an orthogonal projection of the vector (x,y,z).
    return {
        (v.x + 1.) *  width/2,       // Second, since the input models are scaled to have fit in the [-1,1]^3 world coordinates,
        (v.y + 1.) * height/2,       // we want to shift the vector (x,y) and then scale it to span the entire screen.
        (v.z + 1.) *   255./2 
    };
}</code></pre>
<p><code>project</code>메서드는 그냥 obj의 각 정점의 좌표가
<code>-1~1</code>로 정규화 되어있으니, 이걸 <code>0~1</code>사이의 좌표로 변환하고
이미지 width와 height에 비율을 맞춰 증가시키는 거임</p>
<p>z축이 <code>255.0 / 2</code>인것도, <code>-1~1</code>의 값을 <code>0~255</code>의 값으로 바꾸려는 거임ㅇㅇ</p>
<p>다음은
이 <code>triangle</code>메서드를 봐보자
2차원 무게중심좌표를 이용해 z축의 값을 계산했음</p>
<ol>
<li>zbuffer는 같은 픽셀좌표에 대해 z축을 비교해서 픽셀 색상을 결정짓는 거임</li>
<li>zbuffer에 x축,y축에 해당되는 값이 존재하지 않으면 x축,y축,z값을 zbuffer에 저장함</li>
<li>만약 현재 x축, y축에 해당되는 값이 zbuffer에 존재하면, x축,y축에 해당되는 Z-buffer에 저장된 z값과 현재 삼각형의 z값을 비교해서 <ol>
<li>현재 삼각형의 z좌표가 더 작은값이면 색상계산 건너뛰고, </li>
<li>현재 삼각형의 z좌표가 더 큰값이면, zbuffer에 새로 x축, y축에 해당되는 z값을 저장하고 색상계산을 함</li>
</ol>
</li>
</ol>
<p>이런 원리임</p>
<h1 id="affine-transformation">Affine Transformation</h1>
<h2 id="선형변환">선형변환</h2>
<p>애파인 변환을 알기위해선 선형변환을 알아야함</p>
<p>선형변환은 쉬움</p>
<p>프로그래밍이나 수학에서의 $f$는 함수라는 의미임
값을 입력하면 $f$를 거쳐 결과가 나옴
이렇게 변환을 하는것을 $f$, 함수라고 부름</p>
<blockquote>
<p>임의의 벡터$\overrightarrow a, \overrightarrow b$ 와 스칼라 $k$가 있을때,</p>
</blockquote>
<ol>
<li>$kf(a) = f(ka)$</li>
<li>$f(a) + f(b) = f(a+b)$</li>
</ol>
<p>의 조건을 만족하면 
해당 $f$, 함수는 선형변환 함수인거임</p>
<p>선형변환은 아래처럼 표현함</p>
<blockquote>
<p>$T(x) = Ax$</p>
</blockquote>
<p>여기서 중요한건 행렬도 선형변환이라는 거임</p>
<blockquote>
<p>임의의 벡터$\overrightarrow a, \overrightarrow b$ 와 스칼라 $k$, 행렬$A$가 있을때,</p>
</blockquote>
<ol>
<li>$kA(a) = A(ka)$ -&gt; 행렬에 스칼라 곱은 위치 바꿔도 동일함</li>
<li>$A(a) + A(b) = A(a + b)$ -&gt; 행렬과 벡터의 연산(곱셈)에서는 덧셈에 대한 분배법칙이 성립</li>
</ol>
<p>하지만 선형변환은 문제가 있음</p>
<p>$T(x) = Ax$라고 했지?
그럼 $x = (0,0)$일때, 곱연산 가능한 어떠한 행렬A가 오더라도,
$x$는 0벡터를 유지함</p>
<p>$\begin{pmatrix}
3 &amp; 6 \ 7 &amp; 101
\end{pmatrix} \cdot
\begin{pmatrix}
0\0
\end{pmatrix} = \begin{pmatrix}
0\0
\end{pmatrix}
$ 이라는거임</p>
<h2 id="애파인-공간">애파인 공간</h2>
<p>그래서 등장한게 애파인 공간임</p>
<p>애파인 공간/변환을 이해하기 위해서는 먼저 2가지의 지식이 필요함</p>
<h3 id="1-벡터는-위치좌표를-표시할-수-없다---동차좌표">1. 벡터는 위치(좌표)를 표시할 수 없다 - 동차좌표</h3>
<p>$\overrightarrow {ab}$라는 벡터가 있음</p>
<p>이 벡터의 시작위치를 알 수 있음?</p>
<p>2차원이든 3차원이든 n차원이든
n차원 벡터의 시작 위치는 절대로 벡터로 정의할 수 없음</p>
<p>그래서 사용하는게 Homogeneous Coordinates, 동차좌표임</p>
<p>n차원 좌표를 n+1차원 좌표로 표시하는거임</p>
<blockquote>
<p>$a = (1,3)$이라는 좌표가 있음
이게 벡터인지 좌표인지 어떻게 알 수 있지?</p>
</blockquote>
<p>a = $\begin{bmatrix}
1 \ 3 \ w
\end{bmatrix}$이런식으로 동차좌표에서는 표시함</p>
<blockquote>
</blockquote>
<p>이때 $w$는 $0 |1$의 값으로, 
$a$가 점인 경우, $w = 1$ 
$a$가 벡터인 경우, $w = 0$ 으로 표시함</p>
<h3 id="2-선형변환은-평행이동이-안된다---애파인-변환">2. 선형변환은 평행이동이 안된다 - 애파인 변환</h3>
<p>$T(x) = Ax$가 선형변환 식임</p>
<blockquote>
<p>$T(x) = Ax$라고 했지?
그럼 $x = (0,0)$일때, 곱연산 가능한 어떠한 행렬A가 오더라도,
$x$는 0벡터를 유지함</p>
</blockquote>
<p>이걸 위에서 설명했음</p>
<p>이걸 해결하기 위해</p>
<blockquote>
<p>$T(x) = Ax + b$</p>
</blockquote>
<p>처럼 벡터 $b$를 더함으로써 $x$가 원점이어도 원점에서 벗어날 수 있도록 함</p>
<p>선형변환은
$T(0) = 0$인데 반해, 
애파인 변환은
$b \neq 1, T(0) \neq 0$
$b = 0, T(0) = 0$ 이 됨</p>
<hr />
<p>애파인 공간이 뭐냐?
동차좌표를 이용해서 n차원 공간을 표시한 공간임</p>
<p>애파인 변환이 뭐냐?
선형변환에서 불가능한 평행이동이라는 개념을 추가한 변환임</p>
<p>쉽지?</p>
<blockquote>
<p>$T(x) = Ax + b$</p>
</blockquote>
<p>이게 선형변환 + 평행이동 이라는 뜻이지?</p>
<p>모든 변환의 $A$행렬은 n차원일때, n*n크기의 정사각행렬임</p>
<p>따라서 2차원일때를 예시로
$\begin{pmatrix}
    x_{new}\y_{new}
\end{pmatrix} = 
\begin{pmatrix}
    A_{11}&amp;A_{12}\
    A_{21}&amp;A_{22}
\end{pmatrix} \cdot
\begin{pmatrix}
    x_{origin}\y_{origin}
\end{pmatrix} + 
\begin{pmatrix}
    b_x\b_y
\end{pmatrix}
$으로 표현가능 함</p>
<p>이걸 하나의 행렬 연산으로 표현하기 위해
동차좌표를 이용한다는거임</p>
<p>$\begin{pmatrix}
    x_{new}\y_{new}\(0|1)
\end{pmatrix} = 
\begin{pmatrix}
    A_{11}&amp;A_{12}&amp;b_x\
    A_{21}&amp;A_{22}&amp;b_y\
    0&amp;0&amp;1
\end{pmatrix} \cdot
\begin{pmatrix}
    x_{origin}\y_{origin}\(0|1)
\end{pmatrix}
$</p>
<p>근데 조심해야할게 있음</p>
<ol>
<li>행렬의 마지막 행은 항상 $\begin{pmatrix}
 0&amp;0&amp;1
\end{pmatrix}$임</li>
<li>모든 $(0|1)$은 벡터냐, 좌표(위치)냐에 따라 달라짐</li>
</ol>
<p>2번을 살펴보자</p>
<blockquote>
<p>$a$가 $\overrightarrow a = (x,y)$일때,
$\begin{pmatrix}
    A_{11}&amp;A_{12}&amp;b_x\
    A_{21}&amp;A_{22}&amp;b_y\
    0&amp;0&amp;1
\end{pmatrix} \cdot
\begin{pmatrix}
    a_x\a_y\0
\end{pmatrix}=
\begin{pmatrix}
    A_{11} \cdot a_x + A_{12} \cdot a_y\
    A_{21} \cdot a_x + A_{22} \cdot a_y \
    0
\end{pmatrix}
$
이 되어서, 선형변환이 됨
즉, 애파인 변환을 벡터에 적용하면 선형변환과 같이 동작함</p>
</blockquote>
<blockquote>
<p>$a$가 $a = (x,y)$일때,
$\begin{pmatrix}
    A_{11}&amp;A_{12}&amp;b_x\
    A_{21}&amp;A_{22}&amp;b_y\
    0&amp;0&amp;1
\end{pmatrix} \cdot
\begin{pmatrix}
    a_x\a_y\1
\end{pmatrix}=
\begin{pmatrix}
    A_{11} \cdot a_x + A_{12} \cdot a_y + b_x\
    A_{21} \cdot a_x + A_{22} \cdot a_y + b_y\
    1
\end{pmatrix}
$
이 되어서, 선형변환 + 평행이동이 됨
즉, 좌표에서의 애파인 변환은 애파인 변환임</p>
</blockquote>