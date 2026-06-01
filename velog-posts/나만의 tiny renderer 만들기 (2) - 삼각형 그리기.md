<h1 id="추가-사항">추가 사항</h1>
<pre><code class="language-cpp">#ifndef VEC3_H
#define VEC3_H
#include &lt;cmath&gt;

class Vec3
{
public:
    float x, y, z;

    Vec3(float x_ = 0.0f, float y_ = 0.0f, float z_ = 0.0f)
        : x(x_), y(y_), z(z_) {}

    Vec3 operator-(const Vec3 &amp;v) const
    {
        return Vec3(this-&gt;x - v.x, this-&gt;y - v.y, this-&gt;z - v.z);
    }

    Vec3 operator+(const Vec3 &amp;v) const
    {
        return Vec3(this-&gt;x + v.x, this-&gt;y + v.y, this-&gt;z + v.z);
    }

    float length_squared() const 
    {
        return this-&gt;x * this-&gt;x + this-&gt;y * this-&gt;y + this-&gt;z * this-&gt;z;
    }

    float length() const 
    {
        return std::sqrt(length_squared());
    }
};

inline float dot(Vec3 v1, Vec3 v2)
{
    return v1.x * v2.x + v1.y * v2.y + v1.z * v2.z;
}

inline Vec3 cross(Vec3 v1, Vec3 v2)
{
    return Vec3(
        v1.y * v2.z - v1.z * v2.y,
        v1.z * v2.x - v1.x * v2.z,
        v1.x * v2.y - v1.y * v2.x);   
}

class Triangle
{
public:
    Vec3 a;
    Vec3 b;
    Vec3 c;
    Triangle(Vec3 v1, Vec3 v2, Vec3 v3) : a(v1), b(v2), c(v3) {}

};

#endif // VEC3_H
</code></pre>
<p>일단 벡터 클래스와, 벡터 3개를 이용한 Triangle클래스를 만들어줌 ㅇㅇ</p>
<p>벡터클래스에는 연산자 오버로딩, dot, cross 연산을 정의해놓음ㅇㅇ</p>
<h1 id="벡터-외적-특성-이용하기">벡터 외적 특성 이용하기</h1>
<p>일단 2차원에서 진행된다는 점을 잊지 말 것!</p>
<pre><code class="language-cpp">Triangle t1(Vec3(7,45,0), Vec3(35,100,0), Vec3(45,60,0));
Triangle t2(Vec3(120,35,0), Vec3(90,5,0), Vec3(45,110,0));
Triangle t3(Vec3(115,83,0), Vec3(80,90,0), Vec3(85,120,0));

draw_triangle(t1.a.x, t1.a.y, t1.b.x, t1.b.y, t1.c.x, t1.c.y, framebuffer, red);
draw_triangle(t2.a.x, t2.a.y, t2.b.x, t2.b.y, t2.c.x, t2.c.y, framebuffer, white);
draw_triangle(t3.a.x, t3.a.y, t3.b.x, t3.b.y, t3.c.x, t3.c.y, framebuffer, green);</code></pre>
<p>이렇게 3개의 triangle을 그리도록 했음</p>
<p><code>draw_triangle</code>은 </p>
<pre><code class="language-cpp">void line(int ax, int ay, int bx, int by, TGAImage &amp;framebuffer, TGAColor color) 
{
    bool is_x_smaller = (std::abs(ax - bx) &lt; std::abs(ay - by));

    if (is_x_smaller) { std::swap(ax, ay); std::swap(bx, by); }
    if (ax &gt; bx)      { std::swap(ax, bx); std::swap(ay, by); }

    int dx   = bx - ax;
    int dy   = std::abs(by - ay);
    int idiff = 0;
    int y    = ay;
    int ystep = (by &gt; ay) ? 1 : -1;

    for (int x = ax; x &lt;= bx; x++) 
    {
        if (is_x_smaller) framebuffer.set(y, x, color);
        else               framebuffer.set(x, y, color);

        idiff += 2 * dy;
        if (idiff &gt; dx) 
        {
            y     += ystep;
            idiff -= 2 * dx;
        }
    }
}

void draw_triangle(int ax, int ay, int bx, int by, int cx, int cy, TGAImage &amp;framebuffer, TGAColor color) 
{
    line(ax, ay, bx, by, framebuffer, color);
    line(bx, by, cx, cy, framebuffer, color);
    line(cx, cy, ax, ay, framebuffer, color);
}</code></pre>
<p>이렇게 작동함</p>
<blockquote>
<p>왜 저런 코드가 나왓는지 궁금하면
<a href="https://velog.io/@vfx_master/%EB%82%98%EB%A7%8C%EC%9D%98-tiny-renderer-%EB%A7%8C%EB%93%A4%EA%B8%B0-1-Bresenham-Algorithm">나만의 tiny renderer 만들기 (1) - Bresenham Algorithm</a> 참고!</p>
</blockquote>
<p>이런 삼각형 3개가 만들어지지
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e5ddd28c-3857-4d6d-a6bc-ee7f62f1b2bd/image.png" /></p>
<p>여기에 이제 특정 픽셀이 삼각형 내부인지를 판별해야함
이걸 쉽게 구할 수 있는 방식이 있음</p>
<p>그게 바로 외적 공식의 기하학적 특성을 이용한거임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/8bba69ff-8589-476b-8380-d17e2242423b/image.png" /></p>
<p>두 벡터의 외적된 벡터의 절대값(길이)는 
두 벡터가 이루는 평행사변형의 크기와 같음</p>
<p>$\frac{1}{2} * 외적 = 삼각형 크기$ 가 되는거임</p>
<table>
<thead>
<tr>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f1436732-94ef-4551-aaff-d77280fdd506/image.png" /></th>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/c7c9c615-5ec8-4bf1-98d1-dffb66b1fd19/image.png" /></th>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6c5b52be-d9b0-4b6e-9656-05b61953acba/image.png" /></th>
</tr>
</thead>
<tbody><tr>
<td>또, 이렇게</td>
<td></td>
<td></td>
</tr>
<tr>
<td>`점 p에서 삼각형 각 꼭짓점까지 만들 수 있는</td>
<td></td>
<td></td>
</tr>
<tr>
<td>새로운 삼각형 3개의 넓이의 합 == 기존 삼각형의 넓이`</td>
<td></td>
<td></td>
</tr>
<tr>
<td><code>= 세 삼각형 넓이의 합 - 기존 삼각형 넓이 == 0</code></td>
<td></td>
<td></td>
</tr>
<tr>
<td>라면, 점 p는 삼각형 내부에 있는거임</td>
<td></td>
<td></td>
</tr>
</tbody></table>
<p>여기서 외적의 성질을 이용하면 삼각형 넓이가 아닌
두 벡터의 외적 길이인 평행사변형의 크기를 이용해도 문제가 없음</p>
<h2 id="코드">코드</h2>
<p>따라서 먼저 다음과 같은 넓이 구하는 코드 + 점 p의 위치가 삼각형 내부에 있는지 판별하는 메서드를 만들어줌</p>
<pre><code class="language-cpp">float get_triangle_area(const Triangle &amp;t)
{
    auto ba = t.b - t.a;
    auto ca = t.c - t.a;

    auto cr = cross(ba, ca);

    return cr.length();
}

bool is_inside_triangle(const float area, const Vec3 &amp;p, const Triangle &amp;t)
{
    Triangle pt1(Vec3(p), Vec3(t.a), Vec3(t.b));
    Triangle pt2(Vec3(p), Vec3(t.b), Vec3(t.c));
    Triangle pt3(Vec3(p), Vec3(t.c), Vec3(t.a));

    auto pt1_area = get_triangle_area(pt1);
    auto pt2_area = get_triangle_area(pt2);
    auto pt3_area = get_triangle_area(pt3);

    float diff = area - (pt1_area + pt2_area + pt3_area);

    if (0 &lt;= diff &amp;&amp; diff &lt; 1e-4) return true;
    return false;
}</code></pre>
<p>점 p를 이용해 만들 수 있는 삼각형을 만들고,
해당 삼각형의 외적과 외적의 길이를 구하여 평행사변형의 넓이를 구함</p>
<blockquote>
<p>왜 <code>std::abs</code>와 같은 절대값 처리가 없음?</p>
</blockquote>
<p>쉬움
오른손 좌표계냐 왼손 좌표계냐에 따라 외적의 방향이 달라지지?
두 벡터가 오른손 좌표계에서 외적이 특정 방향(d)이었다면
해당 두 벡터는 왼손 좌표계에서 외적은 -d 가 됨</p>
<blockquote>
</blockquote>
<p>근데, 벡터의 길이를 구하는 로직은 자기 자신의 모든 좌표를 제곱하는 것과 같음</p>
<pre><code class="language-cpp">float length_squared() const 
{
    return this-&gt;x * this-&gt;x + this-&gt;y * this-&gt;y + this-&gt;z * this-&gt;z;
}</code></pre>
<p>그러니까 외적의 특정 축이 음수였다고 하더라도,
길이를 구할때는 무조건 양수가 나오게 되는거임</p>
<p>이제 전체 main.cpp를 봐보자</p>
<pre><code class="language-cpp">int main(int argc, char** argv)
{
    TGAImage framebuffer(width, height, TGAImage::RGB);

    Triangle t1(Vec3(7,45,0), Vec3(35,100,0), Vec3(45,60,0));
    Triangle t2(Vec3(120,35,0), Vec3(90,5,0), Vec3(45,110,0));
    Triangle t3(Vec3(115,83,0), Vec3(80,90,0), Vec3(85,120,0));

    draw_triangle(t1.a.x, t1.a.y, t1.b.x, t1.b.y, t1.c.x, t1.c.y, framebuffer, red);
    draw_triangle(t2.a.x, t2.a.y, t2.b.x, t2.b.y, t2.c.x, t2.c.y, framebuffer, white);
    draw_triangle(t3.a.x, t3.a.y, t3.b.x, t3.b.y, t3.c.x, t3.c.y, framebuffer, green);

    float t1_area = get_triangle_area(t1);
    float t2_area = get_triangle_area(t2);
    float t3_area = get_triangle_area(t3);

    for (int i = 0; i &lt; height; i++)
    {
        for (int j = 0; j &lt; width; j++)
        {
            Vec3 p(j, i, 0);

            if (is_inside_triangle(t1_area, p, t1))
            {
                framebuffer.set(j, i, red);
            }

            if (is_inside_triangle(t2_area, p, t2))
            {
                framebuffer.set(j, i, white);
            }

            if (is_inside_triangle(t3_area, p, t3))
            {
                framebuffer.set(j, i, green);
            }
        }
    }

    framebuffer.write_tga_file(&quot;framebuffer.tga&quot;);
    return 0;
}</code></pre>
<p>height와 width를 전부 돌면서
삼각형 t1,t2,t3와 점 p가 어떤 관계인지를 파악하고
그걸 framebuffer에 넣어 출력하는거임</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f98d61e1-cc04-4923-82eb-02a92b2ced2e/image.png" />
그럼 이렇게 내부가 칠해진 삼각형이 나오게 됨~~ㄷㄷㄷㄷ</p>
<h1 id="바운딩-박스-최적화">바운딩 박스 최적화</h1>
<p>지금 코드는 모든 width와 height의 픽셀에 대하여 해당 픽셀이 삼각형 내부인지 아닌지를 판별함</p>
<p>근데, 사진에서 잘 보면 삼각형 어디에도 절대 포함될 수 없는 픽셀들이 존재함</p>
<p>빨간 삼각형을 기준으로 봐보자</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/6ef44f3a-3035-4b39-8b15-3c7f0f15b1f6/image.png" /></p>
<p>이렇게 빗금칠 된 공간은 절대로 삼각형 내부에 들어갈 수 없음</p>
<p>그래서 저부분을 빼고 계산을 해준다는 개념이 <code>바운딩 박스</code>임</p>
<p>먼저 모든 삼각형을 하나의 객체처럼 관리하기 위해 배열에 넣어줌</p>
<pre><code class="language-cpp">std::vector&lt;std::pair&lt;Triangle, TGAColor&gt;&gt; triangles; //add

int main(int argc, char** argv)
{
    TGAImage framebuffer(width, height, TGAImage::RGB);

    Triangle t1(Vec3(7,45,0), Vec3(35,100,0), Vec3(45,60,0));
    Triangle t2(Vec3(120,35,0), Vec3(90,5,0), Vec3(45,110,0));
    Triangle t3(Vec3(115,83,0), Vec3(80,90,0), Vec3(85,120,0));

    triangles.push_back(std::make_pair(t1, red));
    triangles.push_back(std::make_pair(t2, white));
    triangles.push_back(std::make_pair(t3, green));

    //...
}</code></pre>
<p>그리고 특정 점 p가 삼각형 내부인지 판별하기전에
해당 삼각형의 min, max x-y의 범위에서 loop를 돌도록 하면 됨</p>
<p>따라서 전체 main은 다음처럼 바뀜</p>
<pre><code class="language-cpp">std::vector&lt;std::pair&lt;Triangle, TGAColor&gt;&gt; triangles;

int main(int argc, char** argv)
{
    TGAImage framebuffer(width, height, TGAImage::RGB);

    Triangle t1(Vec3(7,45,0), Vec3(35,100,0), Vec3(45,60,0));
    Triangle t2(Vec3(120,35,0), Vec3(90,5,0), Vec3(45,110,0));
    Triangle t3(Vec3(115,83,0), Vec3(80,90,0), Vec3(85,120,0));

    triangles.push_back(std::make_pair(t1, red));
    triangles.push_back(std::make_pair(t2, white));
    triangles.push_back(std::make_pair(t3, green));

    draw_triangle(t1.a.x, t1.a.y, t1.b.x, t1.b.y, t1.c.x, t1.c.y, framebuffer, red);
    draw_triangle(t2.a.x, t2.a.y, t2.b.x, t2.b.y, t2.c.x, t2.c.y, framebuffer, white);
    draw_triangle(t3.a.x, t3.a.y, t3.b.x, t3.b.y, t3.c.x, t3.c.y, framebuffer, green);

    for (int ti = 0; ti &lt; triangles.size(); ++ti)
    {
        auto p = triangles[ti];
        Triangle t = p.first;
        TGAColor color = p.second;

         //box bounding
        int minX = std::min(std::min(t.a.x, t.b.x), t.c.x);
        int minY = std::min(std::min(t.a.y, t.b.y), t.c.y);
        int maxX = std::max(std::max(t.a.x, t.b.x), t.c.x);
        int maxY = std::max(std::max(t.a.y, t.b.y), t.c.y);

        float area = get_triangle_area(t);

        for (int j = minY; j &lt;= maxY; ++j)
        {
            for (int i = minX; i &lt;= maxX; ++i)
            {
                Vec3 point(i, j, 0);

                if (is_inside_triangle(area, point, t))
                {
                    framebuffer.set(i, j, color);
                }
            }
        }
    }

    framebuffer.write_tga_file(&quot;framebuffer.tga&quot;);
    return 0;
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/7bc61d00-d2bd-439e-a33e-e8f34b069419/image.png" /></p>
<p>잘 출력댐</p>
<h2 id="박스-바운딩-전후-속도차이">박스 바운딩 전/후 속도차이</h2>
<table>
<thead>
<tr>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/fc870e5f-eabc-48df-83c4-e3157ec4382f/image.png" /> 박스 바운딩 전</th>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/71f579c8-249f-4345-a514-c45835e7f1b8/image.png" /> 박스 바운딩 후</th>
</tr>
</thead>
</table>
<p>3개의 삼각형을 그리는데도 차이가 심함!</p>
<p>그러니까 바운딩 박스 최적화를 잘 사용해보자</p>
<h1 id="백페이스-컬링">백페이스 컬링</h1>
<p>지금 삼각형 내부를 판별하는 코드는 문제가 있음</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/1a6665d1-04ef-4712-ba1d-865fb4b48f2a/image.png" /></p>
<p>대충 어떤 모델을 렌더링한 결과임</p>
<p>잘 보면 이상한 부분이 많음
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/22e67532-8001-4a96-8c54-49720b4038e7/image.png" /></p>
<p>이게 이렇게 렌더링되는 이유는</p>
<ul>
<li>삼각형의 앞,뒷면을 가리지 않고 모두 렌더링 하기 때문임</li>
</ul>
<h2 id="코드-수정">코드 수정</h2>
<p>먼저 공부중인 코스에 맞춰 코드를 수정함</p>
<pre><code class="language-cpp">float get_triangle_area(Triangle t) 
{
    return 0.5 * (
        (t.b.y-t.a.y)*(t.b.x+t.a.x) + 
        (t.c.y-t.b.y)*(t.c.x+t.b.x) + 
        (t.a.y-t.c.y)*(t.a.x+t.c.x)
    );
}

void triangle(Triangle t, TGAImage &amp;framebuffer, TGAColor color) 
{
    int bbminx = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int bbminy = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int bbmaxx = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int bbmaxy = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_triangle_area(t);

#pragma omp parallel for
    for (int x=bbminx; x&lt;=bbmaxx; x++) 
    {
        for (int y=bbminy; y&lt;=bbmaxy; y++) 
        {
            double alpha = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.b.x, t.b.y, 0), Vec3(t.c.x, t.c.y, 0))) / total_area;
            double beta  = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.c.x, t.c.y, 0), Vec3(t.a.x, t.a.y, 0))) / total_area;
            double gamma =get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.a.x, t.a.y, 0), Vec3(t.b.x, t.b.y, 0))) / total_area;
            if (alpha&lt;0 || beta&lt;0 || gamma&lt;0) continue;
            framebuffer.set(x, y, color);
        }
    }
}</code></pre>
<p>이렇게 코드를 바꿈</p>
<p><code>is_inside_triangle</code>는 삭제하고 위처럼 코드를 수정함</p>
<p>중요한건</p>
<pre><code class="language-cpp">return 0.5 * (
        (t.b.y-t.a.y)*(t.b.x+t.a.x) + 
        (t.c.y-t.b.y)*(t.c.x+t.b.x) + 
        (t.a.y-t.c.y)*(t.a.x+t.c.x)
    );</code></pre>
<p>이부분임</p>
<h3 id="전개">전개</h3>
<p>지금은 2차원 평면이므로, 모든 좌표의 z좌표가 0임</p>
<p>두 3차원 벡터의 외적공식은 다음과 같음</p>
<blockquote>
<p>$\vec{a} \times \vec{b} = (a_y b_z - a_z b_y, a_z b_x - a_x b_z, a_x b_y - a_y b_x)$</p>
</blockquote>
<p>이때 벡터는 각각</p>
<blockquote>
<p>$\vec{b-a}, \vec{c-a}$ </p>
</blockquote>
<p>그리고 z축과 관련된 벡터의 요소는 0임</p>
<p>따라서 $\vec{b-a}, \vec{c-a}$의 외적은 다음과 같음(z축과 관련된 항 모두 제거)</p>
<blockquote>
<p>$\vec{b-a} \times \vec{c-a} = {(b_x - a_x)} \cdot {(c_y - a_y)} - {(b_y - a_y)} \cdot {(c_x - a_x)}$</p>
</blockquote>
<p>이걸 분배법칙을 이용해 전개해보면 다음과 같음</p>
<blockquote>
<p>$= b_xc_y - b_xa_y - a_xc_y + a_xa_y - (b_yc_x - b_ya_x - a_yc_x + a_ya_x)$
$= b_xc_y - b_xa_y - a_xc_y + a_xa_y - b_yc_x + b_ya_x + a_yc_x - a_ya_x$</p>
</blockquote>
<p>이때 두 스칼라의 곱셈법칙은 교환법칙이 성립함</p>
<blockquote>
<p>$= b_xc_y - b_xa_y - a_xc_y + a_xa_y - b_yc_x + b_ya_x + a_yc_x - .a_xa_y$
$= b_xc_y - b_xa_y - a_xc_y - b_yc_x + b_ya_x + a_yc_x$</p>
</blockquote>
<p>좀 어지러우니 식을 정리해보자</p>
<blockquote>
<p>$= a_xb_y - a_yb_x + b_xc_y - b_yc_x - a_xc_y  + a_yc_x$</p>
</blockquote>
<p>여기서 트릭 하나 발동</p>
<blockquote>
<p>$0 = b_xb_y - a_xa_y + c_xc_y - b_xb_y + a_xa_y - c_xc_y$</p>
</blockquote>
<p>라는 식 한개를 정의해보자
이항정리를 하면 0이 되는걸 알수있음</p>
<p>그럼 이 식은 합이 0이니, 위의 식에 넣어도 0이니, 값에 변화가 없음을 알 수 있음</p>
<blockquote>
<p>$= a_xb_y - a_yb_x + b_xc_y - b_yc_x - a_xc_y  + a_yc_x \ + b_xb_y - a_xa_y + c_xc_y - b_xb_y + a_xa_y - c_xc_y$</p>
</blockquote>
<p>와 같이 두개를 더한거나 아닌거나 기존의 외적과 같은 값이라는거임</p>
<p>그럼 이제 분배법칙을 이용하기 위해 식을 조금 정리해보자</p>
<blockquote>
<p>$= b_xb_y + a_xb_y - a_yb_x - a_ya_x \+ c_xc_y + b_xc_y - b_yc_x - b_yb_x \+ a_yc_x  + a_ya_x - a_xc_y   - c_xc_y$</p>
</blockquote>
<p>처럼 전개 가능함(개빡세네 에휴...)</p>
<p>그럼 이제 각 요소의 곱셈자리마다 분배법칙을 이용할 수 있음</p>
<blockquote>
<p>$= b_y(b_x + a_x) - a_y(b_x + a_x) \+ c_y(c_x + b_x) - b_y(c_x + b_x) \+ a_y(c_x  + a_x) - c_y(a_x + c_x)$</p>
</blockquote>
<p>로 식을 변환할 수 있음</p>
<p>그럼 이제 다시 곱셈법칙을 이용해서 처리할 수 있는데</p>
<blockquote>
<p>$= (b_y - a_y)(b_x + a_x)$
$+ (c_y - b_y)(c_x + b_x)$
$+ (a_y - c_y)(c_x  + a_x)$</p>
</blockquote>
<p>로 전개가 완료됨</p>
<hr />
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/662b0075-0775-4c6b-b002-745a8104ff12/image.png" /></p>
<p>원래대로 잘 출력됨!</p>
<p>이제 백페이스 컬링 할차례</p>
<h2 id="백페이스-컬링-1">백페이스 컬링</h2>
<pre><code class="language-cpp">void triangle(Triangle t, TGAImage &amp;framebuffer, TGAColor color) 
{
    int bbminx = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int bbminy = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int bbmaxx = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int bbmaxy = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_area(t);

#pragma omp parallel for
    for (int x=bbminx; x&lt;=bbmaxx; x++) 
    {
        for (int y=bbminy; y&lt;=bbmaxy; y++) 
        {
            double alpha = get_area(Triangle(Vec3(x,y,0), Vec3(t.b.x, t.b.y, 0), Vec3(t.c.x, t.c.y, 0))) / total_area;
            double beta  = get_area(Triangle(Vec3(x,y,0), Vec3(t.c.x, t.c.y, 0), Vec3(t.a.x, t.a.y, 0))) / total_area;
            double gamma =get_area(Triangle(Vec3(x,y,0), Vec3(t.a.x, t.a.y, 0), Vec3(t.b.x, t.b.y, 0))) / total_area;
            if (alpha&lt;0 || beta&lt;0 || gamma&lt;0) continue;
            framebuffer.set(x, y, color);
        }
    }
}</code></pre>
<p>이코드를 잘 봐보자</p>
<p>점 a,b,c가 있을때</p>
<p>오른손 좌표계를 기준으로 함</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/67dbeb4c-a6d2-4574-ae0c-601db8d9cbe2/image.png" /></p>
<p>처럼 됨.
이때 외적 벡터의 z는 양수가 됨</p>
<ul>
<li>왼손 좌표계면 z는 음수지 ㅇㅇ</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/dcc3d739-c08a-4ef4-aaba-512ee116f57e/image.png" /></p>
<p>하지만 이렇게 좌표가 있다면
오른손 좌표계 기준으로
외적 벡터의 z는 음수가 됨</p>
<ul>
<li>왼손 좌표계면 z는 양수지 ㅇㅇ</li>
</ul>
<p>이렇게 좌표계에 맞춰서
원하는 좌표계를 정했다면</p>
<p>정면이 아닌 z축을 가지는 삼각형은 렌더링을 패싱하면 되는거임</p>
<pre><code class="language-cpp">void triangle(Triangle t, TGAImage &amp;framebuffer, TGAColor color) 
{
    int bbminx = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int bbminy = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int bbmaxx = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int bbmaxy = std::max(std::max(t.a.y, t.b.y), t.c.y);

    double total_area = get_area(t);

    //add
    if (total_area &lt; 0) return;

#pragma omp parallel for
    for (int x=bbminx; x&lt;=bbmaxx; x++) 
    {
        for (int y=bbminy; y&lt;=bbmaxy; y++) 
        {
            double alpha = get_area(Triangle(Vec3(x,y,0), Vec3(t.b.x, t.b.y, 0), Vec3(t.c.x, t.c.y, 0))) / total_area;
            double beta  = get_area(Triangle(Vec3(x,y,0), Vec3(t.c.x, t.c.y, 0), Vec3(t.a.x, t.a.y, 0))) / total_area;
            double gamma =get_area(Triangle(Vec3(x,y,0), Vec3(t.a.x, t.a.y, 0), Vec3(t.b.x, t.b.y, 0))) / total_area;
            if (alpha&lt;0 || beta&lt;0 || gamma&lt;0) continue;
            framebuffer.set(x, y, color);
        }
    }
}</code></pre>
<p>그게 add 주석 부분임</p>
<p>졸라 간단스</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/fdaab9e8-5ede-4414-a6f4-062ed55f9925/image.png" />
<img alt="" src="https://velog.velcdn.com/images/vfx_master/post/828d246e-6691-4dcc-85da-098ab8561beb/image.png" /></p>
<p>이런 렌더링이 됨</p>
<p>하지만 아직 문제가 있음</p>
<p>뒷면에 해당되는 삼각형
예를들어 입안, 옷 안쪽은 무시되거나 다른 삼각형 위에 렌더링이 됨</p>
<p>이건 다음에 해결하도록<del>~</del></p>
<h1 id="전체-코드">전체 코드</h1>
<ul>
<li>main.cpp<pre><code class="language-cpp">#include &lt;chrono&gt;
#include &lt;cmath&gt;
#include &lt;cstdlib&gt;
#include &lt;ctime&gt;
#include &quot;tgaimage.h&quot;
#include &lt;iostream&gt;
#include &lt;fstream&gt;
#include &lt;string&gt;
</code></pre>
</li>
</ul>
<p>#include &quot;Model.h&quot;
#include &quot;Vec3.h&quot;</p>
<p>constexpr TGAColor white   = {255, 255, 255, 255};
constexpr TGAColor green   = {  0, 255,   0, 255};
constexpr TGAColor red     = {  0,   0, 255, 255};
constexpr TGAColor blue    = {255, 128,  64, 255};
constexpr TGAColor yellow  = {  0, 200, 255, 255};
constexpr int width  = 1024;
constexpr int height = 1024;</p>
<p>float get_triangle_area(Triangle t) 
{
    return 0.5 * (
        (t.b.y-t.a.y)<em>(t.b.x+t.a.x) + 
        (t.c.y-t.b.y)</em>(t.c.x+t.b.x) + 
        (t.a.y-t.c.y)*(t.a.x+t.c.x)
    );
}</p>
<p>void triangle(Triangle t, TGAImage &amp;framebuffer, TGAColor color) 
{
    int bbminx = std::min(std::min(t.a.x, t.b.x), t.c.x); 
    int bbminy = std::min(std::min(t.a.y, t.b.y), t.c.y); 
    int bbmaxx = std::max(std::max(t.a.x, t.b.x), t.c.x);
    int bbmaxy = std::max(std::max(t.a.y, t.b.y), t.c.y);</p>
<pre><code>double total_area = get_triangle_area(t);

//if (total_area &lt; 0) return;</code></pre><p>#pragma omp parallel for
    for (int x=bbminx; x&lt;=bbmaxx; x++) 
    {
        for (int y=bbminy; y&lt;=bbmaxy; y++) 
        {
            double alpha = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.b.x, t.b.y, 0), Vec3(t.c.x, t.c.y, 0))) / total_area;
            double beta  = get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.c.x, t.c.y, 0), Vec3(t.a.x, t.a.y, 0))) / total_area;
            double gamma =get_triangle_area(Triangle(Vec3(x,y,0), Vec3(t.a.x, t.a.y, 0), Vec3(t.b.x, t.b.y, 0))) / total_area;
            if (alpha&lt;0 || beta&lt;0 || gamma&lt;0) continue;
            framebuffer.set(x, y, color);
        }
    }
}</p>
<p>std::tuple&lt;int,int&gt; project(Vec3 v) 
{
    return { static_cast(std::round((v.x + 1.) * width / 2)),
             static_cast(std::round((v.y + 1.) * height / 2)) };
}</p>
<p>std::vector&lt;std::pair&lt;Triangle, TGAColor&gt;&gt; triangles;</p>
<p>int main(int argc, char** argv)
{<br />    if (argc != 2) 
    {
        std::cerr &lt;&lt; &quot;Err Usage: &quot; &lt;&lt; argv[0] &lt;&lt; &quot; obj/model.obj&quot; &lt;&lt; std::endl;
        return 1;
    }</p>
<pre><code>Model model(argv[1]);
TGAImage framebuffer(width, height, TGAImage::RGB);

// triangle(Triangle(Vec3(7, 45, 0), Vec3(35, 100, 0), Vec3(45,  60, 0)), framebuffer, red);
// triangle(Triangle(Vec3(120, 35, 0), Vec3(90,   5, 0), Vec3(45, 110, 0)), framebuffer, white);
// triangle(Triangle(Vec3(115, 83, 0), Vec3(80,  90, 0), Vec3(85, 120, 0)), framebuffer, green);

Vec3 camera_pos(0,0, 1);

for (int i = 0; i &lt; model.nfaces(); i++) 
{    
    auto [ax, ay] = project(model.vert(i, 0));
    auto [bx, by] = project(model.vert(i, 1));
    auto [cx, cy] = project(model.vert(i, 2));

    TGAColor rnd;
    for (int c = 0; c &lt; 3; c++) rnd[c] = std::rand() % 255;

    triangle(Triangle(Vec3(ax, ay, 0), Vec3(bx, by, 0), Vec3(cx, cy, 0)), framebuffer, rnd);
}

framebuffer.write_tga_file(&quot;framebuffer.tga&quot;);
return 0;</code></pre><p>}</p>
<pre><code>
- Vec3.h
```cpp
#ifndef VEC3_H
#define VEC3_H
#include &lt;cmath&gt;

class Vec3
{
public:
    float x, y, z;

    Vec3(float x_ = 0.0f, float y_ = 0.0f, float z_ = 0.0f)
        : x(x_), y(y_), z(z_) {}

    Vec3 operator-(const Vec3 &amp;v) const
    {
        return Vec3(this-&gt;x - v.x, this-&gt;y - v.y, this-&gt;z - v.z);
    }

    Vec3 operator+(const Vec3 &amp;v) const
    {
        return Vec3(this-&gt;x + v.x, this-&gt;y + v.y, this-&gt;z + v.z);
    }

    float&amp; operator[](int i)
    {
        if (i == 0) return x;
        if (i == 1) return y;
        return z;
    }

    const float&amp; operator[](int i) const
    {
        if (i == 0) return x;
        if (i == 1) return y;
        return z;
    }

    float length_squared() const 
    {
        return this-&gt;x * this-&gt;x + this-&gt;y * this-&gt;y + this-&gt;z * this-&gt;z;
    }

    float length() const 
    {
        return std::sqrt(length_squared());
    }
};

inline float dot(Vec3 v1, Vec3 v2)
{
    return v1.x * v2.x + v1.y * v2.y + v1.z * v2.z;
}

inline Vec3 cross(Vec3 v1, Vec3 v2)
{
    return Vec3(
        v1.y * v2.z - v1.z * v2.y,
        v1.z * v2.x - v1.x * v2.z,
        v1.x * v2.y - v1.y * v2.x);   
}

class Triangle
{
public:
    Vec3 a;
    Vec3 b;
    Vec3 c;
    Triangle(Vec3 v1, Vec3 v2, Vec3 v3) : a(v1), b(v2), c(v3) {}

};

#endif // VEC3_H</code></pre><ul>
<li>Model.h<pre><code class="language-cpp">#ifndef MODEL_H
#define MODEL_H
</code></pre>
</li>
</ul>
<p>#include 
#include </p>
<p>class Vec3;</p>
<p>class Model 
{
    std::vector verts = {};
    std::vector facet_vrt = {};
public:
    Model(const std::string filename);
    int nverts() const;
    int nfaces() const;
    Vec3 vert(const int i) const;
    Vec3 vert(const int iface, const int nthvert) const;
};</p>
<p>#endif // MODEL_H</p>
<pre><code>
- Model.cpp
```cpp
#include &lt;fstream&gt;
#include &lt;iostream&gt;
#include &lt;sstream&gt;
#include &quot;model.h&quot;
#include &quot;Vec3.h&quot;

Model::Model(const std::string filename) {
    std::ifstream in;
    in.open(filename, std::ifstream::in);
    if (in.fail()) return;
    std::string line;
    while (!in.eof()) {
        std::getline(in, line);
        std::istringstream iss(line.c_str());
        char trash;
        if (!line.compare(0, 2, &quot;v &quot;)) {
            iss &gt;&gt; trash;
            Vec3 v;
            for (int i : {0,1,2}) iss &gt;&gt; v[i];
            verts.push_back(v);
        } else if (!line.compare(0, 2, &quot;f &quot;)) {
            int f,t,n, cnt = 0;
            iss &gt;&gt; trash;
            while (iss &gt;&gt; f &gt;&gt; trash &gt;&gt; t &gt;&gt; trash &gt;&gt; n) {
                facet_vrt.push_back(--f);
                cnt++;
            }
            if (3!=cnt) {
                std::cerr &lt;&lt; &quot;Error: the obj file is supposed to be triangulated&quot; &lt;&lt; std::endl;
                return;
            }
        }
    }
    std::cerr &lt;&lt; &quot;# v# &quot; &lt;&lt; nverts() &lt;&lt; &quot; f# &quot;  &lt;&lt; nfaces() &lt;&lt; std::endl;
}

int Model::nverts() const { return verts.size(); }
int Model::nfaces() const { return facet_vrt.size()/3; }

Vec3 Model::vert(const int i) const {
    return verts[i];
}

Vec3 Model::vert(const int iface, const int nthvert) const {
    return verts[facet_vrt[iface*3+nthvert]];
}</code></pre>