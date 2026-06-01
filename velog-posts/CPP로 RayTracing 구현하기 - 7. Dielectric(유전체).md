<h1 id="유전체-절연체">유전체? 절연체?</h1>
<p>유전체는 더 큰 범주의 절연체임</p>
<p>절연체는 아예 전기가 흐르지 못하고, 전극이 와도 분극되지 못하고 그냥 아예 전기가 막힘</p>
<p>유전체는 전기의 흐름을 방해하는 의미에서 절연체임
유전체는 전극이 흐르면 이를 +, -로 분극을 함</p>
<p>따라서 좋은 유전체는 좋은 절연체이지만, 
역은 성립하지 않을 수 있음</p>
<h1 id="스넬-법칙">스넬 법칙</h1>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/3a0b2c3c-d696-4899-9ff0-37e4890f4364/image.png" /></p>
<blockquote>
<p>$\eta , \eta'$ = 매질에 따른 굴절율, 
$\theta , \theta'$ = 법선으로부터의 입사각, 굴절각</p>
</blockquote>
<p>스넬 법칙: $\eta * \sin \theta = \eta' * \sin \theta'$ </p>
<p>빛은 매질이 바뀔때, 빛이 닿은 경계면의 평행한 방향의 성분만 유지가 된다는걸 의미함.</p>
<p>각 물체는 굴절률이 다름
빛이 물체에 닿았을때 굴절이 되는데, 빛의 방향의 성분중에서 빛이 닿은 경계면과 평행한 성분만 유지가 되고, 나머지는 굴절율에 의해 굴절이 되는걸을 의미함.</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/f9cf0ad5-fdc5-4aff-8cc8-abb37d74c130/image.png" /></p>
<blockquote>
</blockquote>
<p>노란 벡터 = 빛 방향,
파란 벡터 = 경계면에서의 Normal
노란 점선 벡터 = 굴절된 후의 빛 벡터
회색 점선 벡터 = 경계면과 평행한 방향(방향만 유지된거임)</p>
<p>이렇게 경계면과 평행한 방향은 유지하면서, 다른 각도로 굴절되는 것이
스넬 법칙임</p>
<blockquote>
<p>중요!!!
입사 벡터, 법선 벡터는 모두 정규화된 벡터임!!</p>
</blockquote>
<h2 id="스넬-법칙-전개">스넬 법칙 전개</h2>
<blockquote>
<p>$\eta * \sin \theta = \eta' * \sin \theta'$ </p>
</blockquote>
<p>이 식을 살펴보자</p>
<p>우리에게 필요한 것은 $\sin\theta'$임
왜냐고?
표면 바깥에서 굴절되어 표면 내부로 들어올 것이기 때문에ㅇㅇ</p>
<p>따라서 료이키 텐카이를 통해 $\sin\theta'$만 남기고 이항 정리를 함</p>
<blockquote>
<p>$\sin\theta' = \frac{\eta}{\eta'}*\sin\theta$</p>
</blockquote>
<p>가 됨</p>
<h3 id="1-벡터-합으로-r-정의">1. 벡터 합으로 R' 정의</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/971839e6-e0f3-4945-a095-a0c6bf58d59b/image.png" /></p>
<blockquote>
<p>$R'$ = 경계에 닿은 빛이 굴절된 벡터
$\theta'$ = 굴절각
$R'_\bot$ = 법선 $n'$에서 수직인 벡터
$R'_\parallel$ = 법선 $n'$에서 평행인 벡터</p>
</blockquote>
<p>위의 사진에서 </p>
<blockquote>
<p>$R' = R'_\bot + R'_\parallel$ </p>
</blockquote>
<p>이라는 것을 확인할 수 있음</p>
<h3 id="2-r_bot--fracetaetar---r-cdot-n--n-정리">2. $R'_\bot = \frac{\eta}{\eta'}(R - (R \cdot N) * N)$ 정리</h3>
<p>핵심은 입사각 $R$을 이용하는 거임</p>
<p>현재 우리는 $R, \theta, n$을 알고있음</p>
<blockquote>
<p>$R = R_\bot + R_\parallel$ </p>
</blockquote>
<p>이라는 걸 알 수 있음
위에처럼 ㅇㅇ</p>
<ul>
<li>여기서 $R_\bot$은 $R'_\bot$과 방향이 동일하고
두 수직성분의 비율은 두 굴절율의 비율과 같음<ul>
<li>결국 경계면과 평행한 두 수직성분은
굴절율의 비율에 따라 크기가 변화하게 됨</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/e70da549-d9d2-492c-b03f-d423d6622e79/image.png" /></p>
<p>여기서 우리는 $R_\parallel$을 구할 수 있음</p>
<p><a href="https://velog.io/@vfx_master/CPP%EB%A1%9C-RayTracing-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0-6.-Matalic-%EC%9E%AC%EC%A7%88-%EA%B5%AC%ED%98%84#1-2-%EC%82%BC%EA%B0%81%ED%95%A8%EC%88%98">Metal재질 반사각 전개</a>
이 링크의 내용을 이용해 $R_\parallel$을 찾은 후 값을 찾아주면됨
아래에서 다시 설명함</p>
<blockquote>
</blockquote>
<p>$\cos\theta$ = 직각삼각형의 밑변 / 빗변
빗변 = 정규화된 벡터 = 1
$\cos \theta$ = 밑변 = $R_\parallel$</p>
<blockquote>
</blockquote>
<p>입사 벡터 $\cdot$(내적) 법선 = 입사벡터 * 법선벡터 $\cos \theta$
입사벡터 = 정규화된 벡터  = 1
법선벡터 = 정규화된 벡터 = 1
입사 벡터 $\cdot$ 법선 = $\cos \theta$</p>
<blockquote>
</blockquote>
<p>입사 벡터($R$) $\cdot$ 법선($N$) = $\cos \theta$ = $R_\parallel$</p>
<blockquote>
</blockquote>
<p>하지만 내적은 스칼라값이므로, 정규화된 벡터인 법선과 곱해서 정확한 밑변 벡터를 찾음</p>
<blockquote>
</blockquote>
<p>-&gt; $R_\parallel = (R \cdot N) * N$</p>
<blockquote>
<p>$R = R_\bot + R_\parallel$
= $R_\bot = R - R_\parallel$</p>
</blockquote>
<p>따라서 식을 전개해보면</p>
<blockquote>
<p>$R_\bot = R - (R \cdot N) * N$ </p>
</blockquote>
<p>이 되는걸 알 수 있음</p>
<blockquote>
<p>여기서 $R_\bot$은 $R'_\bot$과 방향이 동일하고
두 수직성분의 비율은 두 굴절율의 비율과 같음</p>
</blockquote>
<p>위에서 살펴본 위 특성에 의해 </p>
<blockquote>
<p>$R'_\bot = \frac{\eta}{\eta'}(R - (R \cdot N) * N)$</p>
</blockquote>
<p>가 되는 것을 알 수 있음</p>
<h3 id="3-r_parallel---sqrt1---r_bot2n-정리">3. $R_\parallel = -\sqrt{1 - |R'_\bot|^2*n}$ 정리</h3>
<p>2번은 경계와 평행한 성분인 수직성분을 구함</p>
<p>이번에는 경계와 수직인 성분인 평행성분을 구할 차례임</p>
<p>안타깝게도 수직인 성분에 대해서는
두 굴절률의 비율에 따라 값이 변화하지 않음</p>
<p>따라서 피타고라스 공식을 이용해서 구해주면 됨</p>
<blockquote>
<p>피타고라스 정리</p>
<ul>
<li>빗변$^2$ = 밑변$^2$ + 높이$^2$</li>
</ul>
</blockquote>
<p>이때 각 변은 스칼라 값임
그냥 크기라는 거임</p>
<blockquote>
</blockquote>
<p>따라서 값을 구해주면, 나중에 벡터를 곱해서 방향을 만들어줘야함</p>
<p>빗변 = $R'$ =정규화된 벡터 = 1
밑변 = |$R'_\bot$| = |$R' - (R' \cdot N) * N$|
높이 = |$R'_\parallel$|</p>
<p>이걸 정리해보면</p>
<blockquote>
<p>$1^2 = (R'_\bot)^2 + (R'_\parallel)^2$</p>
</blockquote>
<p>이 됨</p>
<p>이항정리를 하면</p>
<blockquote>
</blockquote>
<p>$(R'_\parallel)^2 = 1^2 - ((R'_\bot) * N)^2$</p>
<blockquote>
</blockquote>
<p>$R'_\parallel = \sqrt{1 - ((R'_\bot) * N)^2}$</p>
<p>위처럼 정리가 됨</p>
<p>그리고 이미 구한 $R'_\bot$을 이용하면</p>
<blockquote>
</blockquote>
<p>$R'_\parallel = \sqrt{1 - ((R' - (R' \cdot N) * N) * N)^2}$</p>
<p>이라는 식을 구할 수 있음</p>
<h3 id="4-r--r_bot--r_parallel">4. $R' = R'_\bot + R'_\parallel$</h3>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/971839e6-e0f3-4945-a095-a0c6bf58d59b/image.png" /></p>
<p>다시 이 사진을 보면</p>
<p>$R' = R'_\bot + R'_\parallel$ 이라는 것을 알 수 있음</p>
<h2 id="굴절-코드-구현">굴절 코드 구현</h2>
<pre><code class="language-cpp">//Vector3.h...

inline Vector3 refract(const Vector3&amp; uv, const Vector3&amp; n, double etai_over_etat)
{
    auto cos_theta = std::fmin(dot(-uv, n), 1.0);
    Vector3 r_out_perp =  etai_over_etat * (uv + cos_theta*n);
    Vector3 r_out_parallel = -std::sqrt(std::fabs(1.0 - r_out_perp.length_squared())) * n;
    return r_out_perp + r_out_parallel;
}</code></pre>
<p>먼저 $\cos \theta$를 구하고, 
그걸 이용해서 수직성분, 평행성분을 구해준 후 더하면
굴절된 벡터인 $R'$를 구하는거임!</p>
<p>Dielectric머티리얼 구현</p>
<pre><code class="language-cpp">class Dielectric : public Material
{
public:
    Dielectric(double refraction_index) : refraction_index(refraction_index) {}

    bool scatter(
        const Ray&amp; r_in,
        const HitRecord&amp; rec,
        Color&amp; attenuation,
        Ray&amp; scattered) const override
    {
        attenuation = Color(1,1,1);
        double ratio_of_refraction_index = rec.front_face ? 1.0 / refraction_index : refraction_index;

        Vector3 unit_direction = unit_vector(r_in.direction());
        Vector3 refracted = refract(unit_direction, rec.normal, ri);

        scattered = Ray(rec.p, refracted);
        return true;
    }

private:
    //굴절률
    double refraction_index;
};</code></pre>
<p>여기서 핵심은</p>
<pre><code class="language-cpp">double ratio_of_refraction_index = rec.front_face ? 1.0 / refraction_index : refraction_index;</code></pre>
<p>임!</p>
<p>공기의 굴절률은 1에 수렴함
공기에서 물체의 내부로 들어가는건 front_face인거임</p>
<p>따라서 굴절률 비율은 </p>
<blockquote>
<p>$1(공기) / 굴절률 = \frac{\eta}{\eta'}$ </p>
</blockquote>
<p>인거임!</p>
<p>그리고 내부에서 외부로 나올때는 front_face가 아니므로</p>
<blockquote>
<p>$굴절률 / 1(공기) = \frac{\eta'}{\eta} = \eta'$ </p>
</blockquote>
<p>가 되는거임</p>
<p>이제 사용해보자</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/3eeef65d-a527-438e-a622-5d8d2a38bf16/image.png" /></p>
<p>한번 다이아몬드의 굴절률을 사용해보자</p>
<pre><code class="language-cpp">auto material_left   = make_shared&lt;Dielectric&gt;(2.417);</code></pre>
<p>이렇게 수정하고 렌더링을 해보면...</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/a7eee10d-5ff7-4930-9ba1-fbaa635afcfd/image.png" /></p>
<h1 id="전반사-total-internal-reflection">전반사 (Total Internal Reflection)</h1>
<table>
<thead>
<tr>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/74509987-b5d3-40b3-a8a1-77ee5b9ba4e3/image.png" /></th>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d5cd19f6-d086-4ae0-8fba-f94346aea995/image.png" /></th>
</tr>
</thead>
</table>
<p>밀도가 높은 물질에서 밀도가 낮은 물질로 빛이 나가면 어떻게 될까
무조건 굴절이 될까?</p>
<p>절대 그렇지 못함</p>
<p>위의 세션인 <code>스넬 법칙</code>에서 살펴봤듯</p>
<blockquote>
<p>$\sin \theta * \eta = \sin \theta' * \eta'$
$\sin\theta' = \frac{\eta}{\eta'} * \sin\theta$</p>
</blockquote>
<p>처럼 식이 정리 가능하다.</p>
<blockquote>
<p>밀도 높은 물질의 굴절률 = $\eta$ = 2.4(다이아몬드)
밀도 낮은 물질의 굴절률 = $\eta'$ = 1(공기)</p>
</blockquote>
<p>라고 해보자</p>
<blockquote>
<p>$\sin\theta' = {2.4} * \sin\theta$</p>
</blockquote>
<p>이 됨</p>
<p>근데 이러면 문제가 있음</p>
<p>$\sin \theta$가 대략 0.45정도만 되어도 $\sin\theta'$가 1을 넘어가버림
$\sin \theta$는 최대값이 1임</p>
<p>따라서 물질 내부에서 굴절이 안되는 각도의 빛은 반사가 됨</p>
<p>즉, $\sin \theta$가 1이 넘어가는 경우, 임계각을 넘어가는 경우는 굴절이 아닌 반사를 해줘야함</p>
<p>$\sin\theta$는 피타고라스 정리를 이용해서 구할 수 있음</p>
<blockquote>
<p>$\sin^2\theta + \cos^2\theta = 1$
$\sin^2\theta = 1 - \cos^2\theta$
$\sin\theta = \sqrt{1 - \cos^2\theta}$</p>
</blockquote>
<p>임</p>
<p>위의 스넬법칙에서 $\cos\theta = R\cdot N$이라는 것도 알아냄</p>
<p>따라서 <code>Dielectric</code>머티리얼의 scatter메서드를 조금 수정해주면 됨</p>
<pre><code class="language-cpp">class Dielectric : public Material
{
public:
    Dielectric(double refraction_index) : refraction_index(refraction_index) {}

    bool scatter(
        const Ray&amp; r_in,
        const HitRecord&amp; rec,
        Color&amp; attenuation,
        Ray&amp; scattered) const override
    {
        attenuation = Color(1,1,1);
        double ratio_of_refraction_index = rec.front_face ? 1.0 / refraction_index : refraction_index;

        Vector3 unit_direction = unit_vector(r_in.direction());

        //add
        double cos_theta = std::fmin(dot(-unit_direction, rec.normal), 1.0); //코사인 세타
        double sin_theta = std::sqrt(1 - cos_theta * cos_theta);

        bool cant_refract = ratio_of_refraction_index * sin_theta &gt; 1;
        Vector3 direction;

        if (cant_refract)
        {
            direction = reflect(unit_direction, rec.normal);
        }
        else
        {
            direction = refract(unit_direction, rec.normal, ratio_of_refraction_index);
        }

        scattered = Ray(rec.p, direction);
        return true;
    }

private:
    //굴절률
    double refraction_index;
};</code></pre>
<p>cos_theta를 이용해서 sin_thete를 구하고
굴절 가능한지 판별을 한 후
reflect/refract를 수행함</p>
<p>대충 물속(1.33)의 공기(1)이라고 가정하고 </p>
<pre><code class="language-cpp">auto material_left   = make_shared&lt;Dielectric&gt;(1.00/1.33);</code></pre>
<p>과 같은 코드를 짬ㅇㅇ
그럼 다음과 같은 이미지 차이가 생김</p>
<p>왼쪽은 전반사 코드 추가 전/ 오른족은 전반사 코드 추가 후</p>
<table>
<thead>
<tr>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d08497eb-377b-4737-97f5-6b32aec3f97a/image.png" />전반사 추가 전</th>
<th><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/d43a9874-2930-41f8-a2f3-927bbe41e0a3/image.png" />전반사 추가 후</th>
</tr>
</thead>
</table>
<p>이렇게 반사가 다르게 되는걸 볼 수 있음</p>
<h1 id="슐릭-근사schlick-approximation-프레넬-효과">슐릭 근사(schlick approximation), 프레넬 효과</h1>
<p>프레넬 효과는 유니티 쉐이더그래프를 하며 여러번 다뤄서 익숙함</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/4dbcbf27-0ae0-4d83-b05d-dc26e987222b/image.png" /></p>
<p>프레넬은 빛의 입사각에 따라 반사되는 빛의 양이 달라지는 것을 의미함
그리고 모든 물체는 프레넬을 가짐</p>
<p>위 사진을 보면
프레넬을 가진 주전자는 입사각이 수직에 가까울때는 비교적 어둡고, 더 완만할때는 빛의 반사가 많은 것을 볼 수 있음(외곽부분)</p>
<p>우리가 만든 구체는 저렇게 프레넬 효과가 적용되지 않은 상태여서,
약간 이상하게 보임</p>
<h2 id="프레넬-방정식-vs-슐릭-근사">프레넬 방정식 VS 슐릭 근사</h2>
<p>프레넬을 구할때, 
프레넬 방정식과 슐릭 근사 중 하나를 골라서 사용하면 된다</p>
<h3 id="프레넬-방정식">프레넬 방정식</h3>
<blockquote>
<p>$F_{equation} = (\frac{\eta * \cos\theta - \eta' * cos\theta'}{\eta * \cos\theta + \eta' * cos\theta'})^2$</p>
</blockquote>
<p>$\eta$ : 입사 매질 굴절률
$\eta'$ : 투과 매질 굴절률
$\theta$ : 입사각
$\theta'$ : 굴절각</p>
<h3 id="슐릭-근사">슐릭 근사</h3>
<blockquote>
<p>$F_{schlick} = R0 + (1- R0)(1 - \cos\theta)^5$</p>
</blockquote>
<p>$R0$ : 빛이 표면에 수직으로 입사했을때 반사율
$cos\theta$ : 입사각(0과 가까울수록 수직, 1과 가까울수록 평행)</p>
<blockquote>
<blockquote>
<p>$R0 = (\frac{1 - \eta}{1 + \eta})^2$</p>
<p>$\eta$ : 굴절률</p>
</blockquote>
</blockquote>
<p>코드 구현의 난이도로 보면 슐릭근사가 더 빠르고 쉽게 계산됨</p>
<hr />
<pre><code class="language-cpp">class Dielectric : public Material
{
    //...

    bool scatter(
        const Ray&amp; r_in,
        const HitRecord&amp; rec,
        Color&amp; attenuation,
        Ray&amp; scattered) const override
    {
        //...

        if (cant_refract || schlick_approximate(cos_theta, ratio_of_refraction_index) &gt; random_double())
        {
            direction = reflect(unit_direction, rec.normal);
        }
        //...
    }

private:
    //...

    static double schlick_approximate(double cos, double ratio_of_refraction_index)
    {
        double r0 = (1 - ratio_of_refraction_index) / (1 + ratio_of_refraction_index);
        r0 = r0 * r0;

        return r0 + (1 - r0) * std::pow((1 - cos), 5);
    }
};</code></pre>
<blockquote>
<p>여기서 random_double과 비교하는 이유가 있음</p>
</blockquote>
<p>특정 입사각에서 반사되어야 하는 빛의 양이 0.5라고 해보자</p>
<blockquote>
</blockquote>
<ol>
<li>random_double()을 통해 0.4가 나왔다면<ul>
<li>반사되어야 할때
무조건 반사</li>
<li>굴절되어야 할때
조건을 만족하지 못하므로 굴절</li>
</ul>
</li>
<li>random_double()을 통해 0.6이 나왔다면<ul>
<li>반사되어야 할때
무조건 반사</li>
<li>굴절되어야 할때
조건을 만족하므로 반사<blockquote>
</blockquote>
이렇게 조건에 따라 확률적으로 빛을 반사시켜 프레넬 효과를 만드는거임</li>
</ul>
</li>
</ol>
<p>이걸 이용해서 기존의 왼쪽 구체를 hollow유리 구체로 만들고,
그 구체 앞에 작은 다이아몬드 구체를 만들어보도록 하겠음</p>
<p>유리의 굴절률은 1.5정도임</p>
<pre><code class="language-cpp">#include &quot;Camera.h&quot;
#include &quot;Color.h&quot;
#include &quot;HittableList.h&quot;
#include &quot;hittable.h&quot;
#include &quot;RTWeekend.h&quot;
#include &quot;Sphere.h&quot;

using namespace std;

int main()
{
    HittableList world;

    auto material_ground = make_shared&lt;Lambertian&gt;(Color(0.8, 0.8, 0.0));
    auto material_center = make_shared&lt;Lambertian&gt;(Color(0.1, 0.2, 0.5));

    //hollow glass
    auto material_left = make_shared&lt;Dielectric&gt;(1.5);
    auto material_bubble = make_shared&lt;Dielectric&gt;(1.00/1.5);
    //diamond sphere
    auto material_left_front = make_shared&lt;Dielectric&gt;(2.417);

    auto material_right  = make_shared&lt;Metal&gt;(Color(0.8, 0.6, 0.2), 0.5);

    world.add(make_shared&lt;Sphere&gt;(Point3( 0.0, -100.5, -1.0), 100.0, material_ground));
    world.add(make_shared&lt;Sphere&gt;(Point3( 0.0,    0.0, -1.2),   0.5, material_center));

    //hollow glass
    world.add(make_shared&lt;Sphere&gt;(Point3(-1.0,    0.0, -1.0),   0.5, material_left));
    world.add(make_shared&lt;Sphere&gt;(Point3(-1.0, 0.0, -1.0), 0.45, material_bubble));
    //diamond sphere
    world.add(make_shared&lt;Sphere&gt;(Point3(-1.0, 0.0, -0.3), 0.1, material_left_front));

    world.add(make_shared&lt;Sphere&gt;(Point3( 1.0,    0.0, -1.0),   0.5, material_right));

    Camera cam;
    cam.aspect_ratio = 16.0 / 9.0;
    cam.image_width = 1200;
    cam.samples_per_pixel = 300;
    cam.max_depth = 50;

    cam.render(world);
}</code></pre>
<p>이렇게 수정함</p>
<p>그러고 렌더링 해보면...</p>
<p><img alt="" src="https://velog.velcdn.com/images/vfx_master/post/8ffb6d73-0ae0-450e-b8a1-be0dea06bd81/image.png" /></p>